---
layout: post
title:      "Refactoring a project with React Hooks"
date:       2021-05-12 19:04:33 +0000
permalink:  refactoring_a_project_with_react_hooks
---


The Hooks API in React is a relatively new feature that makes many older patterns of writing React code obsolete. So you may have been wondering, how do you rewrite your React code to take advantage of Hooks?

**Beyond presentational and container components**

A very common React pattern, and one I was taught in my Flatiron bootcamp, is to separate your components into container and presentational components. This is a way to separate the business logic of your code from presentation. Container components are usually class components, while presentational components are usually (but not always) functional components. Dan Abramov, the creator of React, was responsible for popularizing the pattern in the first place. However, he no longer recommends it, and the Hooks feature is the reason. Specifically, he points to the ability to encapsulate the logic using custom hooks, which are functions that can be reused in different components

Here is an example given by React's own documentation, which I discussed in a previous post, defining a custom hook to encapsulate the logic of subscribing and unsubscribing to a friend's status and returning whether they are online.
```
import { useState, useEffect } from 'react';


function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}
```

You could then use this hook in other components, instead of repeatedly copy-pasting the code.

```
function FriendStatus(props) {

  const isOnline = useFriendStatus(props.friend.id);

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

```
function FriendListItem(props) {
  const isOnline = useFriendStatus(props.friend.id);

  return (
    <li style={{ color: isOnline ? 'green' : 'black' }}>
      {props.friend.name}
    </li>
  );
}
```
Custom hooks must always be given a name on the pattern "useHook". State is not shared between all calls to the custom hook, as each call to the hook creates a isolated state. In the above case, you can subscribe or unsubscribe from as many friends as you want.

**Refactoring class components**

Class components are a source of a lot of complexity in React components, and Hooks can simplify them and remove the necessity for classes.

There are three basic scenarios to consider:

*If there are no state or lifecycle methods in the component*

This is a very simple change, and there is no need for class functionality

```
// before
import React, {Component} from 'react';

class App extends Component {
  handleClick = () => {
    console.log("helloooooo")
  }

  render() {
    return <div> 
      Hello World 
      <button onClick={this.handleClick}>
        Click me! 
      </button>
    </div>    
  }
}

export default App
```

```
// after 
import React from 'react'

function App() {
  const handleClick = () => {
    console.log("helloooooo")
  }

  return <div> 
      Hello World 
      <button onClick={handleClick}> Click me! </button>
    </div>  
}

export default App
```
The *class* keyword is replaced with *function*, and there is no longer a need to import Component. All references to *this* can be removed by directly referencing the variable, and defining functions as a declared variable (if you omit the const in the second case, it will throw a syntax error).

*If the component has props*

Again, this change is relatively simple, but it depends on how props function in your component. The below example uses default props:
```
class App extends Component {
  static defaultProps = {
    name: "Hooks"
  }

  handleClick = () => {
    console.log("helloooooo")
  }

  render() {
    return <div> 
      Hello {this.props.name} 
      <button onClick={this.handleClick}> Click me! </button>
    </div>    
  }
}
```
```
function App({name = "Hooks"}) {
  const handleClick = () => {
    console.log("helloooooo")
  }
  
  return <div> 
      Hello {name} 
      <button onClick={handleClick}>Click me! </button>
    </div>
}

```
You can replace the props with a default function parameter, and then you do not need to reference the props directly in your code. 

*If the component has state*

In cases where the component has state and has methods to manipulate it, the useState hook is helpful in refactoring it:
```
class App extends Component {
  state = {
    age: 19
  }

  handleClick = () => {
    this.setState((prevState) => ({age: prevState.age + 1}))
  }

  render() {
    return <div> 
      Today I am {this.state.age} Years of Age 
      <div> 
        <button onClick={this.handleClick}>Get older! </button>
      </div>
    </div>
  }
}
```
```
function App() {
  const [age, setAge] = useState(19);
  const [status, setStatus] = useState('married')
  const [siblings, setSiblings] = useState(10)

  
  const handleClick = () => setAge(age + 1)

  return <div> 
      Today I am {age} Years of Age 
      <div> 
        <button onClick={handleClick}>Get older! </button>
      </div>
   </div>
}
```

The parameter for useState is the desired initial state. You can use useState to set multiple state values. In a class component, with multiple state values you would use an object with multiple values, but with hooks you split them up into the component variables
 There is no need for a this.setState method with the useState hook, instead you can use the setter method provided with the hook, such as setAge. 
 
Note that Hooks are not designed as a replacement for Redux and reducer actions, as these provide functionality beyond hooks. There is a way to use hooks as a reducer to manage state locally, using the useReducer function, but note that it is for simpler use cases than Redux is typically used for.

**How I refactored one of my components**

In my American Ancestries app, I had a class component used to create an input form for demographic data about places. In changing it to a functional component, first I needed to change how state was set:
```

import React, { Component } from "react";

class PlaceInput extends Component {
  constructor(props) {
    super(props);
    this.state = {
      name: "",
      population: 0,
      placeAncestryGroups: null,
    };
  }
```
React hooks make it simple to store the state:

```
import React, { useState } from "react";
function placeInput() {

  const [name, setName] = useState("");
  const [population, setPopulation] = useState(0);
  const [placeAncestryGroups, setPlaceAncestryGroups] = useState(null);
```

Then, I changed the event handlers, which originally used this.setState:
```
  handleNameChange = (event) => {
    this.setState({
      name: event.target.value,
    });
  };
	  handlePopulationChange = (event) => {
    this.setState({
      population: parseInt(event.target.value),
    });
  };
	handleGroupChange = (groups, id, event) => {
    let group = { ...groups.find((group) => group.ancestryGroupId === id) };
    group.population = parseInt(event.target.value);
    const idx = groups.findIndex((group) => group.ancestryGroupId === id);
    groups[idx] = group;
    this.setState({
      placeAncestryGroups: groups,
    });
		};
```
This is a simple change in the first two cases, but remember to add *const* before the method name, or it will throw an error:
```
const handleNameChange = (event) => setName(event.target.value)

 const handlePopulationChange = (event) => setPopulation(parseInt(event.target.value))
```
While my last method *handleGroupChange* was more complex, the only change needed is to change  this.setState to `setPlaceAncestryGroups(groups)`.

Having added hooks, I can then remove references to this.state. For instance the following code in my return code, the input for the place name, references *this*:
```
<FormGroup>
            <Label>
              Place name
              <Input
                id="name"
                name="name"
                type="text"
                value={this.state.name}
                onChange={this.handleNameChange}
              />
            </Label>
          </FormGroup>
```
Without this.state it becomes:
```
 <FormGroup>
          <Label>
            Place name
            <Input
              id="name"
              name="name"
              type="text"
              value={name}
              onChange={handleNameChange}
            />
          </Label>
        </FormGroup>
```
Then I removed references to props in my code, including those in my constructor above, and in the logic to load the data about ancestry groups associated with a place. This had the benefit of fixing the antipattern of setting the initial state based on props (which are passed in to the component, before the user starts making changes, but take a while to load).
```
  render() {
    let groups;
    if (this.state.placeAncestryGroups) {
      groups = [...this.state.placeAncestryGroups];
    } else if (this.props.placeAncestryGroups) {
      groups = [...this.props.placeAncestryGroups];
    } else {
      groups = [];
      return <Loading />;
    }
```
This becomes
```
  let groups;
  if (placeAncestryGroups) {
    groups = [...placeAncestryGroups];
  } else {
    groups = [];
    return <Loading />;
  }
```
I am no longer updating state based on props. This code raised another problem, however, as it no longer loaded the form properly, it didn't update once the props came in. Using default parameters `function PlaceInput({ placeAncestryGroups = null })` didn't work at all and threw an error, since I was already using placeAncestryGroups as a value. So I needed to use a different input `const PlaceInput = ({ initialGroups = null })`. Then placeAncestryGroups can be set conditionally based on this initial value:


```
  const [placeAncestryGroups, setPlaceAncestryGroups] = useState(() => {
    if (initialGroups) {
      return initialGroups;
    } else {
      return null;
    }
  });
```

The ultimate solution to updating the form was to use a key in the parent component. A new instance of the component will be created if props change. This is a general solution to situations where you need props to set an initial state value. 

```
<PlaceInput
              initialGroups={placeAncestryGroups}
              key={this.props.placeAncestryGroups}
/>
```


Stay tuned for more on refactoring and hooks next week!

**References**
[React documentation on avoiding derived state](https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html)
[Dan Abramov deprecating container components in favor of hooks](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)
[Custom hooks](https://reactjs.org/docs/hooks-custom.html)
[LogRocket guide to refactoring](https://blog.logrocket.com/practical-react-hooks-how-to-refactor-your-app-to-use-hooks-b1867e7b0a53/)
[American Ancestries source code](https://github.com/wkdewey/american-ancestries)
