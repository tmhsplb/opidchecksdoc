

## Hosting Environments
There are 2 hosting environments for application OPIDChecks: desktop and production. They differ in the database
connection string used by each.
The connection string is configured as the value of variable SQLSERVER_CONNECTION_STRING in the `<appSettings>` section
of Web.config. The static value configured there is used by the desktop environment. The static value is overwritten by
injection (at AppHarbor)
when OPIDChecks is deployed to create the production release. The transformation file Web.Release.config
plays a role in this deployments. The production deployment at AppHarbor
has its Environment variable set to Release by default. This causes Web.Release.config to be used upon deployment.

## Git for Windows
Visual Studio 2019 (Community Edition) comes with built-in support for GitHub. A new project can be added to Git source control on the desktop by simply
selecting `Add to Source Control` from the context menu of the Solution file in the Solution Explorer. Once a project is under Git source control it can
be added to a remote GitHub repository by using tools available through Visual Studio. However, a technique preferred by many developers is to use [Git
for Windows](https://git-for-windows.github.io/). Git for Windows provides a BASH shell interface to GitHub which uses the same set of commands
available at GitHub itself. Git for Windows integrates with Windows Explorer to allow a BASH shell to be opened on a project that has been added to a
desktop Git repository. Simply point Windows Explorer at the folder containing the project solution file and select `Git BASH Here` from the context
menu of the folder to open a Git for Windows BASH shell. Then execute Git commands from this shell window. Git for Windows also offers Git GUI, a
graphical version of most Git command line functions. To open Git GUI simply select `Git GUI Here` from Windows Explorer.

## GitHub
Application OPIDChecks is stored at GitHub as a repository under an account with the email address peter3418@ymail.com and account name tmhsplb.

Only user tmhsplb can deploy directly to this repository. Any other user needing to deploy a version of OPIDChecks to
this repository
must be declared a collaborator on repository OPIDChecks by user tmhsplb. A collaborator is a user associated with a
different account established at GitHub.  

Git for Windows was used to create a remote to save to this GitHub account. The remote was created in the Git BASH shell by opening the shell on the
folder which contains the OPIDDaily.sln file (folder `C:/VS2019Projects/OPIDChecks`) and issuing the command

    git remote add origin https://github.com/tmhsplb/opidchecks.git

Creating this remote only needs to be done once, because Git for Windows stores the remote.

To remove a remote use the command

     git remote rm myremote

The need for this may arise if there was a typo in the creation of myremote.

## AppHarbor
AppHarbor (appharbor.com) is a Platform as a Service Provider which uses Amazon Web Services infrastructure for hosting applications and Git as a
versioning tool. When an application is defined at AppHarbor, a Git repository is created to manage versions of the application's deployment.
The OPIDChecks application is defined as an application at AppHarbor to create the production repository of the desktop
application. The remote configured for OPIDChecks at AppHarbor is:

    https://tmhsplb@appharbor.com/opidchecks.git

This remote is configured from a Windows Git BASH shell by the command

    git remote add opidchecks https://tmhsplb@appharbor.com/opidchecks.git  

After the remote is configured in the Git BASH shell, issuing the command

    git push opidchecks master

will deploy the master branch of solution opidchecks to AppHarbor as application OPIDChecks, accessible through the URL

    https://opidchecks.apphb.com

If you reset your password at AppHarbor, the 'git push' command will no longer work from the Git BASH shell. You need
to have Git prompt you for your new password. To do this on a Windows 10 machine, go to

    Control Panel > User Accounts > Credential Manager > Windows Credentials

and remove the AppHarbor entry under Generic Credentials. The next time you push, you will be prompted for your
repository password.

Application OPIDChecks is deployed using the free Canoe subscription level at AppHarbor. Under a Canoe subscription,
the IIS application pool of
application OPIDChecks has a 20 minute timeout, which forces OPIIDChecks to spin up its resources again after 20
minutes of idle time.

The free Yocto version of SQL Server is used as an add-on to the OPIDChecks deployment. The Yocto version has a limit
of 20MB of storage space.  However, the database usage must be monitored to avoid exceeding the 20MB limit.
See the Database Utilization section on the Database tab for how to do this. A paid subscription to a SQL Server at
AppHarbor would alleviate this problem.

## Deployment
This section summarizes deployment to AppHarbor. Much of the information here can be found in the section on AppHarbor.

After configuring the **master remote** the Visual Studio production branch can be deployed to AppHarbor by using the Git BASH Shell command

    git push opidchecks master

AppHarbor will automatically deploy application OPIDChecks if the push results in a successful build. After AppHarbor
finishes building and deploying the code, application OPIDChecks can be viewed at

    https://opidchecks.apphb.com
