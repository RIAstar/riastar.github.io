---
layout: post
title: "GradleFx: multiple configurations"
date: 2013-03-28 22:37
comments: true
categories: [actionscript, flex, gradle, gradlefx, build]
---

There has been [some discussion][1] lately about how we might be able to allow for [custom configurations][2] in
[GradleFx][3]. Such a custom configuration would allow a user to:

 - generate different outputs from a single code base
 - use specific dependencies for each of these outputs
 - hook up any other custom task to a particular configuration

As these discussions illustrate it is not currently possible to do so. And though we are looking into some possible
solutions, they may not be implemented soon. So here I am to save the day and provide you with a viable workaround,
until we get the matter sorted out.

##Use case

This solution is heavily based on mrhaki's [excellent article][4], but it is tailored specifically for the Flex
developer that needs to generate multiple outputs for different environments from a single project. I created this when
facing a real-world issue and the solution is being used in production right now, so it's been tried and tested.

The specific situation I was facing was that I needed mock services and embedded mock data, in such a way that the
application could run in standalone mode (i.e. without a server) and give a realistic feel to the users testing the UI.
Of course in the production build I did not want to include all the additional classes and certainly not the embedded
data. So here's how I created a "clean" production output and a heavyweight development output including the mocks.

##A groovy configuration

Let's start by stubbing out our config file for two different environments.

``` groovy
environments {
    dev {
        //development specific stuff goes here
    }

    production {
        //production specific stuff goes here
    }

    //all that is shared by both configurations simply stays in the build.gradle file
}
```

Good, that's how mrhaki described it. Now lets add some properties that we can use in a GradleFx build. Note that I put
all the mock-related code and the resources (the data files) in a separate source tree (`src/mock/`), which allows me
to easily add/remove that part when needed.

``` groovy
environments {
    dev {
        //I added the resources folder to the 'srcDirs' because the files had to be embedded
        srcDirs = ['src/mock/actionscript', 'src/mock/resources']

        //fx-mocks is a library that helps with simulating data from services
        //fx-users-dev is a special build of the fx-users lib that also includes mock services and data
        dependencies.merged = [
            'net.riastar.lib:fx-mocks:1.0-SNAPSHOT',
            'net.riastar.lib:fx-users-dev:1.0-SNAPSHOT'
        ]

        //it's gonna run in standalone mode so we set a bigger default window size
        //mock services are injected, so they're never referenced explicitly;
        //that's why we have to add them through the 'includes' option
        additionalCompilerOptions = [
            "-default-size=1024,600",
            "-includes=" +
                "net.riastar.myapp.service.impl.UserMockService," +
                "net.riastar.myapp.service.impl.MyValueObjectMockService"
        ]
    }

    production {
        //in production we don't use the mock classes and resources,
        //we don't use the mock library and we use the production build of the fx-users library.
        srcDirs = []
        dependencies.merged = ['com.trasysgroup.lib:fx-users:1.0-SNAPSHOT']
        additionalCompilerOptions = []
    }
}
```

Note that in `production` I mirrored every property that I declared in the `dev` block, if only to set its value to an
empty array. This is not strictly nesessary, but it will make the code in the build file a lot more readable later on.

##Use it in a build script

So we've configured our two environments. Next I will show you how to modify the Gradle(Fx) build script to leverage it.
As in mrhaki's post, this script reads the custom `env` property from the issued command and processes the config file
accordingly. Hence you'll be able initiate the build of a specific build like so:

``` bash
$ gradle clean build -Penv=production
```

``` groovy
buildscript {
    repositories.mavenCentral()
    dependencies.classpath 'org.gradlefx:gradlefx:0.6.4'
}

apply plugin: 'gradlefx'
type = 'swf'

//'development' build by default if the 'env' argument wasn't set
//I'm not gonna explain this code: read mrhaki's post
def environment = hasProperty('env') ? env : 'development'
ext.config = new ConfigSlurper(environment).parse(file('config.groovy').toURL())

//add our custom srcDirs to the default ones
srcDirs += config.srcDirs

dependencies {
    //add our custom libraries; swiftsuspenders will be used both in production and development
    merged 'org.swiftsuspenders:swiftsuspenders:2.0.0-rc1',
           config.dependencies.merged

    flexSDK 'org.apache:apache-flex-sdk:4.9.1'
}

//add our custom compiler options
additionalCompilerOptions = config.additionalCompilerOptions
```

See why we declared empty arrays in the `production` block? It would've required some conditional logic if we hadn't,
leaving the result much less elegant and readable. Especially the `merged` dependency would've been butt-ugly; believe
me, I tried.

##Conclusion

Well that's about it. It's not extremely complicated and it's fairly elegant. I know it's not exactly the Gradle way,
but it gets the job done without too many compromises. Hope this helps someone.

[1]: https://github.com/GradleFx/GradleFx/issues/77 It's not possible to keep metadata when running FlexUnit tests
[2]: https://github.com/GradleFx/GradleFx/issues/80 It's not possible to add multiple compileFlex tasks to one project
[3]: http://gradlefx.org/ GradleFx
[4]: http://mrhaki.blogspot.be/2009/11/gradle-goodness-using-properties-for.html
