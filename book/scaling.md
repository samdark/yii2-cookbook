# Configuring a Yii2 Application for an Multiple Servers Stack

These instructions are focused on configuring Yii2 to be a stateless application. Stateless application means each of your servers should be the exact same copy as possible. They should not store data in the instance. Stateless instances means more scalable architecture. Current deployment method described is pretty basic. More efficient and complex deployment methods may be described in the future.

Generally in web development, scaling means horizontal scaling, adding more servers to handle more amount of traffics. This can be done manually or automatically in popular deployment platform. In autoscaled environment, the platform can detect large amount of traffics and handle it by temporarily adding additional servers.

To set up a scalable application, the application needs to be made stateless, generally nothing should be written directly to the application hosting server, so no local session or cache storage. The session, cache, and database needs to be hosted on dedicated server.

Setting up a Yii2 application for auto scaling is fairly straight forward:

## Prerequisites

* Any well performing PaaS (Platform as a Service) solution that supports autoscaling, load balancing, and SQL databases. Examples of such PaaS are Google Cloud through its Instance Group and Load Balancer or Amazon Web Services using its AutoScaling Group and Elastic Load Balancer.
* Dedicated Redis or Memcached server. Easily launched on popular PaaS platforms with [Bitnami Cloud](https://bitnami.com/cloud). Redis generally performs better over Memcached, so this page will be focusing on working with Redis.
* Dedicated database server (Most PaaS platforms let you easily launch one i.e. Google SQL or AWS Relational Database Service).

## Making Your Application Stateless

Use a Yii2 supported Session Storage/Caching/Logging service such as Redis. Refer to the following resources for further instructions:

 * [Yii2 Class yii\redis\Session](http://www.yiiframework.com/doc-2.0/yii-redis-session.html)
 * [Yii2 Class yii\redis\Cache](http://www.yiiframework.com/doc-2.0/yii-redis-cache.html)
 * [Yii2 Redis Logging Component](https://github.com/JackyChan/yii2-redis-log)

When you run on a PaaS platform, make sure to use the Redis server's internal IP and not the external IP. This is essential for your application's speed.


A redis server doesn't require much disk space. It runs on RAM. This guide recommends any new application to start with at least 1GB RAM, and vertically scaling up the instance (i.e. upgrade to a more RAM) depending on usage. You can measure your RAM usage by SSH'ing into the redis server and running `top`.


Also configure your application to use your hosted database server (hosted by i.e. Google SQL).

## Configuring the Stack

The instructions below may be subject to change and improvement.

Set up a temporary single instance server to configure your application with.

The application must be deployed to the server by Git, so that multiple servers will stay up to date with the application. This guide recommends the following process:

* `git clone` the application into the configured `www` directory. The publicly accessed directory path can be varied depending on the platform.
* Set up a cron job to `git pull` the directory every minute.
* Set up a cron job to `composer install` the directory every minute.

When the application is up and running on the temporary server, create a snapshot of the server and use it to create your scalable server group.

Most PaaS platforms such as Google Cloud Managed Instance Groups and Amazon Elastic Beanstalk let you configure 'start up' commands. The start up command should also install/update the application (using `git clone` or `git pull` depending on if the service image already contains the application's git or not), and a `composer install` command to install all composer packages.

When the server group is set up using a disk based on the snapshot from the temporary server instance, you can remove the temporary server instance.

Your server group is now configured. Set up a load balancer on your PaaS platform (i.e. Load Balancer on Google) for the server group, andset your domain's A or CNAME records to your load balancer's static IP.

The mechanism described above is really simple yet sufficient enough to have your application up and running. There can be another process incorporated in the mechanism such as deployment failure prevention, version rollback, zero downtime, etc. These will be described in another topic.

There are couple of deployment mechanism that is provided by different platform such as Google Cloud or Amazon Web Services. These also will be described in another topic.

## Assets Management

By default Yii generate assets on the fly and store in `web/assets` directory with names that depends on the file created time. If you deploy in multiple servers, the deployment time in each server can be different even by several seconds. This can be caused by the difference of latency to the code storage or other factors or especially in autoscaling environment when a new instance can be spun hours after last deployment.

This can cause inconsistencies for URLs generated by different servers for a single asset. Setting the server affinity in the load balancer can avoid this, meaning requests by same user will be directed to the very same server hit by the first request. But this solution is not recommended since you may cache the result of your page in a persistent centralized storage. Furthermore, in autoscaling environment underutilized server can be shut down anytime leaving the next requests served by different server that generated different URL.

A more robust solution is by configuring `hashCallback` in [AssetManager](http://www.yiiframework.com/doc-2.0/yii-web-assetmanager.html#%24hashCallback-detail) so it will not depend on time, rather an idempotent function.

For example, if you deploy your code in exact path in all servers, you can configure the `hashCallback` to something like

```php
$config = [
    'components' => [
       'assetManager' => [
           'hashCallback' => function ($path) {
               return hash('md4', $path);
            }    
       ]
    ]
];
```

If you use HTTP caching configuration in your Apache or NGINX server to serve assets like JS/CSS/JPG/etc, you may want to enable the [`appendTimestamp`](http://www.yiiframework.com/doc-2.0/yii-web-assetmanager.html#%24appendTimestamp-detail) so that when an asset gets updated the old asset will be invalidated in the cache.

```php
$config = [
    'components' => [
       'assetManager' => [
           'appendTimestamp' => true,
           'hashCallback' => function ($path) {
               return hash('md4', $path);
            }    
       ]
    ]
];
```

Load balancing without server affinity increases scalability but raises one more issue regarding the assets: assets availability in all servers. Consider request **1** for a page is being received by server **A**. Server **A** will generate assets and write them in local directory. Then the HTML output returned to the browser which then generates request **2** for the asset. If you configure the server affinity, this request will hit server **A** given the server is still available and the server will return the requested asset. But in this case the request may or may not hit server **A**. It can hit server **B** that still has not generated the asset in local directory thus returning `404 Not Found`. Server **B** will eventually generate the assets. The more servers you have the longer time they need to catch up with each others and that increases the number of `404 Not Found` for the assets.

The best thing you can do to avoid this is by using [Asset Combination and Compression](http://www.yiiframework.com/doc-2.0/guide-structure-assets.html#combining-compressing-assets). As described above, when you deploy your application, generally deployment platform can be set to execute hook such as `composer install`. Here you can also execute asset combination and compression using `yii asset assets.php config/assets-prod.php`. Just remember everytime you add new asset in your application, you need to add that asset in the `asset.php` configuration.
