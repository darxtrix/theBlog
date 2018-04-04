---
title: GSOC @ GNU MAILMAN WEEK-1
tags: gsoc gsoc15 mailman open-source
---

Writing things from scratch is hard, isn't it. You have to think a lot whether to setup all the things first and then actually start doing coding. But I preferred the latter on the former. If you are unaware of what I am working on, you can checkout [here](http://black-perl.in/gsoc15-proposal-mailman.client-written-in-js/).
<br/> 
So, I started by writing the **BaseRequest** class, the *class* keyword sounds weird to me when I am dabbling with JS but I can't help using it as *class* is the best keyword to describe heirarchy in your code. 
<br/>
**BaseRequest** is the *base* request making module. It talks directly to the REST API exposed by the Mailman core i.e it sits at the bottom of the abstraction layer that [mailman-client.js](https://gitlab.com/black-perl/mailman-client.js) adds while communicating to core.
<br/>
<br/>

### 1. Support for HTTP methods
The **BaseRequest** module should provide direct support for general HTTP methods i.e **GET**, **POST**, **PUT** etc.
<br/>
i.e. we want something like this:

<pre>
    <code class='javascript'>
    >> client.get()
    >> client.post()
    >> client.put()
    .
    .
    .
    </code>
</pre>

So, it can be achieved by adding these HTTP methods as properties to the prototype object of *BaseRequest* constructor so that it's instances can directly avail them.
<br/>
The implementation :

<pre>
    <code class='javascript'>
    /*
    HTTP methods
    ============
    */

    BaseRequest.prototype.get = function(callback) {
        // internal implementation

    }


    BaseRequest.prototype.post = function(data,callback) {
        // internal implementation
    }


    BaseRequest.prototype.put = function(data,callback) {
        // internal implementation
    }


    BaseRequest.prototype.delete = function(callback) {
        // internal implementation
    }


    BaseRequest.prototype.head = function(callback) {
        // internal implementation
    }
    </code>   
</pre>


### 2. Support for both Node.js style *Callbacks* and the beautiful *Promises*
The above defined HTTP methods `.get()`,`.post()` etc. should be used by passing in a *Callback* function or treating their output to be a **Promise** object.
<br/>
i.e something like this:

<pre>
    <code class='javascript'>
    // => Node.js style callbacks
    >> client.get(function(err,data) {
        // handle error
        if (err) {
        console.log(err);
        }
        // handle result
        else {
        console.log(data);
        }
    });
    // => Promises
    >>  client.get().then(function(data) {
        console.log(data);
    });
    </code>
</pre>
<br/>
This can be achived by using a promise library. I find [BlueBird](https://github.com/petkaantonov/bluebird) to be quite handy. Now we will see the implementation of the **get** function that let us use it both ways. 

<pre>
    <code class='javascript'>
    // => the magical function that turns these request making functions into  
    //    powerful promises
    function invokeandPromisify(request, callback, transform) {
        callback = ensureCallback( callback );
        console.log(callback);
        transform = transform || identity;
        console.log(transform);
        return new Promise(function(resolve,reject) {
        // Invoke the request
        request.end(function(err,result) {

            // Return the results as a promise
            if (err || result.error) {
                console.log(err);
                reject(err || result.error);
            } else {
                console.log(result);
                resolve(result);
            }
        });
        }).then(transform).nodeify(callback);
    
    }

    //=> inner implementation of the request making functions
    BaseRequest.prototype.get = function(callback) {
        // check if this HTTP method is supported or not
        this._checkMethodSupport('get');
        var url = this._renderURI();
        var request = this._auth(agent.get(url));
        
        // do the magic
        return invokeandPromisify(request,callback,getBody);

    }

    </code>
</pre>

**Note** : `getBody` is just a nice little transform function that returns the **body** of the response after a call to the API. It's implementation:
<pre>
    <code class="javascript">
        // => returns the headers of the result from the agent call
        function getHeaders(result) {
            return result.headers;
        }
    </code>
</pre>


So, these were the desired expectations from the module. Now, some minor details :smiley:

### Structure of the BaseRequest constructor function:
<pre>
    <code class="javascript">
        /**
         * BaseRequest is the base API request object constructor
         *
         * @class BaseRequest
         * @constructor
         * @param {Object} options - A hash of options for the BaseRequest instance
         * @param {String} options.endpoint - The endpoint URI to request
         * @param {String} [options.username] - A username for authenticating API requests
         * @param {String} [options.password] - A password for authenticating API requests
         */
         function BaseRequest(options) {
            this._options = options || {};
            // hash of key,val pairs to get the URL to request by expanding the this._template
            this._path = {};
            // the template that the different URL endpoints for a given class follows
            this._template = '';
            // the BaseRequest should support all the possible methods
            this._supportedMethods = [ 'head', 'get', 'put', 'post', 'delete' ];

         }
    </code>
</pre>


You can checkout the full implementation [here](https://gitlab.com/black-perl/mailman-client.js/blob/master/lib/shared/BaseRequest.js). If you have any queries, comment in. Stay in touch. :wink:





















