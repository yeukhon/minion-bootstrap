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
* if we are using vagrant, we will store our
vagrant files inside ``~/workstation``.


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

You can adopt the sample Vagrantfile directly
from ``vagrant/32`` or ``vagrant/64``. 

    $ cp -r vagrant/32 ~/workstation
    
Change ``32`` to ``64`` if you plan to create
a 64-bit Ubuntu 12.04.2 LTS.

If the assumption above do not hold (ie. you 
are using a directory), change the
ansible configuration in ``Vagrantfile``. You
can always adjust the memory usage and number of 
CPU core for the VM by editing the Vagrantfile.

Next, create an inventory file:

    $ cp inventory.example testing

If you want to use **install** instead
of **develop** mode, open ``Vagrantfile``
using your favorite editor, and change 
``ansible.extra_vars = {develop: true}`` to ``
``ansible.extra_vars = {install: true}``.

If you want to use your own fork instead
of mozilla master repos, you should modify
the inventory file ``testing``, and change
``mozilla`` to your github's user name.


Finally, launch your new vagrant instance by

    $ vagrant up

By default, the machine will be launch with 
a local address ``192.168.33.50``.


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
staging, develop, testing, or production, 
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





    




