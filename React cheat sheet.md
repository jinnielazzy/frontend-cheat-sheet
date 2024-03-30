# React cheat sheet

### Components

- Class Component:

```javascript
class MyComponent extends React.Component {
  render() {
    return <div>Hello, {this.props.name}</div>;
  }
}
```

- Functional Component:

```javascript
function MyComponent({ name }) {
  return <div>Hello, {name}</div>;
}
```

### JSX

```javascript
const element = <h1>Hello, world!</h1>;
```

### Props vs. State:

- Props:

```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

- State:

```javascript
function Counter() {
  const [count, setCount] = React.useState(0);
  return (
    <div>
      {count}
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

```javascript
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      user: { name: "John Doe", age: 28 },
    };
  }

  // Other methods...
}
```

```javascript
class App extends React.Component {
  state = {
    user: { name: "John Doe", age: 28 },
  };

  // Other methods...
}
```

### React Lifecycle Methods

```javascript
class MyComponent extends React.Component {
  componentDidMount() {
    console.log("Component mounted");
  }

  componentDidUpdate(prevProps, prevState) {
    console.log("Component updated");
  }

  componentWillUnmount() {
    console.log("Component will unmount");
  }

  render() {
    return <div>Hello, World!</div>;
  }
}
```

```javascript
import React, { useEffect } from "react";

function MyComponent() {
  useEffect(() => {
    console.log("Component mounted");

    return () => {
      console.log("Component will unmount");
    };
  }, []);

  return <div>Hello, World!</div>;
}
```

### State Management

- Update states

```javascript
class Counter extends React.Component {
  constructor(props) {
    super(props);
    // State initialization
    this.state = {
      count: 0,
    };
  }

  // Method to increment count
  incrementCount = () => {
    this.setState({ count: this.state.count + 1 });
  };

  render() {
    return (
      <div>
        <h1>Count: {this.state.count}</h1>
        <button onClick={this.incrementCount}>Increment</button>
      </div>
    );
  }
}
```

```javascript
import React, { useState } from "react";

function Counter() {
  // State initialization using useState hook
  const [count, setCount] = useState(0);

  // Function to increment count
  const incrementCount = () => {
    setCount(count + 1);
  };

  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={incrementCount}>Increment</button>
    </div>
  );
}
```

- Context API

```javascript
// Creating Context
import React, { createContext, useState } from "react";

const UserContext = createContext();

function App() {
  const [user, setUser] = useState({ name: "Jane Doe", age: 30 });

  return (
    <UserContext.Provider value={{ user, setUser }}>
      <ChildComponent />
    </UserContext.Provider>
  );
}

// Consuming Context
function ChildComponent() {
  const { user, setUser } = React.useContext(UserContext);

  return (
    <div>
      <p>Name: {user.name}</p>
      <p>Age: {user.age}</p>
      <button onClick={() => setUser({ ...user, age: 35 })}>Update Age</button>
    </div>
  );
}
```

```javascript
// Creating Context
import React, { Component } from "react";

const UserContext = React.createContext();

class App extends Component {
  state = {
    user: { name: "John Doe", age: 28 },
  };

  setUser = (user) => {
    this.setState({ user });
  };

  render() {
    return (
      <UserContext.Provider
        value={{ user: this.state.user, setUser: this.setUser }}
      >
        <ChildClassComponent />
      </UserContext.Provider>
    );
  }
}

class ChildClassComponent extends Component {
  static contextType = UserContext; // NOTE: static type

  render() {
    const { user, setUser } = this.context;

    return (
      <div>
        <p>Name: {user.name}</p>
        <p>Age: {user.age}</p>
        <button onClick={() => setUser({ ...user, age: 33 })}>
          Update Age
        </button>
      </div>
    );
  }
}
```

`useReducer`

```javascript
import React, { useReducer } from "react";

// Reducer function that defines how the state changes
function counterReducer(state, action) {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "decrement":
      return { count: state.count - 1 };
    case "reset":
      return { count: 0 };
    default:
      throw new Error(`Unhandled action type: ${action.type}`);
  }
}

function Counter() {
  // Initialize useReducer with the reducer function and an initial state
  const [state, dispatch] = useReducer(counterReducer, { count: 0 });

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: "increment" })}>Increment</button>
      <button onClick={() => dispatch({ type: "decrement" })}>Decrement</button>
      <button onClick={() => dispatch({ type: "reset" })}>Reset</button>
    </div>
  );
}
```

use `useReducer` with context

1. create reducer

```javascript
function reducer(state, action) {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "decrement":
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}
```

2. create context

```javascript
import React, { createContext, useReducer } from "react";

const CountContext = createContext();

function CountProvider({ children }) {
  const initialState = { count: 0 };
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <CountContext.Provider value={{ state, dispatch }}>
      {children}
    </CountContext.Provider>
  );
}
```

3. use them

```javascript
function Counter() {
  const { state, dispatch } = React.useContext(CountContext);

  return (
    <div>
      Count: {state.count}
      <button onClick={() => dispatch({ type: "increment" })}>+</button>
      <button onClick={() => dispatch({ type: "decrement" })}>-</button>
    </div>
  );
}

function App() {
  return (
    <CountProvider>
      <Counter />
    </CountProvider>
  );
}
```

### React Hooks

```javascript
function MyComponent() {
  const [count, setCount] = React.useState(0);

  React.useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

- Custom Hooks

```javascript
import { useState } from "react";

// Custom hook for handling form inputs
function useForm(initialValues) {
  const [values, setValues] = useState(initialValues);

  // Handle changes in input fields
  const handleChange = (event) => {
    setValues({
      ...values,
      [event.target.name]: event.target.value,
    });
  };

  // Handle form submission
  const handleSubmit = (callback) => (event) => {
    event.preventDefault();
    callback();
  };

  return {
    values,
    handleChange,
    handleSubmit,
  };
}

// Using useForm in a Component
function MyForm() {
  const { values, handleChange, handleSubmit } = useForm({
    name: "",
    email: "",
  });

  const submitForm = () => {
    console.log("Form data:", values);
  };

  return (
    <form onSubmit={handleSubmit(submitForm)}>
      <label>
        Name:
        <input
          type="text"
          name="name"
          value={values.name}
          onChange={handleChange}
        />
      </label>
      <label>
        Email:
        <input
          type="email"
          name="email"
          value={values.email}
          onChange={handleChange}
        />
      </label>
      <button type="submit">Submit</button>
    </form>
  );
}
```

```javascript
import { useState, useEffect } from "react";

// Custom hook for fetching data from an API
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      setLoading(true);
      try {
        const response = await fetch(url);
        if (!response.ok) {
          throw new Error(`Error: ${response.status}`);
        }
        const data = await response.json();
        setData(data);
      } catch (error) {
        setError(error.message);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]); // Effect runs when the URL changes

  return { data, loading, error };
}

// Using useFetch in a Component
function DataFetcher({ url }) {
  const { data, loading, error } = useFetch(url);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div>
      <h1>Data</h1>
      <pre>{JSON.stringify(data, null, 2)}</pre>
    </div>
  );
}
```

## Advanced Concepts:

### Higher-Order Components (HOCs):

```javascript
function withLogging(WrappedComponent) {
  return function WrappedComponentWithLogging(props) {
    console.log("Component rendered with props:", props);
    return <WrappedComponent {...props} />;
  };
}
```

### Render Props:

```javascript
function MouseTracker(props) {
  const [mousePosition, setMousePosition] = React.useState({ x: 0, y: 0 });

  const handleMouseMove = (event) => {
    setMousePosition({ x: event.clientX, y: event.clientY });
  };

  return <div onMouseMove={handleMouseMove}>{props.render(mousePosition)}</div>;
}
```

## Performance Optimization

### `React.memo`

React.memo is a higher-order component for memoizing a functional component. It prevents the component from re-rendering if its props have not changed.

```javascript
import React from "react";

const ExpensiveComponent = React.memo(({ compute, value }) => {
  const computedValue = compute(value);
  return <div>{computedValue}</div>;
});

function computeExpensiveValue(value) {
  // An expensive computation
  console.log("Computing expensive value...");
  return value * 2; // Example of an expensive computation
}

function App() {
  const [count, setCount] = React.useState(0);

  return (
    <div>
      <ExpensiveComponent compute={computeExpensiveValue} value={count} />
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

ExpensiveComponent will only re-render if compute or value props change, thus avoiding unnecessary re-computation and re-rendering.

### `useMemo`

useMemo memoizes expensive calculations and only re-computes the cached value when one of the dependencies has changed.

```javascript
import React, { useMemo } from "react";

function App({ value }) {
  const expensiveComputation = useMemo(() => {
    console.log("Computing expensive value...");
    return value * 2; // Simulate an expensive operation
  }, [value]);

  return <div>{expensiveComputation}</div>;
}
```

expensiveComputation will only be recalculated when value changes, not on every render.

### `useCallback`

useCallback returns a memoized callback function. This is useful when passing callbacks to optimized child components that rely on reference equality to prevent unnecessary renders.

```javascript
import React, { useState, useCallback } from "react";

const Button = React.memo(({ onClick, children }) => {
  console.log(`Rendering button: ${children}`);
  return <button onClick={onClick}>{children}</button>;
});

function App() {
  const [count, setCount] = useState(0);

  const increment = useCallback(() => {
    setCount((c) => c + 1);
  }, []);

  return (
    <div>
      Count: {count}
      <Button onClick={increment}>Increment</Button>
    </div>
  );
}
```

In this example, increment is memoized with useCallback, so Button only re-renders when count changes, not on every render of App.

### `ShouldComponentUpdate`

```javascript
class MyComponent extends React.Component {
  shouldComponentUpdate(nextProps, nextState) {
    // Only re-render if the data prop has changed
    return nextProps.data !== this.props.data;
  }

  render() {
    return <div>{this.props.data}</div>;
  }
}
```

### Avoiding Inline Functions and Objects in Render

Passing new functions or objects as props on each render can cause child components to re-render unnecessarily because these props are recreated on each render and are thus new by reference every time.

```javascript
class MyComponent extends React.Component {
  handleClick = () => {
    console.log("Clicked!");
  };

  render() {
    return <ChildComponent onClick={this.handleClick} />;
  }
}
```

By binding the function in the constructor or using class property syntax (as shown above)

## Routing with React Router v6

### Basic Routing:

```javascript
import { BrowserRouter, Routes, Route } from "react-router-dom";

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </BrowserRouter>
  );
}
```

## Use of `setInterval` and `setTimeout`

```javascript
import React, { useEffect } from "react";

function MyComponent() {
  useEffect(() => {
    const timeoutId = setTimeout(() => {
      console.log("Timeout in useEffect");
    }, 3000);

    return () => clearTimeout(timeoutId); // Cleanup function to clear the timeout
  }, []); // Empty dependency array means this effect runs only once after the initial render

  return <div>Check console after 3 seconds</div>;
}
```

```javascript
class MyComponent extends React.Component {
  componentDidMount() {
    this.intervalId = setInterval(() => {
      console.log("Interval tick");
    }, 1000); // 1 second interval
  }

  componentWillUnmount() {
    clearInterval(this.intervalId); // Clear the interval on component unmount
  }

  render() {
    return <div>Check console every second</div>;
  }
}
```
