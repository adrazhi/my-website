---
layout: post
title: MySQL Workbench + Laravel Homestead (Vagrant)
---

I've been using [Laravel Homestead](http://laravel.com/docs/master/homestead), a pre-packaged [Vagrant](http://www.vagrantup.com) "box", to setup the best Laravel local development environment.

Sometimes i need a visual tool (e.g. [MySQL Workbench](http://en.wikipedia.org/wiki/MySQL_Workbench)) for creating, executing, and optimizing SQL queries. So in this post i will demonstrate how you can connect to the installed MySQL databases with MySQL Workbench.


## What is your SSH Hostname?
The SSH Hostname is the IP you set in your `Homestead.yaml` plus the port number. In this example i will consider `10.0.0.100:22`.


## What is your MySQL Hostname?

1- Boot your Vagrant environment:
{% highlight text %}
$ vagrant up
{% endhighlight %}

2- SSH into the machine:
{% highlight text %}
$ vagrant ssh
{% endhighlight %}

3- And finally run the following command:
{% highlight text %}
$ cat /etc/mysql/my.cnf | grep bind-address

Output: bind-address = 10.0.2.15
{% endhighlight %}

Note that in this case your MySQL Hostname will be ***10.0.2.15***.

## Using MySQL Workbench

At this time you already know all you need to create a new MySQL connection. But you can also duplicate the example below:

### Connection Method
{% highlight text %}
Standard TCP/IP over SSH
{% endhighlight %}

### Connection parameters
{% highlight text %}
SSH Hostname: 10.0.0.100:22
SSH Username: vagrant
SSH Password: vagrant

MySQL Hostname: 10.0.2.15
MySQL Server Port: 3306
Username: homestead
Password: secret
{% endhighlight %}
