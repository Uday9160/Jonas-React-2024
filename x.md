- **Creating a C# Class**

  - Use the `class` keyword followed by the class name.
  - No semicolons after the class declaration.
  - Class names should start with a capital letter by convention.

  ```csharp
  public class Rectangle
  {
      // Class definition
  }
  ```

- **Fields of a Class**

  - Fields are variables that belong to an object of a class.
  - Each instance of the class can have different values for its fields.
  - Example for `Rectangle` class with `width` and `height` fields:

  ```csharp
  public class Rectangle
  {
      public int width;
      public int height;
  }
  ```

- **Creating an Object**

  - Instantiate an object using the `new` keyword.
  - Default parameterless constructor is used if no custom constructor is defined.

  ```csharp
  Rectangle myRectangle = new Rectangle();
  ```

- **Default Constructor**

  - Automatically created if no constructors are defined.
  - Initializes fields to their default values (e.g., `int` fields to `0`).

- **Field Initialization**

  - Fields are automatically initialized to their default values if not explicitly set.
  - Example:

  ```csharp
  Rectangle rect = new Rectangle();
  Console.WriteLine(rect.width);  // Output: 0
  Console.WriteLine(rect.height); // Output: 0
  ```

- **Compilation Errors and Field Accessibility**
  - Attempting to use uninitialized variables results in a compilation error.
  - Fields have default values if not initialized: integers default to `0`.
  - Accessing fields with insufficient protection levels results in compilation errors.

[End of Notes]



Advanced React Interview Questions with In-Depth Answers (Set 1)


---

1. How do you handle memory leaks in React applications?

Memory leaks occur when memory that is no longer needed is not released, leading to increased memory usage and degraded app performance. In React, memory leaks often happen when side effects are not properly cleaned up.

Common scenarios that cause memory leaks:

Forgetting to clear timers (e.g. setTimeout, setInterval)

Not unsubscribing from subscriptions like WebSocket, EventEmitter

Trying to update state after a component is unmounted (e.g., inside a promise or async function)


React’s solution: useEffect Cleanup React provides a cleanup function inside useEffect which is called during component unmounting.

useEffect(() => {
  const timer = setInterval(() => console.log("Running"), 1000);
  return () => clearInterval(timer); // Cleanup
}, []);

Async example with memory leak prevention:

useEffect(() => {
  let isMounted = true;

  fetch('/api/data').then(res => res.json()).then(data => {
    if (isMounted) setData(data);
  });

  return () => { isMounted = false; };
}, []);

Best Practices:

Always clean up subscriptions and timers

Guard async operations with isMounted

Use AbortController to cancel fetch requests



---

2. How do you optimize React performance in large applications?

Performance bottlenecks usually arise from unnecessary re-renders, large data rendering, or inefficient component updates.

Optimization Techniques:

1. Code Splitting

Use React.lazy() and Suspense to load components lazily


const LazyComponent = React.lazy(() => import('./HeavyComponent'));


2. Memoization

Use React.memo to memoize functional components

Use useMemo to memoize computed values

Use useCallback to memoize functions



3. Avoid Unnecessary Re-renders

Use stable keys

Avoid passing anonymous functions to child components

Use shouldComponentUpdate / React.memo



4. Virtualize Large Lists

Use libraries like react-window or react-virtualized to render only visible rows



5. Debounce / Throttle Events

Prevent functions like scroll or input handlers from firing too often



6. Profiling

Use React DevTools Profiler to analyze rendering performance





---

3. What is the difference between useEffect and useLayoutEffect?

Key Difference: useLayoutEffect blocks the browser from painting until the code inside is executed. It’s useful when layout calculation or DOM mutation is needed before paint.

Example:

useLayoutEffect(() => {
  const height = ref.current.offsetHeight;
  console.log("Height before paint:", height);
}, []);

Use useLayoutEffect sparingly—it can block rendering and degrade performance.


---

4. How do you persist state between page reloads in React?

Options:

1. Local Storage / Session Storage



const [theme, setTheme] = useState(() => {
  return localStorage.getItem("theme") || "light";
});

useEffect(() => {
  localStorage.setItem("theme", theme);
}, [theme]);

2. Redux Persist

Automatically saves Redux state to localStorage or sessionStorage.

Example setup using redux-persist.



3. IndexedDB

Used for larger or more complex structured data

Libraries like idb-keyval simplify the API



4. URL parameters or Cookies

For short-term or per-session persistence





---

5. What are controlled vs uncontrolled components in React?

Controlled Components

Form data is handled by React state

One source of truth (component state)

More predictable and easy to validate


const [name, setName] = useState("");
<input value={name} onChange={e => setName(e.target.value)} />

Uncontrolled Components

Data is handled by the DOM itself

Accessed via refs

Useful when you don’t need to monitor changes on every keystroke


const inputRef = useRef();
<input ref={inputRef} />

When to use what?

Controlled: When real-time validation or dynamic behavior is needed

Uncontrolled: Simple or third-party form integrations



---

6. What happens when you update state inside useEffect?

Scenario: Updating state inside useEffect can lead to a re-render. If the effect depends on the updated state, this can cause an infinite loop.

useEffect(() => {
  setCount(count + 1); // Infinite loop if count is in dependency
}, [count]);

Correct Way:

useEffect(() => {
  setCount(prev => prev + 1); // Safe if [] is dependency
}, []);

Always check your dependencies carefully. Use functional updates if you are using stale state.


---

7. How do you handle errors gracefully in React components?

Options:

1. Error Boundaries (class-based)

Catches errors during rendering and lifecycle methods

Does not catch errors in event handlers or async code




class ErrorBoundary extends React.Component {
  constructor() {
    super();
    this.state = { hasError: false };
  }
  static getDerivedStateFromError() {
    return { hasError: true };
  }
  componentDidCatch(error, info) {
    logErrorToMonitoring(error, info);
  }
  render() {
    return this.state.hasError ? <FallbackUI /> : this.props.children;
  }
}

2. Try-Catch for Async Functions



try {
  const result = await fetchData();
} catch (e) {
  handleError(e);
}


---

8. How do you manage deeply nested component state without prop drilling?

Techniques:

1. React Context API

Best for global or semi-global state (like theme, auth)



2. Redux / Zustand / Recoil

For large-scale state management



3. Custom Hooks

Abstract and reuse logic




Context API Example:

const ThemeContext = React.createContext();

const App = () => (
  <ThemeContext.Provider value={{ darkMode: true }}>
    <Dashboard />
  </ThemeContext.Provider>
);

const Dashboard = () => {
  const { darkMode } = useContext(ThemeContext);
};


---

9. Why is the key prop important in React lists?

The key prop uniquely identifies list elements.

Why it matters:

Helps React determine what changed

Prevents unnecessary re-renders

Ensures correct element mapping


Incorrect (causes re-rendering bugs):

items.map((item, index) => <li key={index}>{item}</li>);

Correct:

items.map(item => <li key={item.id}>{item.name}</li>);

Tip: Never use array index as key unless list is static and never changes.


---

10. How does the React reconciliation algorithm work?

Reconciliation is the process by which React updates the DOM efficiently after state or prop changes.

Steps React follows:

1. Creates a new Virtual DOM


2. Compares it with the previous Virtual DOM (diffing)


3. Calculates the minimum set of changes


4. Applies those changes to the actual DOM (patching)



Heuristics used:

Elements of the same type: Keep and update attributes

Different types: Replace entire subtree

Children with keys: Optimize insertions/deletions


How to help React reconcile efficiently:

Use stable, unique key props

Avoid unnecessary nested components

Minimize side-effects inside render


Advanced React Interview Questions with In-Depth Answers (Set 1)


---

1. How do you handle memory leaks in React applications?

Memory leaks occur when memory that is no longer needed is not released, leading to increased memory usage and degraded app performance. In React, memory leaks often happen when side effects are not properly cleaned up.

Common scenarios that cause memory leaks:

Forgetting to clear timers (e.g. setTimeout, setInterval)

Not unsubscribing from subscriptions like WebSocket, EventEmitter

Trying to update state after a component is unmounted (e.g., inside a promise or async function)


React’s solution: useEffect Cleanup React provides a cleanup function inside useEffect which is called during component unmounting.

useEffect(() => {
  const timer = setInterval(() => console.log("Running"), 1000);
  return () => clearInterval(timer); // Cleanup
}, []);

Async example with memory leak prevention:

useEffect(() => {
  let isMounted = true;

  fetch('/api/data').then(res => res.json()).then(data => {
    if (isMounted) setData(data);
  });

  return () => { isMounted = false; };
}, []);

Best Practices:

Always clean up subscriptions and timers

Guard async operations with isMounted

Use AbortController to cancel fetch requests



---

2. How do you optimize React performance in large applications?

Performance bottlenecks usually arise from unnecessary re-renders, large data rendering, or inefficient component updates.

Optimization Techniques:

1. Code Splitting

Use React.lazy() and Suspense to load components lazily


const LazyComponent = React.lazy(() => import('./HeavyComponent'));


2. Memoization

Use React.memo to memoize functional components

Use useMemo to memoize computed values

Use useCallback to memoize functions



3. Avoid Unnecessary Re-renders

Use stable keys

Avoid passing anonymous functions to child components

Use shouldComponentUpdate / React.memo



4. Virtualize Large Lists

Use libraries like react-window or react-virtualized to render only visible rows



5. Debounce / Throttle Events

Prevent functions like scroll or input handlers from firing too often



6. Profiling

Use React DevTools Profiler to analyze rendering performance





---

3. What is the difference between useEffect and useLayoutEffect?

Key Difference: useLayoutEffect blocks the browser from painting until the code inside is executed. It’s useful when layout calculation or DOM mutation is needed before paint.

Example:

useLayoutEffect(() => {
  const height = ref.current.offsetHeight;
  console.log("Height before paint:", height);
}, []);

Use useLayoutEffect sparingly—it can block rendering and degrade performance.


---

4. How do you persist state between page reloads in React?

Options:

1. Local Storage / Session Storage



const [theme, setTheme] = useState(() => {
  return localStorage.getItem("theme") || "light";
});

useEffect(() => {
  localStorage.setItem("theme", theme);
}, [theme]);

2. Redux Persist

Automatically saves Redux state to localStorage or sessionStorage.

Example setup using redux-persist.



3. IndexedDB

Used for larger or more complex structured data

Libraries like idb-keyval simplify the API



4. URL parameters or Cookies

For short-term or per-session persistence





---

5. What are controlled vs uncontrolled components in React?

Controlled Components

Form data is handled by React state

One source of truth (component state)

More predictable and easy to validate


const [name, setName] = useState("");
<input value={name} onChange={e => setName(e.target.value)} />

Uncontrolled Components

Data is handled by the DOM itself

Accessed via refs

Useful when you don’t need to monitor changes on every keystroke


const inputRef = useRef();
<input ref={inputRef} />

When to use what?

Controlled: When real-time validation or dynamic behavior is needed

Uncontrolled: Simple or third-party form integrations



---

6. What happens when you update state inside useEffect?

Scenario: Updating state inside useEffect can lead to a re-render. If the effect depends on the updated state, this can cause an infinite loop.

useEffect(() => {
  setCount(count + 1); // Infinite loop if count is in dependency
}, [count]);

Correct Way:

useEffect(() => {
  setCount(prev => prev + 1); // Safe if [] is dependency
}, []);

Always check your dependencies carefully. Use functional updates if you are using stale state.


---

7. How do you handle errors gracefully in React components?

Options:

1. Error Boundaries (class-based)

Catches errors during rendering and lifecycle methods

Does not catch errors in event handlers or async code




class ErrorBoundary extends React.Component {
  constructor() {
    super();
    this.state = { hasError: false };
  }
  static getDerivedStateFromError() {
    return { hasError: true };
  }
  componentDidCatch(error, info) {
    logErrorToMonitoring(error, info);
  }
  render() {
    return this.state.hasError ? <FallbackUI /> : this.props.children;
  }
}

2. Try-Catch for Async Functions



try {
  const result = await fetchData();
} catch (e) {
  handleError(e);
}


---

8. How do you manage deeply nested component state without prop drilling?

Techniques:

1. React Context API

Best for global or semi-global state (like theme, auth)



2. Redux / Zustand / Recoil

For large-scale state management



3. Custom Hooks

Abstract and reuse logic




Context API Example:

const ThemeContext = React.createContext();

const App = () => (
  <ThemeContext.Provider value={{ darkMode: true }}>
    <Dashboard />
  </ThemeContext.Provider>
);

const Dashboard = () => {
  const { darkMode } = useContext(ThemeContext);
};


---

9. Why is the key prop important in React lists?

The key prop uniquely identifies list elements.

Why it matters:

Helps React determine what changed

Prevents unnecessary re-renders

Ensures correct element mapping


Incorrect (causes re-rendering bugs):

items.map((item, index) => <li key={index}>{item}</li>);

Correct:

items.map(item => <li key={item.id}>{item.name}</li>);

Tip: Never use array index as key unless list is static and never changes.


---

10. How does the React reconciliation algorithm work?

Reconciliation is the process by which React updates the DOM efficiently after state or prop changes.

Steps React follows:

1. Creates a new Virtual DOM


2. Compares it with the previous Virtual DOM (diffing)


3. Calculates the minimum set of changes


4. Applies those changes to the actual DOM (patching)



Heuristics used:

Elements of the same type: Keep and update attributes

Different types: Replace entire subtree

Children with keys: Optimize insertions/deletions


How to help React reconcile efficiently:

Use stable, unique key props

Avoid unnecessary nested components

Minimize side-effects inside render



---

Would you like to continue with detailed answers for questions 11–20 next?


---

Would you like to continue with detailed answers for questions 11–20 next?



Here are the fully detailed answers for the first 10 React advanced interview questions, updated with deep concepts, code examples, and explanations. Let me know when you're ready for questions 11–20, and I’ll continue the same in-depth format.

