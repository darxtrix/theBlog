---
title: GSoC'15 - GNU Mailman mid term report
tags: gsoc gsoc15 mailman open-source
---

Tomorrow marks the mid of the GSoC'15 journey. The journey till now was full of roadblocks and surprises for me. At the same time, I learned about many asynchronous programming techniques while working with NodeJS and different testing strategies. It wouldn't have been possible without my awesome mentors, *@florianf* and *@maxking*. 
Here are the details of what I've achieved so far : 


### Request making modules
These modules serve as the backbone to the project. They provide classes that are directly inherited by the classes in other modules and are responsible for providing an abstraction of connection to the mailman-core REST API server. At the same time these provide pleasant API to talk to the REST server. These modules have been fully tested and have gained more robustness over the time. 

### Other modules
Then I went on to implement classes analogous to *_Addresses*,  *_Address*,  *_Preferences*,  *_Settings*,  *_Member* and *_User* as in [mailman.client](https://gitlab.com/mailman/mailmanclient). These have been fully tested from the command line and working as expected. I implemented them in three phases:

- First I tried to cover the basic API they can offer. 
- Then, with discussion with mentors I decided what changes I can make to the public API to make it more user friendly. This required considerable changes to the implementation of the methods of the respective classes.
- Finally, in the last phase I refactored the code so as to separate out the redundant logic in a bunch of utility functions and to provide a consistent usage API to developers and the end SDK users.
 
<br/>

### Current Work
A bunch of above mentioned modules has been completely unit tested but due to recent code changes some of the tests are failing :cold_sweat:, so working on the testing part at current.

### Future work
After finishing testing I will jump on to implement the left modules *List*, *Domain*, *Client* etc. After that, I will improve the code quality by using code linting and time profiling tools. And, yeah documentation !


Here is the repository URL if you want to give it a shot:                                                
**Repo URL** : [https://gitlab.com/black-perl/mailman-client.js](https://gitlab.com/black-perl/mailman-client.js)

Stay in touch !














 







