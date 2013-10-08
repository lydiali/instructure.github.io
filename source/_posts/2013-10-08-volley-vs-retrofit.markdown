---
layout: post
title: "Volley vs Retrofit"
author: "Josh Ruesch"
date: 2013-10-08 13:47
comments: true
categories: 
---

As you are probably aware, we recently released Canvas for Android 2.0. Although it was a huge improvement from previous installments in terms of features, design, and usability, there was one nagging issue in the back of our minds: speed. There are times when the app isn’t as snappy as we’d like it to be. After some profiling, benchmarks, and common sense, we determined that retrieving data from the API (the networking) was the bottleneck.

The Old Way: AsyncTasks
-------------
As of the latest version, we use the built-in AsyncTasks to retrieve data from Canvas’ servers. A quick Google search will explain the numerous issues with our approach: no orientation-change support, no ability to cancel network calls, as well as no easy way to make API calls in parallel. With the exception of Froyo and Gingerbread, AsyncTasks (by default) run in a serialized fashion. In a practical sense, this means that only one AsyncTask is running at any given time. Views that require multiple API calls (the DashBoard currently has 7) run extremely slow; sometimes taking multiple seconds to load.

Introducing Volley and Retrofit
-------------
Luckily, there are a few third party libraries that provide support for concurrent background threads, network caching, as well as other features that clean up networking code substantially. The first library that we looked into was [Volley](https://android.googlesource.com/platform/frameworks/volley/), an open-source library written by Google. It’s currently used in AOSP Android as well as most of Google’s first-party applications. The other library we looked into was [RetroFit](http://square.github.io/retrofit/), another open-source library written by Square. 

How We Decided
-------------
The decision to switch the architecture of our networking code was not an easy one to make. The interface with the Canvas API is a substantial part of our application. At the time of writing this blog post, the Canvas for Android project has about 43,000 lines of actual code. Around 3,500 lines of that code are dedicated to interfacing with the Canvas API (setting up API endpoints, etc). JSON parsing comprises of another 7,300 lines of code. That means that more than 25% of our code is dedicated to retrieving/saving data from the Canvas API. That’s just making the API calls and parsing the results. That doesn’t include any AsyncTask code or caching, which I feel should be categorized as networking code as well.
 
As you can see, if we were going to switch out our networking code, it would have to be for _compelling_ reasons as it’d be a huge refactor. In order to decide if it was worth it, I did a lot of reading about both Volley and Retrofit. I looked at forums, blogposts, example code; I read as much as I could, so I could be educated. I learned a lot, but I will try to summarize it the best I can. From outwards appearances, they are quite similar in usage. They both allow you to provide a “callback”, which is an interface with two methods that you must override: success and failure. One of the methods will be called on the main thread at the completion of the asynchronous network call. The big difference is in how you specify the API endpoint and what you actually get back. With Volley, you specify the __entire__ endpoint dynamically (parameters and all) at the time of making the API call. By default, Volley returns a JSONObject or a JSONArray depending on the type of request. Retrofit, on the other hand, has you set the base endpoint url for all API calls, then it has you build static interfaces that specify endpoints using Java _annotations_. You can cleanly and dynamically substitute path-segments, POST/GET variables, etc. into the endpoint at the time of making the API call.  In order to make an API call with RetroFit, you call a method on the interface, pass in any substitutions, and it will return to you a java model object. By default, Retrofit does the JSON parsing automatically using GSON (which is really, really fast) although you can plug in your own JSON parser if you want.  Even though the setup is slightly different, the actual API calls are done in a similar way.

In order to get some performance benchmarks, I wrote a sample application where I could control/simulate real-world API calls that Canvas for Android actually makes. It would also give me a little bit of experience actually using the libraries. To start off with, I wrote a very basic app that allowed me toggle between an easy API with little JSON parsing and a complex API with a long response. The amount of times that the API call would be called could be changed in the app as well. I also included the exact suite of API calls that the Canvas for Android Dashboard makes for a more real-world test. This benchmark showed me three things: 1) they were both significantly easier to use than AsyncTasks, 2) they both cleaned up the codebase, and 3) they were both a lot faster than what we were currently doing. Obviously the benchmarks fluctuated based upon network conditions; however, they consistently outperformed the way we are currently doing our networking. 

 ![](http://i.imgur.com/tIdZkl3.png)

In all three tests with varying repeats (1 - 25 times), Volley was anywhere from 50% to 75% faster. Retrofit clocked in at an impressive 50% to 90% faster than the AsyncTasks, hitting the same endpoint the same number of times. On the _Dashboard test suite_, this translated into loading/parsing the data several seconds faster. That is a massive real-world difference. In order to make the tests fair, the times for AsyncTasks/Volley included the JSON parsing as Retrofit does it for you automatically.

At this point, we wanted to switch our networking library for performance reasons, but our decision had to take other criteria into consideration. If we were going to spend time refactoring a quarter of our code base, we would have to be a little bit picky. Some of the things we took into account were speed, ease of integration, code cleanup, scalability, and time required to write new API calls. 

RetroFit Wins
-------------
In the end, we decided to go with Retrofit for our application. Not only is it ridiculously fast, but it meshes quite well with our existing architecture. We were able to make a parent _Callback Interface_ that automatically handles the error function, caching, and pagination with little to no effort for our APIs. In order to merge in Retrofit, we have to rename our variables to make our models GSON compliant, write a few simple interfaces, delete functions from the old API, and modify our fragments to not use AsyncTasks. Now that we have a few fragments completely converted, it’s pretty painless. There are still some growing pains and issues that we have to overcome, but overall it’s going smoothly. In the beginning, we ran into a few technical issues/bugs, but Square has a fantastic [Google+ community](https://plus.google.com/communities/109244258569782858265) that was able to help us through it. So far, we have successfully converted Conversations, Discussions, and most of the Dashboard API calls to Retrofit. We have made good progress, but we know we have a long way to go still.
	
You may be wondering why you should care about what library our Android app uses for networking. From a Canvas User’s perspective, you’ll be able to see significant performance improvements in later versions of our Android app. You’ll also see tablet and landscape support sooner than if we hadn’t switched (both are coming soon I promise). From an developer’s perspective, you are now more knowledgeable about Android’s shortcomings and a few solutions. Finally, integrating retrofit and cleaning up our networking code is a HUGE (and possibly final) step towards being able to _open source_  CanvasKit for Android, which will be an Android library that will handle network calls with local caching.

We are excited to bring these updates to you in whatever form they impact you the most. Feel free to email me at [joshr@instructure.com](mailto:joshr@instructure.com) if you have any questions/comments/concerns, or if you just want to chat. 

Keep learning, <br>
Josh Ruesch


Resources:
-------------
####Retrofit:
*   [Source and Samples](https://github.com/square/retrofit) (Source and samples)
*   [Square Homepage](http://square.github.io/retrofit/)
*   [Square Google+ Community](https://plus.google.com/u/0/communities/109244258569782858265)

####Volley:
*   [Source](https://android.googlesource.com/platform/frameworks/volley/)
*   [Demonstration](https://developers.google.com/live/shows/474338138)
*   [Github of Examples](https://github.com/ogrebgr/android_volley_examples)
*   [Usage Example](http://www.technotalkative.com/android-volley-library-example)