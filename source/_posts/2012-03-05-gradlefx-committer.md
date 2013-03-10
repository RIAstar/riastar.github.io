---
layout: post
title: "GradleFx committer"
date: 2012-03-08 00:00
comments: true
categories: [actionscript, flex, gradle, gradlefx, build, open source]
---

Today I've taken my first steps in the open-source world: I became a committer to the [GradleFx][1] project.

##What project say you?

[GradleFx][1] is an automated build tool for building Flex and ActionScript applications. It's a plugin for [Gradle][2],
which is an alternative to the ubiquitous [Maven][3]. Actually calling it an alternative would not be doing it justice:
it's [Maven][3], [Ivy][4] and [ANT][5] mixed up and put on [steroids][6].

Until [GradleFx][1] came to the scene, us poor Flex developers only had [FlexMojos][7] (a [Maven][3] plugin with the
same goal) at their disposal. And truth be said: it has some _very_ annoying bugs. So when I discovered [GradleFx][1] I
was delighted.

When I first gave [GradleFx][1] a try, I got a few builds up and running in no time. Unfortunately, I soon found out
that [GradleFx][1] - being a fairly young project - was missing a few pieces. But since [Gradle][2] was designed to
be an easily extensible framework, I worked my way around those shortcomings with some custom tasks.

##First steps

I soon discovered that [GradleFx][1]' codebase was fairly transparent (as opposed to [FlexMojos][7]'). So I forked the
project on [GitHub][8] and I started adding some minor features. [Yennick][9], one of the founders and currently the
only active committer, seemed to like my ideas. He merged them into the codebase rather quickly, encouraged me to keep
on contributing and before I knew what happened, I became part of the 'team'.

##Contributions

So far I've added only a few minor improvements:

 - a `theme` dependency scope: well, it was missing
 - localization conventions: use localized .properties files with zero configuration
 - the `frameworkLinkage` convention: a possibility to determine how the Flex framework is linked into the application

Coming up:

 - a `flashbuilder` plugin that generates a FlashBuilder project with all the dependencies on its build path

[1]: http://gradlefx.org/ GradleFx
[2]: http://gradle.org/ Gradle
[3]: http://maven.apache.org/ Maven
[4]: http://ant.apache.org/ivy/ Ivy
[5]: http://ant.apache.org/ ANT
[6]: http://groovy.codehaus.org/ Groovy
[7]: http://code.google.com/p/flex-mojos/ FlexMojos
[8]: https://github.com/GradleFx/GradleFx GradleFx on GitHub
[9]: https://github.com/SlevinBE Yennick Trevels on GitHub
