---
title: Creating Uber Jar in SBT
modified: 2016-05-01
tags: [dev, sbt]

---


In order to have a easier time to submit job to spark cluster, it is usually recommended to create e uber jar, a jar that includes all the dependencies.  Uber jar eliminated the need to distribute dependent jars to each worker nodes and make sure their classpathes lie up.  It usually is not a fun job to do.

We use scala and sbt to create spark jobs.  To create the uber jar in SBT, follow the steps:

1.  Add the following line to "< project root >/project/plugins.sbt
{% highlight html %}
{% raw %}

        addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.12.0")

{% endraw %}
{% endhighlight %}

2.  Add the following line to build.sbt

{% highlight html %}
{% raw %}

        assemblyMergeStrategy in assembly := {
            case PathList("META-INF", xs @ _*) => MergeStrategy.discard
            case x => MergeStrategy.first
        }

{% endraw %}
{% endhighlight %}

3.  Run command

{% highlight html %}
{% raw %}

        sbt assembly

{% endraw %}
{% endhighlight %}

There are 2 ways to confirm your dependencies are included:

1.  From output of "sbt assembly" command

2.  Run

{% highlight html %}
{% raw %}

        jar tf < jar file >

{% endraw %}
{% endhighlight %}
