#  DevOps BootCamp: CI/CD

## Day 0: Continuous integration vs. delivery vs. deployment

### Continuous integration

```
Developers practicing continuous integration merge their changes back to the main branch as often as possible. 
The developer's changes are validated by creating a build and running automated tests against the build. 
By doing so, you avoid integration challenges that can happen when waiting for release day to merge changes into 
the release branch.

Continuous integration puts a great emphasis on testing automation to check that the application is not broken 
whenever new commits are integrated into the main branch

```

### Continuous delivery
```
Continuous delivery is an extension of continuous integration since it automatically deploys all code changes to 
a testing and/or production environment after the build stage. 

This means that on top of automated testing, you have an automated release process and you can deploy your application 
any time by clicking a button.

In theory, with continuous delivery, you can decide to release daily, weekly, fortnightly, or whatever suits your 
businessrequirements. However, if you truly want to get the benefits of continuous delivery, you should deploy 
to production as early as possible to make sure hat you release small batches that are easy to troubleshoot in
case of a problem.
```

### Continuous deployment

```
Continuous deployment goes one step further than continuous delivery. With this practice, every change that passes
all stages of your production pipeline is released to your customers. There's no human intervention, and only a 
failed test will prevent a new change to be deployed to production.

Continuous deployment is an excellent way to accelerate the feedback loop with your customers and take pressure 
off the team as there isn't a "release day" anymore. Developers can focus on building software, and they see their 
work go live minutes after they've finished working on it.
```

**How the practices relate to each other:** <br>
  To put it simply continuous integration is part of both continuous delivery and continuous deployment.
And continuous deployment is like continuous delivery, except that releases happen automatically.
![alt text](https://github.com/zm99by/DevOps_BootCamp_CI_CD/blob/main/pic.png?raw=true)


### Six Strategies for Application Deployment

There are a variety of techniques to deploy new applications to production, so choosing the right strategy 
is an important decision, weighing the options in terms of the impact of change on the system, and on the end-users.

In this post, we are going to talk about the following strategies:

(add zero downtime)

**Recreate:** Version A is terminated then version B is rolled out. <br>
**Ramped (also known as rolling-update or incremental):** Version B is slowly rolled out and replacing version A.<br>
**Blue/Green:** Version B is released alongside version A, then the traffic is switched to version B.<br>
**Canary:** Version B is released to a subset of users, then proceed to a full rollout.<br>
**A/B testing:** Version B is released to a subset of users under specific condition.<br>
**Shadow:** Version B receives real-world traffic alongside version A and doesn’t impact the response.<br>

Let’s take a look at each strategy and see which strategy would fit best for a particular use case. 
For the sake of simplicity, we used Kubernetes and tested the example against Minikube. Examples of 
configuration and step-by-step approaches on for each strategy can be found in this git repository.

## Recreate
The recreate strategy is a dummy deployment which consists of shutting down version A then deploying version B after version A is turned off. This technique implies downtime of the service that depends on both shutdown and boot duration of the application.
![alt text](https://github.com/zm99by/DevOps_BootCamp_CI_CD/blob/main/recreate.gif?raw=true)

Pros:
- Easy to setup.
- Application state entirely renewed.

Cons:
- High impact on the user, expect downtime that depends on both shutdown and boot duration of the application.

## Ramped

The ramped deployment strategy consists of slowly rolling out a version of an application by replacing instances one 
after the other until all the instances are rolled out. It usually follows the following process: with a pool of version 
A behind a load balancer, one instance of version B is deployed. When the service is ready to accept traffic, the instance
is added to the pool. Then, one instance of version A is removed from the pool and shut down.

Depending on the system taking care of the ramped deployment, you can tweak the following parameters to increase the deployment time:

- Parallelism, max batch size: Number of concurrent instances to roll out.
- Max surge: How many instances to add in addition of the current amount.
- Max unavailable: Number of unavailable instances during the rolling update procedure.
![alt text](https://github.com/zm99by/DevOps_BootCamp_CI_CD/blob/main/ramped.gif?raw=true)

Pros:
- Easy to set up.
- Version is slowly released across instances.
- Convenient for stateful applications that can handle rebalancing of the data.

Cons:
- Rollout/rollback can take time.
- Supporting multiple APIs is hard.
- No control over traffic.

## Blue/Green
The blue/green deployment strategy differs from a ramped deployment, version B (green) is deployed alongside version A (blue) with exactly the same amount of instances. After testing that the new version meets all the requirements the traffic is switched from version A to version B at the load balancer level.

![alt text](https://github.com/zm99by/DevOps_BootCamp_CI_CD/blob/main/blue-green.gif?raw=true)

Pros:
- Instant rollout/rollback.
- Avoid versioning issue, the entire application state is changed in one go.

Cons:
- Expensive as it requires double the resources.
- Proper test of the entire platform should be done before releasing to production.
- Handling stateful applications can be hard.

## Canary
A canary deployment consists of gradually shifting production traffic from version A to version B. Usually the traffic is split based on weight. For example, 90 percent of the requests go to version A, 10 percent go to version B.

This technique is mostly used when the tests are lacking or not reliable or if there is little confidence about the stability of the new release on the platform.

![alt text](https://github.com/zm99by/DevOps_BootCamp_CI_CD/blob/main/canary.gif?raw=true)

Pros:
- Version released for a subset of users.
- Convenient for error rate and performance monitoring.
- Fast rollback.

Con:
- Slow rollout.

## A/B testing
A/B testing deployments consists of routing a subset of users to a new functionality under specific conditions. It is usually a technique for making business decisions based on statistics, rather than a deployment strategy. However, it is related and can be implemented by adding extra functionality to a canary deployment so we will briefly discuss it here.

This technique is widely used to test conversion of a given feature and only roll-out the version that converts the most.

Here is a list of conditions that can be used to distribute traffic amongst the versions:

- By browser cookie
- Query parameters
- Geolocalisation
- Technology support: browser version, screen size, operating system, etc.
- Language

![alt text](https://github.com/zm99by/DevOps_BootCamp_CI_CD/blob/main/a-b.gif?raw=true)

Pros:
- Several versions run in parallel.
- Full control over the traffic distribution.

Cons:
- Requires intelligent load balancer.
- Hard to troubleshoot errors for a given session, distributed tracing becomes mandatory.

## Shadow
A shadow deployment consists of releasing version B alongside version A, fork version A’s incoming requests and send them to version B as well without impacting production traffic. This is particularly useful to test production load on a new feature. A rollout of the application is triggered when stability and performance meet the requirements.

This technique is fairly complex to setup and needs special requirements, especially with egress traffic. For example, given a shopping cart platform, if you want to shadow test the payment service you can end-up having customers paying twice for their order. In this case, you can solve it by creating a mocking service that replicates the response from the provider.

![alt text](https://github.com/zm99by/DevOps_BootCamp_CI_CD/blob/main/shadow.gif?raw=true)

Pros:
- Performance testing of the application with production traffic.
- No impact on the user.
- No rollout until the stability and performance of the application meet the requirements.

Cons:
- Expensive as it requires double the resources.
- Not a true user test and can be misleading.
- Complex to setup.
- Requires mocking service for certain cases.

To Sum Up
There are multiple ways to deploy a new version of an application and it really depends on the needs and budget. When releasing to development/staging environments, a recreate or ramped deployment is usually a good choice. When it comes to production, a ramped or blue/green deployment is usually a good fit, but proper testing of the new platform is necessary.

Blue/green and shadow strategies have more impact on the budget as it requires double resource capacity. If the application lacks in tests or if there is little confidence about the impact/stability of the software, then a canary, a/ b testing or shadow release can be used. If your business requires testing of a new feature amongst a specific pool of users that can be filtered depending on some parameters like geolocation, language, operating system or browser features, then you may want to use the a/b testing technique.

Last but not least, a shadow release is complex and requires extra work to mock egress traffic which is mandatory when calling external dependencies with mutable actions (email, bank, etc.). However, this technique can be useful when migrating to a new database technology and use shadow traffic to monitor system performance under load.

Below is a diagram to help you choose the right strategy:

![alt text](https://github.com/zm99by/DevOps_BootCamp_CI_CD/blob/main/deployment_strategies.png?raw=true)

## Day 1: Continuous Integration with Jenkins
### ***Description***

```
The course describes the continuous integration and delivery processes. 
Practical part shows how to setup the Jenkins based CI from scratch.
The goal of this course is to learn how continuous integration and delivery processes work.
```

https://learn.epam.com/detailsPage?id=59bdf234-6664-4f38-a9c5-6689edd6f8d4

## Day 2: Jenkins Fundamentals
### ***Description***

```
In this course, we will cover the fundamentals of Jenkins, including continuous integration/continuous delivery (CI/CD) 
and continuous deployment. After that, we will move on to installing and configuring Jenkins, and get familiar with the 
graphical user interface. We will explore jobs and builds as well as build agents. This will give us the foundation we 
need to accomplish more complex Jenkins tasks.
```

**https://learn.acloud.guru/course/e81aa3d0-bd3e-4a49-9bf8-dc4412e74f6c/overview**

## Day 3: Jenkins by Doing
### ***Description***

```
You learn faster and better when you learn by doing. With that in mind, this course has been designed to allow 
you to practice core Jenkins Builds through a 100% hands-on experience. To accomplish this, Linux Academy's Training 
Architects have hand-selected a set of the best Jenkins hands-on labs we have to offer.

Everything in this course will be on one or more Linux servers provisioned with whatever needed through our hands-on lab 
and Cloud Playground platform.

There's no reason to wait; learn by doing today!
```

**link: https://learn.acloud.guru/course/03c036a0-ef8c-41a0-bc5f-0ce9c0b29000/dashboard**

## Useful links

https://www.guru99.com/software-development-life-cycle-tutorial.html <br>
https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow <br>


