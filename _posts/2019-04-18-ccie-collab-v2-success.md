---
layout: post
title: How to pass CCIE Collaboration v2
subtitle: Success story, tips and experience
tags: [ccie, collaboration, success, tips, experience]
---
This is a blog post about passing relatively new CCIE Collaboration v2, and becoming one of the first 10 people in the world who passed the version 2 of the exam. This post is about general concepts and approaches that should be done in order to be successful with the exam, from my perspective of course.

[![alt text](/img/2019-04-18-ccie-collab-v2-success/CCIECOL_UseLogo.png "CCIE Collaboration v2")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/2019-04-18-ccie-collab-v2-success/CCIECOL_UseLogo.png)

The exam itself is balanced and quite interesting to prepare to. The exam is testing not only the technical knowledge, but also commitment, endurance and abilities to adapt. I highly recommend to take it, if you are still deciding.

# My Journey
With 6 years of experience in Collaboration domain I've decided to pursue this certification.

I was ready to start actively preparing in the beginning of 2018, and at the same time it was announced that the blueprint will be changed starting from July 23 2018. The plan was to pass until the changes, as we all more or less new what to expect. However, my manager and friend advised me to wait for the new version of both, written and the lab exams. I did. And I'm very glad that I've followed the advised, and didn't join the **"golden rush"** of passing the previous CCIE Collaboration v1 blueprint.

It took me some time to pass the Written part of the exam, as I was targeting the new version and there were no official Study Guides or something similar. I will talk later on what exactly I used to prepare myself. And in the mid-October the Written was passed. I had some time to switch off as in one week was my wedding. I'm was lucky that my then-fiancé took almost all responsibilities for the wedding, so I could concentrate on preparing to the test.  

I've started preparing for the Lab in November 2018 by building it. From scratch. I've got some UCS servers, which were placed in the rack, found available IPs and started building the topology. After some hiccups the Lab was ready right after Christmas holidays and I could continue with technology learning and practising. My first attempt was scheduled for the end-February 2019. I felt prepared, but failed Troubleshooting and Configuration sections.

And my first advise to you would be, as soon as you back in the hotel or airport, **write down everything you remember from the exam**. I'd made a mistake not recording it, as I felt exhausted, and felt confident that I'll be able to recall it later. Don't neglect it and take time and try to remember everything what was there, as it will be essential in case the first attempt not successful.

It was terrible to wait for results, recalling questions that I most likely failed, thinking what I could've done differently and not knowing how my next month would look like. I received the results on the second day in the morning, and it was Fail.

My advise to you, even if you failed your first attempt, **schedule the second one as soon as possible** (of course you need to wait 30 days before any re-attempt). Because, in my opinion, any day you wait longer will fade-out some concepts or steps in the configuration process.

My second attempt was at the end of March. I needed to return to my duties at work, but still kept on practicing during weekends.
Due to family situation, I needed to push the attempt a couple of days forward. There is a fee you need to pay, and you cannot reschedule the exam less than 3 days before the actual attempt.

 I've passed the exam at the very end of March, and become one of the first 10 people to pass Collaboration v2 exam. **CCIE #61654**.  

Enough about that, let's switch to the "meat" of this post, is to how make you SUCCESSFUL with the exam.

# Exam Structure
So, yeah, there are three sections Troubleshooting (TS) - 2 hours, Diagnostics (DIAG) - 1 hour and Configuration (CFG) - 5 hours.

Very good description of the exam structure could be found via this link [CCIE Collaboration Written and Lab Content Updates v2.0 Follow](https://learningnetwork.cisco.com/community/ccie-collaboration-written-lab-updates).

[![alt text](/img/2019-04-18-ccie-collab-v2-success/ccie-collab-web-delivery.png "CCIE Collaboration Web Delivery")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/2019-04-18-ccie-collab-v2-success/ccie-collab-web-delivery.png)

As you can see, 30 minutes could be spared in TS and moved to CFG.
One recommendation here, even though it says that you could transfer 30 minutes from TS to CFG section, I would highly advise to try to finish TS even earlier, and **transfer more than 30 minutes** and use those time in CFG. And yes, it is allowed, and you could finish TS even earlier. For example, if you finish TS in 1 hour, you will have 6 hour for CFG.

DIAG is exactly 1 hour and you cannot skip or finish earlier.
My advise here, finish it in 30 minutes, and spend the other 30 minutes preparing some text configuration, having a short break and taking coffee/tea. Additionally, you should have the access to a text editor, so you can copy some configuration from devices in the TS section, and analyze it during the DIAG.

# Preparation and resources
In the sections below I'll provide the resources I've used for preparing to both Written and Lab exams.

## Written exam
As during the preparation to the Written, there were no official guides for the version 2 of the exam, I was mostly using the materials for the previous version and also official documentation.

1. ine.com videos.
2. Cisco Learning Network resources: [Study Materials](https://learningnetwork.cisco.com/community/certifications/ccie_collaboration/written_exam/study-material).
3. Official design and deployment guides.
4. And for the Evolving Technologies section there is just an excellent materials at [CCIE/CCDE Evolving Technologies Study Guide](https://learningnetwork.cisco.com/docs/DOC-31004).

## Lab exams
For the Lab part of the exam, as obvious it could be, you need to have your own **Lab Setup**, where you could practice, repeat and verify the technology. I will discuss later on how my lab looked like.

As for the Training companies out there, I would highly recommend collabcert.com, specifically for the Lab part. I was using their video and workbook offers, and I'm convinced that it was an integral part of my success.

And below there are some really awesome component specific sources, that were right to the point and helped me in understanding CMS and CUBEs better:
* [CMS Lab](https://cmslab.ciscolive.com/)
* [CUBE lab](http://siplab.ciscolive.com/)

# Lab setup

## Topology
Our team was lucky and got two BE6000 servers (based on UCS C220 M4S). The RAM was doubled (for future expansions and testing).

You can find some sample logical topologies on the Internet, so let me offer you the one that I was using for preparations:

[![alt text](/img/2019-04-18-ccie-collab-v2-success/Lab.jpg "Lab topology")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/2019-04-18-ccie-collab-v2-success/Lab.jpg)

As you can see, for practice I've created HQ, Site B and ITSP. It should be enough for understanding and repeating of the required concepts.

We've used two UCS C220 M4S servers with the following parameters:
* 2 x Intel(R) Xeon(R) CPU E5-2630 v3 @ 2.40GHz
* Memory: 128 GB
* HDD: 1.9 TB

And below you could see the utilisation of the servers when all the machines are turned on:
[![alt text](/img/2019-04-18-ccie-collab-v2-success/utilisation.png "Server utilisation")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/2019-04-18-ccie-collab-v2-success/utilisation.png)

The main bottleneck is the required disk space. As we were not using the smallest available ova templates and didn't use tricks to make some disk space requirements smaller, we needed to use two servers. But in my opinion, we could've used less space and fit into one UCS server.

## Software versions
* CUCM - 12.0.1.10000-10
* IMP - 12.0.1.10000-12
* CUC - 12.0.1.10000-12
* Expressways - 8.11.4
* CMS - 2.5
* CCX -  11.6.1.10000-51
* CUBE - IOS-XE 16.06.05, IOS 16.6.5

# Tips for preparation
Below I've tried to collect the most useful tips that, in my opinion, is very important for a successful attempt.

## Tips
### Troubleshooting
The best way to prepare for the Troubleshooting section is to have everything configured, and start changing some small bits and pieces, some checkboxes or making typos in one particular place of the configuration, and then observing and noting how the product or a system behaves. What are the error messages or log entries are correlated with a particular error. In that way you will get used to a different outputs that the products provide.

My favourite debugs/logs that I was collecting or checking at the very beginning of encountering issues:
* CUCM: file tail activelog /cm/trace/ccm/sdl/ recent
* CUBE:

~~~
debug ccsip messages
debug voice ccapi inout
debug voice translation
terminal monitor

sh call active voice compact
~~~

* CMS: From Admin Web Interface - Logs->Event log
* Expressways: Status->Logs->Event Log

### Diagnostics
In this section you will be presented with some common issues that you could face in the UC environments.
My advise here would be to follow the strategy of crossing out the options that you are sure that are not correct. You need to through options one by one, completely understanding what they are asking about and finally choosing only one correct answer, which is not obvious, and that's why it's better to eliminate the wrong answers first.

### Configuration
As this section is highly packed with task, you should be very time aware.
* You must try to make your practise environment to be as close to the real lab environment as possible, not only the logical setup, but also physical. Try to get two monitors, get used to accessing your environment through RDP or start using the simplest keyboards.
* Practise, practise and once more practise. At some point it will become boring, but I can assure you it's crucial for your success. Each time I was configuring, I was running into some issues, needed to troubleshot, and it was also helpful for the TS section.
* Snapshot empty configuration for every component in VMware, and each time configure from the very beginning
* Time yourself
* Details are very important. When you are reading the tasks during the Lab, don't neglect the **namings**, even if they are not influencing the functionality, most likely they are influencing scoring.
* You must leave time for even **double verification and testing** of what you've configured. Each time I found some typos or misconfigurations in my Lab, and needed to fix it.

## Faced issues
During the practice, I've faced the following issues, and there is a chance that you could face it in your setups as well:
* Bug CSCvf79013 with Learned Directory URI that were not synced to the CUCM Subscriber. Workaround: run **utils dbreplication rebuild all** from the CUCM Publisher.
* Learned Numbers through ILS are not considered in the results of Dialed Number Analyzer.
* With doing a lot of snapshots I've faced with an issue that [ICD Extension Option Does Not Appear on the Cisco CallManager Global Directory User Page](https://www.cisco.com/c/en/us/support/docs/voice-unified-communications/unified-contact-center-express/91935-user-not-shown-global-dir.html) and needed to follow the fix explained there.

## Testing Centres
I have not faced any issues with the POD setup, RDPs or any of the 8845's phones (except the fact that I accidentally kicked the power cord of one of the monitors and switched it off), however I've heard a lot of stories that some components of the Lab were failing for no particular reason. And my advise here would be, if there are some strange issues with the POD setup, some devices are not accessible, RDP cannot start or phones are not registering - **inform lab proctors** as soon as possible. There are high chances that something could be wrong with the Lab itself which is not related to your activities.

Not everything works as in real life, but you need to approach that from different sides as it could be not so obvious or straight forward, and here your real-life experience would be the best helper.

## Preparation strategy
The exam requires full dedication and excluding all the external distractions. At first I was preparing to the Lab after work and during weekends, but I felt that it's not so useful or that it is bringing me closer to my goal.
That's a must to take preparatory vacation and focus only on the Lab. At least for me, I couldn't combine work and preparations, as work always took priority.





I wish you all the best of luck and I know, it's hard, but I'm sure it’s worth it. Good Luck.
And don't forget to celebrate with the loved once, as it's very important and we tend to neglect that part.

Dmytro Kravchenko, CCIE Collaboration #61654
