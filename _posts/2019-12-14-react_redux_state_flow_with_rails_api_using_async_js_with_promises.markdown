---
layout: post
title:      "React/Redux state flow with Rails API using async JS with Promises"
date:       2019-12-14 13:34:30 +0000
permalink:  react_redux_state_flow_with_rails_api_using_async_js_with_promises
---


One of the main requirements of the last Flatiron project is the understanding of React/Redux flow. Essentially student needs to fully comprehend the difference between using only React and React with Redux + Thunk Middleware. 

So what is Redux?

Redux is a popular JavaScript library for managing the state of your application.

It is like a global object which holds information that you use for various purposes later in the app.  It encourages storing all of the necessary data in our application in a JavaScript object separate from our components.

Redux places all of our data in one place -- the state. This state is just a plain JavaScript object. In fact, all the pieces of Redux are plain old JavaScript. It is the pattern, the way the information flows that makes Redux awesome.


To change our application state, we need create an action that holds information for how to update that state. The action, combined with the previous state, produces an updated state.


So basically an Action is being sent to the Reducer, Reducer then updates the State and after that happens we can take the information from the State and use it to show the data we got, pass it to other components if we want, manipulate and much more.

Redux removes the complexity of tracking all props being sent from one component to another by creating that one store where we just pull our data from.

To explain this I will use an example where we fetch and GET seeded data from Rails backend, use actions and reducer to create and change the state and show that data to the user.

[Redux flow](https://ibb.co/gDVdbxS)

In our Rails backend we have a path that we will use to fetch data. That path is where all our seeded data is.

Rails routes.rb:


```
  get "/api/v1/get_current_user", to: "api/v1/sessions#get_current_user"
```

Rails controller returns data in json format:

```
    def get_current_user
        render json: UserSerializer.new(current_user)
    end
```

Json data:

```
{
data: {
	id: "1",
	type: "user",
	attributes: {
		username: "Alan",
		projects: [{...},{...},{...},{...}]
		abouts: [{...}]
		experiences: [{...},{...}]
		educations: [{...},{...}]
		contacts: [{...}]
		blogs: [{...},{...},{...},{...}]
				}
		}
}

```

So this is basically what we will get as an object and then show to our user when he opens our app.

Let’s go back to the React/Redux.

As the Redux flow picture  showed we have actions and reducers. The way I always start is by creating an action first. We will have two of them for better understanding.

Synchronous action creator.  

```
export const setCurrentUser = user => {  
    return {
      type: "SET_CURRENT_USER",
      user                  
    }
  }
```

Asynchronous action creator.

```
export const getCurrentUser = () => {
    return dispatch => {
      return fetch("http://localhost:3001/api/v1/get_current_user", {
        credentials: "include",
        method: "GET",
        headers: {
          "Content-Type": "application/json"
        },
      })
        .then(r => r.json())
        .then(response => {
            dispatch(setCurrentUser(response.data))
        })
        .catch(console.log)
    }
  }
```

Synchronous action creator will return an object while asynchronous will return a dispatch function.

Asynchronous function fetches data from our Rails backend that we mentioned earlier. 

```
fetch(http://localhost:3001/api/v1/get_current_user,..
```

It uses the GET method which means we are only taking data from our backend and nothing else.

After we use then() method that returns a Promise. It takes up to two arguments: callback functions for the success and failure cases of the Promise.

```
        .then(r => r.json())
        .then(response => {
            dispatch(setCurrentUser(response.data))
        })
        .catch(console.log)
```

What we get in the promise as our returned value is the data fetched, so our current_user with all seeded data.

Then we take that data (response) and dispatch it to the reducer using synchronous action creator with the response data.

```
     dispatch(setCurrentUser(response.data))
```

Basically what our synchronous action creator will do it will just create action.type and action.user.

```
    return {
      type: "SET_CURRENT_USER",
      user                  
```

And now we have our first part finished. We got all data we need from our backend using fetch, and we created our action.type and action.user to send to the reducer.

Then we create reducer:

```
export default (state = null, action) => {
    switch (action.type) {
      case "SET_CURRENT_USER":
        return action.user
     default:
        return state
    }
  }
```

Pretty simple. When we dispatched our action we sent an “action” and our reducer function took it as argument. It checks if the switch case statement is true and if it is it returns action.user.

And what is action.user again? Seeded data that we got from our backend. 

So just by doing this we created a REDUX state that would look like this in the redux console:

[Redux state](https://ibb.co/q9H7sWP)

But we didn't really showed what user does to get this data. 

We have to use a function in one of our components that will call our dispatched function and return this new state.

For this example I used the lifecycle method called componentDidMount().

So when user opens the page for the first time or refreshes it, he will get all his data.

```
  componentDidMount() {
    this.props.getCurrentUser()
  }
```

There are a many more things that can be added to this blog like explaining how Thunk Middleware works, mapDispatchToProps(), importing the action in this component when it mounts, the way the prop is being sent to the component. 

I wanted to show the core feature on how action and reducer work and how they are connected using Redux. Hope this blog helped a bit and if you need any more info be sure to contact me and I can explain in more details exactly the flow of the whole app. From creating our initial store using createStore() all the way to maping our data and rendering on the page using a functional component.




