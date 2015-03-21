# Tracker vs Promises for Eventual Consistency

Everyone knows I'm a Meteor fanboy. And Tracker - or rather, all the magic it enables - is a big reason why.

But I believe I've found a case where I think Promises are the hands-down winner over Tracker for simplicty and predictability.

Sashko Stubailo shows in his Meteor [Dev Talk](https://www.youtube.com/watch?v=2WQRm4McIZQ) that Tracker is similar to the event-emitter pattern. If you can imagine updating a web page each time the GeoLocation change, you can express that logic one of two ways: "On a GeoLocation change event, run this function to update the page", or "Here's a procedure which gets the GeoLocation and writes it to the page; run it whenever the GeoLocation changes"

A promise is just a one-time event-emitter. It's an event that only fires once - upon completion of some asynchronous work. You interface with a Promise by passing a function (a "continuation") into its `then` method. I built the `deanius:promise` package to show what it would be like to call Meteor.methods in a way that lets you continue execution upon their completion.

```js
function () {
    var result1 = ReactiveMethod.call("myMethod", "a", "b");

    if (result1) {
      return ReactiveMethod.call("myMethod2", result1, "b");
    }
  }
```

I'm not saying Tracker isn't useful for building tools- it has produced Blaze, and the whole Meteor framework, it clearly is. But, to make an analogy to Fibers, it is not recommended that you do raw programming with Fibers, when Futures are a less leaky abstraction that is less likely to get you into trouble or mis-use. It's the same with Tracker.
