---
title: "Analytics Wrapper"
date: 2017-07-03T10:08:01+06:30
draft: false
---



I wrote this library for those who have to sacrifice apk size to add analytics in Android Apps like me.

Google Analytics uses something called [Google Measurement Protocol](https://developers.google.com/analytics/devguides/collection/protocol/v1/reference) behind the scenes. This is well documented API and you can built with your desired language easily.

I want to use it in other projects and I don't wanna copy this file again and again. So I wrote this wrapper as Android Dependency and publish it to Jcenter. You can use it freely if you don't want to write a lot of codes.

### To use it,
#### Add this line to your module ```build.gradle```
```	
compile 'co.monadlab.wailynnzaw:gawrapper:0.0.2'
```

To use it properly, intialize it in your Application class like this,

```
  AnalyticsWrapper wrapper;

  wrapper = new AnalyticsWrapper.Builder(getContext())
            .trackerId(GA_PROPERTY_ID)
            .quietMode(false)
            .uuid(UUID.randomUUID().toString())
            .build();
```

#### Note:
In the ``uuid`` field, you should add UUID string and save it with Preferences in order to get the unique user.


### Usage

Get the wrapper instance from your application class.

You can track the `screenView` easily like - 

```
Application.getWrapper().screenView("AnalyticsExample");

```
and events like -

```
Application.getWrapper().event("Main", "Share", "News", 0);
```

Get the code via [github repo](https://www.github.com/wailynnzaw/analytics_wrapper)