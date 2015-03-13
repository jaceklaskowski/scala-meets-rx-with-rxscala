# Introduction

[ReactiveX](http://reactivex.io/) is the website for an API for asynchronous programming with observable streams.

[ReactiveX](http://reactivex.io/) is a library for composing asynchronous and event-based programs by using observable sequences.

When you want to access asynchronous sequences of multiple items, that's where `Observable` shines.

The [Observable](http://reactivex.io/RxJava/javadoc/rx/Observable.html) class implements the Reactive Pattern.

Rx is primarily a system for querying data in motion asynchronously. (see [PART 4 - Concurrency](http://www.introtorx.com/Content/v1.0.10621.0/15_SchedulingAndThreading.html))

Copying from [PART 4 - Concurrency](http://www.introtorx.com/Content/v1.0.10621.0/15_SchedulingAndThreading.html#RxIsSingleThreadedByDefault):

* Rx is single-threaded by default
* events are most certainly single threaded and synchronous
* Rx is just a way of chaining callbacks together for a given notification.
* if you do not introduce any scheduling, your callbacks will be invoked on the same thread that the OnNext/OnError/OnCompleted methods are invoked from.
* Rx introduces a very handy mechanism for introducing concurrency and multithreading to your code: Scheduling.
* Use the `subscribeOn` method to describe how you want any warm-up and background processing code to be scheduled.
* The `observeOn` method is used to declare where you want your notifications to be scheduled to.
	* useful when working with STA systems, most commonly UI applications = avoid blocking the UI thread
	* you do not want to block the UI thread
	* but you do need to update UI objects on the UI thread.
	* any I/O, computational intensive work or any processing unrelated to the UI should be marshaled off the UI thread.
	* You would almost certainly want to use `subscribeOn` to leave the UI thread, followed by `observeOn` to ensure you were notified back on it.
* an IScheduler in Rx is used to schedule some action to be performed, either as soon as possible or at a given point in the future.
* The implementation of the IScheduler defines how that action will be invoked i.e. asynchronously via a thread pool, a new thread or a message pump, or synchronously on the current thread.
* Introducing concurrency to your application will increase its complexity = you should avoid doing so = unpredictable timing
* We can ensure that our extension method is lazily evaluated by using `Observable.create`.
* Rx is designed for querying data, parallel computations or composition of asynchronous methods is more appropriate for other frameworks.
* Rx solves the issues for concurrently generating and consuming data via the ObserveOn/SubscribeOn methods.
	* Schedulers provide a rich platform for processing work concurrently without the need to be exposed directly to threading primitives.
	* cancellation, passing state and recursion
	* 

## Use cases

* See http://www.introtorx.com/Content/v1.0.10621.0/15_SchedulingAndThreading.html#SubscribeOnObserveOn:

		_customerService.GetCustomers()
		  .SubscribeOn(Scheduler.NewThread)
		  .Subscribe(Customers.Add)

* read a file larger than the memory available and stream it in chunks leveraging the compositional nature of Rx to apply multiple transformations to the file such as encryption and compression. (the idea from [Creating your own iterator](http://www.introtorx.com/Content/v1.0.10621.0/15_SchedulingAndThreading.html#CreatingYourOwnIterator)).

		Observable.from {
		  println(s"Observable.from executed on [${Thread.currentThread().getName}]")
		  "This is a veeery long file".split(" ")
		}
		  .subscribeOn(IOScheduler())
		  .subscribe { s =>
		    println(s"Saving to a database: [$s] on [${Thread.currentThread().getName}]")
		    TimeUnit.SECONDS.sleep(1)
		  }

* FIXME How to open and close the file above using `Observable` (perhaps `using`)?
* Callbacks => Observables