---
title : Demystifying JavaScript Prototypes - I
tags: javascript javascript-prototypes
---

Every JavaScript developer should know about *Prototypes*{::} and must use it as a part of their JS arsenal. The concept of *Prototypes* is one of the core concepts of JavaScript.
<br/>
<br/>
This article aims to explain JavaScript’s prototype in layman terms. If you don’t understand JavaScript’s prototype after reading this blog post, please ask questions in the comments below. I will personally answer all questions.

###Why prototypes ?

And, the answer is to achieve **inheritance**. So, JavaScript is a *prototyped* language in order to facilitate **inheritance**. Inheritance facilitates **code-reusability**.
Programming languages uses one of the following techniques for achieving inheritance:       

- Classical                                                   
- Prototypal   

<br/>
<span class="red"> **Classical inheritance :** </span>                                                                                    
In **classical inheritance**, the programmer writes a class, which is used to **instantiate** an object. Multiple objects can be instantiated from the same class, so you have code in one place which describes several objects in your program.                      Classes can then be organized into a **hierarchy**, furthering **code reuse**. More general code is stored in a higher-level class, from which lower level classes inherit. This means that an object is sharing code with other objects of the same class, as well as with its parent classes.

<span class="red"> **Prototypal inheritance :** </span>                                                                                   
In the **prototypal inheritance** form, objects **inherit directly** from other objects. There are **no** such **classes**. If you want an object, you just write an object. But code reuse is still a valuable thing, so objects are allowed to be linked together in a hierarchy.In javascript, every object has a secret link to the object which created it, forming a **chain** and this chain is termed as *object prototype chain*. 
<br/>
This is how a property lookup is achieved along the chain:

> When an object is asked for a **property** that it does not have, 
> its parent object is asked... continually **up the chain** until 
> the property is found or until the **root** object is reached.

A bit confused :confused:. Don't worry just get yourself confortable; we will see this by an example in a later section.

### `prototype`&nbsp;&nbsp; v/s &nbsp; `__proto__` 

Each JavaScript object will always have these two properties i.e `prototype` and `__proto__`. Keep in mind that these are also **objects** i.e `<object>.prototype` is also an object.
<br/>
Here, `<object>` refers to the object whose property we are trying to access using the dot operator.
<br/>
<br/>

<span class="green" style="font-size:200%;font-weight:bold"> &lt;object&gt;.prototype </span>
<br/>                                
Let us start by understanding about **prototype** first. This makes most sense for a **constructor** function using which we are going to create objects with the application of **new** operator.

> **prototype** property of a **constructor function** which is an 
> object itself, keeps the information about the properties which 
> are going to be inherited by the objects instantiated from this 
> constructor function. The default value of prototype is **empty**. 

Consider the example:
<br/>

{% highlight javascript %}

// Defining a constructor class
>> function Vehicle(name){
... this.name = name;
... };

// Default value of prototype is empty
>> Vehicle.prototype
{}

// Adding a method
>> Vehicle.prototype.run = function(){
... console.log('I am running');
... };

>> Vehicle.prototype
{ run: [Function] }

// Adding another method
>> Vehicle.prototype.fuel = function(){
... console.log('I need fuel');
... }

>> Vehicle.prototype
{ run: [Function], fuel: [Function] }

// The Vehicle object does not have these methods but the object
// inherting from it will have
>> Vehicle.fuel()
TypeError: Object function Vehicle(name){
this.name = name;
} has no method 'fuel'

{% endhighlight %}

So, the **prototype** of an object bundles up the properties which are **to be installed** into the **child** objects.
<br/>
<br/>

<span class="green" style="font-size:200%;font-weight:bold"> &lt;object&gt;.&#95;&#95;proto&#95;&#95; </span>
<br/>                              
The **__proto__** of an object refers to the **prototype** of the **parent** constructor function.

> **prototype** property of a **constructor function** is 
> installed into the **__proto__** property of the instantiated object.

{% highlight javascript %} 

// Instantiating an object
>> var myCar = new Vehicle('car');

// The __proto__ of myCar
>> myCar.__proto__
{ run: [Function], fuel: [Function] }

// Yep, they are equal
>> myCar.__proto__ == Vehicle.prototype
true

{% endhighlight %}

And, the **__proto__** tells the properties which are **installed** into the created object.
<br/>
<br/>

### Summing Up 
So, we put all the properties which we want the instantiated objects to inherit into the **prototype** property of the **constructor** function and this information is reflected in the **__proto__** of the created objects. This is the **link** between **child** and **parent** objects in JavaScript.


This post covered the basics of how inheritance works in JavaScript.I will be continuing my next blog post on more advanced stuff covering *prototype chain* and its *propagation*. 

Stay in touch :wink: and feel free to ask questions !



 







