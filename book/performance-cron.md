Implementing backgroud tasks (cronjobs)
=======================================

There are at least two ways to run scheduled background tasks:

- Running console application command.
- Browser emulation.

Scheduling itself is the same for both ways. The difference is in actually running a command.

Under Linux and MacOS it's common to use [cronjobs](https://en.wikipedia.org/wiki/Cron).
Windows users should check [SchTasks or at](http://technet.microsoft.com/en-us/library/cc725744.aspx).

## Running console application command

Running console application command is preferred way to run code. First, implement
[Yii's console command](http://www.yiiframework.com/doc-2.0/guide-tutorial-console.html). Then add it to crontab file:

```
42 0 * * * php /path/to/yii.php hello/index
```

In the above `/path/to/yii` is full absolute path to `yii` console entry script. For both basic and advanced project
templates it's right in the project root. Then follows usual command syntax.

`42 0 * * *` is crontab's configuration specifying when to run the command. Refer to cron docs and use
[crontab.guru](http://crontab.guru/) service to verify syntax.

### Browser emulation

Browser emulation may be handy if you don't have access to local crontab, if you're triggering command externally
or if you need the same environment as web application has.

The difference here is that instead of console controller you're putting your code into web controller and then
fetching corresponding page via one of the following commands:

```
GET http://example.com/cron/
wget -O - http://example.com/cron/
lynx --dump http://example.com/cron/ >/dev/null
```

Note that you should take extra care about security since web controller is pretty much exposed. A good idea is to
pass special key as GET parameter and check it in the code.
