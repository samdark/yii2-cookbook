# Configuring a Yii2 Application for an Autoscaling Stack

Applications which can suddenly gain large amounts of traffic should handle it by temporarily adding additional servers. This is called auto scaling.

To set up an application for auto scaling, the application needs to be made stateless (generally nothing should be written directly to the application hosting server, so no local Session or Cache storage), and the database needs to be hosted on a separate server.

Setting up a Yii2 application for auto scaling is fairly straight forward:

## Pre-requisities


* Any well performing PaaS (Platform as a Service) solution that supports autoscaling, load balancing, and SQL databases such as Google Cloud (Instance Group + Load Balancer) or Amazon AWS (Elastic Beanstalk)
* Redis or Memcached server. Easily launched on popular PaaS platforms with [Bitnami Cloud](https://bitnami.com/cloud). Redis generally performs better over Memcached, so this page will be focusing on working with Redis.
* Hosted Database server (Most PaaS platforms let you easily launch one i.e. Google SQL).

## Making your application Stateless

Use a Yii2 supported Session Storage/Caching service such as Redis. Refer to the following Yii2 documention for configuring Redis for Session Storage and Caching:

 * [Yii2 Class yii\redis\Session](http://www.yiiframework.com/doc-2.0/yii-redis-session.html)
 * [Yii2 Class yii\redis\Cache](http://www.yiiframework.com/doc-2.0/yii-redis-cache.html)

When you run on a PaaS platform, make sure to use the Redis server's internal IP and not the external IP. This is essential for your application's speed.


A redis server doesn't require much disk space. It runs on RAM. This guide recommends any new application to start with at least 1GB RAM, and vertically scaling up the instance (i.e. upgrade to a more RAM) depending on usage. You can measure your RAM usage by SSH'ing into the redis server and running `top`.


Also configure your application to use your hosted database server (hosted by i.e. Google SQL).

## Configuring the Stack

Set up a temporary single instance server to configure your application with.

The application must be deployed to the server by Git, so that multiple servers will stay up to date with the application. This guide recommends the following process:

* `git clone` the application into the configured 'www' directory
* Set up a cron job to `git pull` the directory every minute.
* Set up a cron job to `composer update` the directory every minute.

When the application is up and running on the temporary server, create a snapshot of the server and use it to create your scalable server group.

Most PaaS platforms such as Google Cloud Managed Instance Groups and Amazon Elastic Beanstalk let you configure 'start up' commands. The start up command should also install/update the application (using `git clone` or `git pull` depending on if the service image already contains the application's git or not), and a `composer update` command to install all composer packages.

When the server group is set up using a disk based on the snapshot from the temporary server instance, you can remove the temporary server instance.

Your server group is now configured. Set up a load balancer on your PaaS platform (i.e. Load Balancer on Google) for the server group, andset your domain's A or CNAME records to your load balancer's static IP.
