---
title: Use logstash and elastalert in dockers
modified: 2017-01-18
tags: [dev, logstash, elastalert, docker]

---
  We use logstash to forward log events from enterprise log kafka topic onto our team's ELK.  We also use elastalert to query elasticsearch.
  There is a logstash image in docker registry (logstash:2.4 is the version we use).  We use kafka plugin and forward all events to elasticsearch.  Remember to define a unique group_id for each of the different environment or apps if all logs are going to a central logging topic.

  Getting elastalert to work with elasticsearch v.5 is harder though.  Elastalert, as in 2017 Jan, doesn't support elastsearch v.5.  Thanks to opensource, there is a branch with code fixes to make elastalert compatible.  We downloaded the branch, created a gzip, and created a docker image using the gzip.  Several small bugs in testing utility and using default es_host/es_port globally but overall doesn't affect the functionality we need.

  Last fix - to make our docker image running in CST by default.  Fixes below (in Dockerfile)

  {%highlight html %}
  RUN apk add -U tzdata
  RUN ls /usr/share/zoneinfo
  RUN cp /usr/share/zoneinfo/America/Chicago /etc/localtime
  RUN echo "America/Chicago" >  /etc/timezone
  RUN date

  {%endhighlight%}
