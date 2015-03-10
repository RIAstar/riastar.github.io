---
layout: post
title: "CFInvoke with a custom endpoint - ColdFusion"
date: 2011-08-23 00:00
comments: true
categories: [ColdFusion, snippet, web service]
---

Consuming web services in ColdFusion is easy - at least since CF9, because it used to be a PITA in previous versions
for some kinds of services (e.g. SOAP).

You can even write it in one line - woohoo! -, like this:

``` xml Default cfinvoke usage
<cfinvoke webservice="http://www.riastar.net/service/?wsdl"
          method="remoteMethodName" someArgument="hello"
          returnvariable="result" />
```

[CFInvoke][1] assumes that the url of the service and its endpoint are the same. As usual with ColdFusion, this is all
very nice and handy as long as your situation matches the default. But from the moment you have to customize a bit,
you can start pulling out your hair. And the fact is that the real world has this tendency not to match the default
situation.

For example: if the service you want to access has an endpoint that differs from the service url, you’re out of luck.
I found the following - completely undocumented - solution to use a custom endpoint:

``` xml cfinvoke where endpoint != service url
<cfset service = createObject(
          "webservice",
          "http://www.riastar.net/service/?wsdl"
) />
<cfset service._setProperty(
          "javax.xml.rpc.service.endpoint.address",
          "http://www.riastar.net/service"
) />
<cfinvoke webservice="#service#"
          method="remoteMethodName" someArgument="hello"
          returnvariable="result" />
```

Notice the underscore? That means you should probably not be using this.

Anyway, while researching this I also figured out the cfscript syntax to do the same thing.

``` javascript cfinvoke where endpoint != service url as cfscript
service = createObject("webservice",
                       "http://www.riastar.net/service/?wsdl");
service._setProperty("javax.xml.rpc.service.endpoint.address",
                     "http://www.riastar.net/service");
result = service.remoteMethodName(someArgument="hello");
```

You can also find this snippet on [Gist][2].

[1]: http://help.adobe.com/en_US/ColdFusion/9.0/CFMLRef/WSc3ff6d0ea77859461172e0811cbec22c24-7e0a.html
[2]: https://gist.github.com/RIAstar/1164657
