---
title: Single Node Install
layout: page
---

# {{ page.title }}

This page describes procedure of Single Node Setup. This setup can be used to
became more familiar with OpenStack, play with API, testing or development
needs. For production setup you need multi-node setup. We are supporting
RHEL6.1, Scientific Linux 6.1 and CentOS 6 + CR distributions. For all of them
installation procedure is the same except repositories with packages.

For this installation all what you need is HW computer with KVM support and one
of the supported Linux distribution installed.

## Step 1. Include Repositories

For RHEL 6.1 you need only original DVD. All dependencies included in our
repository. To include our repository create a file `gd-openstack.repo` in
`/etc/yum.repos.d` directory with content:

{% highlight ini %}
[gd]
name=Packages from GridDynamics
baseurl=http://yum.griddynamics.net/yum/diablo
enabled=1
gpgcheck=0
priority=1
{% endhighlight %}

The same should be done for Scientific Linux 6.1

For CentOS you should use different repository:

{% highlight ini %}
[gd]
name=Packages from GridDynamics
url=http://yum.griddynamics.net/yum/diablo-centos
enabled=1
gpgcheck=0
priority=1
{% endhighlight %}

And addiditionaly include EPEL and CR repositories.

## Step 2. Install packages

For Single Node Setup you have to install mysql, mysql-server,
openstack-nova-node-full, euca2ools and python-novaclient packages:

{% highlight console %}
$ sudo yum install mysql mysql-server
$ sudo yum install openstack-nova-node-full
$ sudo yum install euca2ools
$ sudo yum install python-novaclient
{% endhighlight %}

All other packages will be installed as dependencies automatically.

## Step 3. Prepare Database

Nova uses MySQL to store information about running VMs (PostgreSQL is also
possible to use).

Use this script (run it without arguments) to prepare database for nova:

{% highlight bash %}
#!/bin/bash

DB_NAME=nova
DB_USER=nova
DB_PASS=nova
PWD=rootpassword

HOSTS="$@"

mysqladmin -uroot -p$PWD -f drop nova
mysqladmin -uroot -p$PWD create nova

for h in $HOSTS localhost; do
        echo "GRANT ALL PRIVILEGES ON $DB_NAME.* TO '$DB_USER'@'$h'
IDENTIFIED BY '$DB_PASS';" | mysql -uroot -p$DB_PASS mysql
done
echo "GRANT ALL PRIVILEGES ON $DB_NAME.* TO $DB_USER IDENTIFIED BY
'$DB_PASS';" | mysql -uroot -p$DB_PASS mysql
echo "GRANT ALL PRIVILEGES ON $DB_NAME.* TO root IDENTIFIED BY
'$DB_PASS';" | mysql -uroot -p$DB_PASS mysql
nova-manage db sync
{% endhighlight %}

## Step 4. Run services

We are ready to run services:

{% highlight console %}
$ sudo service rabbitmq-server start
$ sudo service libvirtd start
$ sudo service openstack-nova-api start
$ sudo service openstack-nova-direct-api start
$ sudo service openstack-nova-compute start
$ sudo service openstack-nova-network start
$ sudo service openstack-nova-objectstore start
$ sudo service openstack-nova-scheduler start
$ sudo service openstack-glance-api start
$ sudo service openstack-glance-registry start
{% endhighlight %}

## Step 5. Play with OpenStack

Now you are ready to use open stack. You can add users to open stack with
`nova-manage` command, then create projects, upload images and start VMs.

