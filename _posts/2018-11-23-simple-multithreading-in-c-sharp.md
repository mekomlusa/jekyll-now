---
layout: post
title: Simple Multithreading in C#
---

Having been fiddling with C# for a while (on a part-time basis), I was finally assigned to help with the redesign of mobile API calls. My first method is to save inspection requests sent from the mobile. A typical scenario would be: a user fills out inspection details on the phone - the mobile app saves inspection details (multiple database tables are involved) and responds to the user - the system sends alerts to users who have opted in push notifications, and follow-up email instructions to the assignee. It is a good chance for me to get exposed to the basic of RESTful API design, plus I realize that mobile requires blasting fast responses. To respond timely, asynchronized calls are necessary (especially for saving large-size attachments to Azure and generating PDF reports). So... this is my very first time working with multithreading, AND not in a classroom setting (It's pretty hard to think of multithreading cases for school projects though). It took me a while to get it to work, so I'd like to write something about it.

## The goal

Once a request is detected, this mobile call should do the following: saves inspection details - saves attachments, if any (async) - fills in the email template with all the details - sends notification/follow-up emails to related parties, generates a PDF report, and sends a JSON string to another service (async). Previously, the call saved attachments in the same thread, thus users often complained about timeouts if there were too many attachments. The async call to do the email/PDF/JSON string was handled by [Task.Factory.StartNew](https://docs.microsoft.com/en-us/dotnet/api/system.threading.tasks.taskfactory.startnew?view=netframework-4.7.2#System_Threading_Tasks_TaskFactory_StartNew_System_Action_System_Object__System_Object_) with some custom logic to enable multithreading. This call is convoluted to understand, as I believe it was originally written in a really old .NET version. Both need to be redesigned this time.

## Solution 0: Manually assign threads

One way to achieve the goal is to use the [`System.Threading`](https://docs.microsoft.com/en-us/dotnet/api/system.threading?view=netframework-4.7.2) namespace. If I ended up pursing this path, then... it's no longer "simple multithreading", right :) It is also suggested not to manually manipulate with threads, unless you're an advanced user and know exactly what you'd like to achieve. Using the [Task Parallel Library (TPL)](https://docs.microsoft.com/en-us/dotnet/standard/parallel-programming/task-parallel-library-tpl) would be a safer choice.

## Solution 1: HostingEnvironment.QueueBackgroundWorkItem

Since our project is built upon .NET framework 4.5.2, [`HostingEnvironment.QueueBackgroundWorkItem`](https://docs.microsoft.com/en-us/dotnet/api/system.web.hosting.hostingenvironment.queuebackgroundworkitem?view=netframework-4.7.2) is available. In fact, it has been used in other mobile calls to save nonessential components that normally take longer time to save. I tried this first on saving attachments, but I soon found out that if an inspection record contains too many attachments (say 10), **not all of the attachments are saved**. It's fine for me that these attachments are not saved in order, but some of them simply are gone. Since the attachments are not saved correctly, the generated PDF and emails do not contain all attachments as well.

On the positive side, `HostingEnvironment.QueueBackgroundWorkItem` automatically assigns tasks on the backend. Even when the main application sends a cancellation token, each background task still gets around 30 seconds to complete the task. The downside, though, is that not each task is completed successfully; that contributes to why some of the attachments were not saved earlier in the testing scenario. Moreover, this call *"schedules a task which can run in the background, independent of any request."* When I applied the method at three different places (save attachments, send email, and the big async task), an interesting thing happened - I might got an email containing a blank PDF report, before the save attachments operation is completed. That's because the three `HostingEnvironment.QueueBackgroundWorkItem` calls were run simultaneously, while they should have been executed strictly in a sequential order. So I had to look for something else.

## Solution 2: Parallel.ForEach

Like the name suggested, [`Parallel.ForEach`](https://docs.microsoft.com/en-us/dotnet/api/system.threading.tasks.parallel.foreach?view=netframework-4.7.2) is basically the parallel version of the `for` loop in C#. One advantage, though, is that one may set the number of concurrent tasks using the [`ParallelOptions.MaxDegreeOfParallelism`](https://docs.microsoft.com/en-us/dotnet/api/system.threading.tasks.paralleloptions.maxdegreeofparallelism?redirectedfrom=MSDN&view=netframework-4.7.2#System_Threading_Tasks_ParallelOptions_MaxDegreeOfParallelism) property. Say that I would like to limit 4 concurrent threads at the same time, I could do the following:

```
Parallel.ForEach(
    listOfWebpages,
    new ParallelOptions { MaxDegreeOfParallelism = 4 },
    webpage => { Download(webpage); }
);
```

Here, `listOfWebpages` is a list containing multiple webpages, `webpage` is the iterated object, and `Download(webpage)` is a separate method that I'd like to run. It's basically a rewrite of the following `ForEach` loop:

```
foreach(webpage in listOfWebpages){
    Download(webpage);
}
```

But for my case, I had to set `MaxDegreeOfParallelism` to 1. Essentially, this is equivalent to single thread operations. But if I don't do so, there is a certain possibility that some of the attachments would not be saved correctly (my guess is because of the atomicity property of the database, but not sure). And I found that `Parallel.ForEach` is indeed doing the operation in background and responds pretty fast. For my case where some orders are desired, this is a good option.

## In short..

This is my first attempt on multithreading and to me, it's a big challenging but fun! Though read about multithreading and concurrency in literature, I've never tried to implement it in a real-world setting. I was glad to know about the threading class and TPL available in C#, but to solve the task at hand with minimal effort I've decided to use high-level wrappers like `HostingEnvironment.QueueBackgroundWorkItem` and `Parallel.ForEach`. I have not yet seen parallel programming in Python yet... once I do, I will write a sister post about how to do it in Python as well!
