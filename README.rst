.. sectnum::

Setting up repositories to share work within organization
=========================================================

**Disclaimer**: The following guide was developed for a remote repository hosted on GitHub and a local system with linux. Other remote hosts and OSs might require slightly different actions to achieve the same results. Especially, the automatic setup script is not suited for windows systems. Please contact phav at dtu.dk if you wish to assist in expanding the guide for other systems.

.. contents::

A GitHub organization can be used to collect the work of a team across many projects.

If a project already belongs to a remote git repository, different solutions can include them in the organization depending on the sharing preferences:


1. `Mirroring a Repository`_: development continues in the original repository
#. `Transferring Ownership`_: the original repository is transferred to the organization for futher development

Both methods ensure that the code is only actively development in a single location. The first method should be used if one prefers to keep the ownership of the repository, otherwise use the second.


Mirroring a Repository
----------------------

This method uses the git hook functionality to force a push to the mirror when a push to the remote origin is preformed.

The following 3 easy steps will create a mirror of the original repository in the organization and make sure it is kept up to date when new work is push to the original remote repository.

Create Mirror Repository
~~~~~~~~~~~~~~~~~~~~~~~~

Start by creating a repository with the same name as the original within the organization on GitHub.com.

Set Mirror as Remote and Install Hook
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The mirror repository need to be set up as a remote and by using the ``pre-push`` hook, the mirror is updated every time a push to the origin is executed.
This setup can be done either automatically or manually as described below.

The Automatic Setup
"""""""""""""""""""
Using the python script ``setupRemoteMirror.py`` from within the project folder installs the setup described in `The Manual Setup`_ automatically.

From within your project with the git repository run:

Linux Terminal
        .. code:: bash

                git clone https://github.com/DTUComputeCognitiveSystems/mirrorSetup.git
                ./mirrorSetup/setupRemoteMirror.py

- TODO: add script for windows systems
- TODO: add script for uninstall

Multiple mirrors can be configured by adding additional organizations to the list defined in the ``setupRemoteMirror.py`` script.

**Remember:** every time a new client repository is created (with ``git clone`` etc.), this ``pre-push`` hook needs to be created.

The Manual Setup
""""""""""""""""


Setup the newly created repository as a remote in original repository with the name ``mirror_repo``:

   .. code:: bash

           $ git remote add --mirror=push mirror_repo [url_to_repo_in_organization]


Create the following file in the .git/hooks/ folder with the name ``pre-push``

   .. code:: bash

           #!/bin/bash
           
           # This hook is called with the following parameters:
           # $1 -- Name of the remote to which the push is being done
           # $2 -- URL to which the push is being done
           
           remote="$1"
           
           if [ "$remote" == "mirror_repo" ]; then
                   exit 0
           else
                   # push to mirror repository when pushing to other destinations
                   echo "pre-push hook pushing to mirror"
                   git push --mirror --quiet mirror_repo
                   echo "push to mirror completed"
                   exit 0
           fi

Every time when you run ``git push origin`` this script will run before the push happens, and thereby update the mirror at the same time as the origin is updated. 

**Remember:** every time a new client repository is created (with ``git clone`` etc.), this ``pre-push`` hook needs to be created.

Reference Original Repository
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To have the mirror repository refer back to the original one, go to the mirror repository on GitHub and edit the repository description (just below the ``<> code`` tab) to say "mirror of https://github.com/[repo_owner]/[repo_name]"

You are all set!



Transferring Ownership
----------------------
In case that the work should be owned by the organization itself, follow the instructions found here: https://help.github.com/articles/transferring-a-repository-owned-by-your-personal-account/

