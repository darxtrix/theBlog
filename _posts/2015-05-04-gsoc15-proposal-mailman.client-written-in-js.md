---
title: My GSoC proposal -- mailman.client written in JS
tags: gsoc gsoc15 mailman open-source
---
This post will walk you through my proposal for GSoC'15 @ GNU Mailman. Along with, it introduces the Mailman project and it's parts.

## <span class="green"> Introduction </span>

[Mailman](http://list.org) consists of the following modules:

- [mailman-core](https://launchpad.net/mailman): The core mailman module. It offers command line tools for using mailman. It has a REST server interface too, with so you can use http requests for talking with **mailman-core**.
- [postorius](https://launchpad.net/postorius): The Mailman web UI to do management tasks like CRUD operations on lists, domains, list_members, list_moderators etc. It depends upon **mailman.client** for talking with **mailman-core**.
- [mailman.client](https://launchpad.net/mailman.client): It talks to **mailman-core**'s REST server and provides API to **postorius** to do various operations. 
- [hyperkitty](https://github.com/hyperkitty/hyperkitty): A web interface to access GNU Mailman v3 archives.
<br/>
<br/>

![Mailman](../img/mailman.png)


Now, the detailed proposal follows :sweat_smile:
<br/>
<br/>

## <span class="green"> Abstract </span>
The project aims to port **mailman.client** to **JavaScript**, providing an API familiar to **Node.js/IO.js** developers, and finally ship it as a npm package for easy installation in a Node.js environment. The other main objectives would be to write a strong test suite for the developed Mailman JavaScript Client and writing API usage as well as design documentation for facilitating future work.



## <span class="green"> Project Proposal Information </span>

### Proposal Title :   Writing the mailman.client in JavaScript

Mailman’s REST interface exposes **a set of endpoints** to talk to **mailman-core**. **mailman.client** library is covering a **subset** of these endpoints. It is the client library that talks to core's REST API through HTTP. Right now **mailman.client** is the official client in **Python** but it would be nice to have it in **JavaScript** as well so that Mailman could be integrated with **Node.js** and **IO.js**. 

### Why a seperate JS client ?

Using the **mailman.client** one can only build **Python** applications that talks to **mailman-core**. The port will facilitate developing apps using **Node.js/IO.js** as well. The new client can come handy in situations where real-time statistics/accounting dashboard apps for Mailman are desired.

### Proposal Detailed Description :

The problem of writing **JavaScript client** for the **mailman.client** which is currently in **Python** is not a simple translation problem. There are several design challenges that need to be studied and worked out through the project. In the initial phase I plan to prepare a design plan and do a preliminary study of the existing **mailman.client** features and codebase.

<span class="red"> **[a]: Deciding the API extent** </span>

**mailman.client** does not cover all the REST API endpoints and functionalities that can be achieved using the REST interface as indicated by some issues:

<https://bugs.launchpad.net/mailman/+bug/1418276>

So it would be good to identify the set of functionalities/endpoints to cover within the new client library and the best option would be to start with understanding what the **current** client offers and what’s missing.

Also, the current client have hard-coded REST API endpoints in it's code. This would break the mailman.client if someone decides to change the REST API endpoints in future. The API users should have some sort of easy way to configure these endpoints without having a look at the code. The best option that I can think of now would be to use a config file ( INI, YAML or JSON ) for defining these endpoints or to have a command line utility for the user to change endpoints if required. **It is obvious that these endpoints aren't changed quite frequently but from my point of view it is a good software practice to have such flexibility.**

<span class="red"> **[b]: Deciding API styles and usage** </span>

- The developed API would be friendly to Node.js developers and not like the existing Python API because we already have a Python client, but the functionality offered will be the same as discussed earlier.
- As the new library is to be used with Node.js, it would be required to have **callbacks** and **promises** style syntax. Because:
	- It would provide surety that the requested data from the server-side is written to the client-side and now you can do further processing on it. 
	- This can be achieved by writing the request making methods **get()** , **post()** ,  **put()** , etc. in **asynchronous** way because they will pose blocking calls; so that callback function can be invoked after these request making functions have got the required data from the server side. For eg.  

<br/>{% gist black-perl/2a7a4f5dd0633a1963a4 %}

- The API can offer a **query builder-style** syntax to let you craft the final **request object** by **chaining filters** and other **methods**. The chaining methods are **non-blocking** of course. The final request object will make request to the Mailman’s REST endpoints and it will be a **blocking** call. For example the following can be used to get the **members of a list** :  

<br/>{% gist black-perl/190da71c294aeb876ddd %}
               
**Note :** The order of chaining may seem ambiguous at first as `client.auth().lists()` would be preferred over `client.lists().auth()` but I have mentioned the latter in context with the **Proof of Concept module [0]** for this project. There is no doubt to use the former while developing the actual client or **authentication** can be made implicit when a **username and password** is supplied during **client instantiation**.

<span class="red"> **[c]: Brainstorming features** </span>

- The current client does require knowledge of some variables such as **list_id**s’ or **email addresses**  before framing urls with them and making a request to the REST server. The mailman.client does not offer any validation for these variables because **Postorius** does this for it. It would be great if some validation checks can be integrated within the **new** client for these variables and generate proper error messages using definition of these variables in the Mailman-core’s dictionary because there are situations where the REST server gives **404** messages when an invalid **list_id** or **email address** is supplied. Using client side validation in **such** situations will make things more meaningful. More information on the implementation and usage aspects is here:   <https://github.com/black-perl/mailman-cli/tree/master/readups/client-side-validation>

- Some useful client side filtering support can be added, for eg:
	- Fetching lists matching a given list name.
	- List with most members etc.
	- More information on the implementation is here:  <https://github.com/black-perl/mailman-cli/tree/master/readups/client-side-filtering>
	- Upon successful implementation of the above features in the new mailman client, feature-requests for these can be added to the tracker of the mailman.client so that these can be integrated in it also.

<br/>
<span class="red"> **[d]: Implementation phase and design** </span>

I have implemented some of the following ideas in a **Proof of Concept module.** [0]

- **Request handling via ‘state’ transfer:**                        
The idea is to have a **base** request handling class that will provide methods to do dispatching of the request eg. **get()**, **post()**, **put()** , etc. and handling of responses i.e pagination support etc. It will also have methods to build the final URI to request from a **state** object passed to it. Let’s call this class **RequestA**.                                                  
Further this class can be extended for adding support for client side filters if someone wants to implement. Let’s call it **RequestB**.

<br/>
![Request Class Inheritance](http://i.imgur.com/oUw2nfE.png)   


- It would be good to handle the same category of endpoints by a single class. Like a **ListRequest class** for handling **/lists/*** endpoints.


![Request Handling](http://i.imgur.com/WqRSdl6.png)

- Another idea is to make other classes such as **ListsRequest** i.e those created to offer the functionality of  **_List** , **_Domains** etc. as in **mailman.client** to inherit **RequestB** with the exception of the main **Client** class which will be the entry point for passing the **state** object. This will enable request dispatching directly from any **ListsRequest** instance using inherited **post()**, **get()** etc.

- These all classes share a **state** object which gets its initial value from the user while instantiating a **Client** instance. This **state** object is then passed through and gets modified multiple times during chaining calls and is finally used to extract the information like **endpoint to request**, **auth variables** etc. Using this information a request is made. For example take the following query:

{% gist 86188dc3276066ce0c89 %}

<br/>     
![Request State Transfer](http://i.imgur.com/6Y7vcBy.jpg)


- **WHAT to port and WHAT not to:**                                 
A drilled summary of what functionality is needed to port and what don't is here : <https://github.com/black-perl/mailman-cli/tree/master/readups/mailman.client-python-port-to-js> .

- **Python classes and JavaScript prototypes:**                     
It is required to have object oriented structure for the client library as suggested by the current Python implementation of mailman.client. Since, JavaScript is classless and is a prototype based language, so the constructor functions **[1]** can be used to serve the purpose of classes and the prototype chain for inheritance. **[2]** Also, it is required to write getters and setters for duplicating the effect of Python’s **@property**. **[3]**  Description of how I used these techniques in my **Proof of Concept** is here : <https://github.com/black-perl/mailman-cli/tree/master/readups/js-prototype-setters-getters-python-classes-property>

 

<span class="red"> **[e]: Preparing proper toolkit** </span>

Vanilla JS with a request library and a promise library will be sufficient. As, Node.js will automatically take care of most of the browser differences.                                 

-> These are few popular choices for a request library : 

* Request ( https://github.com/request/request )                 
* Superagent ( http://visionmedia.github.io/superagent/ ) 

<br/>
-> And few good choices for a promise library :

* Bluebird ( <https://github.com/petkaantonov/bluebird> )           
* Promise ( <https://www.npmjs.com/package/promise> )              
* Q ( <https://github.com/kriskowal/q> )       

<br/>                          
**  I have made use of Superagent in my Proof of Concept for this project [4] because it offers call-chaining style syntax like JQuery's Ajax calls and I am pretty used to it. But Request is more popular than Superagent in the Node.js community. So, I would propose to use Request.                                  
<br/>
**  I have made use of **Bluebird** in my Proof of Concept for this project **[5]**.  Some comparisons on promise libraries suggest Bluebird to be the best on the basis of performance of such promise libraries. These are few of them : 

<http://complexitymaze.com/2014/03/03/javascript-promises-a-comparison-of-libraries/#contentleft>

<https://news.ycombinator.com/item?id=6494622>

Another promise library that can be used is es6-promise ( <https://github.com/jakearchibald/es6-promise> ) as pointed out by Florian on the mailing list.

 

<span class="red"> **[f]: Code Quality** </span>

- Writing legible code:
A good idea is to use **JSCS** **[6]** to force **code styling** and **JSHint** **[7]** to lint code for potential errors and problems in the code.

These are good community driven tools helpful in writing legible JavaScript and are being used for projects like JQuery, Grunt, AngularJS etc. **[8]** **[6]**

- Writing less complex code:
Decreasing the time complexity of the code can help in increasing the performance of the library. Well, Plato **[9]** is a great tool to do **code visualization, static analysis and complexity**.

- Running **plato** over the code gives a rough idea of the following:

	* Maintainability of the code

	* Lines of code

	* Estimated errors in implementation ( based on the **Halstead complexity measures** **[10]** )

	* Lint errors ( based on the results of **JsHint** **[7]** )

Once the parts of the code causing issues are identified, they can be improved.

                                                    
- **Testing** is one of the main goals of this project as code quality is directly reflected from the code coverage. Here, it becomes more important as we are writing everything from scratch. The choice of testing tools is equally important.
Some good framework options are : **Mocha** and **Jasmine**          
**Jasmine** **[14]** is a full fledge BDD testing framework while **Mocha** **[11]** is more of a test runner supporting different types of interfaces BDD, TDD etc. Since, Mocha does not come with an assertion library, it is required to use an external assertion library like **Chai.js** **[12]** or **Should.js** **[16]** or any other. Also, **Sinon** **[13]** is a very popular library which provides spies, stubs, and mocks for JavaScript ; and is frequently used with Mocha.                    <br/>                           
The comparisons between **Jasmine** and **Mocha** suggests **Mocha** to be a good choice because it lets you write more cleaner asynchronous code compared to Jasmine. Jasmine on the other hand, requires an external plugin **[19]** to work with Node. **Mocha** also offers much more flexibility of assertion styles and mocking. A few comparisons between Mocha and Jasmine : <br/>                      
<http://www.techtalkdc.com/which-javascript-test-library-should-you-use-qunit-vs-jasmine-vs-mocha/>                                     
<http://thejsguy.com/2015/01/12/jasmine-vs-mocha-chai-and-sinon.html>                                         <br/>                     
** I have integrated a few tests using **Mocha** and **Chai** as an assertion library in my **Proof of Concept** module and found it to fit in very nice ! A report on these : <https://github.com/black-perl/mailman-cli/tree/master/readups/testing-with-mocha>
                                                                                                                                                                
<br/>                                                     
<span class="red"> **[g] Npm and IO.js integration** </span>

Since, the library is aimed to be served as a Node.js and IO.js package. The library must be turned into a **npm** package by creating a **package.json** file and registering the module at npm registry. This Gist contains all the steps related to creation and updating of a npm module :  <https://gist.github.com/coolaj86/1318304>

Also most of the npm packages are compatible with IO.js as suggested by the IO.js website **[15]**  but some of the dependencies may cause compatibility issues. In these cases such tools can prove to be helpful : <https://github.com/mderazon/iojs-dep-check> .

So, the set of libraries we choose while porting mailman.client to JavaScript should play well with both Node.js and IO.js.

**  I have turned the Proof of Concept module into a npm package. **Link** : <https://www.npmjs.com/package/mailman-cli>

                                                                                                                              
<span class="red"> **[h] Documentation** </span>

**Inline commenting** can be used to generate documentation for various **classes and methods** in the library by making use of great tools like **JSDoc** **[17]**. Further, there are quite awesome themes available like this: <http://davidshimjs.github.io/jaguarjs/doc/>

Similarly, **YUIDoc** **[18]** can be used to serve the purpose of generating documentation from inline comments. But it is required to have a separate **usage documentation** for the API users and it is equally important to work on it parallel to working on writing the library.

** I have done a lot of **inline commenting** in my **Proof of Concept** module for this purpose **[0]**

 

## <span class="green"> Pre-GSoC GNU Mailman Project Involvement </span>

I believed that it is important to become a GNU Mailman contributor ahead of the GSoC proposal deadline. This has allowed me to get acquainted with the extensive codebase, inner workings of the project and helped me produce what I believe is a solid project plan in this proposal. The following are some of my contributions till date:

Bug Found #1425359 : 500 error code shown by postorius instead of 409 error code.                                                     
**Link**: <https://bugs.launchpad.net/mailman/+bug/1425359>
**Bug status** : Marked High                                 
**Pull request status** : Merged                                   
**Pull request Link** : <https://code.launchpad.net/~black-perl/mailman/fix-mailman/+merge/251709>                                

 
Bug Found #1428169 : client not present in the scope of the interpreter invoked by doing python manage.py mmclient.             
**Link** : <https://bugs.launchpad.net/postorius/+bug/1428169>      
**Pull request status** : Merged                                    
**Link** : <https://code.launchpad.net/~black-perl/postorius/fix-postorius/+merge/251761>                                           

 
Bug Found #1428543 : Typos in the postorius code which potentially can lead to unwanted code cascading problems.                       
**Link** : <https://bugs.launchpad.net/postorius/+bug/1428543>      
**Pull request link** : <https://code.launchpad.net/~black-perl/postorius/fix-postorius-typo/+merge/251879>                         
**Pull request status** : Merged                                    


Bug Found #1429366 : Anatomy of list ids does not keep with that of urls causes some REST end points to return 404 always.              
**Link** : <https://bugs.launchpad.net/mailman/+bug/1429366>        
**Pull request links** : <https://code.launchpad.net/~black-perl/mailman/handling-special-chars-in-email/+merge/252867>                                  
<https://code.launchpad.net/~black-perl/mailman.client/handling-special-chars-in-email/+merge/252899>                               

 
Developed a bootstrap script for setting Mailman up for development. **Link** : <https://github.com/black-perl/mmdev_bootstrap>
 


## <span class="green"> Proposed Timeline and Milestones </span>

Given that I am already a Mailman contributor, I am up to speed with things and look forward to start this project as soon as acceptance is known. I have already got a good idea on the working of the existing **mailman.client** while working on a pull request for the bug **#1429366** and writing a proof of concept thing has already given me a start on this.

After studying the current implementation of **mailman.client**, it is clear to have the functionality offered by the classes  **_Page**, **_Settings**, **_Preferences** etc. before trying to have any other functionality because they are directly or indirectly used by other core classes such as **_List**, **_Domain**, **_User** etc. So, a good idea would be to find such classes and implement them first.

Keeping this in the mind, the proposed timeline would be like this :

**Note** : The names of the classes used in the milestones follows directly from the implementation section as discussed earlier in the proposal.
 

- **27/4/2015 - 24/5/2015: Community Bonding Period**                

	* Take all of the choices of libraries in account and choose best of them to serve the purpose. Get my hands dirty on these.
	* Read some JavaScript coding guidelines and best practices to follow.
	* Setup development environment with the selected toolkit.  

                                                            
- **25/5/2015 - 7/6/2015: Actual Coding period starts**               

	* Implementing the request making functionality by writing **RequestA** and **RequestB** classes by using some client side filters which can prove to be good assets by discussing with mentor(s).
	* Write tests for these classes.                                                               
                                                           
- **8/6/2015 - 26/6/2015: Mid-Term Evaluation**

	* Implement the core Client class along with classes analogous to **_Page**, **_Settings**, **_Preferences** etc. as discussed above.
	* Implement the **ListsRequest** and **DomainsRequest** classes. ( classes analogous to **_Lists** and **_Domains** respectively )
	* Write test suite parallely.

                                                  
- **3/7/2015 - 16/8/2015**

	* Implement rest of the functionality i.e classes analogous to **_Member**, **_User**, etc.
	* Write remaining test suite.
	* Generate documentation of classes and methods in the library by making use of documentation generation tools as discussed earlier.

                                                            
- **17/8/2015 - 18/8/2015: Preparing for End-Term Evaluation**

	* Do code linting and performance tuning using tools described earlier.
	* Final test suite touch ups to increase code coverage.
	* Detailed documentation for the usage API.
	* Making the library a npm package and publish it to the npm registry.

                                                          
- **After 18/8/2015 : Adding more functionality to the library ( Optional )**

	* There may be some functionality added to **mailman.client** during the GSoC period. So, the goal would be add this functionality in the new library.
	* Further improvements or additions to the documentation.
 


## <span class="green"> Proof of Concept module </span>

I have written a npm package that prototypes some of the expected functionality of the library aimed to be developed through this project. I have taken full care of code quality and expects it to serve as a proof of implementation of my proposed ideas.

**Link :** <https://github.com/black-perl/mailman-cli>


[0]: https://github.com/black-perl/mailman-cli

[1]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function

[2]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain

[3]: http://stackoverflow.com/questions/812961/javascript-getters-and-setters-for-dummies

[4]: https://github.com/black-perl/mailman-cli/blob/master/lib/shared/mailman-request.js#L8

[5]: https://github.com/black-perl/mailman-cli/blob/master/lib/shared/mailman-request.js#L7

[6]: http://jscs.info/

[7]: https://github.com/jshint/jshint

[8]: http://jshint.com/about/

[9]: https://www.npmjs.com/package/plato

[10]: http://en.wikipedia.org/wiki/Halstead_complexity_measures

[11]: http://mochajs.org/

[12]: http://chaijs.com/

[13]: http://sinonjs.org/

[14]: http://jasmine.github.io/

[15]: https://iojs.org/en/index.html

[16]: https://github.com/shouldjs

[17]: https://github.com/jsdoc3/jsdoc

[18]: http://yui.github.io/yuidoc/

 

Well, that's all for this post. See you next time :wink:





