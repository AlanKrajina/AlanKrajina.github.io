---
layout: post
title:      "VisualCV Final Project"
date:       2019-11-30 10:41:54 -0500
permalink:  visualcv_final_project
---


My final Flatiron project. What can I say except that I am very proud for being in this position. If somebody asked me one year ago what coding is and how it works I would start laughing. For that kind of question I would never have an answer, that was for smart kids, I play video games not code. But after random sequences of events while I was working for Apple in Ireland I started playing around with code. To cut the story (more on my path to greatness on another blog :D) one year later I am developing single page application using Rails backend and React/Redux frontend. And I did it all by myself. For this reason I will explain how all requirements are meet in this project. This project is just one in many to come, there is still a lot to learn and since looking at the screen for 5+ hours a day is fun for me, I look forward to my future in software development. And to finish this intro with my all-time favorite quote: 
> 
“Things are only impossible until they are not.” ― Jean-Luc Picard
> 

Lets check how the requirements for this projects are meet:

**	The code should be written in ES6 as much as possible
**

As I am learning to code, getting to know new ways of doing something can become a challenge. I started coding one year ago and at the beginning was using pre ES6 syntax and features. For a newbie that was good enough since you just want stuff to work, no matter how the code looks and performs.

During my study in Flatiron I got introduced to React and Redux, and this is where it all changed. Since React is a new framework all developers use ES6 syntax from scratch. For example:

*Use of Classes*

A class is a type of function, but instead of using the keyword function to initiate it, we use the keyword class, and the properties are assigned inside a constructor() method. A newer way does't even require usage of constructor method.

```
class SearchableWeathersContainer extends Component {
  state = {
      weathers: [],
      searchTerm: ""
  }; 
```
*Use of Arrow Functions
*

Arrow functions allow us to write shorter function syntax:

```
const mapStateToProps = state => {
  return {
    comments: state.myComments
  }
}
```

and many more great features.

Currently I basically only use ES6, it offers so many advancements from pre ES6 that I would recommend it to everybody, but be wary of certain downsides. 
Great article to check that out:

[https://medium.com/tfogo/advantages-and-pitfalls-of-arrow-functions-a16f0835799e
](http://)


**Use the create-react-app generator to start your project.
**

Official React team primarily recommends these solutions:
1. If you’re learning React or creating a new single-page app, use Create React App.
2. If you’re building a server-rendered website with Node.js, try Next.js.
3. If you’re building a static content-oriented website, try Gatsby.
4. If you’re building a component library or integrating with an existing codebase, try More Flexible Toolchains.

Create React App is a comfortable environment for learning React, and is the best way to start building a new single-page application in React.

I used it to create my React app and will continue using it unless requirements are demanding different approach like stated above.

**Your app should have one HTML page to render your react-redux application
**

 index.js file corresponds to index.html. This file has the following line of code which is very significant.

`ReactDOM.render(<Provider store={ store }><BrowserRouter><App /></BrowserRouter></Provider>, document.getElementById('root'));`

The above line of code is telling that App Component has to be loaded into an html element with id root. This is nothing but the div element present in index.html.

App.js : This is the file for App Component. App Component is the main component in React which acts as a container for all other components.


**There should be 2 container components
**

**There should be 5 stateless components
**

Words from Dan Abramov Co-author of Redux and Create React App:

[https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0](http://)

“You’ll find your components much easier to reuse and reason about if you divide them into two categories. I call them Container and Presentational components* but I also heard Fat and Skinny, Smart and Dumb, Stateful and Pure, Screens and Components, etc. These all are not exactly the same, but the core idea is similar.”


But since 2019 he updated this post with these words:

“I don’t suggest splitting your components like this anymore. If you find it natural in your codebase, this pattern can be handy. But I’ve seen it enforced without any necessity and with almost dogmatic fervor far too many times.”

My app has 13 presentational and 6 container components using class declarations. I could completely overhaul this and have less presentation components that would just be concerned with how things look rarely having their own state and would receive data and callbacks exclusively via props. 

While writing this blog I am ok as the way they work now but since I want everything to be perfect as possible I will surely rearrange these components to make more sense in the future.


**There should be 3 routes
**

App uses *BrowserRouter* that is used with Rails server that will handle dynamic requests (knows how to respond to any possible URI).

Router components only expect to receive a single child element. To work within this limitation, I used *App* component that renders the rest of application.

App.js includes 7 routes. Couple of examples:

```
<Route exact path='/login' component={Login}/> 
<Route exact path='/logout' component={Logout}/>
<Route exact path='/projects' component={MyProjects}/>
```


The *Route* component is the main building block of React Router. Anywhere that we want to only render content based on the location’s pathname, we should use a *Route* element.

*Route* expects a path prop, which is a string that describes the pathname that the route matches — for example, *Route exact path='/ login' *  should match a pathname that begins with / login'. When the current location’s pathname is matched by the path, the route will render a React element.

**Use Redux middleware to respond to and modify state change
**

App uses Thunk middleware.

With a plain basic Redux store, you can only do simple synchronous updates by dispatching an action. Middleware extend the store's abilities, and let you write async logic that interacts with the store.

Thunks are the recommended middleware for basic Redux side effects logic, including complex synchronous logic that needs access to the store, and simple async logic like AJAX requests.

Thunk allows us to return a function inside of our action creator instead of a plain JavaScript object. That returned function receives the store's dispatch function, and with that we are able to dispatch multiple actions: one to place the state in a loading state, and another to update our store with the returned data.

Without any middleware, your action creator might look like



```
// action creator
function loadData(dispatch, userId) { // needs to dispatch, so it is first argument
  return fetch(`http://data.com/${userId}`)
    .then(res => res.json())
    .then(
      data => dispatch({ type: 'LOAD_DATA_SUCCESS', data }),
      err => dispatch({ type: 'LOAD_DATA_FAILURE', err })
    );
}



// component
componentWillMount() {
  loadData(this.props.dispatch, this.props.userId); // don't forget to pass dispatch
}
```

But with Thunk Middleware you can write it like this:

```
// action creator
function loadData(userId) {
  return dispatch => fetch(`http://data.com/${userId}`) // Redux Thunk handles these
    .then(res => res.json())
    .then(
      data => dispatch({ type: 'LOAD_DATA_SUCCESS', data }),
      err => dispatch({ type: 'LOAD_DATA_FAILURE', err })
    );
}

// component
componentWillMount() {
  this.props.dispatch(loadData(this.props.userId)); // dispatch like you usually do
}
```

**
Make use of async actions to send data to and receive data from a server
Your Rails API should handle the data persistence. You should be using fetch() within your actions to GET and POST data from your API - do not use jQuery methods.**

There are two GET and POST requests in my App. 

First is the currentUser.js action that posts user credentials like Username and Password, and then gets seeded data from Rails. Seeded data is everything that a User sees on the page.

Example for POST login:


```
export const login = (credentials, history) => {     
/*{username: "Alan", password: "password"} */
                                                     // returns FUNCTION that returns FETCH with dispatch from redux
    return dispatch => {                    
        return fetch("http://localhost:3001/api/v1/login", {       // login Routes.rb rails + sessions controller (def create) 
                                                                   //       post "/api/v1/login", to: "api/v1/sessions#create"
          credentials: "include", //added from rails
          method: "POST",         // sending data
          headers: {
            "Content-Type": "application/json"
          },
          body: JSON.stringify(credentials) 
        })                                  
        .then(r => r.json())
        .then(response => {
          if (response.error) { //error from sessions controller
            alert(response.error)
          } else {
            dispatch(setCurrentUser(response.data)) // dispatch function
            // setCurrentUser -> ACTION creator
            dispatch(getMyComments())   
            dispatch(resetLoginForm())               // clears on login (redux loginForm)
            history.push('/')                        // receiving history object to return to initial url
          }
        })
        .catch(console.log)
    }
  }
```


Second action is MyComments.js that as well GETs and POSTs data from Rails. User has the ability to comment on a Blog, comment gets saved to database and returned to the User while updating Redux store.

Example for GET request:

```
export const getMyComments = () => {
    return dispatch => {
      return fetch("http://localhost:3001/api/v1/comments", {
        credentials: "include",
        method: "GET",
        headers: {
          "Content-Type": "application/json"
        },
      })
        .then(r => r.json())
        .then(response => {   // response = {data: Array(2)}
          if (response.error) {
            alert(response.error)
          } else {
            dispatch(setMyComments(response.data)) // http://localhost:3001/api/v1/comments 
          }
        })
        .catch(console.log)
    }
  }
```

With the GET and POST request I implemented DELETE request that deletes comment from the database and updates in the Redux store.

```
  export const deleteComment = (commentId, history) => {
    return dispatch => {
      return fetch(`http://localhost:3001/api/v1/comments/${commentId}`, {
        credentials: "include",
        method: "DELETE",
        headers: {
          "Content-Type": "application/json"
        }
      })
        .then(r => r.json())
        .then(resp => {
          if (resp.error) {
            alert(resp.error)
          } else {
            dispatch(deleteCommentSuccess(commentId))
            history.push(`/comments`)
          }
        })
        .catch(console.log)
    }
```


Extra feature that I implemented for this project that was not in the requirements was usage of an external API.
![](https://openweathermap.org/current
http://)

User is able to type in the name of his city and get the current weather forecast. 

Feature requires use of API ID that fetches to 

```
"https://api.openweathermap.org/data/2.5/weather?q="
```

Received value is a json object that looks like this:
```

{
•	weather: [
	   {
id: 300,
main: "Drizzle",
description: "light intensity drizzle",
icon: "09d"
}
],
•	base: "stations",
•	main: 
{
temp: 280.32,
pressure: 1012,
humidity: 81,
temp_min: 279.15,
temp_max: 281.15
},
•	visibility: 10000,
•	wind: 
{
speed: 4.1,
deg: 80
},
•	clouds: 
{
all: 90
},
•	id: 2643743,
•	name: "London",
•	cod: 200
}
```

Where my presentational component returns weather results.


	return (

	<div id="weatherStyle">
			<p>City: { weathers.name}</p>
			<p>Temperature: {Math.round(weathers.main.temp -273.15)}°C</p>
			<p>Humidity: {weathers.main.humidity}%</p>
			<p>Wind: {weathers.wind.speed}km/h</p>
			<p>Forecast: {weathers.weather.map(w=>w.main)}</p>
	</div>
	)}
	
**Your application should have some minimal styling: feel free to stick to a framework (like react-bootstrap), but if you want to write (additional) CSS yourself, go for it!**

Since my App is my own personal website and an online CV I decided to use CSS for modals, background video and images, react-bootstrap for navbar, links, input fields, buttons and fonts. 

For the ending of this blog just one reminder, don’t forget to hover over “Hover to engage” with your mouse at the bottom. :D









