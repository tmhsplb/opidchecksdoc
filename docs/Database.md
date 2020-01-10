# Database
OPIDDaily is a database driven application built using SQL Server technology. In the desktop environment OPIDDaily is built using the Sql Server Express
database engine. In the online environment at AppHarbor a full SQL Server is used. The two versions are compatible with each other with respect to the
database features used. SQL Server Management Studio v18.0 (SSMS) is used to manage both database engines. In the desktop environment, Windows
Authentication is used to connect to the Sql Server Express database. In the online enviroment, SQL Server Authentication is used to connect to the
SQL Server database.

When application OPIDDaily was created at AppHarbor, a free version of SQL Server was added on through the AppHarbor interface. The connection string
to this SQL Server is found by selecting the SQL Server add-on and following the "Go to SQL Server" link on the page that appears. The value of this
connection string is stored as the value of Config.WorkingProductionConnection string on file Config.cs. The 3 components of the connection string,
HostName, UserName and Password, are also displayed on this page. The components may be used to configure a SQL Server Authentication connection to
the AppHarbor database through SSMS.

The same connection string displayed at AppHarbor is retrieved at runtime by accessing Config.ConnectionString, which returns the value of
SQLSERVER_CONNNECTION_STRING configured in the `<appSettings>` section of file Web.config. The statically configured value on file Web.Config points to
the OpidDailyDB on the desktop SQL Server Express. At runtime, AppHarbor will overwrite this statically configured value with the value displayed at
AppHarbor. See the section on the Connection String on this tab.

## Connection String
In the desktop environment, SSMS was used to create an empty project database by executing the SQL query

    create database OpidDailyDB

The Visual Studio Server Explorer (found under the OPIDDaily project View menu) was then used to discover the connection string to database OpidDailyDB
by creating a new Data Connection to it and copying the Connection String property of the data connection as the value of the variable
SQLSERVER_CONNECTION_STRING in the `<appSettings>` section of Web.config. The value is accessed on files IDentityDB.cs and OpidDailyDB.cs by
reading the value of the static variable Config.ConnectionString.

The online version of OPIDDaily is hosted as an application at AppHarbor and it uses a database server provided as an add-on. The add-on database server
includes a database which serves as the application database, so it is not necessary to create the application database as was done above for the
desktop version.

ELMAH uses the configuration string used by the OPIDDaily application. This is accomplished by configuring the connection
string **OpidDailyConnectionString** in the `<connectionStrings>` section on Web.config and setting the connection string alias for the SQL Server
add-on at AppHarbor to be **OpidDailyConnectionString**. To set this alias, select the SQL Server add-on for application OPIDDaily at AppHarbor and then
follow  the link "Go to SQL Server" on the page that appears. Click the button labeled "Edit database configuration" to set
**OpidDailyConnectionString** as the alias value for the connection string. When this is done, OpidDailyConnectionString will appear as the value of
SQLSERVER_CONNECTION_STRING_ALIAS in the Configuration variables section of application OPIDDaily at AppHarbor.

When application OPIDDaily is deployed to AppHarbor, this alias will overwrite the configured value on file
Web.config by the value of the connection string for the AppHarbor database. This is explained in the same knowledge base article referenced above.

## Database Diagram
![Database Diagram](OpidDailyDB.png)

The diagram was created by SSMS, copied to the clipboard (using the "Copy Diagram to Clipboard" command found on the freespace context menu) and then
pasted into the Paint tool. Inside of Paint it is saved as a .PNG file. Version 18.0 of SSMS does not allow database diagrams to be created. Newer
releases have restored this capability. But the diagram seen here was created by an earlier release of SSMS, which did have the ability to create
database diagrams.

The 3 tables in the upper left of the above diagram are created by ASP.NET Identity 2.0 to manage registered users of OPIDDaily. The 3 tables are
managed by their own data context which cannot be augmented by additional tables.  However, data fields can be added to table **AspNetUsers** if
necessary. The data field AgencyId was added to table **AspNetUsers** to store the unique AgencyId stored in table
**Agencies** by the Superadmin user. (The AgencyId of an Operation ID user - example the TicketMaster user -  is
always 0.) Non ASP.NET Identity tables in the diagram are managed by a separate data context.

The 2 data contexts of project OPIDDaily are referred to as IdentityDb and OpidDailyDB. (See the section Entity Framework Code First of the
Infrastructure tab.) The technique for establishing a single connection string over 2 data contexts is described in
[Scott Allen's Pluralsight video](https://app.pluralsight.com/player?author=scott-allen&name=aspdotnet-mvc5-fundamentals-m6-ef6&mode=live&clip=1&course=aspdotnet-mvc5-fundamentals).

The tables belonging to data context OpidDailyDB were created at AppHarbor using a script file. This was done by running the command

    PM> update-database -ConfigurationTypeName OPIDDaily.DataContexts.OPIDDailyMigrations.Configuration -Script -SourceMigration $InitialDatabase

to generate a SQL script to be run in SSMS against the database at AppHarbor.  

The command created a script file necessary to create the tables for this data context using all the migrations applied since the initial migration.
Notice that there is no colon (:) following -SourceMigration in this command.

To generate a script to run the Down methods of multiple down-migrations, do, for example,

    PM> Update-Database -ConfigurationTypeName OPIDDAILY.DataContexts.OpidDailyMigrations.Configuration -Script -TargetMigration: ExpressClient

Notice that there is a colon (:) following -TargetMigration in this command. This command will create a script to run the Down methods of all
migrations since (and not including) migration ExpressClient. It is important to be able to generate this script if changes need to be backed out,
because the deployed versions of application OPIDDaily do not use Entity Framework to manage the database.

## Managing Users
OPIDDaily is a role based database application administered by a Superadmin user. The Superadmin user has the responsibilty of establishing a login
account for each OPIDDaily user, which includes the use's role. This is done prevent a user from specifying his/her own role when logging in and to
force the user into his/her assigned role instead. See the introduction and Role Controllers sections of the Impementation tab for a discussion of
roles.

The Superadmin will be given a user name and email address for a new user. For example, if Mary Atwood would like to use the user name Mary and email
address maryatwood@gmail.com, this request would be given to the Superadmin user. Provided that the user name Mary is not already in use, the Superadmin
user would use a private interface to enter Mary Atwood in the **Invitations** table under UserName Mary (with FullName Mary Atwood) and Email Address
maryatwood@gmail.com. The Supeadmin would also use the OPIDDaily interface to assign a role to user Mary Atwood in the **Invitations** table.

The record in the **Invitations** table is in effect an invitation for Mary Atwood to register under user name Mary and email address
maryatwood@gmail.com in the assigned role. The Superadmin will notify Mary that her account has been created and that she may
register with application OPIDDaily using the credentials she has supplied together with a password of her own choosing.  

When Mary registers, the user name and email address she provides will be checked against the **Invitations** table. If this pair of credentials is not
found in the **Invitations** table, Mary's attempt to register will be rejected. If they are found, a record will be created for her in the
**AspNetUsers** table using the password she has specified and using the role assigned by the Superadmin, which has been stored in the **Invitations**
table. On subsequent visits to OPIDDaily, Mary may simply login with the credentials established by her registration. When logged in she will be
recognized in her assigned role.

User email addresses do not need to be unique per account. This is not the default behavior; it is enabled by the setting

    RequireUniqueEmail = false

in method ApplicationUserManager.Create on file App_Start/IdentityConfig.cs

There are two special accounts reserved for usage by the two users who serve at the front desk on any given day of operation. Each of these accounts
has the pre-assigned role called FrontDesk. The users are the **Screener** and the **TicketMaster** which correspond to the pipeline stages
**Screening** and **Checkin**, respectively. (See the  Background tab for information about the pipeline stages.) Having dedicated accounts avoids the
need to create unique accounts in the role of FrontDesk.

There are also two additional special users called **Client1** and **Client2** corresponding to the pipeline stages **Screening** and **CheckIn**,
respectively. (See the  Background tab for information about the pipeline stages.) During the screening stage, the **Screener** user will enter the name
and date of birth  of an entering client into the OPIDDaily database. To ensure that this information has been correctly entered, the **Screener** may
click a button to  have this information appear on a small tablet computer which will be handed to the client for verification. This small tablet
computer will be logged  into the OPIDDaily application as **Client1**. During the **Checkin** stage, by consultng the Apricot database, the
**TicketMaster**  user will record any  previous visit history by a screened client in the OPIDDaily database. If previous visits indicate that the
screened client is ineligible for a service  being  sought, the **TicketMaser** may click a button to have the visit history appear on a second small
tablet computer which  will be handed to the client.  This second small tablet computer will be logged into the OPIDDaily application as **Client2**.
Both users **Client1** and **Client2** are assigned the role FrontDesk.

## Database Utilization
SSMS can be used to check on the utilization of a datbase. To do so:

* Right click a database name
* Navigate to Reports > Standard Reports > Disk Usage

This is important because of the 20MB disk space limit of a free AppHarbor database. Use it to make sure the disk limits are not exceeded.

Application OPIDDaily allows the Superadmin user to delete client datbase records corresponding to previous days of operation. This must be done
periodically to avoid exceeding the 20MB limit.
