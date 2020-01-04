---
title: Scala - Use Try to handle exception the functional way
modified: 2016-07-07
tags: [dev, Scala, beginner]

---
I started to use Scala 2 months ago.  My goal is to use Scala the functional way.  
When it comes to try/catch block and exception handling in scala, the traditional "try-catch" works too.  Well, but it doesn't seem functional.

Several good blog posts I found related to Scala, Try, and Exception Handling:

[The Neophyte's Guide to Scala - Part 6](http://danielwestheide.com/blog/2012/12/26/the-neophytes-guide-to-scala-part-6-error-handling-with-try.html)

[SO - returning a value from try catch](http://stackoverflow.com/a/17501085)

[Exception Handling in Apache Spark])http://www.nicolaferraro.me/2016/02/18/exception-handling-in-apache-spark/)

This is my implementation:

{% highlight scala linenos %}

  def getProductIDfromGenericRecord(genericObject: Object): Try[Int] = Try {
    val product_id = genericObject.asInstanceOf[GenericRecord].get("product_id").toString.toInt
    product_id
  }

{% endhighlight %}

The above function is called and handled in the code block:

{% highlight scala linenos %}

val productidStream = ProductInfoStream.union(AnotherProductInfoStream)
    .map(msg => getProductIDfromGenericRecord(msg))

// Handle exceptions
productidStream.filter(_.isFailure)
.map(rec=> logger.error("Error - Log it up" + rec.get.toString))

// Good data - keep going
val APIResults = productidStream.filter(_.isSuccess)
     .map(_.get).......

{% endhighlight %}

Why I pick this way?  I want to avoid nested coding structure as much as I can so my code would be more readable.
