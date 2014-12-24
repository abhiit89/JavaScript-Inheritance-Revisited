**JavaScript Inheritance Revisited**

In one of my previous blog, I had talked about inheritance in JavaScript. The main idea of that blog was to share that how inheritance can be achieved using prototype chaining.
But the demo given in the previous blog had some serious flaws or if not a flaw then you can say a bad advice or maybe not a good way to implement inheritance.

Demo in the previous blog looked like:

```JavaScript
function LivingThing() {
    this.move = function() {
        console.log("I am living thing! I can move!!");
    };
}
function Bird() {
    this.fly = function() {
        console.log("I am bird! I can fly!!");
    };
}
function Peacock() {
    this.dance = function() {
      console.log("I am Peacock! I can dance");
    };
}
Bird.prototype = new LivingThing();
Bird.prototype.constructor = Bird;
Peacock.prototype = new Bird();
Peacock.prototype.constructor = Peacock;
var peacock = new Peacock("A");
peacock.dance(); // I am Peacock! I can dance
peacock.fly(); // I am bird! I can fly!!
peacock.move(); // I am living thing! I can move!!
```

So let's discuss the problem with the above piece of code:

1. We were creating a function inside the constructor. So every time a new object would be created, it would get its own copy of that function. So suppose of there are 10 objects in the chain, then 10 functions would also be there. We know that functions are data, therefore if one function takes 10 bytes of data then 10 would obviously take 100 bytes which is quite an inefficient way of doing things. Let's understand more with the help of this diagram:

![Figure 1 - Inheritance Revisited.jpg](https://raw.githubusercontent.com/NamitaMalik/JavaScript-Inheritance-Revisited/master/Figure%201%20-%20Inheritance%20Revisited.jpg)

Above diagram shows how **inheritance** is happening through **prototype chaining** in and in addition to it, it also shows that how function declared in super class is available in the further sub classes also. But only problem is number of new functions getting created whenever a new object is created.

So what is the better approach? How to get move() and fly() functions in Peacock class without making their copies each time a new object is created?

**The Solution**

Let's keep the functions move() and fly() at place which is common, so that they can be accessed by any **object**. To do this let's make this function **static** and to make a function **static** in **JavaScript**, all we have to do is put that **function** in the **prototype**.

So, instead of creating **function** move() inside the constructor of LivingThing, create it inside the **prototype** of LivingThing.

Now, create an **object** of **prototype** and pass it in the **prototype** of next object. This can be seen in the snippet below:

```JavaScript
function LivingThing() {
}
LivingThing.prototype.move = function () {
    console.log("I am living thing! I can move!!");
};
function Bird() {
}
Bird.prototype = Object.create(LivingThing.prototype);
Bird.prototype.constructor = Bird;
Bird.prototype.fly = function () {
    console.log("I am bird! I can fly!!");
};
function Peacock() {
}
Peacock.prototype = Object.create(Bird.prototype);
Peacock.prototype.constructor = Peacock;
Peacock.prototype.dance = function () {
    console.log("I am Peacock! I can dance");
};
var peacock = new Peacock();
peacock.dance(); // I am Peacock! I can dance
peacock.fly(); // I am bird! I can fly!!
peacock.move(); // I am living thing! I can move!!
```

In the above snippet, we have added **function** move() to the **prototype** of LivingThing **object**. In the **prototype** of Bird, we have passed the copy **object** of **prototype** of LivingThing **object**.
Similarly, in the **prototype** of Peacock **object**, we have passed the copy **object** of **prototype** of Bird.

**Object.create** was basically takes following two arguments(second one being the optional):

1. Prototype
2. Set of properties

On the basis of **prototype** and set of properties passed **Object.create** creates a new object.

Let's us see a diagrammatic representation of the snippet given above:

