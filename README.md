Ruby Chef box
=============

This project is a base configuration to run a Ruby project both in development with Vagrant and in production.

It uses Chef to provision the server.

Pre-requisites
--------------

Install Vagrant: http://docs.vagrantup.com/v2/installation/
Install Virtual Box: https://www.virtualbox.org/wiki/Downloads

```shell
vagrant plugin install vagrant-omnibus
gem install berkshelf
```

Usage
-----

```shell
git clone https://github.com/gfauredumont/ruby-chef-box.git my_project
cd my_project
rm -rf .git
berks install --path chef/cookbooks
vagrant up
vagrant ssh
```

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
```shell
gem: --no-rdoc --no-ri
```

```shell
sudo su deploy
echo "source /etc/profile.d/rbenv.sh" >> ~/.bashrc
exit
```

[RBenv](https://github.com/sstephenson/rbenv) for Ruby installation management:
```shell
# list installed Ruby versions:
$ rbenv versions

# list all available versions:
$ rbenv install -l

# install a Ruby version:
$ rbenv install 2.0.0-p247
```


If the previous steps were fine, you should get a list of installed Ruby versions. Then go for some tests:
```shell
cd /vagrant
gem install bundler
gem install rails
rbenv rehash
bundle  /   rails new .
```

Postgresql
----------
Default user/password: `postgres/postgres`

To generate a password:
```shell
echo -n 'my_password''postgres' | openssl md5 | sed 's/^.* //' | sed 's/^/md5/'
```

Test postgresql connection:
```shell
psql --username=postgres --password --host=localhost
```

Quit postgresql shell:
```shell
\q<ENTER>
```


Using Vagrant snapshots
-----------------------
Taking snapshots of your Vagrant VM can save you a lot of time, especially when you experimenting. Vagrant comes with a build in snapshot engine, which can be controlled from the command line thanks to this plugin :
https://github.com/dergachev/vagrant-vbox-snapshot

On your host:
```shell
vagrant plugin install vagrant-vbox-snapshot
```

Here are the most usefull commands, from the plugin page:
```shell
vagrant snapshot take [NAME]            # take snapshot, labeled by NAME
vagrant snapshot list                   # list snapshots
vagrant snapshot back                   # restore last taken snapshot
vagrant snapshot delete [NAME]          # delete specified snapshot
vagrant snapshot go [NAME]				# restore specified snapshot
```

Remember you can also manage snapshots using Vagrant GUI. 


TODO
----
* Fix rbenv PATH for deploy user
* Use UNIX socket for postgres instead of TCP connection
* Do not configure gems doc by default
