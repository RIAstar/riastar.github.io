---
layout: post
title: "Gigs - Easier ActionScript event handling"
date: 2012-01-29 13:07
comments: true
categories: [actionscript, library, flex, events]
---

Some time ago I created Protection. It was a library intended to help me write more readable ActionScript code without
breaking out of the paradigms that were set out by Adobe. You can find a more thorough explanation and motivation in
[an article I wrote earlier this year][1]. I have been using it a lot now and as much as I like the basic idea, I came
to realise that I had made a few wrong decisions. Most importantly:

 - _overcomplicated nomenclature_: in a silly attempt at originality I used military metaphors throughout the library
 (Sentries, Captains, Spies, etc.). These names make the code actually less transparent to someone who doesn't know
 where they originated (i.e. anyone but me). This is in clear violation with my original intent: more readable code.
 - _too many optional arguments_: a lot of different configuration was passed through one method with a bunch of
 optional arguments. Again, this would not be transparent to someone who read such code for the first time.
 - _used inheritance (instead of composition)_ to add the functionality to custom components
 - _string-based conditionals_: `"r:propertyA", "f:propertyB"` to pass 'propertyA' to the result handler and 'propertyB'
 to the failure handler? Not so pretty.

**Enter Gigs** (a.k.a RIAstar events)

So I decided to rethink the whole thing. To start with, the library name will remain my only attempt at originality.
But let's get down to the real stuff. This is how you use Gigs for tracking native Events:

``` actionscript3
follow(submitButton).by(MouseEvent.CLICK).handle(submit);

private function submit():void {
    //do some submitting
}
```

You can also join a 'gigs.properties' file to the application in which you can associate default event types to specific
classes. For instance 95% of the time on Buttons we listen for CLICK events. So we register a default for that:

``` properties
spark.components.supportClasses.ButtonBase = click
```

And then we can write the following to do the same thing as before:
``` actionscript3
follow(submitButton).handle(submit);
//we can still explicitely listen for
//another event than the registered default
follow(submitButton).by(MouseEvent.MOUSE_DOWN).handle(startDrag);
```


**Passing property values**

The 'handle' method takes one additional ...rest argument to which you can pass all the event properties you want pass
to the result handler. For example (I've registered the ListBase class with the IndexChangeEvent.CHANGE event type):

``` actionscript3
follow(itemList).handle(editItemAt, "newIndex");

private function editItemAt(index:int):void {
     //open item editor
}
```


**Unfollowing** (a.k.a. removing event listeners)

There are three ways to stop following Events.

Very specifically:
``` actionscript3
unfollow(submitButton).by(MouseEvent.CLICK).handle(submit);
```


Somewhat specifically (this will remove all event handlers that react to a CLICK event):
``` actionscript3
unfollow(submitButton).by(MouseEvent.CLICK).all();
```


Loose cannon (removes all event listeners for all event types for the given IEventDispatcher):
``` actionscript3
unfollow(submitButton).all();
```


**To be continued**

That's it for now. I have yet to recreate all functionality that existed in Protection, but I will hopefully get to
that soon.

[1]: http://blog.riastar.net/development/protection-actionscript-guarding/ Protection - an ActionScript guarding library
