# Redux cheat sheet

- Store: The central repository for all the state in a Redux application.
- Action: A plain JavaScript object that represents an intention to change the state.
- Reducer: A function that takes the current state and an action, and returns the new state.

1. Creating Actions Types

```javascript
// actionTypes.js
export const INCREMENT = "INCREMENT";
export const DECREMENT = "DECREMENT";
```

2. Create Actions

```javascript
// actions.js
import { INCREMENT, DECREMENT } from "./actionTypes";

export const increment = () => ({
  type: INCREMENT,
});

export const decrement = () => ({
  type: DECREMENT,
});
```

3. Create Reducer

```javascript
// reducer.js
import { INCREMENT, DECREMENT } from "./actionTypes";

const initialState = {
  count: 0,
};

function counterReducer(state = initialState, action) {
  switch (action.type) {
    case INCREMENT:
      return { count: state.count + 1 };
    case DECREMENT:
      return { count: state.count - 1 };
    default:
      return state;
  }
}

export default counterReducer;
```

4. Create Store

```javascript
// store.js
import { createStore } from "redux";
import counterReducer from "./reducer";

const store = createStore(counterReducer);

export default store;
```

5. Use Redux in a React Component with `connect`

```javascript
// App.js
import React from "react";
import { Provider, connect } from "react-redux";
import store from "./store";
import { increment, decrement } from "./actions";

function Counter({ count, increment, decrement }) {
  return (
    <div>
      <h1>{count}</h1>
      <button onClick={increment}>Increment</button>
      <button onClick={decrement}>Decrement</button>
    </div>
  );
}

const mapStateToProps = (state) => ({
  count: state.count,
});

const mapDispatchToProps = {
  increment,
  decrement,
};

const ConnectedCounter = connect(mapStateToProps, mapDispatchToProps)(Counter);

function App() {
  return (
    <Provider store={store}>
      <ConnectedCounter />
    </Provider>
  );
}

export default App;
```

OR `useDispatch` and `useSelector`

```javascript
import React from "react";
import { useSelector, useDispatch } from "react-redux";
import { increment, decrement } from "./actions";

function Counter() {
  const count = useSelector((state) => state.count);
  const dispatch = useDispatch();

  return (
    <div>
      <h1>{count}</h1>
      <button onClick={() => dispatch(increment())}>Increment</button>
      <button onClick={() => dispatch(decrement())}>Decrement</button>
    </div>
  );
}

function App() {
  return (
    <Provider store={store}>
      <Counter />
    </Provider>
  );
}

export default App;
```

Action with API with `redux-thunk`

```javascript
import { createStore, applyMiddleware } from "redux";
import thunk from "redux-thunk";
import rootReducer from "./reducers"; // assuming you have a root reducer

const store = createStore(rootReducer, applyMiddleware(thunk));
```

```javascript
// actionCreators.js
import { FETCH_DATA_SUCCESS, FETCH_DATA_FAILURE } from "./actionTypes";

// Action creator for synchronous action
export const fetchDataSuccess = (data) => {
  return {
    type: FETCH_DATA_SUCCESS,
    payload: data,
  };
};
// Action creator for fetching data
export const fetchData = () => {
  return async (dispatch) => {
    try {
      const response = await fetch("https://api.example.com/data");
      const data = await response.json();
      dispatch({ type: FETCH_DATA_SUCCESS, payload: data });
    } catch (error) {
      dispatch({ type: FETCH_DATA_FAILURE, payload: error });
    }
  };
};
```

```javascript
import React, { useEffect } from "react";
import { useDispatch } from "react-redux";
import { fetchData } from "./actionCreators";

function DataComponent() {
  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(fetchData());
  }, [dispatch]);

  return <div>{/* Render your data here */}</div>;
}

export default DataComponent;
```
