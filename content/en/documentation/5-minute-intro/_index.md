---
title: "5 minute intro"
subtitle: "Get started quickly using these easy examples and our comprehensive documentation!"
date: 2020-04-30T11:12:23+02:00
layout: "documentation"
menu: 
  main: 
    parent: 'documentation'
    weight: 5
---

## Take a look at our example projects:
We have different example projects on GitHub that help you start:

- https://github.com/jobrunr/example-spring<br>
This repository is the most simple example with Spring integration. It exists out of a backend module and a frontend module with a shared module containing the background job. The frontend module enqueues new background jobs which are being processed by the background module.
- https://github.com/jobrunr/example-salary-slip<br>
This repository contains an example on how to generate a lot of salary slips each Sunday evening using a recurring job.
- https://github.com/jobrunr/example-salary-slip/tree/kubernetes<br>
Do you want to scale and get the processing done faster? In this example we use Terraform to define infrastructure as code and setup a Kubernetes cluster with 10 instances of JobRunr. More info? See also the accompanying blog post.

There are also other example projects available - you can find them here: https://github.com/jobrunr?q=example

## Or follow along!
### Add the dependency to JobRunr
Using your build tool of choice, add the dependency to the following artifact:
- groupId: `org.jobrunr`
- artifactId: `jobrunr`
- version: `${jobrunr.version}`

### Choose your storage system and configure JobRunr
Using your RDBMS of choice, create a DataSource and pass it to JobRunr:

```java
JobRunr.configure()
        .useStorageProvider(SqlStorageProviderFactory.using(applicationContext.getBean(DataSource.class )))
        .useJobActivator(applicationContext::getBean)
        .useDefaultBackgroundJobServer()
        .useDashboard()
        .initialize();
```

### Start enqueueing jobs!
```java
BackgroundJob.enqueue(() -> System.out.println("Simple!"));
```

### And monitor them
Using your browser, go to the JobRunr dashboard located at http://localhost:8000/dashboard