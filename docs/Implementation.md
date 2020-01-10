# Implementation
Application OPIDChecks is implemented as an ASP.NET Framework application using the ASP.NET MVC 5 project template provided by Visual Studio 2019
(Community Edition). It uses ASP.NET Identity 2.0 to define a set of user roles. Each user role is associated with a separate MVC controller.

It may be useful to upgrade application OPIDChecks to use the more modern ASP.NET Core technology. It may also be useful to provide an alternative to the
free hosting service AppHarbor, which is currently used by OPIDChecks. See the AppHarbor section of the Infrastructure
tab for details on this.

The graphical user interface of OPIDChecks is built using Bootstrap 3.0.0. Each user role is associated with its own
layout file which defines a
Bootstrap navbar containing links to the OPIDChecks features available to users in the role. The ASP.NET Identity
system ensures that a user in a
specified role cannot visit any pages outside of those allowed to users in that role. (See the section on Role
  Controllers.)

Because of its use of SignalR, application OPIDChecks will always require a server side component. See the section on
SignalR on this tab for a discussion of this.

## Users
OPIDChecks is a role-based system. Each registered user will be assigned a user role by the OPIDChecks administrator. The role that a user is assigned
will determine the OPIDChecks features available to the user.  

The OPIDChecks administrator will be in the role of Superadmin and will have access to features necessary for the maintenance of application OPIDChecks.
There will be only one Superadmin account, but the credentials for this account will be available to maintainers of the application.

## The Superadmin User
OPIDDaily defines a pre-registered Superadmin user who has privileges to

* create new roles
* invite new users to register in a pre-determined role

The credentials for the Superadmin user are configured on file Startup.cs. There is only a single user with role of
Superadmin.

## MVC Routing
Application OPIDChecks uses only the default routing rule supplied by the Visual Studio MVC 5 template. This default routing rule is found in
`.../App_Start/RouteConfig.cs`.

    routes.MapRoute(
      name: "Default",
      url: "{controller}/{action}/{id}",
      defaults: new { controller = "Users", action = "Index", id = UrlParameter.Optional }
    );

For the sake of simplicity, future development of application OPIDChecks should strive to keep this as the one and
only routing rule.

## The UsersController
The UsersController controls access to the ASP.NET Identity tables used to store registered users and the roles they a
are in. The method UsersController.Index is the entry point for an authenticated user. The role an authenticated user
is in determines the method the user will be redirected to from this entry point.

## SignalR
Application OPIDChecks uses version 2.4.1 of the JavaScript SignalR package installed via NuGet. SignalR is
used in the implementation of progress bars used by application OPIDChecks.
