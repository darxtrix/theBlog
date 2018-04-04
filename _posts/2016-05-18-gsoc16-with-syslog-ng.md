---
title : Yet again, GSoC'16 with the syslog-ng team
tags: gsoc gsoc16 syslog-ng open-source
---

w00t, Yeah I am overwhelmed :blush:. I applied to [syslog-ng](https://syslog-ng.org/), a software tool for log management for this year [Google Summer of Code](https://summerofcode.withgoogle.com) program and got selected. 

<br/>

![GSOC'16-Ankush-Sharma](../img/gsoc2016.jpg)

<br/>

As always, doing scripting is a cup of cake for me :bowtie: and it's been a while I haven't done any ! For the past year, I have been working with techs like **Node.js** & **C/C++**, I merely got a chance to do any kind of scripting. So, I decided to apply for the [Release Automation Project](https://github.com/balabit/syslog-ng/wiki/GSoC2016-Idea-&-Project-list#project-automated-release-generation-for-syslog-ng); keeping in mind that it would involve a lot of **Python** scripting and dealing with Web APIs and at the same time I get to explore [Release Automation](https://en.wikipedia.org/wiki/Application_release_automation) paradigm.

Here is the link to the [proposal](https://github.com/balabit/syslog-ng/wiki/%5BGSoC'16%5D-Automated-release-generation-for-Syslog-ng). I also wrote a proof of concept assignment to get used to things and as part of the selection procedure, you can check it out [here](https://github.com/black-perl/release_eventlog). 

Having release automation setup for your project is always handy and it saves time for testing builds and notifying user space about the new releases. Talking about the release automation of syslog-ng, the project is hosted on [GitHub](https://github.com/syslog-ng/syslog-ng) and uses [OpenSuse Build Service](http://openbuildservice.org/) for testing binaries. The releases are published using the cool new [Releases](https://help.github.com/articles/creating-releases/) feature of [GitHub](https://github.com). So, a tentative workflow would be like this: 

<br/>

![syslog-ng-release-automation-workflow](https://camo.githubusercontent.com/f96a14cd1db781ffae5b9f1fe0aa40ea5cdef2c4/687474703a2f2f692e696d6775722e636f6d2f6a6b5a353151312e706e67)

<br/>
I also aim to learn more about the core of syslog-ng at the same time. The syslog-ng itself is written in **C** programming language. Diving in the core architecture would be fun for sure :wink:. 

I will be blogging a lot this summer, as I am totally free and hacking on syslog-ng ;-)
So, stay in touch !
                                              
`#summers_are_gonna_be_fun_again`