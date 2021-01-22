## Provider Pattern

React’s provider pattern is a powerful concept. React uses provider pattern in Context API to share data across the tree descendant nodes. You may not find this useful when you are using plain react. However, this pattern comes handy when you are designing a complex app since as it solves multiple problems.

### What is React’s context API?

React Context API is a way to essentially create global variables that can be passed around in a React app. This is an alternative to "prop drilling", or passing props from grandparent to parent to the child and so on. Context is often touted as a simpler or a ighter solution to using Redux for state management.

### Provider pattern and Context API

Provider pattern: However, it is not only about react context. You might have used a state management library like redux and mobX. Here provider is the top most component and it is provided by `react-redux`. We write it the following way:

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import store from './store';
import App from './App';

const rootElement = document.getElementById('root');
ReactDOM.render(  
  <Provider store={store}>    
    <App />  
  </Provider>,  
  rootElement
);
```

### How do we create Context

**React.createContext**

Creates a new context object that is used to pass down the value to a component tree without using props.

```jsx
const MyContext = React.createContext(defaultValue);
```

**Context.Provider**

Provider allows the consuming React component to subscribe to the context changes.

```jsx
<MyContext.Provider value={someValue}>
```

All the consumers that are descendants of Provider will re-render whenever the value prop changes.

**useContext Hook**

It can be used in functional components which accepts a context object and returns the current context value.

```jsx
const value = useContext(MyContext);
```

### Lets look at an example

We will create one custom provider `UserProvider`, which will be used to provide the value down to every descendant. To consume that value; we will create one custom Hook `useUser`.

```jsx
import React, {
  useState,
  useEffect,
  useContext,
} from 'react'
import axios from 'axios'

const UserContext = React.createContext(undefined);

const UserProvider = ({
  children
}) => {
  const [user, setUser] = useState(null);
  const [random, setRandom] = useState(1);

  useEffect(() => {
    const url = `https://jsonplaceholder.typicode.com/users/${random}`;
    axios.get(url).then(res => {
      setUser(res.data);
    }).catch(err => {
      console.error(err)
    });
  }, [random]);

  const changeUser = () => {
    const randomNumber = Math.floor(Math.random() * 10 + 1);
    setRandom(randomNumber);
  }

  const data = [
    user,
    changeUser
  ]

  return (
    <UserContext.Provider value={data}>
      {children}
    </UserContext.Provider>
  )
}

const useUser = () => {
  const context = useContext(UserContext);
  if (context === undefined) {
    throw new Error('useUser can only be used inside UserProvider');
  }
  return context;
}

export {
  UserProvider,
  useUser
}
```

Here we have given undefined as the default value to the context. This default value, in general; is used whenever we try to use the value of context objects in components that are not the decendants of the provider of this context object.

We are giving an array to the value prop of `UserContext.Provider`. This array has value to users for them to state and has a function of changeUser that is calculating a random number from 1 to 10 and storing it in a state called random.

We are making an Ajax call to our endpoint in the `useEffect` Hook, which has only random state in its dependency array. That means this effect will only execute when our random state changes.

In the return statement; we are writing the main context provider logic, which will pass down the values to all consumers which are its descendants.

```jsx
<UserContext.Provider value={data}>
  {children}
</UserContext.Provider>
```

After our custom provider, we have created our custom Hook. In this Hook; first we are using a `useContext` hook to get the value of context object. After that, to prevent accidental usage of our custom Hook in any non-descendant component, we are checking whether the value obtained using `useContext` is equal to our defaultValue given during the creation of our context object. In our case, this default value is undefined. If the value is undefined, then we can say that our custom Hook is used in any non-descendant component.

```js
//undefined is given as default value
const UserContext = React.createContext(undefined);
```

Thus, from our custom Hook, we return the obtained context value.

Next [>> Styling Patterns](../styling-pattern/)
