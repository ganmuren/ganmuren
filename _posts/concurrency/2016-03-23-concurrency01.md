---
layout: post
title: "Concurrency(01)"
tagline: "understanding concept & avoiding dead lock" 
category: tech essays
tags: [concurrency]

---
{% include JB/setup %}

I am working on the server side and fundamental component these years. Concurrency thought is applied every time, so simply talk something about it for self summarizing and other people assist. 

*(This article will be written till where I think to, not regularly update… )*

## 1.Introduction

What is concurrency? And what’s the deference between Concurrency and Multi-thread? Let’s see below scenario:
You are eating lunch, while your phone ringing. What would you do?

1.	Let’s finish lunch, and then I will pick up the phone. This is neither concurrency nor multi-thread.
2.	You bite the bread, and then say something on the phone; sequentially you bite the bread again, swallow it, and continued to say words on the phone. This is concurrency but not multi-thread.
3.	You accidently have two mouths (OYE!). You can chew the bread all the time, while continue talking on the phone by anther mouth. This is multi-thread and also is concurrency.

**Concurrency**: Doing several tasks at the same time, but actually these tasks (maybe) are being processed sequentially in the kernel processer. Tasks are divided to many time spans, processer deal with them by switch their context.

**Multi-thread**: The system have indeed 2 or more processer, it can handle multiple tasks separately. 

## 2.	Simplest concurrency

Tasks do not affect each other; these do not access same entity. Just mind their own business.

Tasks will access same entity, but not at the same time. In that case, for this exact entity, accessing operation is sequential. This is safe concurrency too.

## 3.	Dangerous concurrency
Two tasks access entity at the same time. This is called **“Dirty Read and Write”**, there’re a scenario: Let’s say parameter ‘a’=1, Two thread increment 1 in parameter ‘a’, so the result of ‘a’ should be 3. But reality is:

1.	Thread1 read ‘a’; thread2 read ‘a’;
2.	Thread1 a++, then restore to a, a=2;
3.	Thread2 a++, then restore to a, a=2, too;

After 2 operations, ‘a’ is not 3 as we expected, is 2 actually. This is concurrency mistake. If ‘a’ is disposable, thead2 access ‘a’ when thread3 are disposing it, this makes more fetal error!

## 4.	How to avoid it?
1.	Do task1, task2 sequentially! It’s easy way. You play safe, but abandon the efficiency.
2.	Concurrency T1,T2, but will not access same entity at same time (we also call these kind of entity is Critical Entity). That is to say entity operation is not concurrency, it’s sequential.
3.	“Lock & Clone” strategy. The entity, which be created by clone, shall not be modified, it’s just for read access. We operate on this temporary clone entity this time. We lock & clone the critical entity every time. 

Method 2&3 is always be used. Let’s discuss about them.

## 5.	Access Critical Entity Sequentially 
Core concept of method 2 is sequential. Tasks are not sequential, access critical entity sequentially. Handling things one by one is common sense for human. It’s easy for maintenance people to understand. An easy way is **“Lock then access”**.  So we say something about the “Lock”.
![](/images/2016/serial_by_entity.png)

## 6.	Lock to hell
If we say concurrency, we use “LOCK”. Unfortunately, misuse happens. Program is running for a period of time, and suddenly, crash. There’re problems we met frequently:

1.	Dead lock: T1 lock A request B, T2 lock B request A. Thead1 and thread2 block each other. 
2.	Recursive lock: T1 lock A and lock A again, thread block itself.

Recursive lock is easy to solve. We have `std::recursive_mutex` in C++11. Higher level program language has this mechanism.  Say, lock in C#. We use counter to implement recursive lock, generally. We counter++ when lock operation, counter-- when unlock it, counter==0 means released. This is a semaphore thought. C++’s `share_ptr/auto_ptr` are implemented by this way. Reference counter can judge where object is be used or not. Memory management in C# or Java is according this thinking.

## 7.	Prevent program from Dead lock
OK then, what about the “Dead Lock”? Exactly say that, it’s hard to avoid dead lock in program tier. Better paying attention to the resource which will require each other, and make a good software design; than abusing “lock” keyword everywhere. I am an API implementer, so I can publish document to inform the best practice of the interface.  But if user do not follow the rule, and make system crash, what could I do? En…..OK, There’s something you can do, in fact:

**“Try-lock”** will save your dead lock program somehow. U can use a time-lock instead of normal-lock. If the program does not get access authority of the critical entity for a long-time (u can set the time span), It just skip following operation, leave LOG. Then try again in a while if you want. U can use “semaphore” thought I mentioned above to implement this feature. Core concept is use spin-lock and Flag parameter. I know u can do it. ;-)

*(to be continued...)*
