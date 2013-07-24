minion-bootstrap
================

Bootstrap Minion using Ansible


Requirements
============

You will need the following software in order
to make use of this provisioning script.

* Virtualbox
* Ubuntu 12.04 (either 32 or 64 bit)
* Ansible


To install ansible, I recommend doing
``sudo pip install ansible``. 

Provisioning
============

You can launch a new VM either using Vagrant,
or using a raw virtualbox manager (VBoxManage 
or the GUI console).

Before we move on, the tutorial below
assumes:

* this repository has been checkout to your
current user's home directory, and


Modes
-----

Before we jump into launching, the autotmation
script provides two modes: ``develop`` and 
``install`` modes.

**develop** will create two virtualenv, one
for each repository (backend, frontend). 
Using **develop** will run ``python setup.py develop`` instead. 

**install** will setup everything in the global
system Python interepreter. 


Github
------

We clone everything from Github. If
you want to clone your fork, instead of ``mozilla``
as user, you need to provide ``GIT_USER`` to the inventory file, which we will discuss below.


Launching with Vagrant
----------------------

First, create an inventory file:

    $ cp inventory.example testing

If you want to use your own fork instead
of mozilla master repos, you should modify
the inventory file ``testing``, and change
``mozilla`` to your github's user name.

You can adopt the sample Vagrantfile directly
from ``32`` or ``64``. 

    $ cd 32     if you want 32-bit
    $ cd 64     if you want 64-bit

You can always change configuration in this 
Vagrantfile. For example, adjust the memory
usage or CPU core number.

If you want to use **install** instead
of **develop** mode, open ``Vagrantfile``
using your favorite editor, and change 
``ansible.extra_vars = {develop: true}`` to ``
``ansible.extra_vars = {install: true}``.


Finally, launch your new vagrant instance by

    $ vagrant up

By default, the machine will be launch with 
a local address ``192.168.33.50``. You can do

    $ vagrant ssh
    $ ssh vagrant@192.168.33.50

The latter approach, the password is ``vagrant``.


Launching with raw VM
----------------------

Instead of using Vagrant to manage 
virtual machine, if you choose to
run a VM directly through the virtualbox
interface (commandline or GUI), you
need to get:

* the ip address of the machine
* the username, password, or private-key

Next, create an **inventory** file:

    $ cp inventory.example testing

We have chosen the name of the inventory file
to be ``testing``. You are advised to choose
staging, development, testing, or production, 
as a convention. 

You need to give the IP address and the username.

**Note**: you normally don't want to
commit this file into version control
so this repository have already
rulled out the convention names using
``.gitignore``.


Finally, launch Ansible to start provisioning
the new vm in the following format:

    $ ansible-playbook -i testing bootstrap.yml [auth] --extra-vars='[option]'  (not an actual command)

``option`` is either ``develop=True`` or ``install=True``. We recommend you to use ``develop``.

If your new vm is using key-auth rather than password-auth, you should run:

    $ ansible-playbook -i testing bootstrap.yml --private-key=/path/to/private-key/ --extra-vars='[option]'

If you use password auth, do this:

    $ ansible-playbook -i testing bootstrap.yml -u username -k password -U username -K password --extra-vars='[option]'


In summary, if using develop:

    $ ansible-playbook -i testing bootstrap.yml -u username -k password -U username -K password --extra-vars='develop=True'     (password-auth)

    $ ansible-playbook -i testing bootstrap.yml --private-key=/path/to/private-key/ --extra-vars='develop=True'     (key-atuh)


Post-provisioning
-----------------

Due to a bug, we cannot automatically restart
supervisord to allow our worker to
pick up the PATH we set for ZAP.

So do this:

    $ vagrant ssh
    $ startsuper
    

If this is a branch new machine, you probably
want to load in fixtures:

    $ benv && minion-db-init

It will prompt you to answer for a Persona email
address and your name.

You are done! Now open a browser and visit ``http://192.168.33.50:8080``.


Aliases
-------

I love aliases. Here are the aliases I have 
written into ``.bash_aliases``.

* **minion**: cd into /opt/minion which holds
    various repos and scripts.
* **backend**: cd into minion-backend repo
* **frontend**: cd into minion-frontend repo
* **plugins**: cd into a directory currently holding
    minion plugins.
* **benv**: alias for sourcing into backend's virtualenv.
* **fenv**: alias for sourcing into frontend's virtualenv.
* **super**: alias for calling supervisorctl. You can restart minion-backend process by calling ``super restart minion-backend``, or ``super stop all`` to stop all running prcesses, just to name two.
* **startsuper**: alias to start supervisord (in order to start the console you must have the daemon running first).




    




