---
layout: post
title: Cisco Spark Bot for daily scrum meetings
subtitle: Bot powered by MongoDB and Flask
tags: [cisco spark, bot, automation]
---

We've received a request from one of our Project Team members to automate the daily stand-up meetings using Cisco Spark.

The script can be found at [SparkScrumBot](https://github.com/dmkravch/SparkScrumBot)

The script is running from **/etc/crontab** as a service each morning.
```
10 9 * * 1,2,3,4,5 root systemctl force-reload ScrumBot.service
```

Some short description of the code actions:
1. At the defined time, the script will check all the participants of the selected space. Return a list.
2. It should be possible to specify exclusions (which users needs to be deleted from that list). Return a list.
3. The bot sends the same question to all the participants from the space.  
4. When user answers, the script retrieves the latest message from the user collection, and, if it's a pointer, records the first answer into the answer collection. For each user. Ask a second question. Third.
5. Post a message to the general space, with mentioning the user.
6. Repeat the previous cycle with getting responses from each user.

That's it. We are still planing to make some modifications and add additional features. 
