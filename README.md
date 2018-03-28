# React Blog

- Save and Retrive the Posts
- Navigate Users (React Router)
- Add Different Page
- Load Data from backend API based on user current Route
- Validation Form to POST data to a remote server

# How to Build this APP

### Redux Blog Post API Reference
[Post API](https://reduxblog.herokuapp.com/)

- POST Request API
```
POST Method
https://reduxblog.herokuapp.com/api/posts
```
```json
{
    "title": "Hi!",
    "categories": "Kevin",
    "content": "Kevin"
}
```
- GET Request API
```
GET Method
https://reduxblog.herokuapp.com/api/posts
```
```json
[
  {
      "id": 216869,
      "title": "Hi!",
      "categories": "Kevin",
      "content": "Kevin"
  },
  {
      "id": 216868,
      "title": "Hi!",
      "categories": "Kevin",
      "content": "Kevin"
  },
]
```
***

## React Router (Single Page Application)
No longer navigating between distinct HTML documents that are being created by some remote web server. Instead, we always deal with "single HTML document" and rely on the JavaScript code to chagne the set of components that a user sees apprearing on the screen. We're tracking the user and showing them different sets of components.

- Manage the URL and our appliaction.
```
npm install --save react-router-dom@4.0.0
```
![history](./image/demo.png)
- User clicks on some links to change the URL inside their browser.
- Browser said to the History Library and History wil run behind the server for us. To do some oarsing over it and figures out exactly what changed about the URL and pass it to React Router Library.
- React Router receive the new Route and updates the react component shown on the screen.
- React will rerender all components based on the new component.

#### BrowserRouter
Object is what interacts with the history library and decides exactly what to do based on the change of URL

#### Route
React Component that we can render inside of any other React Component inside our app (configuraion)

#### Render into ReactDOM
- Create Two Components to show how to use React Router
```js
class Hello extends React.Component{
  render(){return <div>Hello!</div>}
}

class GoodBye extends React.Component{
  render(){return <div>GoodBye!</div>}
}
```

```js
ReactDOM.render(
  <Provider store={createStoreWithMiddleware(reducers)}>
    <BrowserRouter>
      <div>
        <Route path = "/hello" component = { Hello } />
        <Route path = "/goodbye" component = { GoodBye}/>
      </div>
    </BrowserRouter>
  </Provider>
  , document.querySelector('.container'));
  ```

#### Root Route of App 
- What will be showed for users
```js
<Route path = "/" component = { PostIndex}>
```

#### Route for Patricular Posts of App
- Need to set up route configuration: wildcard -> colin id
```js
<Route path = "/posts/:id" component = { PostsShow }>
```

#### Route for Create Brand New Post
```js
<Route path = "/posts/new" component = { PostsNew }>
```

#### Delete App component
- Now we had a React Router, no longer need this app component anymore.


# State as an Object
![Show Pages](./image/demo1.png)
- ActivePost is really a "duplicate piecce of state". Since the current route that a user is looking at is really another piece of state inside our application. Also, the ID that is reflected inside of that route or this particular route provides the exact same information as is being provided by ActivePost.
- Since we've already used :id to reflect the current state, we just need a state to handle the posts.
- When we had a URL, go back to see on the posts with that ID out of the list and show on the screen for users.
- Save as an Object -> key: id of Post / value: post itself : make it easy to find the post we fetched
```
Easy to look up:
state.posts[postId]
```

![Show Pages](./image/demo2.png)

# Index Action
## Action Creator
- To fetch a list of posts and serve them up to our post index component

### Axios & Redux Promise
- Making Network Requests inside of an action creator.
- Handle asynchronous nature of the request itself.
- allpy Middleware "promise" intp index file
```js
const createStoreWithMiddleware = applyMiddleware(promise)(createStore);
```
### Build up a Request URL
- Given a ROOT_URL
- Given a Unique API_KEY
- Get a Request by axios

```js
import axios from 'axios';
export FETCH_POSTS = 'fetch_posts';

const ROOT_URL = 'https://reduxblog.herokuapp.com/api';
const API_KEY = '?key=weichien';

export function fetchPosts() {
  const request = axios.get(`${ROOT_URL}/posts${API_KEY}`);

  return {
    type: FETCH_POSTS,
    payload: request 
  }
}
```

# Post Reducer
- Produce the post piece of state to reutrn an Object
```js
const rootReducer = combineReducers({
    posts: PostsReducer
});
```
- Default the state as an Object
- Need a loadash libraries to take the array and create an Object

### _.mapKeys
- First Argument : an Array
- Second Argument : Property that we want to poll off of each object in here to use as the key on the resulting Objects.
```js
const posts = [
  { id: 4, title: "Hi"},
  { id: 5, title: "Hi There!"},
  { id: 33, title: "Hi Guy!"}
 ]
```
```js
_.mapKeys(posts,'id')
```
- Output will be likes
```js
{"4":{"id":4,"title":"Hi"},"5":{"id":5,"title":"Hi There!"},"33":{"id":33,"title":"Hi Guy!"}}
```
- Simple ID Lookup
```js
const state = _.mapKeys(posts,'id')
state["4"]
```
### Posts Reducer
```js
import { FETCH_POSTS} from '../actions';
import _ from 'lodash';

export default function(state = {}, action) {
  switch(action.type) {
    case FETCH_POSTS:
      return _.mapKeys(action.payload.data, 'id');
     
    default:
      return state;  

  }
}
```
## Connect Action Creator with Post Index Component
- Directly Passing Action Creator itself instead of connectint funciton "mapDispatchToProp"
- mapDispatchToProp: Separate function format when we want to use a seperate function
```js
export default connect(null, {fetchPosts : fetchPosts})(PostsIndex);
```

## When to reach out the API 
- Event: when are we going to attempt to reach out to the API and fetch our list of Posts?


### ComponentDidMount
Will be called by React immedicately when the component was created and showed on the DOM
- Great location to go and fetch some data or initiate some one time loading procedure.
```js
  componentDidMount() {
    this.props.fetchPosts();
  }
```

#### Fetchig data is an asynchronous operation.
Take some times to take the data. React wouldn't automatically wait till the data was took, so we need some React lifecylec methods to help.


## Manually Create a post to test
- By using PostMan, make a POST Request to https://reduxblog.herokuapp.com/api/posts?key=weichien

```js
{
    "title": "Kevin",
    "categories": "Fun",
    "content": "Great Fun Stories!"
}
```
- Then, we can see in Google Developer Tool, Network category, there are some XHR requests.

![NetWork](./image/demo3.png) 

## Hoop up Application level State
- Everytime we need to use application level state, we need "mapStateToProps" function
```js
function mapStateToProps(state) {
  return { posts : state.posts } 
}

export default connect(mapStateToProps, { fetchPosts })(PostsIndex);
```
- Console.log:
```
218163:
categories:"Fun"
content:"Great Fun Stories!"
id:218163
title:"Kevin"
```
## renderPosts()
- Since we request to an Object that contains a list of Posts, when we look at this.props.posts, it was an Object and we don't get a helper method to map it!
- Map over an Object and return an array -> loadash

```js
  renderPosts() {
    return _.map(this.props.posts, post => {
      return(
        <li className = "list-group-item" key = {post.id}>
          {post.title}
        </li> 
      )
    })
  }
```

### Render the renderPosts in <ul>
```js
      <div>
         <h3>Posts</h3>
         <ul className = "list-group">
          {this.renderPosts()}
         </ul>
      </div>
```

# Create New Posts
![NewPosts](./image/demo4.png)
- 1)  Scaffold "PostsNew" component
- 2) Add Route configuration to show PostsNew component (/posts/new)
- 3) Add navigation between Index and New (Link between each Pages)
- 4) Add Form to PostNew
- 5) Make Action Creator to "Save Post" when use click 'Create', make sure it will be save to Backend

## Route Configuration
- Wrong about Route defination : When React Router see a "/", it will render a PostsIndex Component. But when "/posts/new" also have slash, it still show the PostsIndex 
- Not like the traditional Routing as Django or Express
```js
  <Route path = "/" component = { PostsIndex } />
  <Route path = "/posts/new" component = { PostsNew } />
```
#### Switch Component
- Import the Swithch Component from 'react-router-dom'
- Put the most sepcific Path in the top
```js
<Switch> 
  <Route path = "/posts/new" component = { PostsNew } />
  <Route path = "/" component = { PostsIndex } />
</Switch>
```

## Navigation with the Link Component
- In React Router, we just tell it to show a new set of components, not use anchor tags to do discrete navigation between different routes inside the browser.

### Link Component
- When click on a link tag, it has a couple of event handlers on it that prevent the browser to do what it normal did. (ex issue another Http Request to fetch other HTML documents from server)
```js
<div className = "text-xs-right">
  <Link className = "btn btn-primary"  to = "/posts/new">
    Add a Post
  </Link>
</div>
```

# Redux Form
- Need to validate whatever the user puts into each of these inputs.
- Redux from - Handle any type of form that we put together with redux validating the input and then submitting the form in some fashion.

[Redux Form](https://redux-form.com/7.3.0/)
- Provide all kinds of examples for all Form!

```
npm install --save redux-form@6.6.3
```

- Redux Form uses our Redux instances for handling of the state that is being produced by the form like the actual form that's getting rendered on the screen

### formReducer
- The form Reducer is being applied to the form piece of state.
```js
import { reducer as formReducer} from 'redux-form';

const rootReducer = combineReducers({
    posts: PostsReducer,
    form: formReducer
});
```


## How we use the Redux Form
![Redux Form](./image/demo5.png)

1. Identify Three differents piece of States we had(Title, Categories, Contents)
2. Create a Firld Component by React Form. We need to tell what kinds of Inputs we would like to receive from users.(ex Check box)
3. User changes a 'Field' input
4. Redux automatically handles those changes for us. (Doing the onClick function, set the states etc. for us) Handle Any Changes!!!
5. User Submit the form 
6. Two Callbacks to React Form: validate inputs and handle from submittal.(React Form: OK, the user's inputs data is valid and go take these data to whatever you like to do) 

## Field Component
- Import Two Helpers from redux-form in PostsNew Component
- reduxForm: Allow Component to communicate with reduxReducer (Just like Connect Helper)

```js
import { Field, reduxForm } from 'redux-form';

export default reduxForm({
    form : 'PostsNewForm'
})(PostsNew);
```

#### Pass in some props into Field
- name: What piece of states we're going to use
- component : Take a function or Component
```js
  <Field
    name = "title"
    component = {this.renderTitleField}
  />
```

#### Component props inside Field Component
![Helper Function](./image/demo6.png)
- Redux Form function Helper : JSX blob
- In the renderTitleField, filed argument contains EventHandler and Props for making sure the Field know it will contact with "title" 
```js
  renderTitleField(field) {
    return(
      <div>
          <input 
            type = "text"
            {...field.input}
          />
      </div>
    )
  }
```

## Generalizing Fields
- Add additional Styling to format that input more nicely.
```js
<div className = "form-group">
  <label>Title</label>
    <input 
      className = "form-control"
```
- Generalizing Other Render Function together by passing arbitrary propseties(label) into Field
- Used another Label attribute!
```js
<label>{field.label}</label>


<Field
  label = "Title"
  name = "title"
  component = {this.renderField}
/>
<Field
  label = "Tags"
  name = "tags"
  component = {this.renderField}
/>

```

## Submit Inputs and Validating Forms
### Validation - Automatically! (from Redux Form)
- validate function: When User try to submit(press Enter) wil be called, values contain all the inpurs from users
- -> {title: "abc", categories: "abc", content: "abc"}
- Logic to validate inputs from 'values': 
- A variable Errors: If errors is empty, the form is fine to submit

```js
function validate(values) {
    const errors = {};

    if(!values.title || values.title.length < 3) {
      errors.title = "Please Enter a title as least 3 characters!";
    }

    if(!values.categories) {
      errors.categories = "Please Enter some catefories!";
    }

    if(!values.content) {
      errors.content = "Please Enter some content!";
    }

    return errors;
}
```

### Showing Errors to Users
- Used Errors Props in renderField function
```js
  {field.meta.error}
```
### Handling Form Submittal
- When Redux take information out of this form, the things it will do still depend on us.
- We want to make sure when we click "onSubmit", validate function will be called automatically

```js
const { handleSubmit } = this.props;
```
- This is a property that is being passed to the component on behalf of redux form.
- Inside OnSubmit Function -> Since Redux form only handle states and validation, we need to handle other things by oursleves. 
```js
  onSubmit(values) {
      console.log(values);
  }

  render() {
    const { handleSubmit } = this.props;

    return(
      <form onSubmit = { handleSubmit(this.onSubmit.bind(this)) }>
```
- We got the values passed out from Redux Form:
```js
{title: "adsf", categories: "asdf", content: "asdf"}
```

### Form and Field States
Redux Form Works Internally (3 different States):
- Pristine State: First Show on Screen (User havn't selected or inputed)
- Touched State: User has selected or focused (When users are typing the input)
- Invalid State: Show the Error Message

```js
// Need to show error message when user stats typing or submit

{field.meta.touched ? field.meta.error : ''}
```


### Conditional Styling
- Give a conditional Styling by declaring a className as variable
```js
  renderField(field) {
    const className = `form group ${field.meta.touched && field.meta.error ? 'has-danger' : ''}`
    return(
      <div className = {className}>
```

### Navigation - Submit and back to Root Page
- Link Component to root page
```js
  <button type = "submit" className = "btn btn-primary"> Submit </button>
  <Link to = "/" className = "btn btn-danger">Cancel</Link>
```

## Post Action Creator - Save the Post on Backend
- Action Creator: Make a request to our API with axios, and return an Action with Request as payload.
```js

```

### Navigation Through Callbacks

