---
title: "Frequently Asked Questions"
translationKey: "faq"
subtitle: "Some frequently asked questions about JobRunr"
description: "Find out all about the architecture and terminology behind JobRunr"
date: 2020-09-16T11:12:23+02:00
layout: "documentation"
menu: 
  main: 
    identifier: faq
    parent: 'documentation'
    name: FAQ
    weight: 95
sitemap:
  priority: 0.1
  changeFreq: monthly
---

## BackgroundJobServer FAQ
### Does JobRunr need open ports for distributing jobs?
No, JobRunr does not require an open port for distributing the workload - this is orchestrated via the `StorageProvider`.

### How is the coordination between different nodes done?
Each [`BackgroundJobServer`]({{<ref "_index.md#backgroundjobserver">}}) registers itself on startup in the `StorageProvider`. For an RDBMS, this is a plain old table called `jobrunr_backgroundjobservers`. The master is the server which is the longest running (so, the one that was registered as first node).  
Then, every 15 seconds, each `BackgroundJobServer` updates a lastHeartBeat timestamp. If a node crashes for some reason (this can also be the master node), the lastHeartBeat timestamp is not updated anymore. All other server participating in processing jobs see that the master node is not active anymore and it is removed from the `StorageProvider`.  
Next, the master reelection process starts which is again nothing more than the longest running `BackgroundJobServer`.

> Pro tip: if you are running in a Kubernetes environment, it is best to always keep your first `BackgroundJobServer` running and scale other pods up and down. This will result in less Master reelection processes and thus less database queries.

### What is the role of the master?
The master is a `BackgroundJobServer` like all other nodes processing but it does some extra tasks:
- it checks for recurring jobs and schedules them when they are about to run
- it checks for scheduled jobs and enqueues them when they need to run
- it checks for orphaned jobs and reschedules them
- it does some zookeeping like deleting all the succeeded jobs

<!-- ### How can I control the amount of workers per BackgroundJobServer? -->

## Job FAQ
### What if I don't want to have 10 retries when a job fails?
You can configure the amount of retries for all your jobs or per job.
- To change the default for all jobs, just register a [`RetryFilter`]({{<ref "_index.md#retryfilter">}}) with the amount of retries you want using the `withJobFilter` method in the [Fluent API]({{<ref "configuration/fluent/_index.md">}}) or in case of the [Spring configuration]({{<ref "configuration/spring/_index.md">}}), just pass the filter to the `setJobFilters` method of the `BackgroundJobServer` class.
- To change the amount of retries on a single Job, just use the `@Job` annotation:

```java
@Job(name = "Doing some work", retries = 2)
public void doWork() {
    ...
}
```