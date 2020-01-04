---
title: Notes about spark
modified: 2016-07-08
tags: [dev, Spark]

---
* Remember to include "action" in the code including foreachRDD and foreachpartition.  Otherwise data will just fly through Spark without doing anything.  Spark is lazy anyway.

* Throughput is a better performance metrics than processing time.  I have a streaming job, each of the event calls a rest api.  The average API response time is ~30ms (latency captured from elapsed time in log output).  If I calculate total processing time per message from throughput, # of threads and batch processing time, message processing time becomes ~8ms.  In Spark, throughput is a much better performance metrics than processing time/latency.

* Spark will distribute remaining tasks according to processing duration to keep most of the workers busy.  (In Spark detailed application web UI->stages, task distribution can be found with different node locality.  When a node is busy tasks will be shifted to other idle worker with "ANY" locality mode.)

* You can override SPARK_WORKER_CORES.  My job is network intensive.  In my use case, it makes sense to launch more threads (than # of cores) because a big portion of time will be spent on waiting for rest API to respond, for instance.  After I defined SPARK_WORKER_CORE = 6 on a 4 core worker, Spark launched 6 parallel tasks with 6 different threads.
