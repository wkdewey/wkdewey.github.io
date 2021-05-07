---
layout: post
title:      "React hooks"
date:       2021-05-07 16:56:45 +0000
permalink:  react_hooks
---


Hooks in React are a new way to manage state, and reuse code that deals with state, without an excess of code. The Hooks API gives you a direct link into props, state, the context, and the lifecycle of component. You can directly "hook into" state and lifecycle, without using classes, eliminating a common source of complexity in React code. There are built in hooks in React, like useState or useEffect, and you can also write your own hooks.

**useState hook**

In this example, hooks are used to create a simple counter:
```
import React, { useEffect, useState } from "react";

function Example() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}

export default Example;
```

In this case, useState is a hook that allows the code to dynamically update state.  It includes the *count* variable which holds the current value of state, and a function *setCount* that updates the state. This function is similar to the this.setState function ordinarily used in React, but doesn't merge the state. The argument passed to useState, setting count equal to 0, is the state that will be used on the first render. You can also set multiple state variables, calling on useState every time:

```
import React, { useState } from "react";
function ExampleWithManyStates() {
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState("banana");
  const [todos, setTodos] = useState([{ text: "Learn Hooks" }]);
  return (
    <div>
      <p>{age}</p>
      <p>{fruit}</p>
      <p>{todos[0]["text"]}</p>
    </div>
  );
}

export default ExampleWithManyStates;
```

Note the use of array destructuring syntax. The variable names (age, fruit, todos) are set by the user rather than the API, so you can use the useState function every time.

**useEffect hook**

The *useEffect* hook is a way to perform side effects when you update state. It is like calling the lifecycle methods componentDidMount, componentDidUpdate, and componentWillUnmount all in one method. The following code extends the above counter code to also update the title bar every time you click.

```
import React, { useEffect, useState } from "react";

function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}

export default Example;
```

Every time that React flushes changes to the DOM, in other words after every render, the effect will be called. Effects can not only update the DOM, but also clean up after unmounting (unsubscribe from a subscribed service, for instance). The following code, which combines the counter with a display of the online status of a friend on a social network, also shows that it is possible to have multiple effects in the same component:

```
import React, { useState, useEffect } from "react";

function FriendStatusWithCounter(props) {



  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }
  return isOnline ? "Online" : "Offline";
}
export default FriendStatusWithCounter;
```

The ability to use multiple effects makes it possible to group effects by related functionality, rather than by where they fall in the component lifecycle. 

React suggests two additional rules to make sure that hooks function properly. They should only be called at the top level, not inside a loop, conditional, or nested function (this prevents unpredictable or repeated re-rendering). Also, they should only be called within a React function.

**Creating a custom hook**

Suppose you wanted to use the above code in another component to subscribe or unsubscribe from friend statuses. The code can be extracted into a custom hook, defined much like an ordinary component (except that it takes in an argument, and returns a value rather than JSX code).
```
import React, { useState, useEffect } from "react";

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}
export default useFriendStatus;

```

Then you could refactor the above component as 
```
import React, { useState, useEffect } from "react";

function FriendStatusWithCounter(props) {

  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });
  isOnline = useFriendStatus(props.friend.id)
  return isOnline ? "Online" : "Offline";
}
export default FriendStatusWithCounter;
```
and write a second component that lists friends based on whether they are online or offline:
```
import React, { useState, useEffect } from "react";
import useFriendStatus from "./useFriendStatus";

function FriendListItem(props) {
  const isOnline = useFriendStatus(props.friend.id);

  return (
    <li style={{ color: isOnline ? "green" : "black" }}>{props.friend.name}</li>
  );
}
export default FriendListItem;
```
You can see here how custom hooks can remove repetition and help make your code more DRY.

**References**
[React's introduction to hooks](https://reactjs.org/docs/hooks-intro.html)
