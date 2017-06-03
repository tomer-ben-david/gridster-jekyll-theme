---
layout: post
title:  "Timing spray total request response time"
date:   2015-04-12 22:18:00
categories: scala,functional-programming
comments: true
---
So you wish to measure how much the spray `request-response` cycle takes; you also wish to add it to your `metrics` set.  You have come to the right place, here is the `complex` recipe for achieving that. (unfortunately there is no simple recipe it's going to be damn complex), that's life with spray, they taste both delicious and bad this is the effect of `scala` + `DSL` + `reactive` + `continuations` - complex coding at it's top glory.  But in anyway its a good mind bending practice so the neurons won't die.  Note we are using [codahale-metrics](https://github.com/dropwizard/metrics) as our `metrics` framework.

### The routing directive
This is the routing directive we wish to measure

{% highlight scala %}
val helloRoute = path("/hello") {
    get {
      complete("time this from request to response please (and hello to you!)")
    }
}
{% endhighlight %}

#### Create metric
{% highlight scala %}
val requestResponseTimer = RequestResponseTimedMetric("spray.request.response.timer", metricRegistry).time
{% endhighlight %}

`TimedMetric`, warning, magic behind that, we are going to code that class below.

#### Update code to use the timed metric
{% highlight scala %}
val helloRoute = path("/hello") {
    get {
        requestResponseTimer {
            complete("time this from request to response please (and hello to you!)")
        }
    }
}
{% endhighlight %}

### RequestResponseTimedMetric explained
{% highlight scala %}
  case class TimerMetric(timerName: String, metricRegistry: MetricRegistry) {
    val time: Directive0 =
      around { ctx =>
        val timerContext = metricRegistry.timer(timerName).time()
        val startTime = System.currentTimeMillis()
        (ctx, buildAfter(timerContext, startTime))
      }

    def buildAfter(timerContext: Timer.Context, startTime: Long): Any => Any = { possibleRsp: Any =>
      possibleRsp match {
        case _ =>
          timerContext.stop()
          trace(durationMarker, s"$timerName duration of ${System.currentTimeMillis() - startTime}}")
      }
      possibleRsp
    }

  }

  def around(before: RequestContext => (RequestContext, Any => Any)): Directive0 =
    mapInnerRoute { inner =>
      ctx =>
        val (ctxForInnerRoute, after) = before(ctx)
        try inner(ctxForInnerRoute.withRouteResponseMapped(after))
        catch { case NonFatal(ex) => after(Failure(ex)) }
    }
{% endhighlight %}

1. `TimedMetric` is just a `case class` which receives the timer name and the `metricsRegistry`
1. `TimerMetric.time` returns a `function` because it returns a call to `around` and `around` is a function from `before: RequestContext` to the output which is `(RequestContext, Any => Any)`.
1. Note the output has the `RequestContext` because you wish to have the `request` when handling response that would be nice.
1. Note the output outcome is `Any => Any` which means it's just a general function whatever you coded to handle the request.
1. `around` then runs `before` on context then `inner` then `after` its a general wrapper.
1. `TimerMetric.time` calls the `around` and as a code block to execute it passes the `timer` starting and stopping.
1. So when you called TimerMetric.time you are passing it a `block` to run because `.time` runs `around` and `around` receives a `code block` and the code block you are passing to it is the actual code to run.  However `.time` does not only run that code it also creates the actual `metrics` timer initializes it records the `startTime` and creates another block to be run the `after` which is the `buildAfter(timerContext, startTime)
1. The `around` knows to run that `after` code block after the whole run of the code block to run has finishes, by having `ctxForInnerRoute.withRouteResponseMapped(after)`