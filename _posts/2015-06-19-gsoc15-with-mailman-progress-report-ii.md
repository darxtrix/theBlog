---
title: GSoC'15 - GNU Mailman progress report
tags: gsoc gsoc15 mailman open-source
---

The first three weeks were full of excitement and knowledge for me as a GSoC student for [GNU Mailman](http://list.org). I accomplished weekly goals and at the same time faced challenges too. An overview of my journey so far:

- The main aspect and probably the most important thing was to implement generic full-fledged request making classes which support the required HTTP methods for the [mailman.client](https://launchpad.net/mailman.client). Along with it, these classes should provide flexibility in framing URLs from query paramteters, power to customize the request endpoints and of course, a beautiful public API :wink: . To make API more handy, I decided to offer both types of syntax, the node style callback and the promises style. For this, I have to take proper care of doing promisification of responses from the REST server and nodefication of the callbacks. [This](http://black-perl.in/black-perl-gsoc-with-mailman-week-i) blog post contains the details and design decisions of implementation of these classes. Links to these classes :

    - [BaseRequest](https://gitlab.com/black-perl/mailman-client.js/blob/master/lib/shared/BaseRequest.js)
    - [ApiRequest](https://gitlab.com/black-perl/mailman-client.js/blob/master/lib/shared/ApiRequest.js)
<br/>
<br/>

- The next thing was testing of these classes. I went on to use *Mocha-Chai-Sinon* suite for testing. The testing process for the synchronous functions, mostly the non-request making functions was pretty straight. The tough part was to test the request making functions i.e *.get()* etc. I am using [Superagent](https://visionmedia.github.io/superagent/) as the request library. There were two choices, first was either I create mocking fixtures or the second one was to do something out of the box. Then, I decided to go for the second. I injected the **mocked** Superagent [object](https://gitlab.com/black-perl/mailman-client.js/blob/master/test/shared-test/mocks/superagent-mock.js) and then I simply spied on it for the called methods. And everything was super easy thanks to this [sandboxed-module](https://github.com/felixge/node-sandboxed-module) npm package. Few links:
    
    - [ApiRequest.tests.js](https://gitlab.com/black-perl/mailman-client.js/blob/master/test/shared-test/ApiRequest.tests.js)
    - [BaseRequest.tests.js](https://gitlab.com/black-perl/mailman-client.js/blob/master/test/shared-test/BaseRequest.tests.js)
<br/>
<br/>

- After finishing this crucial part of the project, I went on to implement the analogous classes *_Addresses*, *_Address*, *_Preferences*, *_Settings* as in [mailman.client](https://launchpad.net/mailman.client). There were a few challenges I faced to port the existing python functionality so as to provide a Node.js style API in the [new SDK](https://gitlab.com/black-perl/mailman-client.js), but these were easy to handle :smiley:. Links to the implementations :
    
    - [Addresses.js](https://gitlab.com/black-perl/mailman-client.js/blob/master/lib/Addresses.js)
    - [Address.js](https://gitlab.com/black-perl/mailman-client.js/blob/master/lib/Address.js)
    - [Preferences.js](https://gitlab.com/black-perl/mailman-client.js/blob/master/lib/Preferences.js)
    - [Settings.js](https://gitlab.com/black-perl/mailman-client.js/blob/master/lib/Settings.js)
<br/>
<br/>

- Currently I am working on testing of the classes I mentioned above. I am planning to try writing tests before hand for the coming implementations to try something new :smiley: I don't know how it will go but certainly it will give me a clear cut view of the public interface of a class before actually implementing it. 

So, that's a brief summary of my work till date. Happy coding !














 







