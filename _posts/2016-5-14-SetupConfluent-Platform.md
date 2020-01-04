---
title: Setup Confluent Platform Locally on Mac
modified: 2016-05-14
tags: [dev, confluent, setup]

---

How to setup confluent platform locally (without using docker containers):

Download the package:
http://docs.confluent.io/2.0.1/installation.html



I use "confluent-2.0.1-2.11.7.tar.gz".

Before following steps in quick start guide, update ./etc/kafka/server.properties


{% highlight html %}
{% raw %}
host.name=localhost
advertised.host.name=localhost
{% endraw %}
{% endhighlight %}

(Otherwise, you will encounter "LeaderNotFound" or other strange Kafka communication errors.)

Follow quick start:
http://docs.confluent.io/2.0.1/quickstart.html

When you need to clean up:
Zookeeper, Kafka and schema registry save intermediate files in /tmp folder.  If you run into "nodeExists", "Kafka broker" exists, etc those kind of strange problems, stop the services, clean up /tmp/<service name> folder, then restart the service.  It might help.
