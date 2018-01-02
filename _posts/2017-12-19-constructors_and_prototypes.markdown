---
layout: post
title:      "Hoist Away"
date:       2017-12-19 11:00:13 -0500
permalink:  constructors_and_prototypes
---


Today we are going to go over hoisting and its important role in Javascript as well as the mechanics of it. We will cover what hoisting is, how it is executed and how it can create problems if a clear understanding is not developed as a programmer.

To begin with: What exactly is hoisting? 

*  When a computer engine steps in to compile all JS code, there are two phases that occur: creation and execution.  It is important to understand that before any engine can interpret code, it will compile the code. Hoisting is the oil that runs the compilation process by declaring and storing variables in memory. After the variable has been stored, the engine runs through again and assigns any values that have been given to the variables.
*  When code is being hoisted, all declarations and functions are "moved" to the top of the current scope. The scope can vary from global to local scopes. While the term "moving" is used a lot when referring to hoisting, it is vital to understand that no code is physically being moved or altered. It is a behind-the-scenes action that is required to interpret and store  variables in memory. 
*** IMPORTANT:**  While JS allows for a 2-in-1 process of initializing variables, hoisting does not interpret it that way.  It is important to understand that hoisting ONLY hoists declarations and not initializations. ***What does this mean?*** TO understand exactly what this means we will take a look at the difference between initialization vs declaration. 

Initialization Example: 
 
 Var hoisting = 'Will this hoist?' 
 
*  During the compile phase, what hoisting will do to this code is separate it so that it is interpreted via the engine as the example below:
 
Declaration Example:
 
 Var hoisting;
 
 hoisting = "Will this hoist?' 
 
 
So whats the big deal?

To understand hoisting would mean becoming better at debugging any issues that a developer might come across involving undefined variables. Because declarations are interpreted before any code executions, this gives a developer an idea of what to look for if they come across undefined variables. The first question one could ask themselves is: "is the variable declared and initialized before a code is executed? An example below shows how this could be problematic:

Lets say we have a function that contains variables within its scope:

function hoistAway() {

   console.log(hoist)

   var hoist = 'will this hoist?'

};

Some might look at this and say, well  JS will do what it needs to do in the background, so of course the value of 'hoist' will be displayed in the console. However if you're thinking this (much like I did before understanding hoisting) then you are just a little off. Here what is happening is we are logging the value of an undefined variable --> then initializing the variable. When JS interprets this code, what it will see is:

function hoistAway() {

    var hoist;

   console.log(hoist)

    hoist = 'will this hoist?'

};


The problem here is that we are telling the program to execute the console.log(hoist) with an undefined variable. Or better yet, we are declaring and defining the variable AFTER we tell it to perform some code. When the code is being compiled, hoisting does its magic and "moves" the declaration at the top of the scope, before the execution code, creating in human terms, an empty variable. It isn't until later in code that we give the variable a definition. While JS has a lot of magic within it, it still lacks predictablity and creating code this way, would be asking of it to predict that 'hoist' is in fact defined. 

The right way??

The convention would be to always declare and initialize any variables before any code execution. Referring to our example above, the accurate way to create and execute this function with the appropriate values would be:

function hoistAway() {

 var hoist = 'will this hoist?'
​
   console.log(hoist)
​
​
};


This will then be interpreted as:

function hoistAway() {
​
 var hoist;
 
 hoist =  'will this hoist?'

 console.log(hoist)

};

our hoist variable is declared and defined before our execution code. 
​
 
