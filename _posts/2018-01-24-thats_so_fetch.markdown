---
layout: post
title:      "That's So Fetch"
date:       2018-01-24 20:28:12 -0500
permalink:  thats_so_fetch
---


React is a front-end JavaScript framework that is revolutionizing the dynamic abilities of the internet. Big rigs like Facebook, Tesla and more use it for their websites. However, React is a front-end framework and therefore does not exchange data to and from a database, unless told to do so through Redux. Thats why we need a backend, which in this case, I used a Rails API. Once your Rails API and React front end are set up, theres this big question looming: whats next, how do we connect the two as to share information back and forth? Here comes the fetch, a JavaScript method used  to retrieve and send data from your back-end API. 

An example of how you would fetch in React is as follows: 

```fetch('http://localhost:3000/api/books')```


Here I am fetching the index of my Book model via the routes I set up through Rails. The index holds the list of all my books, or in better terms, the data of all my books that I want to use for React. In my Rails controllers, I specified that I want the data returned as JSON. This is a convention adopted by developers and looking more into it, there is a consensus that anything other than a JSON object will presumably cause uknown errors and issues. 

So, whats next? 


Think of fetching in three steps, or as someone once explained it to me, think of fetching as simple as playing fetch with a dog. 

You throw the ball(fetch) -> dog runs after it(promise) -> dog returns ball(data). First comes the fetch, then the promise, then the data. 


So now the promise: The simplest explanation of a promise is to compare it to the document.ready() that is used when making a JQUERY or AJAX call. It is exactly what the name says it is, a promise. We are fetching data, and in between asking and actually retrieving the data, we are made a promise. Sometimes the promise will get fulfilled(or in developer terms, resolved), and sometimes it will not. In this case, it would mean there is an error in either the data we are fetching or what we are fetching to begin with. 

When the promise is resolved, we receive a response. What we will do with that response is parse it using the json() method. This is why it is important to set up your back end to render a JSON object instead of HTML.

an example of the parse is:

```.then(res => res.json())``` 



Once the promise has been resolved and parse, we arrive at our data. 

```.then(data => ())```


This is where I failed completely to understand the process and what is being passed on along the process. During my assessment, I was told to console.log the data. Unknowingly, I attempted to console.log the response. What I found out in my console was that, true it held the value of the data, but it was not the data itself. The response is the promise. In order to access the data in your console, you would have to, well... console.log the data. 

```.then( data => console.log(data) )```




A final code for a call to the API would entirely depend on your need. But an example of my call to the API to fetch the data  of all the books in my database, this is what it would look like:

```export function fetchBooks() {
           return (dispatch) => {
           return fetch('http://localhost:3000/api/books')
           .then(res => res.json())
           .then(data => dispatch({ type: 'FETCH_BOOKS', payload: data}))
          }
}```


I hope this helps clear up the confusion for  you as much as it did for me. And after reading this, you can look at the code above and understand exactly what is happening. 
