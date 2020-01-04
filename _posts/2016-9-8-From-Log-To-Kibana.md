---
title: From logs to Kibana
modified: 2016-09-08
tags: [dev, logstash, kibana, logging]

---
Our team is developing a spark streaming app.  In a distributed platform, it is a challenge to understand application behavior in a deeper level without the help of log aggregation tool.  Our app uses slf4j, which directly tie to the logging framework supplied by Spark (log4j and configurations) nicely.  Our platform team has provided basic pluming to get logs into elasticsearch + Kibana.  However, Kibana cannot provide any visualization if the information is not indexed.  Certainly, it won't play well with normal text message based logging mechanism.

To solve the problem (logs are easily consumable and viewable in Kibana), we need the following changes:
1. Change all or part of the logging messages to flexible JSON format.  I want the ability to enter one or more custom fields.  (For example, including "method name", "unique identifier", and "elapse time" in a single log message.)

2. Be able to index the custom JSON fields.

3. Able to discover or visualize info in custom fields in Kibana.

* First - Change logging format in Spark application
I decide to preserve the original logging framework format so that the timestamps, severity level, etc can still be appended without much extra effort.  Here is the format I came up with:

{% highlight scala linenos %}
16/08/30 22:34:28 INFO spark.PresminStreamDriver$: {"identifier" : "6th test", "elapsed_ms" : 12}
{% endhighlight %}

One wrapper class JSONLogger is introduced.  Like this:

{% highlight scala linenos %}
// log Elapse_Time in ms
class JsonLogger(logger: Logger){
def infoElapseMs(method: String, identifier: String, time_ms: Int) : Unit = {
  logger.info(JSONObject(makeElapseMap(method, identifier, time_ms)).toString())
}

// Create map for logging purpose
def makeElapseMap (method: String, identifier: String, time_ms: Int): Map[String, Any] = {
  val elapse_map = Map(("MethodTag", method), ("IdentifierTag", identifier), ("ElapsedTag", time_ms))
  elapse_map
}
}
{% endhighlight %}

In my application we will only use JSONLogger so that all log messages will have consistent format.

* Second - Logstash agent filter

In order to proper index my JSON message, we have to let the logstash filter recognize that part of the message is in JSON format.

Final logstash configuration file looks like this:

{% highlight html %}
{% raw %}
input {
  file {
    path => ["/Path/to/my/logs.log"]
    start_position => "beginning"
    type => "spark"
    tags => ["cluster_name"]
    add_field => { "env" => "stage" }
  }
}
filter {
  if [type] == "spark_inr_apps" {
    multiline {
      pattern => "(^[a-zA-Z.]+(?:Error|Exception): .+)|(^\s+at .+)|(^\s+... \d+ more)|(^\s*Caused by:.+)"
      what => "previous"
    }
    grok {
      match => [ "message", "%{DATESTAMP:timestamp} %{LOGLEVEL:severity} %{DATA:source} %{GREEDYDATA:json}" ]
      overwrite => [ "message" ]
    }
    json {
      source => "json"
    }
    if "_jsonparsefailure" in [tags] {
    mutate {
      remove_tag => [ "_jsonparsefailure" ]
      }
    }
  }
}

{% endraw %}
{% endhighlight %}

We basically tell grok to make the last portion of log messages as a blob called "json", then we feed the "json blob" into json parser (source => "json").  For normal messages that are not in json format, we ignore and remove the warning message.

To manually test the change, several useful commands:
{% highlight html %}
{% raw %}
// restart logstash agent to activate the new config
sudo sv restart logstash_agent

// Execute logstash with config file
./logstash -f <path to config file>

{% endraw %}
{% endhighlight %}

* Third - Consume logs in Kibana
Until now, the logs should be in JSON format, indexed properly and appears in Kibana.  Last thing you have to do - index it in Kibana.  Otherwise you cannot use them in dashboard or visualization.
To do so, select the 4th tab on the top "Setting" -> "Index" -> Refresh.  Afterward the newly added indexes will show up in "fields" in different visualization tools.  
