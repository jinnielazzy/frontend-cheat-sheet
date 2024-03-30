# React Testing Library Cheat Sheet

### Basic Imports

```javascript
import React from "react";
import { render, screen, fireEvent, waitFor } from "@testing-library/react";
import { rest } from "msw";
import { setupServer } from "msw/node";
```

### Rendering a Component

```javascript
render(<MyComponent />);
```

### Finding Elements

```javascript
// ByText
screen.getByText("Hello World");

// ByPlaceholderText
screen.getByPlaceholderText("Enter text");

// ByRole
screen.getByRole("button", { name: "Submit" });

// ByTestId
screen.getByTestId("custom-element");
```

### User Events

```javascript
// Click
fireEvent.click(screen.getByRole("button"));

// Change
fireEvent.change(screen.getByPlaceholderText("Enter text"), {
  target: { value: "New value" },
});
```

### Assertions

```javascript
// Element presence
expect(screen.getByText("Hello World")).toBeInTheDocument();

// Element absence
expect(screen.queryByText("Goodbye World")).not.toBeInTheDocument();

// Text content
expect(screen.getByRole("button")).toHaveTextContent("Submit");

// Value
expect(screen.getByPlaceholderText("Enter text")).toHaveValue("New value");
```

### Async Utilities

```javascript
// waitFor
await waitFor(() => screen.getByText("Delayed text"));

// findBy
await screen.findByText("Delayed text");
```

```javascript
import { render, screen, fireEvent, waitFor } from "@testing-library/react";

test("loads items eventually", async () => {
  render(<MyComponent />);

  fireEvent.click(screen.getByText("Load Data"));

  await waitFor(() => screen.getByText("Loaded"));

  expect(screen.getByText("Loaded")).toBeInTheDocument();
});

test("renders fetched data", async () => {
  render(<MyListComponent />);

  const listItem = await screen.findByText("List item 1");

  expect(listItem).toBeInTheDocument();
});
```

### Mocking with MSW

```javascript
// Setup Mock Server
const server = setupServer(
  rest.get("/api/endpoint", (req, res, ctx) => {
    return res(ctx.json({ key: "value" }));
  })
);

// Start/Stop Server in Tests
beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

// Overriding Handlers for Specific Tests
server.use(
  rest.post("/api/submit", (req, res, ctx) => {
    return res(ctx.status(200));
  })
);
```

#### More explicit

1. basic imports

```javascript
import { rest } from "msw";
import { setupServer } from "msw/node";
```

2. create handlers

```javascript
const handlers = [
  rest.get("/api/user", (req, res, ctx) => {
    return res(ctx.status(200), ctx.json({ username: "admin" }));
  }),
  rest.post("/api/login", (req, res, ctx) => {
    return res(
      ctx.status(200),
      ctx.json({ message: "Logged in successfully" })
    );
  }),
];
```

3. setup server

```javascript
const server = setupServer(...handlers);
```

4.Configure Server in Test Lifecycle

```javascript
// In global test setup file or at the top of your test file
beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

5.Overriding Handlers in Tests

```javascript
test("should return a 500 error", () => {
  server.use(
    rest.get("/api/user", (req, res, ctx) => {
      return res(ctx.status(500));
    })
  );

  // Your test code here
});
```

Create `setUpTest.js` File: This file is often located at the root of your project or inside a test setup directory, and it's automatically loaded by Jest (or your test runner) before the tests run.

```javascript
import { setupServer } from "msw/node";
import { handlers } from "./mocks/handlers"; // Import your MSW handlers

// Set up the MSW server
const server = setupServer(...handlers);

beforeAll(() => server.listen()); // Start the server before all tests
afterEach(() => server.resetHandlers()); // Reset handlers after each test
afterAll(() => server.close()); // Close the server after all tests

export default server;
```

```javascript
// mocks/handlers.js
import { rest } from "msw";

export const handlers = [
  rest.get("/api/endpoint", (req, res, ctx) => {
    return res(ctx.json({ key: "value" }));
  }),
  // Define other handlers as needed
];
```

overwrite handler

```javascript
import { rest } from "msw";
import { server } from "./setUpTest"; // Import the server from your setup file

describe("My feature tests", () => {
  it("should test feature with overridden handler", async () => {
    // Overwrite specific handlers for this test
    server.use(
      rest.get("/api/endpoint", (req, res, ctx) => {
        return res(ctx.json({ key: "overwritten value" }));
      })
    );

    // Now, when your test runs, the request to '/api/endpoint' will use the overwritten handler

    // Your test code here...
  });

  // Other tests will still use the original handler unless overwritten similarly
});
```

### Cleanup

RTL automatically cleans up rendered components between tests.

- Example:

```javascript
test("increments counter from 0 to 1", () => {
  render(<Counter />);
  fireEvent.click(screen.getByText("Increment"));
  expect(screen.getByText("Count is 1")).toBeInTheDocument();
});

test("increments counter from 0 to 2", () => {
  render(<Counter />);
  fireEvent.click(screen.getByText("Increment"));
  fireEvent.click(screen.getByText("Increment"));
  expect(screen.getByText("Count is 2")).toBeInTheDocument();
});
```

each test starts with the counter at 0, thanks to RTL's cleanup. Without this automatic cleanup, the second test might incorrectly start with the counter at 1

### Mocking Modules

```javascript
// Mock a Module
jest.mock("module-name", () => ({
  functionName: jest.fn(() => "Mocked return value"),
}));
```

- Example:

```javascript
// api.js
export const fetchData = () => {
  return fetch("https://example.com/data").then((res) => res.json());
};
```

Mock the entire module

```javascript
import { fetchData } from "./api";

jest.mock("./api", () => ({
  fetchData: jest.fn(() => Promise.resolve({ data: "mocked data" })),
}));

test("uses mocked API call", async () => {
  const data = await fetchData();

  expect(data).toEqual({ data: "mocked data" });
});
```

Mock a single function

```javascript
import * as api from "./api";

jest.spyOn(api, "fetchData").mockImplementation(() => {
  return Promise.resolve({ data: "mocked data" });
});

test("uses mocked API call", async () => {
  const data = await api.fetchData();

  expect(data).toEqual({ data: "mocked data" });
});
```

This cheat sheet combines essential React Testing Library usage and MSW setup for mocking server-side handling, providing a comprehensive guide for testing React components and their interactions with APIs.
