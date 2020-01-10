# Background
Operation ID is a ministry that exists to serve the need of individuals who need to obtain identification documents to support their application for
a variety of services from housing to job application. The ministry is volunteer driven and has been in place for over thirty years. During this time
thousands of individuals have received help to support their identification needs. Help is provided in the form of vouchers that can be used to pay
for services rendered by either the Texas Department of Public Safety for a Texas ID or by the Bureau of Vital Statistics of Texas or another state
to acquire a certified copy of a birth certificate.

Operation ID makes every attempt to be a good steward of the limited resources available to it. To this end Operation
ID seeks to enforce a twice-in-a-lifetime policy with respect to the vouchers it issues. A client of Operation ID
is limited to 2 IDs or 2 birth certificates in a lifetime. The 2 IDs restriction applies to Texas state IDs or Texas
Drivers licenses or one of each.

## MkDocs
This document was created using MkDocs as was the [MkDocs website](http://www.mkdocs.org/) itself. MkDocs was installed
following the guide
on [this page](https://www.sitepoint.com/building-product-documentation-mkdocs/). This guide is useful for setting up
the environment; however,
the syntax for the file mkdocs.yml has changed from that described in the guide. The new syntax can be found at in the
User Guide section of
[this document](https://www.mkdocs.org/user-guide/writing-your-docs/#configure-pages-and-navigation).

An MkDocs document is a static website and can be hosted by any service that supports static sites. This MkDocs document
is hosted by
[GitHub Pages](https://pages.github.com/). The [Atom](http://atom.io/) open source text editor was used to
develop the document on the desktop.
An MkDocs document uses HTML Markdown for a desktop development version of a document. GitHub provides a
[cheatsheet for Markdown syntax](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet).

MkDocs provides a built-in preview server. To start this server, open a BASH Shell on the folder containing the
mkdoc.yml file of the project and execute

    mkdocs serve

Then go to

    http://127.0.0.1:8000

in a desktop browser. Pages can be edited and saved while in preview mode. The changes will be reflected in the browser
document.

When it is time to publish a version of a document, in a Git BASH shell opened on the folder containing the mkdocs.yml
file, issue the command

    mkdocs build

to expand the Markdown version of the document into an HTML version in the /site folder. Then open the Git GUI on the
folder containing the mkdocs.yml file and use the GUI to create a new Git repository on the local disk.

Next at GitHub create repository opiddchecksdoc to hold the documentation. Then create a repository on the desktop
machine to associate with the GitHub repository. Issue the following command in the folder containing the mkdocs.yml
file:

    git init

After this, in the folder containing the mkdocs.yml file, define a remote called origin for the document:

    git remote add origin https://github.com/tmhsplb/opidchecksdoc

This command references the GitHub repository opidchecksdoc. The remote only needs to be defined once. It will be
remembered by the Git BASH shell.

In the shell issue the following commands:

    git add -A

    git commit -a -m 'Initial commit'

    git push origin master

This will push the master branch of the document to the repository identified by the remote called origin. Then click
on the Settings tab for the newly created repository and scroll down to the GitHub Pages section. Select the master
branch source and click on the Save button.

Finally, to view the published document go to:

    https://tmhsplb.github.io/opidchecksdoc/site

Unless a new file is added to file `mkdocs.yml`, subsequent edits only require the commands

    mkdocs build

    git commit -a -m '<Comment for new commit>'

    git push origin master

to update repository opidchecksdoc at GitHub.

If a new file is added to `mkdocs.yml` then

    git add -A

must be run before the `mkdocs build` command is run. This causes any new files to be added to the local git repository. In either case it may take
several minutes before edits are available.
