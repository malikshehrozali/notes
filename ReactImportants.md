# ⚛️ React Complete Notes — Developer Revision Guide

> Crafted for developers returning to React after backend work. Covers all essential hooks including **React 19 new hooks**, state management with Redux & Zustand, with explanations, use cases, and real code examples.

> ✅ **React Version:** This guide covers up to **React 19.2** (released 2025)

---

## 📚 Table of Contents

1. [Core Concepts Quick Recap](#core-concepts)
2. [useState](#usestate)
3. [useEffect](#useeffect)
4. [useRef](#useref)
5. [useContext](#usecontext)
6. [useReducer](#usereducer)
7. [useMemo](#usememo)
8. [useCallback](#usecallback)
9. [useLayoutEffect](#uselayouteffect)
10. [useId](#useid)
11. [useTransition](#usetransition)
12. [useDeferredValue](#usedeferredvalue)
13. [useImperativeHandle](#useimperativehandle)
14. [Custom Hooks](#custom-hooks)
15. [⚡ React 19 — New Hooks](#react-19-hooks)
    - [use](#use-hook)
    - [useActionState](#useactionstate)
    - [useOptimistic](#useoptimistic)
    - [useFormStatus](#useformstatus)
16. [React Redux](#react-redux)
17. [Zustand](#zustand)
18. [When to Use What](#when-to-use-what)

---

## 🧠 Core Concepts Quick Recap <a name="core-concepts"></a>

| Concept        | One-liner                                    |
| -------------- | -------------------------------------------- |
| Component      | A function that returns JSX                  |
| Props          | Read-only inputs passed from parent to child |
| State          | Mutable data local to a component            |
| Re-render      | Happens when state or props change           |
| Virtual DOM    | React's in-memory copy of the real DOM       |
| Reconciliation | Process of diffing old vs new Virtual DOM    |

```jsx
// Functional Component — the modern way
const Greeting = ({ name }) => {
  return <h1>Hello, {name}!</h1>;
};
```

---

## 1. useState <a name="usestate"></a>

### What it is

`useState` lets you add **local reactive state** to a functional component. When state changes, the component re-renders.

### Use Cases

- Form inputs (text, checkbox, select)
- Toggle (show/hide modal, dark mode)
- Counter, list of items
- Any value that should cause UI to update when it changes

### Syntax

```js
const [state, setState] = useState(initialValue);
```

### Code Examples

```jsx
import { useState } from "react";

// --- Example 1: Simple Counter ---
const Counter = () => {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount((prev) => prev - 1)}>Decrement</button>
      {/* Use functional update when new state depends on old state */}
    </div>
  );
};

// --- Example 2: Form Input ---
const LoginForm = () => {
  const [form, setForm] = useState({ email: "", password: "" });

  const handleChange = (e) => {
    setForm((prev) => ({ ...prev, [e.target.name]: e.target.value }));
    // Spread old state, update only the changed field
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log(form);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input name="email" value={form.email} onChange={handleChange} />
      <input
        name="password"
        type="password"
        value={form.password}
        onChange={handleChange}
      />
      <button type="submit">Login</button>
    </form>
  );
};

// --- Example 3: Toggle ---
const Modal = () => {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <>
      <button onClick={() => setIsOpen(true)}>Open Modal</button>
      {isOpen && (
        <div className="modal">
          <p>Hello from Modal!</p>
          <button onClick={() => setIsOpen(false)}>Close</button>
        </div>
      )}
    </>
  );
};
```

### ⚠️ Common Mistakes

```jsx
// ❌ WRONG — mutating state directly (React won't detect change)
const [items, setItems] = useState([]);
items.push("new item"); // This does NOT trigger re-render

// ✅ CORRECT — always return a new reference
setItems((prev) => [...prev, "new item"]);

// ❌ WRONG — stale closure bug
setCount(count + 1); // If called multiple times, 'count' may be stale

// ✅ CORRECT — use functional update
setCount((prev) => prev + 1);
```

---

## 2. useEffect <a name="useeffect"></a>

### What it is

`useEffect` lets you perform **side effects** in function components — things like API calls, subscriptions, timers, or manually touching the DOM.

### Use Cases

- Fetch data when component mounts
- Subscribe/unsubscribe to events (WebSocket, resize)
- Update document title
- Sync state with localStorage
- Run code when a specific value changes

### Syntax

```js
useEffect(() => {
  // side effect code

  return () => {
    // cleanup (optional) — runs before next effect or on unmount
  };
}, [dependencies]); // dependency array controls when effect runs
```

### Dependency Array Rules

| Dependency Array         | When Effect Runs                      |
| ------------------------ | ------------------------------------- |
| No array `useEffect(fn)` | After EVERY render                    |
| Empty array `[]`         | Once — on mount only                  |
| `[value]`                | On mount + every time `value` changes |

### Code Examples

```jsx
import { useState, useEffect } from "react";

// --- Example 1: Fetch on mount ---
const UserList = () => {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const fetchUsers = async () => {
      try {
        const res = await fetch("https://jsonplaceholder.typicode.com/users");
        const data = await res.json();
        setUsers(data);
      } finally {
        setLoading(false);
      }
    };

    fetchUsers();
  }, []); // [] = run once on mount

  if (loading) return <p>Loading...</p>;
  return (
    <ul>
      {users.map((u) => (
        <li key={u.id}>{u.name}</li>
      ))}
    </ul>
  );
};

// --- Example 2: Cleanup (Event Listener) ---
const WindowWidth = () => {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    const handleResize = () => setWidth(window.innerWidth);
    window.addEventListener("resize", handleResize);

    return () => {
      window.removeEventListener("resize", handleResize); // cleanup
    };
  }, []); // runs once, cleanup on unmount

  return <p>Window width: {width}px</p>;
};

// --- Example 3: Refetch when ID changes ---
const UserProfile = ({ userId }) => {
  const [user, setUser] = useState(null);

  useEffect(() => {
    let cancelled = false; // prevent state update on unmounted component

    fetch(`/api/users/${userId}`)
      .then((res) => res.json())
      .then((data) => {
        if (!cancelled) setUser(data);
      });

    return () => {
      cancelled = true;
    };
  }, [userId]); // re-run whenever userId changes

  return <div>{user ? user.name : "Loading..."}</div>;
};
```

### ⚠️ Common Mistakes

```jsx
// ❌ Missing dependency causes stale closure
useEffect(() => {
  setCount(count + 1); // 'count' is stale if not in deps
}, []);

// ✅ Add all used values to deps array
useEffect(() => {
  setCount(c => c + 1); // or use functional update to avoid stale closure
}, []);

// ❌ Async function directly in useEffect
useEffect(async () => { ... }); // creates a Promise that React ignores

// ✅ Define async inside
useEffect(() => {
  const load = async () => { ... };
  load();
}, []);
```

---

## 3. useRef <a name="useref"></a>

### What it is

`useRef` returns a **mutable ref object** whose `.current` property is initialized with the given value. Unlike state, **changing `.current` does NOT cause a re-render**.

### Use Cases

- Access DOM elements directly (focus, scroll, measure)
- Store mutable values that persist across renders without causing re-renders
- Store previous value of a state
- Hold timer/interval IDs

### Syntax

```js
const ref = useRef(initialValue);
// Access: ref.current
```

### Code Examples

```jsx
import { useRef, useEffect, useState } from "react";

// --- Example 1: Focus an input on mount ---
const AutoFocusInput = () => {
  const inputRef = useRef(null);

  useEffect(() => {
    inputRef.current.focus(); // directly call DOM method
  }, []);

  return <input ref={inputRef} placeholder="I'm auto-focused!" />;
};

// --- Example 2: Store interval ID to clear it ---
const Stopwatch = () => {
  const [seconds, setSeconds] = useState(0);
  const intervalRef = useRef(null); // stores interval ID

  const start = () => {
    intervalRef.current = setInterval(() => {
      setSeconds((prev) => prev + 1);
    }, 1000);
  };

  const stop = () => {
    clearInterval(intervalRef.current); // clear using stored ID
  };

  return (
    <div>
      <p>{seconds}s</p>
      <button onClick={start}>Start</button>
      <button onClick={stop}>Stop</button>
    </div>
  );
};

// --- Example 3: Track previous state value ---
const PreviousValue = ({ count }) => {
  const prevCountRef = useRef();

  useEffect(() => {
    prevCountRef.current = count; // update ref AFTER render
  });

  return (
    <p>
      Current: {count} | Previous: {prevCountRef.current}
    </p>
  );
};
```

---

## 4. useContext <a name="usecontext"></a>

### What it is

`useContext` lets you **consume a React Context** without wrapping in a Consumer component. It solves prop drilling — passing data through many layers.

### Use Cases

- Theme (dark/light mode)
- Authenticated user info
- Language/locale settings
- Any global data needed across many components

### Syntax

```js
const value = useContext(MyContext);
```

### Code Examples

```jsx
import { createContext, useContext, useState } from "react";

// --- Step 1: Create Context ---
const ThemeContext = createContext(null);

// --- Step 2: Create Provider ---
export const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState("light");

  const toggleTheme = () => {
    setTheme((prev) => (prev === "light" ? "dark" : "light"));
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

// --- Step 3: Custom hook (best practice) ---
export const useTheme = () => {
  const context = useContext(ThemeContext);
  if (!context) throw new Error("useTheme must be used within ThemeProvider");
  return context;
};

// --- Step 4: Consume in any child component ---
const Header = () => {
  const { theme, toggleTheme } = useTheme();

  return (
    <header style={{ background: theme === "dark" ? "#333" : "#fff" }}>
      <button onClick={toggleTheme}>Toggle Theme</button>
    </header>
  );
};

// --- Step 5: Wrap your app ---
const App = () => (
  <ThemeProvider>
    <Header />
  </ThemeProvider>
);
```

### ⚠️ Performance Note

Every consumer re-renders when the context value changes. Split contexts if different parts of the app need different data.

---

## 5. useReducer <a name="usereducer"></a>

### What it is

`useReducer` is an alternative to `useState` for **complex state logic**. It uses a reducer function (like Redux but local) — `(state, action) => newState`.

### Use Cases

- Multiple related state values that change together
- Next state depends on complex logic
- When you find yourself writing many `setState` calls
- State machines, shopping carts, forms with many fields

### Syntax

```js
const [state, dispatch] = useReducer(reducer, initialState);
```

### Code Examples

```jsx
import { useReducer } from "react";

// --- Example 1: Shopping Cart ---
const initialState = {
  items: [],
  total: 0,
};

const cartReducer = (state, action) => {
  switch (action.type) {
    case "ADD_ITEM":
      return {
        ...state,
        items: [...state.items, action.payload],
        total: state.total + action.payload.price,
      };
    case "REMOVE_ITEM":
      const item = state.items.find((i) => i.id === action.payload);
      return {
        ...state,
        items: state.items.filter((i) => i.id !== action.payload),
        total: state.total - item.price,
      };
    case "CLEAR_CART":
      return initialState;
    default:
      return state;
  }
};

const Cart = () => {
  const [cart, dispatch] = useReducer(cartReducer, initialState);

  const addItem = () => {
    dispatch({
      type: "ADD_ITEM",
      payload: { id: 1, name: "Apple", price: 1.5 },
    });
  };

  const removeItem = (id) => {
    dispatch({ type: "REMOVE_ITEM", payload: id });
  };

  return (
    <div>
      <h2>Cart (Total: ${cart.total.toFixed(2)})</h2>
      <button onClick={addItem}>Add Apple</button>
      <ul>
        {cart.items.map((item) => (
          <li key={item.id}>
            {item.name} - ${item.price}
            <button onClick={() => removeItem(item.id)}>Remove</button>
          </li>
        ))}
      </ul>
      <button onClick={() => dispatch({ type: "CLEAR_CART" })}>Clear</button>
    </div>
  );
};
```

### useState vs useReducer

| Scenario                                 | Use          |
| ---------------------------------------- | ------------ |
| 1-2 simple values                        | `useState`   |
| Complex object with many fields          | `useReducer` |
| State transitions with logic             | `useReducer` |
| New state depends on multiple old values | `useReducer` |

---

## 6. useMemo <a name="usememo"></a>

### What it is

`useMemo` **memoizes (caches) the result of an expensive calculation**. It only recalculates when its dependencies change. Prevents re-running heavy logic on every render.

### Use Cases

- Expensive computations (sorting, filtering large lists)
- Derived data that depends on props/state
- Preventing unnecessary recalculations
- Reference equality for objects passed as props (to avoid child re-renders)

### Syntax

```js
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

### Code Examples

```jsx
import { useState, useMemo } from "react";

// --- Example 1: Filter a large list ---
const ProductList = ({ products }) => {
  const [search, setSearch] = useState("");
  const [sortBy, setSortBy] = useState("name");

  // Only recalculates when products, search, or sortBy changes
  const filteredAndSorted = useMemo(() => {
    console.log("Recalculating..."); // watch when this runs
    return products
      .filter((p) => p.name.toLowerCase().includes(search.toLowerCase()))
      .sort((a, b) => (a[sortBy] > b[sortBy] ? 1 : -1));
  }, [products, search, sortBy]);

  return (
    <div>
      <input
        value={search}
        onChange={(e) => setSearch(e.target.value)}
        placeholder="Search..."
      />
      <select value={sortBy} onChange={(e) => setSortBy(e.target.value)}>
        <option value="name">Name</option>
        <option value="price">Price</option>
      </select>
      <ul>
        {filteredAndSorted.map((p) => (
          <li key={p.id}>
            {p.name} - ${p.price}
          </li>
        ))}
      </ul>
    </div>
  );
};

// --- Example 2: Expensive calculation ---
const fibonacci = (n) => (n <= 1 ? n : fibonacci(n - 1) + fibonacci(n - 2));

const FibCalculator = () => {
  const [n, setN] = useState(10);
  const [theme, setTheme] = useState("light"); // unrelated state

  // Without useMemo, fibonacci runs on EVERY render (even theme changes)
  const fib = useMemo(() => fibonacci(n), [n]); // only recalculates when n changes

  return (
    <div>
      <input
        type="number"
        value={n}
        onChange={(e) => setN(Number(e.target.value))}
      />
      <p>
        Fibonacci({n}) = {fib}
      </p>
      <button
        onClick={() => setTheme((t) => (t === "light" ? "dark" : "light"))}
      >
        Toggle Theme (won't recalculate fib)
      </button>
    </div>
  );
};
```

### ⚠️ Don't Over-Use

```jsx
// ❌ Unnecessary — simple operations are fast enough
const doubled = useMemo(() => value * 2, [value]);

// ✅ useMemo is worth it for genuinely expensive work
const result = useMemo(() => processMillionRecords(data), [data]);
```

---

## 7. useCallback <a name="usecallback"></a>

### What it is

`useCallback` **memoizes a function** so its reference stays the same across renders. Useful when passing callbacks to child components that are wrapped in `React.memo`.

### Use Cases

- Passing stable function references to `React.memo` children
- Functions used in `useEffect` dependency arrays
- Event handlers in performance-sensitive lists

### Syntax

```js
const memoizedFn = useCallback(() => {
  doSomething(a, b);
}, [a, b]);
```

### Code Examples

```jsx
import { useState, useCallback, memo } from "react";

// --- Example 1: Prevent child re-renders ---

// Child wrapped in memo — only re-renders if props change
const Button = memo(({ onClick, label }) => {
  console.log(`Button "${label}" rendered`);
  return <button onClick={onClick}>{label}</button>;
});

const Parent = () => {
  const [count, setCount] = useState(0);
  const [text, setText] = useState("");

  // Without useCallback: new function reference on every render → Button re-renders
  // With useCallback: same reference → Button does NOT re-render when text changes
  const handleIncrement = useCallback(() => {
    setCount((prev) => prev + 1);
  }, []); // no deps — this function never changes

  return (
    <div>
      <p>Count: {count}</p>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      <Button onClick={handleIncrement} label="Increment" />
    </div>
  );
};

// --- Example 2: Stable function for useEffect ---
const Search = ({ query }) => {
  const [results, setResults] = useState([]);

  const fetchResults = useCallback(async () => {
    const data = await fetch(`/api/search?q=${query}`).then((r) => r.json());
    setResults(data);
  }, [query]); // only changes when query changes

  useEffect(() => {
    fetchResults();
  }, [fetchResults]); // safe to add as dependency now

  return (
    <ul>
      {results.map((r) => (
        <li key={r.id}>{r.title}</li>
      ))}
    </ul>
  );
};
```

### useMemo vs useCallback

| Hook          | Memoizes             | Returns                |
| ------------- | -------------------- | ---------------------- |
| `useMemo`     | Result of a function | The computed value     |
| `useCallback` | The function itself  | The function reference |

```js
// These are equivalent:
useCallback(fn, deps) === useMemo(() => fn, deps);
```

---

## 8. useLayoutEffect <a name="uselayouteffect"></a>

### What it is

Like `useEffect`, but fires **synchronously after DOM mutations** and **before the browser paints**. Use it when you need to read layout and synchronously re-render.

### Use Cases

- Measuring DOM element dimensions before paint
- Preventing visual flicker (tooltip position, scroll restoration)
- Animations that need DOM measurements

### Code Example

```jsx
import { useLayoutEffect, useRef, useState } from "react";

const Tooltip = ({ text }) => {
  const tooltipRef = useRef(null);
  const [position, setPosition] = useState({ top: 0, left: 0 });

  useLayoutEffect(() => {
    // Runs after DOM update but BEFORE browser paints
    // No flicker — position is set before the user sees anything
    const { width, height } = tooltipRef.current.getBoundingClientRect();
    setPosition({
      top: -height - 8,
      left: -width / 2,
    });
  }, []);

  return (
    <div ref={tooltipRef} style={{ position: "absolute", ...position }}>
      {text}
    </div>
  );
};
```

### useEffect vs useLayoutEffect

|                  | useEffect                | useLayoutEffect       |
| ---------------- | ------------------------ | --------------------- |
| Timing           | After paint              | Before paint          |
| Use for          | API calls, subscriptions | DOM measurements      |
| Risk of blocking | No                       | Yes (can block paint) |

> **Rule:** Default to `useEffect`. Switch to `useLayoutEffect` only if you see a visual flicker.

---

## 9. useId <a name="useid"></a>

### What it is

`useId` generates a **unique, stable ID** for accessibility attributes. IDs are consistent between server and client (important for SSR/hydration).

### Use Cases

- Linking `<label>` to `<input>` with `htmlFor`
- ARIA attributes (`aria-labelledby`, `aria-describedby`)
- Any element needing a unique ID

### Code Example

```jsx
import { useId } from "react";

const FormField = ({ label, type = "text" }) => {
  const id = useId(); // generates something like ":r0:", ":r1:", etc.

  return (
    <div>
      <label htmlFor={id}>{label}</label>
      <input id={id} type={type} />
    </div>
  );
};

// Usage — each instance gets its own unique ID
const Form = () => (
  <form>
    <FormField label="Email" type="email" />
    <FormField label="Password" type="password" />
  </form>
);
```

---

## 10. useTransition <a name="usetransition"></a>

### What it is

`useTransition` lets you **mark some state updates as non-urgent**. Urgent updates (typing, clicking) happen first; transitions are deferred, keeping the UI responsive.

### Use Cases

- Filtering/searching large lists (keep input responsive)
- Tab switching with heavy content
- Any heavy update that could block input

### Code Example

```jsx
import { useState, useTransition } from "react";

const SearchPage = () => {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);
  const [isPending, startTransition] = useTransition();

  const handleSearch = (e) => {
    const value = e.target.value;
    setQuery(value); // urgent — input updates immediately

    startTransition(() => {
      // non-urgent — React can interrupt this to handle user input
      const filtered = heavyFilter(value); // e.g., filtering 10,000 items
      setResults(filtered);
    });
  };

  return (
    <div>
      <input value={query} onChange={handleSearch} placeholder="Search..." />
      {isPending && <p>Updating results...</p>}
      <ul>
        {results.map((r) => (
          <li key={r.id}>{r.name}</li>
        ))}
      </ul>
    </div>
  );
};
```

---

## 11. useDeferredValue <a name="usedeferredvalue"></a>

### What it is

`useDeferredValue` defers re-rendering a part of the UI. Similar to `useTransition` but used when you **don't control the state update** (e.g., it comes from a prop).

### Code Example

```jsx
import { useState, useDeferredValue, memo } from "react";

const HeavyList = memo(({ query }) => {
  // This component is expensive to render
  const items = filterItems(query); // imagine 10k items
  return (
    <ul>
      {items.map((i) => (
        <li key={i.id}>{i.name}</li>
      ))}
    </ul>
  );
});

const App = () => {
  const [query, setQuery] = useState("");
  const deferredQuery = useDeferredValue(query); // lags behind 'query'

  return (
    <div>
      <input value={query} onChange={(e) => setQuery(e.target.value)} />
      {/* Input stays snappy; HeavyList updates with slight delay */}
      <HeavyList query={deferredQuery} />
    </div>
  );
};
```

---

## 12. useImperativeHandle <a name="useimperativehandle"></a>

### What it is

Used with `forwardRef` to **customize what a parent can access via a ref**. Instead of exposing the entire DOM node, you expose a controlled API.

### Use Cases

- Custom component libraries (expose `focus()`, `reset()`, `scroll()`)
- When parent needs to trigger behavior in child

### Code Example

```jsx
import { forwardRef, useRef, useImperativeHandle } from "react";

// Child exposes only specific methods
const FancyInput = forwardRef((props, ref) => {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => ({
    focus: () => inputRef.current.focus(),
    clear: () => {
      inputRef.current.value = "";
    },
    // Parent cannot access inputRef.current directly — only these methods
  }));

  return <input ref={inputRef} {...props} />;
});

// Parent
const Form = () => {
  const inputRef = useRef(null);

  return (
    <div>
      <FancyInput ref={inputRef} placeholder="Type here" />
      <button onClick={() => inputRef.current.focus()}>Focus</button>
      <button onClick={() => inputRef.current.clear()}>Clear</button>
    </div>
  );
};
```

---

## 13. Custom Hooks <a name="custom-hooks"></a>

### What it is

Custom hooks are **functions starting with `use`** that call other hooks. They let you extract and reuse stateful logic across components.

### Use Cases

- Reusable data fetching logic
- Form state management
- Local storage sync
- Window events

### Code Examples

```jsx
// --- useFetch: Generic data fetching hook ---
import { useState, useEffect } from "react";

const useFetch = (url) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let cancelled = false;
    setLoading(true);
    setError(null);

    fetch(url)
      .then((res) => {
        if (!res.ok) throw new Error(`HTTP ${res.status}`);
        return res.json();
      })
      .then((data) => {
        if (!cancelled) setData(data);
      })
      .catch((err) => {
        if (!cancelled) setError(err.message);
      })
      .finally(() => {
        if (!cancelled) setLoading(false);
      });

    return () => {
      cancelled = true;
    };
  }, [url]);

  return { data, loading, error };
};

// Usage
const Posts = () => {
  const { data: posts, loading, error } = useFetch("/api/posts");
  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  return (
    <ul>
      {posts.map((p) => (
        <li key={p.id}>{p.title}</li>
      ))}
    </ul>
  );
};

// --- useLocalStorage: Sync state with localStorage ---
const useLocalStorage = (key, initialValue) => {
  const [value, setValue] = useState(() => {
    try {
      const stored = localStorage.getItem(key);
      return stored ? JSON.parse(stored) : initialValue;
    } catch {
      return initialValue;
    }
  });

  const setStoredValue = (newValue) => {
    setValue(newValue);
    localStorage.setItem(key, JSON.stringify(newValue));
  };

  return [value, setStoredValue];
};

// Usage
const Settings = () => {
  const [theme, setTheme] = useLocalStorage("theme", "light");
  return (
    <button onClick={() => setTheme((t) => (t === "light" ? "dark" : "light"))}>
      Current theme: {theme}
    </button>
  );
};

// --- useDebounce: Delay state update ---
const useDebounce = (value, delay = 500) => {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
};

// Usage
const Search = () => {
  const [query, setQuery] = useState("");
  const debouncedQuery = useDebounce(query, 400);

  useEffect(() => {
    if (debouncedQuery) {
      fetch(`/api/search?q=${debouncedQuery}`); // fires 400ms after user stops typing
    }
  }, [debouncedQuery]);

  return <input value={query} onChange={(e) => setQuery(e.target.value)} />;
};
```

---

---

## ⚡ React 19 — New Hooks <a name="react-19-hooks"></a>

React 19 (released December 2024, latest 19.2) introduced **4 major new APIs** focused on async actions, forms, and optimistic UI. These are a game-changer for form handling and data mutations.

| New Hook / API   | Purpose                                                     |
| ---------------- | ----------------------------------------------------------- |
| `use()`          | Read a Promise or Context anywhere — even inside `if`/loops |
| `useActionState` | Manage form action state + pending + errors in one hook     |
| `useOptimistic`  | Show instant UI feedback before server responds             |
| `useFormStatus`  | Read parent form's pending status from any child component  |

---

### 15a. `use()` Hook <a name="use-hook"></a>

### What it is

`use` is a new React API (not a traditional hook) that lets you **read a Promise or a Context** inside a component. The biggest difference from all other hooks — **it can be called inside `if` statements and loops**.

### Use Cases

- Read async data (Promise) inside a component with Suspense
- Read Context conditionally (something `useContext` can't do)
- Replace boilerplate `useEffect` + `useState` for data fetching

### Syntax

```js
// With Promise
const data = use(promise);

// With Context (same as useContext but can be conditional)
const value = use(MyContext);
```

### Code Examples

```jsx
import { use, Suspense } from "react";

// --- Example 1: use() with a Promise ---

// Create a promise (e.g., from a fetch)
const fetchUser = (id) => fetch(`/api/users/${id}`).then((res) => res.json());

// Component reads the resolved value — React suspends until it resolves
const UserCard = ({ userPromise }) => {
  const user = use(userPromise); // suspends here until promise resolves

  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  );
};

// Parent wraps with Suspense to handle loading state
const App = () => {
  const userPromise = fetchUser(1); // create promise outside component

  return (
    <Suspense fallback={<p>Loading user...</p>}>
      <UserCard userPromise={userPromise} />
    </Suspense>
  );
};

// --- Example 2: use() with Context CONDITIONALLY ---
// This was IMPOSSIBLE with useContext!

const ThemeContext = createContext("light");

const ThemedButton = ({ isAdmin }) => {
  // ✅ React 19: use() works inside if-blocks
  if (isAdmin) {
    const theme = use(ThemeContext); // conditional context read
    return (
      <button style={{ background: theme === "dark" ? "#333" : "#eee" }}>
        Admin Button
      </button>
    );
  }

  return <button>Regular Button</button>;
};

// --- Example 3: use() vs old pattern ---

// ❌ OLD WAY — lots of boilerplate
const OldComponent = ({ userId }) => {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then((r) => r.json())
      .then((data) => {
        setUser(data);
        setLoading(false);
      });
  }, [userId]);

  if (loading) return <p>Loading...</p>;
  return <p>{user.name}</p>;
};

// ✅ NEW WAY with use() — clean and minimal
const NewComponent = ({ userPromise }) => {
  const user = use(userPromise); // that's it!
  return <p>{user.name}</p>;
};
// Wrap with <Suspense fallback={<p>Loading...</p>}> in parent
```

### ⚠️ Important Notes

```jsx
// use() is NOT a hook by the traditional rules
// ✅ CAN be called inside if, for, while
if (condition) {
  const data = use(somePromise); // this is valid!
}

// ❌ Still can't use regular hooks conditionally
if (condition) {
  const [x, setX] = useState(0); // INVALID — breaks rules of hooks
}
```

---

### 15b. `useActionState` <a name="useactionstate"></a>

### What it is

`useActionState` manages the **state of an async form action** — it tracks the result, error, and pending state all in one hook. Replaces the old pattern of manually managing `isPending`, `error`, and `data` states for form submissions.

### Use Cases

- Form submissions (login, signup, create post)
- Any async mutation that shows pending state
- Replacing manual `useState` + `useEffect` + error handling boilerplate

### Syntax

```js
const [state, formAction, isPending] = useActionState(actionFn, initialState);
```

### Code Example

```jsx
import { useActionState } from "react";

// --- Example: Login Form ---

// The "action" function — receives previous state and form data
async function loginAction(prevState, formData) {
  const email = formData.get("email");
  const password = formData.get("password");

  try {
    const res = await fetch("/api/login", {
      method: "POST",
      body: JSON.stringify({ email, password }),
      headers: { "Content-Type": "application/json" },
    });

    if (!res.ok) {
      return { error: "Invalid credentials. Please try again." };
    }

    const data = await res.json();
    return { success: true, user: data.user };
  } catch (err) {
    return { error: "Network error. Please try again." };
  }
}

const LoginForm = () => {
  const [state, formAction, isPending] = useActionState(
    loginAction,
    { error: null, success: false }, // initialState
  );

  return (
    <form action={formAction}>
      {" "}
      {/* pass formAction to form's action prop */}
      <input name="email" type="email" placeholder="Email" required />
      <input name="password" type="password" placeholder="Password" required />
      {/* Show error from action result */}
      {state.error && <p style={{ color: "red" }}>{state.error}</p>}
      {state.success && <p style={{ color: "green" }}>Logged in!</p>}
      <button type="submit" disabled={isPending}>
        {isPending ? "Logging in..." : "Login"}
      </button>
    </form>
  );
};

// --- Before React 19 (old painful way) ---
const OldLoginForm = () => {
  const [isPending, setIsPending] = useState(false);
  const [error, setError] = useState(null);
  const [success, setSuccess] = useState(false);

  const handleSubmit = async (e) => {
    e.preventDefault();
    setIsPending(true);
    setError(null);
    try {
      // fetch...
      setSuccess(true);
    } catch {
      setError("Something went wrong");
    } finally {
      setIsPending(false);
    }
  };
  // ...much more boilerplate
};
```

---

### 15c. `useOptimistic` <a name="useoptimistic"></a>

### What it is

`useOptimistic` lets you **immediately update the UI** as if an action already succeeded, before the server responds. If the action fails, React automatically reverts to the real state.

### Use Cases

- Like/unlike buttons — instant toggle before server confirms
- Adding a comment — show it immediately while saving
- Cart operations — instant add/remove before API responds
- Any mutation where you want zero-latency perceived UX

### Syntax

```js
const [optimisticValue, addOptimistic] = useOptimistic(
  realState,
  (currentState, optimisticValue) => newOptimisticState,
);
```

### Code Example

```jsx
import { useState, useOptimistic, useTransition } from "react";

// --- Example: Like Button ---
const LikeButton = ({ postId, initialLikes }) => {
  const [likes, setLikes] = useState(initialLikes);
  const [liked, setLiked] = useState(false);
  const [isPending, startTransition] = useTransition();

  // optimisticLikes is shown in UI immediately
  // when an optimistic update is pending, it shows the "hoped for" value
  const [optimisticLikes, addOptimisticLike] = useOptimistic(
    likes,
    (currentLikes, delta) => currentLikes + delta, // how to update optimistically
  );

  const handleLike = () => {
    const delta = liked ? -1 : 1;

    startTransition(async () => {
      addOptimisticLike(delta); // ← UI updates INSTANTLY

      try {
        await fetch(`/api/posts/${postId}/like`, { method: "POST" });
        setLikes((prev) => prev + delta); // real state after server confirms
        setLiked((prev) => !prev);
      } catch {
        // if request fails, React auto-reverts optimisticLikes to real 'likes'
        alert("Failed to update like");
      }
    });
  };

  return (
    <button onClick={handleLike} disabled={isPending}>
      {liked ? "❤️" : "🤍"} {optimisticLikes} {/* shows optimistic value */}
    </button>
  );
};

// --- Example 2: Optimistic message list ---
const MessageThread = () => {
  const [messages, setMessages] = useState([
    { id: 1, text: "Hello!", sending: false },
  ]);

  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    (state, newText) => [
      ...state,
      { id: Date.now(), text: newText, sending: true }, // mark as "sending"
    ],
  );

  const sendMessage = async (formData) => {
    const text = formData.get("message");
    addOptimisticMessage(text); // show immediately with "sending: true"

    const saved = await fetch("/api/messages", {
      method: "POST",
      body: JSON.stringify({ text }),
    }).then((r) => r.json());

    setMessages((prev) => [...prev, { ...saved, sending: false }]);
  };

  return (
    <div>
      <ul>
        {optimisticMessages.map((msg) => (
          <li key={msg.id} style={{ opacity: msg.sending ? 0.5 : 1 }}>
            {msg.text} {msg.sending && "(sending...)"}
          </li>
        ))}
      </ul>
      <form action={sendMessage}>
        <input name="message" placeholder="Type a message" />
        <button type="submit">Send</button>
      </form>
    </div>
  );
};
```

---

### 15d. `useFormStatus` <a name="useformstatus"></a>

### What it is

`useFormStatus` is a hook from **`react-dom`** (not `react`) that lets **any child component** read the pending status of its **parent `<form>`**. No need to pass `isPending` as a prop through multiple levels.

### Use Cases

- Submit button that disables itself while form is submitting
- Show a spinner inside any nested component during form submission
- Design systems — build reusable `<SubmitButton>` components

### Syntax

```js
import { useFormStatus } from "react-dom"; // note: from react-dom!

const { pending, data, method, action } = useFormStatus();
```

| Property  | Description                                    |
| --------- | ---------------------------------------------- |
| `pending` | `true` while the parent form action is running |
| `data`    | The `FormData` object being submitted          |
| `method`  | `"get"` or `"post"`                            |
| `action`  | The action function reference                  |

### Code Example

```jsx
import { useFormStatus } from "react-dom"; // ← import from react-dom
import { useActionState } from "react";

// --- Reusable SubmitButton that knows about its parent form ---
const SubmitButton = ({ label = "Submit", pendingLabel = "Submitting..." }) => {
  const { pending } = useFormStatus(); // reads parent form's status

  return (
    <button type="submit" disabled={pending}>
      {pending ? pendingLabel : label}
    </button>
  );
};

// --- Reusable loading spinner tied to form ---
const FormSpinner = () => {
  const { pending } = useFormStatus();
  if (!pending) return null;
  return <span className="spinner">⏳ Please wait...</span>;
};

// --- Usage in a form ---
async function submitContact(prevState, formData) {
  await new Promise(r => setTimeout(r, 2000)); // simulate API delay
  const name = formData.get("name");
  return { success: true, message: `Thanks, ${name}!` };
}

const ContactForm = () => {
  const [state, formAction] = useActionState(submitContact, null);

  return (
    <form action={formAction}>
      <input name="name" placeholder="Your name" required />
      <input name="email" type="email" placeholder="Email" required />
      <textarea name="message" placeholder="Message" />

      {state?.message && <p style={{ color: "green" }}>{state.message}</p>}

      {/* These components automatically know the form is pending */}
      <FormSpinner />
      <SubmitButton label="Send Message" pendingLabel="Sending..." />
    </form>
  );
};

// ⚠️ RULE: useFormStatus must be called inside a CHILD component of the form.
// It does NOT work if called in the same component that renders <form>.
// ❌ WRONG:
const BrokenForm = () => {
  const { pending } = useFormStatus(); // ← won't work here!
  return <form action={...}><button disabled={pending}>Submit</button></form>;
};

// ✅ CORRECT: split the button into a child
const GoodForm = () => {
  return (
    <form action={...}>
      <SubmitButton /> {/* useFormStatus lives inside here ✅ */}
    </form>
  );
};
```

### React 19 — All New Hooks Together (Real-World Form)

```jsx
// Full example combining all 4 React 19 APIs
import { use, useActionState, useOptimistic, Suspense } from "react";
import { useFormStatus } from "react-dom";

// 1. Server action
async function createPost(prevState, formData) {
  const title = formData.get("title");
  if (!title) return { error: "Title is required" };

  const res = await fetch("/api/posts", {
    method: "POST",
    body: JSON.stringify({ title }),
    headers: { "Content-Type": "application/json" },
  });

  if (!res.ok) return { error: "Failed to create post" };
  const post = await res.json();
  return { success: true, post };
}

// 2. Reusable submit button using useFormStatus
const SubmitButton = () => {
  const { pending } = useFormStatus();
  return (
    <button type="submit" disabled={pending}>
      {pending ? "Creating..." : "Create Post"}
    </button>
  );
};

// 3. Posts list with optimistic update
const PostsList = ({ postsPromise }) => {
  const initialPosts = use(postsPromise); // use() handles async
  const [posts, setPosts] = useState(initialPosts);

  const [optimisticPosts, addOptimisticPost] = useOptimistic(
    posts,
    (state, newTitle) => [
      ...state,
      { id: "temp", title: newTitle, pending: true },
    ],
  );

  const [state, formAction] = useActionState(async (prevState, formData) => {
    const title = formData.get("title");
    addOptimisticPost(title); // instant UI update
    const result = await createPost(prevState, formData);
    if (result.success) setPosts((prev) => [...prev, result.post]);
    return result;
  }, null);

  return (
    <div>
      <form action={formAction}>
        <input name="title" placeholder="Post title" />
        {state?.error && <p style={{ color: "red" }}>{state.error}</p>}
        <SubmitButton />
      </form>
      <ul>
        {optimisticPosts.map((post) => (
          <li key={post.id} style={{ opacity: post.pending ? 0.5 : 1 }}>
            {post.title} {post.pending && "(saving...)"}
          </li>
        ))}
      </ul>
    </div>
  );
};

// 4. App with Suspense for use() to work
const App = () => {
  const postsPromise = fetch("/api/posts").then((r) => r.json());
  return (
    <Suspense fallback={<p>Loading posts...</p>}>
      <PostsList postsPromise={postsPromise} />
    </Suspense>
  );
};
```

---

## 15. React Redux <a name="react-redux"></a>

### What it is

Redux is a **predictable global state container**. React Redux is the official binding. State lives in a single store; components dispatch actions; reducers handle updates. Uses Redux Toolkit (RTK) — the modern, recommended way.

### Core Concepts

| Concept      | Role                                                        |
| ------------ | ----------------------------------------------------------- |
| **Store**    | Single source of truth for all global state                 |
| **Slice**    | A reducer + actions for one feature (RTK)                   |
| **Action**   | A plain object `{ type, payload }` describing what happened |
| **Reducer**  | Pure function `(state, action) => newState`                 |
| **dispatch** | Function to send an action to the store                     |
| **Selector** | Function to read data from the store                        |

### Installation

```bash
npm install @reduxjs/toolkit react-redux
```

### Complete Example — Auth + Cart

```jsx
// --- 1. src/store/authSlice.js ---
import { createSlice } from "@reduxjs/toolkit";

const authSlice = createSlice({
  name: "auth",
  initialState: {
    user: null,
    token: null,
    isAuthenticated: false,
  },
  reducers: {
    loginSuccess(state, action) {
      // RTK uses Immer — you CAN mutate state directly here
      state.user = action.payload.user;
      state.token = action.payload.token;
      state.isAuthenticated = true;
    },
    logout(state) {
      state.user = null;
      state.token = null;
      state.isAuthenticated = false;
    },
  },
});

export const { loginSuccess, logout } = authSlice.actions;
export default authSlice.reducer;


// --- 2. src/store/cartSlice.js ---
import { createSlice } from "@reduxjs/toolkit";

const cartSlice = createSlice({
  name: "cart",
  initialState: { items: [], total: 0 },
  reducers: {
    addItem(state, action) {
      const existing = state.items.find(i => i.id === action.payload.id);
      if (existing) {
        existing.qty += 1;
      } else {
        state.items.push({ ...action.payload, qty: 1 });
      }
      state.total += action.payload.price;
    },
    removeItem(state, action) {
      const item = state.items.find(i => i.id === action.payload);
      if (item) {
        state.total -= item.price * item.qty;
        state.items = state.items.filter(i => i.id !== action.payload);
      }
    },
    clearCart(state) {
      state.items = [];
      state.total = 0;
    },
  },
});

export const { addItem, removeItem, clearCart } = cartSlice.actions;
export default cartSlice.reducer;


// --- 3. src/store/index.js ---
import { configureStore } from "@reduxjs/toolkit";
import authReducer from "./authSlice";
import cartReducer from "./cartSlice";

export const store = configureStore({
  reducer: {
    auth: authReducer,
    cart: cartReducer,
  },
});

// TypeScript types (if using TS):
// export type RootState = ReturnType<typeof store.getState>;
// export type AppDispatch = typeof store.dispatch;


// --- 4. src/main.jsx (Provide store to app) ---
import React from "react";
import ReactDOM from "react-dom/client";
import { Provider } from "react-redux";
import { store } from "./store";
import App from "./App";

ReactDOM.createRoot(document.getElementById("root")).render(
  <Provider store={store}>
    <App />
  </Provider>
);


// --- 5. Components using the store ---
import { useSelector, useDispatch } from "react-redux";
import { loginSuccess, logout } from "./store/authSlice";
import { addItem, removeItem } from "./store/cartSlice";

// Reading state with useSelector
const Navbar = () => {
  const { user, isAuthenticated } = useSelector(state => state.auth);
  const cartCount = useSelector(state => state.cart.items.length);
  const dispatch = useDispatch();

  return (
    <nav>
      {isAuthenticated ? (
        <>
          <span>Hello, {user.name}</span>
          <span>Cart: {cartCount}</span>
          <button onClick={() => dispatch(logout())}>Logout</button>
        </>
      ) : (
        <button onClick={() => dispatch(loginSuccess({ user: { name: "Ali" }, token: "abc" }))}>
          Login (demo)
        </button>
      )}
    </nav>
  );
};

const ProductCard = ({ product }) => {
  const dispatch = useDispatch();

  return (
    <div>
      <h3>{product.name} — ${product.price}</h3>
      <button onClick={() => dispatch(addItem(product))}>Add to Cart</button>
    </div>
  );
};


// --- Async Actions with createAsyncThunk ---
import { createAsyncThunk, createSlice } from "@reduxjs/toolkit";

export const fetchPosts = createAsyncThunk("posts/fetch", async () => {
  const res = await fetch("/api/posts");
  return res.json(); // returned value becomes action.payload
});

const postsSlice = createSlice({
  name: "posts",
  initialState: { items: [], loading: false, error: null },
  extraReducers: (builder) => {
    builder
      .addCase(fetchPosts.pending, (state) => {
        state.loading = true;
        state.error = null;
      })
      .addCase(fetchPosts.fulfilled, (state, action) => {
        state.loading = false;
        state.items = action.payload;
      })
      .addCase(fetchPosts.rejected, (state, action) => {
        state.loading = false;
        state.error = action.error.message;
      });
  },
});

// In component:
const PostsPage = () => {
  const dispatch = useDispatch();
  const { items, loading, error } = useSelector(state => state.posts);

  useEffect(() => {
    dispatch(fetchPosts());
  }, [dispatch]);

  if (loading) return <p>Loading posts...</p>;
  if (error) return <p>Error: {error}</p>;
  return <ul>{items.map(p => <li key={p.id}>{p.title}</li>)}</ul>;
};
```

### Redux File Structure

```
src/
├── store/
│   ├── index.js          ← configureStore
│   ├── authSlice.js      ← one slice per feature
│   ├── cartSlice.js
│   └── postsSlice.js
├── components/
└── pages/
```

---

## 15. Zustand <a name="zustand"></a>

### What it is

Zustand is a **lightweight, simple global state management** library. No boilerplate, no Provider wrapping. Just create a store and use it anywhere.

### Why Zustand over Redux?

| Feature         | Redux Toolkit             | Zustand                        |
| --------------- | ------------------------- | ------------------------------ |
| Setup           | More boilerplate          | Minimal                        |
| Learning curve  | Steeper                   | Very easy                      |
| DevTools        | Excellent                 | Good                           |
| Bundle size     | Larger                    | Tiny (~1KB)                    |
| Best for        | Large teams, complex apps | Small-medium apps, quick setup |
| Provider needed | Yes                       | No                             |

### Installation

```bash
npm install zustand
```

### Complete Examples

```jsx
// --- Example 1: Counter Store ---
import { create } from "zustand";

const useCounterStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
  decrement: () => set((state) => ({ count: state.count - 1 })),
  reset: () => set({ count: 0 }),
}));

// Usage — no Provider needed!
const Counter = () => {
  const { count, increment, decrement, reset } = useCounterStore();

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
};

// --- Example 2: Auth Store ---
const useAuthStore = create((set) => ({
  user: null,
  token: null,
  isAuthenticated: false,

  login: (user, token) => set({ user, token, isAuthenticated: true }),
  logout: () => set({ user: null, token: null, isAuthenticated: false }),
}));

const Navbar = () => {
  const { user, isAuthenticated, logout } = useAuthStore();

  return (
    <nav>
      {isAuthenticated ? (
        <>
          <span>Hello, {user.name}</span>
          <button onClick={logout}>Logout</button>
        </>
      ) : (
        <span>Not logged in</span>
      )}
    </nav>
  );
};

// --- Example 3: Cart Store with persist (localStorage) ---
import { create } from "zustand";
import { persist } from "zustand/middleware";

const useCartStore = create(
  persist(
    (set, get) => ({
      items: [],
      total: 0,

      addItem: (product) => {
        const items = get().items;
        const existing = items.find((i) => i.id === product.id);
        if (existing) {
          set({
            items: items.map((i) =>
              i.id === product.id ? { ...i, qty: i.qty + 1 } : i,
            ),
          });
        } else {
          set((state) => ({
            items: [...state.items, { ...product, qty: 1 }],
            total: state.total + product.price,
          }));
        }
      },

      removeItem: (id) => {
        const item = get().items.find((i) => i.id === id);
        set((state) => ({
          items: state.items.filter((i) => i.id !== id),
          total: state.total - (item ? item.price * item.qty : 0),
        }));
      },

      clearCart: () => set({ items: [], total: 0 }),
    }),
    { name: "cart-storage" }, // persists to localStorage with this key
  ),
);

const CartPage = () => {
  const { items, total, removeItem, clearCart } = useCartStore();

  return (
    <div>
      <h2>Cart — Total: ${total.toFixed(2)}</h2>
      {items.map((item) => (
        <div key={item.id}>
          <span>
            {item.name} × {item.qty}
          </span>
          <button onClick={() => removeItem(item.id)}>Remove</button>
        </div>
      ))}
      <button onClick={clearCart}>Clear All</button>
    </div>
  );
};

// --- Example 4: Async actions in Zustand ---
const usePostsStore = create((set) => ({
  posts: [],
  loading: false,
  error: null,

  fetchPosts: async () => {
    set({ loading: true, error: null });
    try {
      const res = await fetch("/api/posts");
      const data = await res.json();
      set({ posts: data, loading: false });
    } catch (err) {
      set({ error: err.message, loading: false });
    }
  },
}));

const PostsPage = () => {
  const { posts, loading, error, fetchPosts } = usePostsStore();

  useEffect(() => {
    fetchPosts();
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  return (
    <ul>
      {posts.map((p) => (
        <li key={p.id}>{p.title}</li>
      ))}
    </ul>
  );
};

// --- Performance Tip: Subscribe to only what you need ---
// ❌ Re-renders on ANY store change
const { count, user, items } = useCounterStore();

// ✅ Re-renders ONLY when count changes
const count = useCounterStore((state) => state.count);
const increment = useCounterStore((state) => state.increment);
```

### Zustand Middleware

```jsx
import { devtools, persist, subscribeWithSelector } from "zustand/middleware";

const useStore = create(
  devtools(
    // Redux DevTools support
    persist(
      // Persist to localStorage
      (set) => ({
        // your store
      }),
      { name: "my-store" },
    ),
    { name: "MyStore" }, // DevTools label
  ),
);
```

---

## 16. When to Use What <a name="when-to-use-what"></a>

### State Management Decision Tree

```
Is the state needed in only ONE component?
  └── YES → useState or useReducer
        ├── Simple value? → useState
        └── Complex object / many transitions? → useReducer

Is the state shared between components?
  └── Few levels deep? → useContext
  └── Many components / complex logic? → Redux or Zustand
        ├── Large team, enterprise app? → Redux Toolkit
        └── Small/medium app, quick setup? → Zustand
```

### Hook Quick Reference

| Hook                                       | Purpose                              | When to Use                                   |
| ------------------------------------------ | ------------------------------------ | --------------------------------------------- |
| `useState`                                 | Local reactive state                 | Default for component state                   |
| `useEffect`                                | Side effects                         | API calls, subscriptions, timers              |
| `useRef`                                   | DOM access / mutable value           | Focus, measure, store IDs                     |
| `useContext`                               | Consume context                      | Theme, auth — avoid prop drilling             |
| `useReducer`                               | Complex local state                  | Many related state values                     |
| `useMemo`                                  | Memoize computed value               | Expensive calculations                        |
| `useCallback`                              | Memoize function reference           | Stable callbacks for child/useEffect          |
| `useLayoutEffect`                          | DOM measurement before paint         | Prevent layout flicker                        |
| `useId`                                    | Unique accessible IDs                | Form labels, ARIA                             |
| `useTransition`                            | Non-urgent state updates             | Keep UI responsive during heavy updates       |
| `useDeferredValue`                         | Defer a prop's update                | When you don't control state update           |
| `useImperativeHandle`                      | Expose child methods to parent       | Component library APIs                        |
| ⚡ `use` _(React 19)_                      | Read Promise or Context anywhere     | Async data with Suspense, conditional context |
| ⚡ `useActionState` _(React 19)_           | Form action state + pending + errors | Form submissions, async mutations             |
| ⚡ `useOptimistic` _(React 19)_            | Instant optimistic UI updates        | Likes, comments, cart — zero-latency feel     |
| ⚡ `useFormStatus` _(React 19, react-dom)_ | Read parent form's pending state     | Reusable submit buttons, form spinners        |

---

## 📝 Key Rules to Remember

1. **Hooks must be called at the top level** — never inside loops, conditions, or nested functions.
2. **Hooks can only be called from React functions** — functional components or custom hooks.
3. **Never mutate state directly** — always return new values from reducers/setters.
4. **Functional updates** (`setState(prev => ...)`) are safer when new state depends on old state.
5. **Cleanup effects** — always return a cleanup function from `useEffect` for subscriptions, timers, and async operations.
6. **Exhaustive deps** — include all values used inside `useEffect`/`useMemo`/`useCallback` in the dependency array.
7. **Custom hook names must start with `use`** — this is how React identifies them.
8. **`React.memo` + `useCallback`** — they work together; `memo` is useless without stable callback references.

---

_Happy coding! — React is much easier once the patterns click. You've got this. 🚀_
