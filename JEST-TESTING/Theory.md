## Lecture Notes: Simulating Data from the Server in Tests

### Introduction

In this lecture, we will learn how to simulate data from a server within our tests. We will focus on testing the order entry page of our application, which involves handling data for both scoop and topping options. We will cover the following key points:

- Overview of the order entry page components.
- Simulating server responses using Mock Service Worker (MSW).
- Writing tests to verify the rendering of scoop and topping options.

### Order Entry Page Overview

The order entry page is an essential part of our ice cream sundae application. It consists of several components that interact to provide users with an intuitive interface for selecting scoops and toppings. The main elements of the order entry page include:

- **Scoop Options**: Displayed as a list of available scoops, each costing $2. The total cost updates dynamically based on the user's selection.
- **Topping Options**: Displayed as checkboxes, each topping costs $1.50. The total cost also updates based on selected toppings.
- **Running Totals**: Separate running totals for scoops and toppings, along with a grand total and an order button.

Here’s an example scenario:

- One vanilla scoop and two mint chip scoops selected.
- Various toppings can be checked or unchecked.

### Testing Components

We will focus on testing the components responsible for rendering scoop and topping options. Specifically, we will:

- Ensure that the option images for scoops and toppings render correctly.
- Use Mock Service Worker (MSW) to simulate server responses for these options.

### Order Entry Component Structure

The order entry component is composed of several subcomponents:

- **Option Subcomponent**: Used for both scoops and toppings, differing only in the props passed to it.
  - **Scoop Options Component**: Renders individual scoop options.
  - **Topping Options Component**: Renders individual topping options.

### Testing Plan

Our testing strategy involves the following steps:

1. **Mocking Server Responses**: Using MSW to simulate API responses for `/scoops` and `/toppings` endpoints.
2. **Rendering Tests**: Verifying that the images for scoop and topping options render correctly based on the mocked server data.

### Implementation Details

- We will start by writing tests for the scoop options.
- The topping options test will be provided as a coding exercise.

### Conclusion

In this lecture, we covered how to simulate server data within our tests using Mock Service Worker. We focused on the order entry page, testing the rendering of scoop and topping options. In the next lecture, we will delve deeper into Mock Service Worker and how it integrates with our testing framework to provide robust, reliable tests for our application components.

## Lecture Notes: Mock Service Worker (MSW) - Setup and Usage

### Introduction

In this lecture, we will delve into the details of Mock Service Worker (MSW), understanding both the rationale behind using it and the steps involved in its setup and usage. By the end of this lecture, you will have a comprehensive understanding of how to intercept network calls in your tests, preventing real network interactions, and creating predictable test conditions.

### Why Use Mock Service Worker?

Mock Service Worker allows us to intercept network calls and return specified responses, which is crucial for functional testing. Functional tests do not involve real server interactions, and MSW helps to:

- **Prevent Real Network Calls**: Ensure no real network calls are made during tests, leading to faster and more reliable tests.
- **Control Test Conditions**: Create controlled conditions based on predetermined server responses, allowing us to predict and verify the behavior of our components.

### Setting Up Mock Service Worker

To set up MSW, follow these steps:

1. **Install MSW**: MSW comes pre-installed with the Sunday starter provided for this app. No need to install it manually for this project.
2. **Create Handlers**: Handlers are functions that determine the response for specific URLs and routes.
3. **Setup Test Server**: Create a test server to handle requests and intercept network calls.

### Creating Handlers

Handlers are the core of MSW, matching incoming URLs and providing mocked responses. Here’s how to create handlers:

1. **Create a Directory and File**: In your project, create a new directory called `mocks` and inside it, a file called `handlers.js`.

2. **Define Handlers**: Define handlers to mock API responses. Here’s an example of a handler that mocks the `/scoops` endpoint:

```javascript
// mocks/handlers.js
import { rest } from "msw";

export const handlers = [
  rest.get("http://localhost:3030/scoops", (req, res, ctx) => {
    return res(
      ctx.json([
        { name: "Chocolate", imagePath: "/images/chocolate.png" },
        { name: "Vanilla", imagePath: "/images/vanilla.png" },
      ])
    );
  }),
];
```

### Anatomy of a Handler

A handler consists of several parts:

- **Handler Type**: For REST APIs, use `rest`. For GraphQL, use the appropriate handler type.
- **HTTP Method**: Specify the HTTP method to mock (e.g., GET, POST).
- **URL**: The exact URL to mock, including protocol and port.
- **Response Resolver**: A function that returns the mocked response. Here, we use `ctx.json()` to return a JSON response.

_Relevant Documentation: [MSW - Basic Setup](https://mswjs.io/docs/getting-started)_

## Lecture Notes: Integrating Mock Service Worker (MSW) with Node

### Introduction

In this lecture, we will integrate Mock Service Worker (MSW) with Node.js to intercept network calls during our tests. We will set up the server to run with Jest or Vite, ensuring that our tests do not make real network calls. By the end of this lecture, you will have a functional MSW setup for testing.

### Setting Up the Server

To set up the MSW server for Node.js, follow these steps:

1. **Create the Server File**: Create a file named `server.js` in the `mocks` directory.
2. **Import Handlers**: Import the handlers we defined in the previous lecture and set up the server.

Here’s how to set up the server:

```javascript
// mocks/server.js
import { setupServer } from "msw/node";
import { handlers } from "./handlers";

export const server = setupServer(...handlers);
```

### Configuring the Setup File

To ensure the server runs during tests and intercepts network calls, we need to configure our setup file. We’ll use Jest’s setup file (`setupTests.js`) to establish the MSW server.

1. **Import the Server**: Import the server we created in `server.js`.
2. **Set Up the Server**: Configure the server to listen before tests, reset handlers after each test, and close the server after all tests.

Here’s how to configure the setup file:

```javascript
// setupTests.js
import "@testing-library/jest-dom"; // Import Jest DOM matchers
import { server } from "./mocks/server";

// Establish API mocking before all tests
beforeAll(() => server.listen());

// Reset any request handlers that we may add during the tests,
// so they don't affect other tests.
afterEach(() => server.resetHandlers());

// Clean up after the tests are finished.
afterAll(() => server.close());
```

### Adding Comments for Clarity

Adding comments helps in understanding the purpose of each line in the setup file:

```javascript
// setupTests.js
import "@testing-library/jest-dom"; // Import Jest DOM matchers
import { server } from "./mocks/server";

// Establish API mocking before all tests.
beforeAll(() => server.listen());

// Reset any request handlers that we may add during the tests,
// so they don't affect other tests.
afterEach(() => server.resetHandlers());

// Clean up after the tests are finished.
afterAll(() => server.close());
```

### Conclusion

In this lecture, we integrated Mock Service Worker (MSW) with Node.js to intercept network calls during our tests. We set up the server to run with Jest or Vite, ensuring that our tests remain isolated and do not make real network calls. In the next lecture, we will explore how to use MSW in our tests to create reliable and predictable testing conditions.

## Lecture Notes: Writing Tests Using Mock Service Worker

### Introduction

In this lecture, we will write tests for the `Options` component using Mock Service Worker (MSW). We will verify that when the `Options` component calls the scoops endpoint on the server, it correctly displays the scoop options. By the end of this lecture, you will have a test that checks for the correct rendering of images for scoop options.

### Setting Up the Test Structure

1. **Create Necessary Files**:

   - Create a new folder called `entry` in the `pages` directory.
   - Inside the `entry` directory, create `Options.js` and `ScoopOption.js` files.
   - Create a `test` directory inside `entry` and create a file named `Options.test.js`.

2. **Shell Components**:
   - In `Options.js`:
     ```javascript
     export default function Options({ optionType }) {
       return <div>Options component</div>;
     }
     ```
   - In `ScoopOption.js`:
     ```javascript
     export default function ScoopOption() {
       return <div>Scoop option</div>;
     }
     ```

### Writing the Test

1. **Import Necessary Libraries and Components**:

   ```javascript
   import { render, screen } from "@testing-library/react";
   import Options from "../Options";
   ```

2. **Write the Test**:

   - The test will check that an image is displayed for each scoop option returned by the server (intercepted by MSW).

   ```javascript
   test("displays image for each scoop option from the server", async () => {
     render(<Options optionType='scoops' />);

     // Use findAllByRole to get all images with the role 'img' and alt text ending with 'scoop'
     const scoopImages = await screen.findAllByRole("img", { name: /scoop$/i });

     // Check that there are two scoop images
     expect(scoopImages).toHaveLength(2);

     // Check that the alt text of the images is correct
     const altText = scoopImages.map((img) => img.alt);
     expect(altText).toEqual(["Chocolate scoop", "Vanilla scoop"]);
   });
   ```

### Explanation

- **Render the Component**: We render the `Options` component with `optionType` set to `"scoops"`.
- **Find Images by Role**: We use `findAllByRole` to find all images (`img` role) whose alt text ends with `"scoop"`. This is done using a regular expression `/scoop$/i`.
- **Assertions**:
  - We check that the number of images found is two.
  - We map the alt text of the images to an array and check that it matches the expected alt text array `['Chocolate scoop', 'Vanilla scoop']`.

### Important Points

- **Mock Service Worker**:
  - We do not directly reference MSW in our test code. MSW is set up in the test environment configuration, intercepting network requests and returning mocked responses.
  - The `Options` component makes a GET request to the server, which MSW intercepts, returning the mocked response defined in the handlers.
    Great, let's walk through the code quiz step-by-step to ensure we can implement the toppings functionality correctly. We'll cover the following steps:

1. **Create the ToppingOption component**
2. **Update the Options component**
3. **Create the handler for the toppings endpoint**
4. **Write the test for toppings**

### Step 1: Create the `ToppingOption` Component

Create a new file named `ToppingOption.jsx` and implement the component similar to `ScoopOption`.

```javascript
import React from "react";
import { Col } from "react-bootstrap";

export default function ToppingOption({ name, imagePath }) {
  return (
    <Col xs={12} sm={6} md={4} lg={3} style={{ textAlign: "center" }}>
      <img
        src={`http://localhost:3030/${imagePath}`}
        alt={`${name} topping`}
        style={{ width: "75%" }}
      />
    </Col>
  );
}
```

### Step 2: Update the `Options` Component

Update `Options.jsx` to handle `toppings` option type and render `ToppingOption` components.

```javascript
import React, { useEffect, useState } from "react";
import axios from "axios";
import ScoopOption from "./ScoopOption";
import ToppingOption from "./ToppingOption";

export default function Options({ optionType }) {
  const [items, setItems] = useState([]);

  useEffect(() => {
    axios
      .get(`http://localhost:3030/${optionType}`)
      .then((response) => setItems(response.data))
      .catch((error) => console.log(error));
  }, [optionType]);

  const ItemComponent = optionType === "scoops" ? ScoopOption : ToppingOption;

  const optionItems = items.map((item) => (
    <ItemComponent
      key={item.name}
      name={item.name}
      imagePath={item.imagePath}
    />
  ));

  return <div>{optionItems}</div>;
}
```

### Step 3: Create the Handler for the Toppings Endpoint

Update your mock service worker setup to handle the toppings endpoint.

```javascript
import { rest } from "msw";

export const handlers = [
  // existing handlers
  rest.get("http://localhost:3030/scoops", (req, res, ctx) => {
    return res(
      ctx.json([
        { name: "Chocolate", imagePath: "/images/chocolate.png" },
        { name: "Vanilla", imagePath: "/images/vanilla.png" },
      ])
    );
  }),
  rest.get("http://localhost:3030/toppings", (req, res, ctx) => {
    return res(
      ctx.json([
        { name: "Cherries", imagePath: "/images/cherries.png" },
        { name: "M&Ms", imagePath: "/images/m-and-ms.png" },
        { name: "Hot fudge", imagePath: "/images/hot-fudge.png" },
      ])
    );
  }),
];
```

### Step 4: Write the Test for Toppings

Add a test in `options.test.js` to verify the toppings are rendered correctly.

```javascript
import { render, screen } from "@testing-library/react";
import Options from "../Options";

test("displays image for each topping option from server", async () => {
  render(<Options optionType='toppings' />);

  // find images
  const images = await screen.findAllByRole("img", { name: /topping$/i });
  expect(images).toHaveLength(3);

  // confirm alt text of images
  const altText = images.map((img) => img.alt);
  expect(altText).toEqual([
    "Cherries topping",
    "M&Ms topping",
    "Hot fudge topping",
  ]);
});
```

### Summary

Here's what we did:

- Created `ToppingOption` component to display topping images.
- Updated `Options` component to render `ToppingOption` based on `optionType`.
- Created a handler for the `toppings` endpoint in the mock service worker.
- Wrote a test to ensure the toppings are rendered correctly.

This will verify that the `toppings` option type is handled correctly and that the images are displayed as expected. If you encounter any issues, check the console logs and ensure the endpoint handlers and test assertions match the expected outputs.

### Code Quiz Review and Implementation

We will follow a similar approach to how we handled scoops to implement and test the toppings functionality. Let's go through the steps systematically.

### Step 1: Create the `ToppingOption` Component

Create a new file `ToppingOption.jsx`:

```javascript
import React from "react";
import { Col } from "react-bootstrap";

export default function ToppingOption({ name, imagePath }) {
  return (
    <Col xs={12} sm={6} md={4} lg={3} style={{ textAlign: "center" }}>
      <img
        src={`http://localhost:3030/${imagePath}`}
        alt={`${name} topping`}
        style={{ width: "75%" }}
      />
    </Col>
  );
}
```

### Step 2: Update the `Options` Component

Update `Options.jsx` to handle `toppings` option type and render `ToppingOption` components:

```javascript
import React, { useEffect, useState } from "react";
import axios from "axios";
import ScoopOption from "./ScoopOption";
import ToppingOption from "./ToppingOption";

export default function Options({ optionType }) {
  const [items, setItems] = useState([]);

  useEffect(() => {
    axios
      .get(`http://localhost:3030/${optionType}`)
      .then((response) => setItems(response.data))
      .catch((error) => console.log(error));
  }, [optionType]);

  const ItemComponent = optionType === "scoops" ? ScoopOption : ToppingOption;

  const optionItems = items.map((item) => (
    <ItemComponent
      key={item.name}
      name={item.name}
      imagePath={item.imagePath}
    />
  ));

  return <div>{optionItems}</div>;
}
```

### Step 3: Create the Handler for the Toppings Endpoint

Update your mock service worker setup to handle the toppings endpoint:

```javascript
import { rest } from "msw";

export const handlers = [
  // existing handlers
  rest.get("http://localhost:3030/scoops", (req, res, ctx) => {
    return res(
      ctx.json([
        { name: "Chocolate", imagePath: "/images/chocolate.png" },
        { name: "Vanilla", imagePath: "/images/vanilla.png" },
      ])
    );
  }),
  rest.get("http://localhost:3030/toppings", (req, res, ctx) => {
    return res(
      ctx.json([
        { name: "Cherries", imagePath: "/images/cherries.png" },
        { name: "M&Ms", imagePath: "/images/m-and-ms.png" },
        { name: "Hot fudge", imagePath: "/images/hot-fudge.png" },
      ])
    );
  }),
];
```

### Step 4: Write the Test for Toppings

Add a test in `options.test.js` to verify the toppings are rendered correctly:

```javascript
import { render, screen } from "@testing-library/react";
import Options from "../Options";

test("displays image for each topping option from server", async () => {
  render(<Options optionType='toppings' />);

  // find images
  const images = await screen.findAllByRole("img", { name: /topping$/i });
  expect(images).toHaveLength(3);

  // confirm alt text of images
  const altText = images.map((img) => img.alt);
  expect(altText).toEqual([
    "Cherries topping",
    "M&Ms topping",
    "Hot fudge topping",
  ]);
});
```

### Summary

Here's a recap of what we did:

1. Created `ToppingOption` component to display topping images.
2. Updated `Options` component to render `ToppingOption` based on `optionType`.
3. Created a handler for the `toppings` endpoint in the mock service worker.
4. Wrote a test to ensure the toppings are rendered correctly.

This will verify that the `toppings` option type is handled correctly and that the images are displayed as expected. If you encounter any issues, check the console logs and ensure the endpoint handlers and test assertions match the expected outputs.

Good luck with the quiz, and let me know if you need any further assistance!

### Handling and Testing Error Responses with Mock Service Worker

In this section, we will cover how to handle error responses from the server using Mock Service Worker (MSW) and ensure our app responds correctly by displaying an error alert. We will follow these steps:

1. **Update the `Options` Component to Handle Errors**
2. **Create an `AlertBanner` Component**
3. **Write Tests for Error Responses**
4. **Override Handlers for Error Responses in Tests**
5. **Debugging Tools in Jest**

### Step 1: Update the `Options` Component to Handle Errors

First, we will update the `Options` component to handle errors by displaying an `AlertBanner` when an error occurs.

```javascript
import React, { useEffect, useState } from "react";
import axios from "axios";
import ScoopOption from "./ScoopOption";
import ToppingOption from "./ToppingOption";
import AlertBanner from "./AlertBanner";

export default function Options({ optionType }) {
  const [items, setItems] = useState([]);
  const [error, setError] = useState(false);

  useEffect(() => {
    axios
      .get(`http://localhost:3030/${optionType}`)
      .then((response) => setItems(response.data))
      .catch((error) => setError(true));
  }, [optionType]);

  if (error) {
    return <AlertBanner />;
  }

  const ItemComponent = optionType === "scoops" ? ScoopOption : ToppingOption;

  const optionItems = items.map((item) => (
    <ItemComponent
      key={item.name}
      name={item.name}
      imagePath={item.imagePath}
    />
  ));

  return <div>{optionItems}</div>;
}
```

### Step 2: Create an `AlertBanner` Component

Create a simple `AlertBanner` component that uses React Bootstrap's `Alert`.

```javascript
import React from "react";
import { Alert } from "react-bootstrap";

export default function AlertBanner({ message, variant }) {
  const alertMessage =
    message || "An unexpected error occurred. Please try again later.";
  const alertVariant = variant || "danger";

  return (
    <Alert variant={alertVariant} style={{ backgroundColor: "red" }}>
      {alertMessage}
    </Alert>
  );
}
```

### Step 3: Write Tests for Error Responses

Now, let's write tests to ensure that our application correctly handles error responses and displays the `AlertBanner`.

```javascript
import { render, screen } from "@testing-library/react";
import { rest } from "msw";
import { setupServer } from "msw/node";
import Options from "../Options";
import { server } from "../../../mocks/server";

// Override handlers for error responses
server.use(
  rest.get("http://localhost:3030/scoops", (req, res, ctx) => {
    return res(ctx.status(500));
  }),
  rest.get("http://localhost:3030/toppings", (req, res, ctx) => {
    return res(ctx.status(500));
  })
);

test("displays error alert when scoops endpoint returns an error", async () => {
  render(<Options optionType='scoops' />);

  const alerts = await screen.findAllByRole("alert");
  expect(alerts).toHaveLength(1);
  expect(alerts[0]).toHaveTextContent(
    "An unexpected error occurred. Please try again later."
  );
});

test("displays error alert when toppings endpoint returns an error", async () => {
  render(<Options optionType='toppings' />);

  const alerts = await screen.findAllByRole("alert");
  expect(alerts).toHaveLength(1);
  expect(alerts[0]).toHaveTextContent(
    "An unexpected error occurred. Please try again later."
  );
});
```

### Step 4: Override Handlers for Error Responses in Tests

We have already included the overridden handlers for the error responses in the test setup above. Make sure your server setup in `mocks/server.js` looks like this:

```javascript
import { setupServer } from "msw/node";
import { handlers } from "./handlers";

export const server = setupServer(...handlers);

// Setup API mocking before all tests.
beforeAll(() => server.listen());

// Reset any request handlers that we may add during the tests,
// so they don't affect other tests.
afterEach(() => server.resetHandlers());

// Clean up after the tests are finished.
afterAll(() => server.close());
```

### Step 5: Debugging Tools in Jest

Jest provides useful tools to isolate and debug tests. Here are some of the key techniques:

- **Run a specific test file:** You can run a specific test file by providing the file path.

  ```bash
  npm test -- options.test.js
  ```

- **Run a specific test within a file:** Use `.only` to run a specific test within a test file.

  ```javascript
  test.only("displays error alert when scoops endpoint returns an error", async () => {
    render(<Options optionType='scoops' />);

    const alerts = await screen.findAllByRole("alert");
    expect(alerts).toHaveLength(1);
    expect(alerts[0]).toHaveTextContent(
      "An unexpected error occurred. Please try again later."
    );
  });
  ```

- **Debugging failed tests:** Use `console.log` statements within your tests or components to inspect values and diagnose issues.

### Summary

We have implemented error handling for our `Options` component, created an `AlertBanner` component for displaying error messages, and wrote tests to ensure our application handles server errors correctly. Additionally, we introduced some useful Jest debugging techniques.

By following these steps, you can effectively handle and test error responses in your React application using Mock Service Worker and ensure a robust user experience even in the face of server errors.
