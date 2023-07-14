---
title: "Celery"
date: 2023-07-14T17:22:43+08:00
tags: [python]
---


https://blog.gevent.org/2010/02/27/why-gevent/
https://zhuanlan.zhihu.com/p/420890013


#### About the [`--app`](https://docs.celeryq.dev/en/stable/reference/cli.html#cmdoption-celery-A) argument[](https://docs.celeryq.dev/en/stable/getting-started/next-steps.html#about-the-app-argument "Permalink to this headline")

The [`--app`](https://docs.celeryq.dev/en/stable/reference/cli.html#cmdoption-celery-A) argument specifies the Celery app instance to use, in the form of `module.path:attribute`

But it also supports a shortcut form. If only a package name is specified, it’ll try to search for the app instance, in the following order:

With [`--app=proj`](https://docs.celeryq.dev/en/stable/reference/cli.html#cmdoption-celery-A):

1.  an attribute named `proj.app`, or

2.  an attribute named `proj.celery`, or

3.  any attribute in the module `proj` where the value is a Celery application, or

If none of these are found it’ll try a submodule named `proj.celery`:

4.  an attribute named `proj.celery.app`, or

5.  an attribute named `proj.celery.celery`, or

6.  Any attribute in the module `proj.celery` where the value is a Celery application.

This scheme mimics the practices used in the documentation – that is, `proj:app` for a single contained module, and `proj.celery:app` for larger projects.


### [Mingle: Worker synchronization](https://docs.celeryq.dev/en/latest/history/whatsnew-3.1.html#id12)[](https://docs.celeryq.dev/en/latest/history/whatsnew-3.1.html#mingle-worker-synchronization "Permalink to this heading")

The worker will now attempt to synchronize with other workers in the same cluster.

Synchronized data currently includes revoked tasks and logical clock.

This only happens at start-up and causes a one second start-up delay to collect broadcast responses from other workers.

You can disable this bootstep using the [`celery worker --without-mingle`](https://docs.celeryq.dev/en/latest/reference/cli.html#cmdoption-celery-worker-without-mingle) option.

### [Gossip: Worker <-> Worker communication](https://docs.celeryq.dev/en/latest/history/whatsnew-3.1.html#id13)[](https://docs.celeryq.dev/en/latest/history/whatsnew-3.1.html#gossip-worker-worker-communication "Permalink to this heading")

Workers are now passively subscribing to worker related events like heartbeats.

This means that a worker knows what other workers are doing and can detect if they go offline. Currently this is only used for clock synchronization, but there are many possibilities for future additions and you can write extensions that take advantage of this already.

Some ideas include consensus protocols, reroute task to best worker (based on resource usage or data locality) or restarting workers when they crash.

We believe that although this is a small addition, it opens amazing possibilities.

You can disable this bootstep using the [`celery worker --without-gossip`](https://docs.celeryq.dev/en/latest/reference/cli.html#cmdoption-celery-worker-without-gossip) option.
