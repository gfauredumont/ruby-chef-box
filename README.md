Ruby Chef box
=============

This project is a base configuration to run a Ruby project both in development with Vagrant and in production.

It uses Chef to provision the server.

Pre-requisites
--------------

Install Vagrant: http://docs.vagrantup.com/v2/installation/
Install Virtual Box: https://www.virtualbox.org/wiki/Downloads

~~~ sh
$ vagrant plugin install vagrant-omnibus
$ gem install berkshelf
~~~

Usage
-----

~~~ sh
$ git clone https://github.com/gfauredumont/ruby-chef-box.git my_project
$ cd my_project
$ rm -rf .git
$ berks install --path chef/cookbooks
$ vagrant up
$ vagrant ssh
~~~

If VirtualBox Guest Additions are not on the right version:
-----------------------------------------------------------
Go and follow these steps:
[Ref](https://github.com/TryGhost/Ghost-Vagrant#updating-virtual-box-guest-additions)

The packaged vagrant box from Ubuntu contains outdated Virtual Box Guest Additions.  Most of the time this shouldn't be a problem, but if you want to update them I recommend this procedure:

1. Install the [vagrant-vbguest plugin](https://github.com/dotless-de/vagrant-vbguest): `vagrant plugin install vagrant-vbguest`
1. Boot the vm without provisioning: `vagrant up --no-provision`
1. Login with `vagrant ssh` and run `sudo apt-get -y -q purge virtualbox-guest-dkms virtualbox-guest-utils virtualbox-guest-x11`
1. Logout and `vagrant halt`
1. `vagrant up --provision`


Other improvements:
-------------------

On the VM:
to avoid Docs installation in VM, create `.gemrc` file then edit it:
~~~ sh
gem: --no-rdoc --no-ri
~~~

~~~ sh
$ sudo su deploy
$ echo "source /etc/profile.d/rbenv.sh" >> ~/.bashrc
$ exit
~~~

[RBenv](https://github.com/sstephenson/rbenv) for Ruby installation management:
~~~ sh
# list installed Ruby versions:
$ rbenv versions

# list all available versions:
$ rbenv install -l

# install a Ruby version:
$ rbenv install 2.0.0-p247
# then assign it as default for current user:
$ rbenv local 2.0.0-p247

# NEW list of installed versions should list the 247 with a star:
$ rbenv versions

# WHEN INSTALLING new gems, refrech rbenv so he can use them:
$ rbenv rehash
~~~


If the previous steps were fine, you should get a list of installed Ruby versions. Then go for some tests:
~~~ sh
$ cd /vagrant
$ gem install bundler
$ gem install rails
$ rbenv rehash
$ bundler     # or  'rails new .'  for a new project ;)
~~~

Postgresql
----------
Default user/password: `postgres/postgres`

To generate a password:
~~~ sh
$ echo -n 'my_password''postgres' | openssl md5 | sed 's/^.* //' | sed 's/^/md5/'
~~~

Test postgresql connection:
~~~ sh
$ psql --username=postgres --password --host=localhost
~~~

Quit postgresql shell:
~~~ sql
\q<ENTER>
~~~


Using Vagrant snapshots
-----------------------
Taking snapshots of your Vagrant VM can save you a lot of time, especially when you experimenting. Vagrant comes with a build in snapshot engine, which can be controlled from the command line thanks to this plugin :
https://github.com/dergachev/vagrant-vbox-snapshot

On your host:
~~~ sh
$ vagrant plugin install vagrant-vbox-snapshot
~~~

Here are the most usefull commands, from the plugin page:
~~~ sh
$ vagrant snapshot take [NAME]            # take snapshot, labeled by NAME
$ vagrant snapshot list                   # list snapshots
$ vagrant snapshot back                   # restore last taken snapshot
$ vagrant snapshot delete [NAME]          # delete specified snapshot
$ vagrant snapshot go [NAME]				# restore specified snapshot
~~~

Remember you can also manage snapshots using Vagrant GUI. 


TODO
----
* Fix rbenv PATH for deploy user
* Use UNIX socket for postgres instead of TCP connection
* Do not configure gems doc by default
