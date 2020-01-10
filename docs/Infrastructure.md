





















## MkDocs
This document was created using MkDocs as was the [MkDocs website](http://www.mkdocs.org/) itself. MkDocs was installed following the guide
on [this page](https://www.sitepoint.com/building-product-documentation-mkdocs/). This guide is useful for setting up the environment; however,
the syntax for the file mkdocs.yml has changed from that described in the guide. The new syntax can be found at in the User Guide section of
[this document](https://www.mkdocs.org/user-guide/writing-your-docs/#configure-pages-and-navigation).

An MkDocs document is a static website and can hosted by any service that supports static sites. This MkDocs document
is hosted by
[GitHub Pages](https://pages.github.com/). The [Brackets](http://brackets.io/) open source text editor was used to
develop the document on the desktop.
An MkDocs document uses HTML Markdown for a desktop development version of a document. GitHub provides a
[cheatsheet for Markdown syntax](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet).

MkDocs provides a built-in preview server. To start this server, open a BASH Shell on the folder containing the
mkdoc.yml file of the project and execute

    mkdocs serve

Then go to

    http://127.0.0.1:8000

in a desktop browser. Pages can be edited and saved while in preview mode. The changes will be reflected in the browser document.

When it is time to publish a version of a document, in a Git BASH shell opened on the folder containing the mkdocs.yml file, issue the command

    mkdocs build

to expand the Markdown version of the document into an HTML version in the /site folder. Then open the Git GUI on the
folder containing the mkdocs.yml file and use the GUI to create a new Git repository on the local disk.

Next create repository opiddailydoc to hold the documentation at GitHub.

After this, in the folder containing the mkdocs.yml file, define a remote called origin for the document:

    git remote add origin https://github.com/tmhsplb/opidchecksdoc.git

This command references the GitHub repository opidchecksdoc. The remote only needs to be defined once. It will be remembered by the
Git BASH shell.

In the shell issue the following commands:

    git add -A

    git commit -a -m 'Initial commit'

    git push origin master

This will push the master branch of the document to the repository identified by the remote called origin. Then click on the Settings tab for the newly
created repository and scroll down to the GitHub Pages section. Select the master branch source and click on the Save button.

Finally, to view the published document go to:

    https://tmhsplb.github.io/opiddailydoc/site

Unless a new file is added to file `mkdocs.yml`, subsequent edits only require the commands

    mkdocs build

    git commit -a -m '<Comment for new commit>'

    git push origin master

to update repository opiddailydoc at GitHub.

If a new file is added to `mkdocs.yml` then

    git add -A

must be run before the `mkdocs build` command is run. This causes any new files to be added to the local git repository. In either case it may take
several minutes before edits are available.
