---
title: Let Kafka register and resolve Avro schema automatically
modified: 2016-06-10
tags: [dev, kafka]

---

Register Avro schema, serialize and deserialize are easy with Kafka and Schema Registry integration.

Here is the [source code for Avro producer](https://github.com/confluentinc/examples/tree/master/kafka-clients/specific-avro-producer/)
and [source code for Avro consumer](https://github.com/confluentinc/examples/tree/master/kafka-clients/specific-avro-consumer/)

Summary of the key features:
* Use GenericRecord as message format.

* For producer, schema is still required.  GenericRecord needs schema to initialize messages and put values to fields.

{% highlight html %}
{% raw %}
      val parser = new Schema.Parser()
      val schema = parser.parse(this.getClass.getResourceAsStream("/my_schema.avsc"))

      val avroRecord = new GenericData.Record(schema)
      avroRecord.put("id", "010123")
      avroRecord.put("name", "Kafka Avro")        
{% endraw %}
{% endhighlight %}

* If Kafka topic is keyed and also described by Avro schema, the key and value schema will be registered by Kafka API as 2 different subjects: topic_key and topic_value.

* Consumer should use KafkaAvroDecoder to deserialize into GenericRecord

* Use field names to get values out from GenericRecord, or use GenericRecord as is.
