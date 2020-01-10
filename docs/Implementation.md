# Implementation
Application OPIDDaily is implemented as an ASP.NET Framework application using the ASP.NET MVC 5 project template provided by Visual Studio 2019
(Community Edition). It uses ASP.NET Identity 2.0 to define a  set of user roles. Each user role is associated with a separate MVC controller.
Controller inheritance is used to share editing functionality across user roles.

It may be useful to upgrade application OPIDDaily to use the more modern ASP.NET Core technology. It may also be useful to proivde an alternative to the
free hosting service AppHarbor, which is currentl used by OPIDDaily. See the AppHarbor section of the Infratructure tab for details on this.

The graphical user interface of OPIDDaily is built using Bootstrap 3.0.0. Each user role is associated with its own layout file which defines a
Bootstrap navbar containing links to the OPIDDaily features available to users in the role. The ASP.NET Identity system ensures that a user in a
specified role cannot visit any pages outside of those allowed to users in that role. (See the section on Role Controllers.)

Because of its use of SignalR, application OPIDDaily will always require a server side component. See the section on SignalR on this tab for a
discussion of this.

## The Superadmin User
OPIDDaily defines a pre-registered Superadmin user who has privileges to

* create new roles
* invite new users to register in a pre-determined role
* add new agencies

The credentials for the Superadmin user are configured on file Startup.cs. There is only a single user with role of Superadmin.

## MVC Routing
Application OPIDDaily uses only the default routing rule supplied by the Visual Studio MVC 5 template. This default routing rule is found in
`.../App_Start/RoutConfig.cs`.

    routes.MapRoute(
      name: "Default",
      url: "{controller}/{action}/{id}",
      defaults: new { controller = "Users", action = "Index", id = UrlParameter.Optional }
    );

For the sake of simplicity, future development of application OPIDDaily should strive to keep this as the one and only routing rule.

## Role Controllers
There is an MVC controller defined for each role defined by the superadmin user. Each controller defined for a role inherits from SharedController to
implement shared funtionality. The role controllers manage the views of application OPIDDaily. The implementation of each role controller
defines methods that are accessible through the menubar defined on the layout file for the role. For example, the FrontDeskController - which implements
the FrontDesk role - contains methods ExpressClient and ExistingClient (found on the SharedController) invoked from the menubar defined on file

   ~/Shared/_FrontDesk.cshtml.

This is the layout file for the FrontDesk role. Each view returned by the FrontDeskController includes this layout file, thereby ensuring that a
user in the role of FrontDesk will only invoke methods defined by the FrontDeskController.

Each other role controllers is implemented the same way: each has a defined layout file that is included in each view returned by the controller.
The layout file defines a menubar that specifies the methods that users in the role can invoke.

As protection against unauthorized access to methods of a role controller, use of each role controller is limited to users in the role associated with
the controller. For example, the FrontDeskController is protected by the annotation

    [Authorize(Roles = "FrontDesk")]

Access is then restricted by the functionality of ASP.NET Identity to authenticated users in role FrontDesk.

## The SharedController
Each role controller derives from SharedController. The SharedController implements the shared editor functionality available to the different roles.
Deriving role controllers from a shared controller is an extremely useful technique for building a role-based editor.

## The UsersController
The UsersController controls access to the ASP.NET Identity tables used to store registered users and the roles they are in. The method
UsersController.Index is the entry point for an authenticated user. The role an authenticated user is in determines the method the user will be
redirected to from this entry point.

## jqGrid
The entire implementation of application OPIDDaily is structured around instances of jqGrid appearing in MVC Views. Each jqGrid is initially populated
by a call to an MVC action made through the url property of the grid. For example, the clientsGrid on view FrontDesk/Clients.cshtml is initially
populated by the call

    "@Url.Action("GetClients", "FrontDesk)"

which is the value of the url argument to grid clientsGrid. (Method GetClients is found on the SharedController.)

Each instance of a jqGride defines a *pager*, which defines the CRUD operations supported by the grid. Each CRUD operation is
implemented by an MVC action of the role controller associated with the grid.

Initial population of a grid, grid pagination, grid searching and grid CRUD operations are all supported by server side code.

There is a collection of [jqGrid Demos](http://trirand.com/blog/jqgrid/jqgrid.html) that was very helpful during the development of OPIDDaily.

## NowServing
An important concept in the implementation of OPIDDaily is the concept of the client currently being served by a registered OPIDDaily user. For example,
when a row in the clientsGrid defined on FrontDeskClients.cshtml is selected, the JavaScript function that is the value of the onSelectRow property of
the grid is invoked. When the function is invoked, the value passed to its nowServing argument is the id associated with the client represented by the
selected row. The function posts to the server side method NowServing of the FrontDeskController (found on SharedController) via the code

    Url.Action("NowServing", "FrontDesk")

passing the JavaScript variable nowServing in the post as a result of the line

    postData: { nowServing: nowServing }

Method SharedController/NowServing has an optional argument called nowServing. MVC data binding will cause this variable to be bound to the JavaScript
variable in the post.

## The AgencyId Data Field in table **AspNetUsers**
Application OPIDDaily is used by users at Operation ID and by users representing agencies that refer clients to
Operation ID. The AgencyId field in table **AspNetUsrs** will be set to 0 for Operation ID users (for example
the TicketMaster user) and will be set to the AgencyId of their referring agency for other users. The AgencyId
data field in the **Clients** table is set in the same way. So, the AgencyId of a client entered into the **Clients**
table at Operation ID on a day of operation will be set to 0. The AgencyId of a client entered by a user representing
a referring agency will be set to the AgencyId of the referring agency. Clients are tagged in this way to allow
Operation ID users to see all clients and to allow a users from a referring agency to see only the clients referred
by that agency.

## The ServiceDate and Expiry data fields in Table **Clients**
When the TicketMaster adds a new client to the **Clients* table the date the client is entered is recorded in the
ServiceDate data field of table **Clients**. The same date is recorded in the Expiry data field. When a Case Manager
user enters a new client into the **Clients** table, the ServiceDate data field is set to the date the client is
entered, but the Expiry data field is set at least 30 days into the future. This expiry date will be printed
on the Case Manager Voucher given to a client by his/her Case Manager. Application OPIDDaily will preload the
**Clients** table seen by the TicketMaster with any clients whose Expiry date has not yet past. This will allow
a client entered by a Case Manager to be given priority service on the day he/she appears at Operation ID. It is
the client's responsibility to appear at Operation ID on or before the expiry date printed on his/her voucher.  

## SessionHelper
The SessionHelper class found on fie DAL/SessionHelper.cs is the key method for managing state in application OPIDDaily. This class is used to store key
value pairs in the sesssion context private to each authenticated user.

Managing the value of NowServing is a key use of the SessionHelper. Instead of having methods called GetNowServing and SetNowServing, the
SharedController uses polymorphism to define two methods called NowServing with different signatures. The NowServing method with zero arguments invokes
method SessionHelper.Get and the NowServing method with optional argumnt nowServing invokes method SessionHelper.Set. These two NowServing methods are
invoked by many methods on SharedController to implement editing functionality private to an autheticated user.

The other usage of the SessionHelper class is to manage the the back button helper methods ServiceTicketBackButtonHelper and
SpecialReferralBackButtonHelper found on the SharedController.

## Express Clients and Existing Clients
A first time client to Operation ID is referred to as an Express Client. Determining whether a given client is an Express Client is done by consulting
the Apricot database to determine whether the client has a *visit history*. A visit history is a list of services previously performed for a client.
If a client is determined to be an Express Client, then a user in the role FrontDesk (a FrontDesk admin) must use the OPIDDaily interface to edit the
client and mark him/her as an Express Client.

If consulting the Apricot database indicates that a client has a previous service history at Operation ID, then a FrontDesk admin must use the OPIDDaily
interface to copy the history of previous visits to the **Visits** table. This table is related to the **Clients** table by a foreign key relationship.
See the section Entity Framework Code First on the Infrastructure tab for a discussion of this foreign key relationship.

Method AddClient of the SharedController receives the id returned by method Clients.AddVisit. This id will be the id of the client inserted into
the **Clients** table. See [this StackOverflow article](https://stackoverflow.com/questions/5212751/how-can-i-get-id-of-inserted-entity-in-entity-framework) for an explantion of the side effect of record insertion relied upon for this.
The client with this id will become the NowServing client. (See the section NowServing.)

The foreign key relationship existing between tables **Clients** and **Visits** is not supported by a cascading delete; deleting a client
from the **Clients** table does not by default perform a cascading delete of any related records in the **Visits** table. The cascading delete must be
performed manually. To see how this is done, see method RemoveClients in the SuperadminController.

A client not marked as an Express Client is referred to as an Existing Client. A FrontDesk admin is responsible for using the interface to distinguish
between these two types of client. This has the advantage that a user in the role of Interviewer (an Interviewer admin) need not know the difference.
An Interviewer admin prepares Service Tickets (see next section) which automatically include the service history which has been recorded by a FrontDesk
admin for Existing Clients.

## Service Tickets
A primary goal of application OPIDDaily is the production of *Service Tickets*. A service ticket is a single piece of paper that shows the services
requested by a given client together with the documents the client is supplying in support of his/her service request. In addition a service ticket
provides a history of service requests from previous visits by the client, if any.

Service Tickets are produced by an Interviewer Admin. The interviewing process begins by determining the service needs of a client together with
documents the client is supplying in support of those needs. An Interviewer admin will use the OPIDDaily interface to capture this information. A
client's history of previous visits to Operation ID will have already been recorded by a FrontDesk admin by consulting the Apricot database.

After the Service Ticket for a client has been produced, an Interviewer admin will assist the client in filling out paperwork in support of service
sought. It is suggested that a client's Service Ticket be forwarded to a BackOffice admin before work on a client's paperwork begins. By passing
the Service Ticket to a BackOffice admin, a client's service voucher(s) can be cut and recorded in Apricot while the client is busy with paperwork.
In effect a client's **BackOffice** stage can proceed in parallel with his/her **Interviewing** stage once the Service Ticket has bee produced.

## SignalR
Application OIDDaily uses version 2.4.1 of the JavaScript SignalR package installed via NuGet. SignalR is a push-technology used to inform authenticated
users about the progress of an Operation ID client as he/she passes through the Operation ID pipeline.

The SignalR connection hub is defined on file DAL/DailyHub.cs. Invoking a method of DailyHub in the server side code causes a push-notifcation to
go out to all clients registered to the hub. For example, when a FrontDesk admin adds a new client to the jqGrid managed by the FrontDeskController,
the method SharedController.AddClient will invoke DailyHub.Refresh. A call to the
line

    hubContext.Clients.All.refreshPage();

will send a push notification to all registered clients of the hub. Those clients listening for this notification, for example the view
Interviewer/Clients.cshtml, will act upon the notification. In this case, the action will be to refresh the jqGrid of clients displayed by the
grid. In this way this grid is kept in synch with the grid to which the client was added by the FrontDesk admin.

SignalR is also used by both the special users **Client1** and **Client2** to refresh the tablet computer displays used by these users. (See the section
Managing Users on the Database tab.)
