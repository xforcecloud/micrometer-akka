# prometheus-akka

This project is a fork of [Kamon-Akka](http://kamon.io/documentation/kamon-akka/0.6.6/overview/). The Kamon team have done a great job and if you just experimenting with metrics collection, then their tools and documentation are a great starting point. 
Our internal monitoring tools work better with [Prometheus Java Client](https://github.com/prometheus/client_java) based metrics than with [Kamon](http://kamon.io/documentation/get-started/) based metrics.
The use of the [Kamon-Prometheus](https://github.com/MonsantoCo/kamon-prometheus) bridge was not as smooth as we had hoped.

Other Differences from Kamon-Akka:
- we do not support Kamon TraceContexts as currently have no use case for them yet
- we only support Scala 2.11 and Scala 2.12
- we only build with Akka 2.4 but this jar works fine with Akka 2.5 too
- we have added Actor Group support (see description in Metrics section)

The jar will soon be published to Maven Central.

## Usage

To enable monitoring, include the appropriate jar as a dependency and include the following Java runtime flag in your Java startup command (aspectjweaver is a transitive dependency of prometheus-akka):

-javaagent:/path/to/aspectjweaver-1.8.10.jar

If you don't have a Prometheus Metrics endpoint already, you can use the [Prometheus MetricsServlet](https://github.com/prometheus/client_java/blob/master/simpleclient_servlet/src/main/java/io/prometheus/client/exporter/MetricsServlet.java). If you don't ant to use a servlet, you can work directly with the [Prometheus TextFormat](https://github.com/prometheus/client_java/blob/master/simpleclient_common/src/main/java/io/prometheus/client/exporter/common/TextFormat.java) class.

## Configuration

The metrics are configured using [application.conf](https://github.com/typesafehub/config) files. There is a default [reference.conf](https://github.com/Workday/prometheus-akka/blob/master/src/main/resources/reference.conf) that enables only some metrics.

### Metrics

#### Dispatcher

- differs a little between ForkJoin dispatchers and ThreadPool dispatchers
- ForkJoin: parallelism, activeThreadCount, runningThreadCount, queuedSubmissionCount, queuedTaskCountGauge stealCount
- ThreadPool: activeThreadCount, corePoolSize, currentPoolSize, largestPoolSize, maxPoolSize, completedTaskCount, totalTaskCount

#### Actor

- One metric per actor instance
- mailboxSize (current size), processingTime, timeInMailbox, message, errors

#### Actor Router

- One metric per router instance, summed across all routee actors
- routingTime, timeInMailbox, message, errors

#### Actor Group

- Each actor group has its own include/exclude rules and you can define many groups with individual actors being allowed to be included in many groups - the metrics are summed across all actors in the group
- actorCount (current active actors), mailboxSize (current size), processingTime, timeInMailbox, message, errors