---
title: Kamon | Datadog | Documentation
layout: documentation
---

Reporting Metrics to Datadog
===========================

[Datadog] is a monitoring service for IT, Operations and Development teams who write and run applications at scale, and
want to turn the massive amounts of data produced by their apps, tools and services into actionable insight.

Installation
------------

Add the `kamon-datadog` dependency to your project and ensure that it is in your classpath at runtime, that's it.
Kamon's module loader will detect that the Datadog module is in the classpath and automatically start it.


Configuration
-------------

By default, this module assumes that you have an instance of the Datadog Agent running in localhost and listening on
port 8125. If that is not the case the you can use the `kamon.datadog.hostname` and `kamon.datadog.port` configuration
keys to point the module at your Datadog Agent installation.

The Datadog module subscribes itself to the entities included in the `kamon.datadog.subscriptions` key. By default, the
following subscriptions are included:

{% code_block typesafeconfig %}
kamon.datadog {
  subscriptions {
    histogram       = [ "**" ]
    min-max-counter = [ "**" ]
    gauge           = [ "**" ]
    counter         = [ "**" ]
    trace           = [ "**" ]
    trace-segment   = [ "**" ]
    akka-actor      = [ "**" ]
    akka-dispatcher = [ "**" ]
    akka-router     = [ "**" ]
    system-metric   = [ "**" ]
    http-server     = [ "**" ]
  }
}
{% endcode_block %}

If you are interested in reporting additional entities to Datadog please ensure that you include the categories and name
patterns accordingly.


### Metric Naming Conventions ###

For all single instrument entities (those tracking counters, histograms, gaugues and min-max-counters) the generated
metric key will follow the `application.instrument-type.entity-name` pattern. Additionaly all tags supplied when
creating the instrument will also be reported.

For all other entities the pattern is a little different: `application.entity-category.entity-name` and a identification
tag using the category and entity name will be used. For example, all mailbox size measurements for Akka actors are
reported under the `application.akka-actor.mailbox-size` metric and a identification tag similar to
`actor:/user/example-actor` is included as well.

Finally, the application name can be changed by setting the `kamon.datadog.application-name` configuration key.


Integration Notes
-----------------

* Contrary to other Datadog client implementations, we don't flush the metrics data as soon as the measurements are
  taken but instead, all metrics data is buffered by the `kamon-datadog` module and flushed periodically using the
  configured `kamon.datadog.flush-interval` and `kamon.datadog.max-packet-size` settings.
* It is advisable to experiment with the `kamon.datadog.flush-interval` and `kamon.datadog.max-packet-size` settings to
  find the right balance between network bandwidth utilisation and granularity on your metrics data.
* All timing measurements are sent in nanoseconds, if you want to scale them to milliseconds or to any other unit you
  need to manually edit the graph JSON definition to include a "/ 1000000" as show here:

<img class="img-responsive" src="/assets/img/datadog-scaling-metrics.png">


Visualization and Fun
---------------------

Creating a dashboard in the Datadog user interface is really simple, just start typing the application name ("kamon" by
default) in the metric selector and all metric names will start to show up. You can also break it down based on the entity
names. Here is a very simple example of a dashboard created with metrics reported by Kamon:

<img class="img-responsive" src="/assets/img/datadog-dashboard.png">

[Datadog]: http://www.datadoghq.com/
[get started]: /introduction/get-started/
