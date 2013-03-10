---
layout: post
title: "RIA utilities: copying instance properties"
date: 2012-02-20 14:18
comments: true
categories: [actionscript, flex, snippet, utilities, introspection]
---

I've lost count on how many times I've needed this in RIA development with Flex or ActionScript: the possibility to
quickly copy property values from one class instance to another. The two most important uses I can think of right now
are these:

 - You send a value object (VO) to the server. It comes back with a bunch of additional data. So far so good. But what
 you get back is another instance than the one you've just sent up the wire. Since the original instance may already be
 referenced somewhere in the application we do not want to simply replace it. We want the original instance filled with
 the new property values.
 - You show the user a form with which he can edit a VO. After he's changed some values he can either commit his changes
 ('OK' button) or discard them ('cancel' button). In this scenario you'll usually keep a copy of the original VO
 somewhere handy because you want to be able to restore it when needed. Now the story is the same as the previous one:
 we want to restore the values of the original instance, not replace it entirely.

So I finally made this convenience method that uses [describeType()][1] for introspection as an addition to my
`ObjectUtil` class:

``` actionscript3
public static function copyProperties(source:Object, target:Object):void {
    if (!source || !target) return;

    //copy properties declared in Class definition
    var sourceInfo:XML = describeType(source);
    var propertyLists:Array = [sourceInfo.variable, sourceInfo.accessor];

    for each (var propertyList:XMLList in propertyLists) {
        for each (var property:XML in propertyList) {
            if (property.@access == undefined ||
                property.@access == "readwrite")
            {
                var name:String = property.@name;
                if (target.hasOwnProperty(name))
                    target[name] = source[name];
            }
        }
    }

    //copy dynamic properties
    for (name in source)
        if (target.hasOwnProperty(name))
            target[name] = source[name];
}
```

I have needed this so many times I find it hard to fathom why something similar is not included in the
[mx.utils.ObjectUtil class][2]. It marries perfectly with [ObjectUtils.clone()][3].

A simple example:
```actionscript3
var editableItem:MyClass = ObjectUtil.clone(originalItem);

//the user edits editableItem
//if he cancels we just discard this instance
//if he commits his changes we copy the new values
ObjectUtil.copyProperties(editableItem, originalItem);
```

[1]: http://help.adobe.com/en_US/FlashPlatform/reference/actionscript/3/flash/utils/package.html#describeType%28%29" describeType() global function
[2]: http://help.adobe.com/en_US/FlashPlatform/reference/actionscript/3/mx/utils/ObjectUtil.html mx.utils.ObjectUtil
[3]: http://help.adobe.com/en_US/FlashPlatform/reference/actionscript/3/mx/utils/ObjectUtil.html#clone%28%29 ObjectUtil.clone() method
