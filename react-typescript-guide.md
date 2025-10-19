# React TypeScript Conceptual Guide

A comprehensive guide to understanding TypeScript typing in React, from core concepts to advanced patterns.

---

## Table of Contents

1. [Core Concepts](#core-concepts)
2. [Component Typing](#component-typing)
3. [Props and State](#props-and-state)
4. [Hooks with TypeScript](#hooks-with-typescript)
5. [Event Handling](#event-handling)
6. [Forms and Inputs](#forms-and-inputs)
7. [Context API](#context-api)
8. [Advanced Patterns](#advanced-patterns)
9. [Utility Types](#utility-types)
10. [Best Practices](#best-practices)

---

## Core Concepts

### Why TypeScript with React?

TypeScript adds static typing to React, providing:
- Compile-time error detection
- Better IDE autocomplete and IntelliSense
- Self-documenting code through type definitions
- Safer refactoring
- Enhanced component prop validation

### Basic Type Annotations

```typescript
// Primitive types
const name: string = "John";
const age: number = 30;
const isActive: boolean = true;
const data: null = null;
const value: undefined = undefined;

// Arrays
const numbers: number[] = [1, 2, 3];
const names: Array<string> = ["Alice", "Bob"];

// Objects
const user: { name: string; age: number } = {
  name: "John",
  age: 30
};

// Type aliases
type User = {
  name: string;
  age: number;
  email?: string; // Optional property
};

// Interfaces
interface Product {
  id: number;
  title: string;
  price: number;
  description?: string;
}
```

---

## Component Typing

### Function Components

```typescript
// Basic function component
function Greeting(props: { name: string }) {
  return <h1>Hello, {props.name}!</h1>;
}

// With destructured props
function Greeting({ name }: { name: string }) {
  return <h1>Hello, {name}!</h1>;
}

// Using type alias
type GreetingProps = {
  name: string;
  age?: number;
};

function Greeting({ name, age }: GreetingProps) {
  return (
    <div>
      <h1>Hello, {name}!</h1>
      {age && <p>Age: {age}</p>}
    </div>
  );
}

// Using interface
interface ButtonProps {
  label: string;
  onClick: () => void;
  disabled?: boolean;
}

function Button({ label, onClick, disabled = false }: ButtonProps) {
  return (
    <button onClick={onClick} disabled={disabled}>
      {label}
    </button>
  );
}
```

### React.FC Type (Functional Component)

```typescript
// Using React.FC (includes children implicitly in older versions)
const Greeting: React.FC<{ name: string }> = ({ name }) => {
  return <h1>Hello, {name}!</h1>;
};

// Modern approach: explicit children typing
type CardProps = {
  title: string;
  children: React.ReactNode;
};

const Card: React.FC<CardProps> = ({ title, children }) => {
  return (
    <div>
      <h2>{title}</h2>
      {children}
    </div>
  );
};

// Note: React.FC is less commonly used in modern React
// Prefer explicit prop typing without React.FC
```

### Component Return Types

```typescript
// Explicit return type
function Header(): JSX.Element {
  return <header>My App</header>;
}

// React.ReactElement
function Footer(): React.ReactElement {
  return <footer>© 2024</footer>;
}

// React.ReactNode (more flexible, includes null, strings, etc.)
function Layout({ children }: { children: React.ReactNode }): React.ReactNode {
  return <div>{children}</div>;
}

// Conditional rendering
function Status({ isOnline }: { isOnline: boolean }): JSX.Element | null {
  if (!isOnline) return null;
  return <span>Online</span>;
}
```

---

## Props and State

### Props Interface

```typescript
// Basic props
interface UserCardProps {
  name: string;
  email: string;
  age: number;
  isActive: boolean;
}

// Optional props
interface ProfileProps {
  username: string;
  bio?: string;
  avatar?: string;
}

// Props with default values
interface ButtonProps {
  variant?: "primary" | "secondary" | "danger";
  size?: "small" | "medium" | "large";
  children: React.ReactNode;
}

function Button({ 
  variant = "primary", 
  size = "medium", 
  children 
}: ButtonProps) {
  return <button className={`btn-${variant}-${size}`}>{children}</button>;
}

// Props with union types
interface AlertProps {
  type: "success" | "error" | "warning" | "info";
  message: string;
}

// Props with complex objects
interface PostProps {
  post: {
    id: number;
    title: string;
    author: {
      name: string;
      avatar: string;
    };
    tags: string[];
    publishedAt: Date;
  };
}
```

### Children Props

```typescript
// Children as ReactNode (most flexible)
interface ContainerProps {
  children: React.ReactNode;
}

// Children as specific type
interface ListProps {
  children: React.ReactElement<ListItemProps>[];
}

// Children as render function
interface TabsProps {
  children: (activeTab: number) => React.ReactNode;
}

// Multiple children with specific types
interface ModalProps {
  header: React.ReactNode;
  content: React.ReactNode;
  footer?: React.ReactNode;
}
```

### Extending Native Props

```typescript
// Extending HTML button props
interface CustomButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant: "primary" | "secondary";
  isLoading?: boolean;
}

function CustomButton({ variant, isLoading, children, ...rest }: CustomButtonProps) {
  return (
    <button {...rest} className={`btn-${variant}`}>
      {isLoading ? "Loading..." : children}
    </button>
  );
}

// Extending HTML input props
interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label: string;
  error?: string;
}

// Extending div props
interface CardProps extends React.HTMLAttributes<HTMLDivElement> {
  title: string;
  elevated?: boolean;
}
```

---

## Hooks with TypeScript

### useState

```typescript
// Type inference (preferred when possible)
const [count, setCount] = useState(0); // number
const [name, setName] = useState("John"); // string
const [isOpen, setIsOpen] = useState(false); // boolean

// Explicit typing
const [count, setCount] = useState<number>(0);

// With objects
interface User {
  id: number;
  name: string;
  email: string;
}

const [user, setUser] = useState<User>({
  id: 1,
  name: "John",
  email: "john@example.com"
});

// With null/undefined (union types)
const [user, setUser] = useState<User | null>(null);
const [data, setData] = useState<string | undefined>(undefined);

// With arrays
const [items, setItems] = useState<string[]>([]);
const [users, setUsers] = useState<User[]>([]);

// Complex state with type assertion
interface FormState {
  username: string;
  email: string;
  age: number;
}

const [form, setForm] = useState<FormState>({
  username: "",
  email: "",
  age: 0
});

// Updating nested state
setForm(prev => ({
  ...prev,
  username: "newname"
}));
```

### useEffect

```typescript
// Basic useEffect (no return value needed)
useEffect(() => {
  console.log("Component mounted");
}, []);

// With cleanup function
useEffect(() => {
  const timer = setInterval(() => {
    console.log("Tick");
  }, 1000);

  // Cleanup must return void or a cleanup function
  return () => {
    clearInterval(timer);
  };
}, []);

// With async operations (proper pattern)
useEffect(() => {
  const fetchData = async () => {
    const response = await fetch("/api/data");
    const data: User[] = await response.json();
    setUsers(data);
  };

  fetchData();
}, []);

// Dependencies array typing (inferred)
useEffect(() => {
  console.log(count);
}, [count]); // TypeScript knows count is a number
```

### useRef

```typescript
// DOM element refs
const inputRef = useRef<HTMLInputElement>(null);
const divRef = useRef<HTMLDivElement>(null);
const buttonRef = useRef<HTMLButtonElement>(null);

// Using refs
function FocusInput() {
  const inputRef = useRef<HTMLInputElement>(null);

  const handleFocus = () => {
    inputRef.current?.focus(); // Optional chaining for null check
  };

  return (
    <>
      <input ref={inputRef} type="text" />
      <button onClick={handleFocus}>Focus Input</button>
    </>
  );
}

// Mutable value refs (non-DOM)
const countRef = useRef<number>(0);
const timerRef = useRef<NodeJS.Timeout | null>(null);

// Using mutable refs
function Timer() {
  const intervalRef = useRef<NodeJS.Timeout | null>(null);

  const start = () => {
    intervalRef.current = setInterval(() => {
      console.log("Tick");
    }, 1000);
  };

  const stop = () => {
    if (intervalRef.current) {
      clearInterval(intervalRef.current);
    }
  };

  return (
    <>
      <button onClick={start}>Start</button>
      <button onClick={stop}>Stop</button>
    </>
  );
}
```

### useReducer

```typescript
// State and action types
interface State {
  count: number;
  error: string | null;
}

type Action =
  | { type: "INCREMENT" }
  | { type: "DECREMENT" }
  | { type: "RESET" }
  | { type: "SET_ERROR"; payload: string };

// Reducer function
function reducer(state: State, action: Action): State {
  switch (action.type) {
    case "INCREMENT":
      return { ...state, count: state.count + 1 };
    case "DECREMENT":
      return { ...state, count: state.count - 1 };
    case "RESET":
      return { ...state, count: 0 };
    case "SET_ERROR":
      return { ...state, error: action.payload };
    default:
      return state;
  }
}

// Using the reducer
function Counter() {
  const [state, dispatch] = useReducer(reducer, { count: 0, error: null });

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: "INCREMENT" })}>+</button>
      <button onClick={() => dispatch({ type: "DECREMENT" })}>-</button>
      <button onClick={() => dispatch({ type: "RESET" })}>Reset</button>
    </div>
  );
}

// Complex example with payload
interface User {
  id: number;
  name: string;
}

interface UserState {
  users: User[];
  loading: boolean;
  error: string | null;
}

type UserAction =
  | { type: "FETCH_START" }
  | { type: "FETCH_SUCCESS"; payload: User[] }
  | { type: "FETCH_ERROR"; payload: string }
  | { type: "ADD_USER"; payload: User }
  | { type: "DELETE_USER"; payload: number };

function userReducer(state: UserState, action: UserAction): UserState {
  switch (action.type) {
    case "FETCH_START":
      return { ...state, loading: true, error: null };
    case "FETCH_SUCCESS":
      return { ...state, loading: false, users: action.payload };
    case "FETCH_ERROR":
      return { ...state, loading: false, error: action.payload };
    case "ADD_USER":
      return { ...state, users: [...state.users, action.payload] };
    case "DELETE_USER":
      return { ...state, users: state.users.filter(u => u.id !== action.payload) };
    default:
      return state;
  }
}
```

### useCallback

```typescript
// Basic useCallback
const handleClick = useCallback(() => {
  console.log("Clicked");
}, []);

// With parameters
const handleSubmit = useCallback((data: FormData) => {
  console.log(data);
}, []);

// With dependencies
const handleUpdate = useCallback((id: number) => {
  updateItem(id, value);
}, [value]); // TypeScript infers value type from dependency

// Explicit typing
const handleChange = useCallback<React.ChangeEventHandler<HTMLInputElement>>(
  (event) => {
    console.log(event.target.value);
  },
  []
);

// With complex function signature
type SearchFunction = (query: string, filters: string[]) => Promise<Result[]>;

const search = useCallback<SearchFunction>(
  async (query, filters) => {
    const results = await api.search(query, filters);
    return results;
  },
  []
);
```

### useMemo

```typescript
// Basic useMemo
const expensiveValue = useMemo(() => {
  return computeExpensiveValue(a, b);
}, [a, b]);

// With explicit type
const sortedItems = useMemo<Item[]>(() => {
  return items.sort((a, b) => a.name.localeCompare(b.name));
}, [items]);

// Memoizing objects
const config = useMemo(() => ({
  apiUrl: process.env.API_URL,
  timeout: 5000,
  headers: {
    "Content-Type": "application/json"
  }
}), []);

// Memoizing computed values
const statistics = useMemo(() => {
  const total = data.reduce((sum, item) => sum + item.value, 0);
  const average = total / data.length;
  const max = Math.max(...data.map(item => item.value));
  
  return { total, average, max };
}, [data]);
```

### Custom Hooks

```typescript
// Basic custom hook
function useCounter(initialValue: number = 0) {
  const [count, setCount] = useState(initialValue);

  const increment = useCallback(() => setCount(c => c + 1), []);
  const decrement = useCallback(() => setCount(c => c - 1), []);
  const reset = useCallback(() => setCount(initialValue), [initialValue]);

  return { count, increment, decrement, reset };
}

// Hook with generic type
function useLocalStorage<T>(key: string, initialValue: T) {
  const [value, setValue] = useState<T>(() => {
    const stored = localStorage.getItem(key);
    return stored ? JSON.parse(stored) : initialValue;
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue] as const;
}

// Usage
const [user, setUser] = useLocalStorage<User>("user", { name: "", email: "" });

// Hook with return type annotation
function useFetch<T>(url: string): {
  data: T | null;
  loading: boolean;
  error: Error | null;
} {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch(url);
        const json = await response.json();
        setData(json);
      } catch (err) {
        setError(err as Error);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]);

  return { data, loading, error };
}

// Advanced hook with multiple generics
function useAsync<T, E = Error>(
  asyncFunction: () => Promise<T>
): {
  execute: () => Promise<void>;
  data: T | null;
  loading: boolean;
  error: E | null;
} {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<E | null>(null);

  const execute = useCallback(async () => {
    setLoading(true);
    setError(null);
    
    try {
      const result = await asyncFunction();
      setData(result);
    } catch (err) {
      setError(err as E);
    } finally {
      setLoading(false);
    }
  }, [asyncFunction]);

  return { execute, data, loading, error };
}
```

---

## Event Handling

### Mouse Events

```typescript
// Basic mouse event
function handleClick(event: React.MouseEvent<HTMLButtonElement>) {
  console.log(event.currentTarget.value);
}

// Different element types
function handleDivClick(event: React.MouseEvent<HTMLDivElement>) {
  console.log("Div clicked");
}

function handleLinkClick(event: React.MouseEvent<HTMLAnchorElement>) {
  event.preventDefault();
  console.log("Link clicked");
}

// Generic element
function handleGenericClick(event: React.MouseEvent<HTMLElement>) {
  console.log(event.clientX, event.clientY);
}

// Inline handler
<button onClick={(e: React.MouseEvent<HTMLButtonElement>) => {
  console.log(e.currentTarget);
}}>
  Click me
</button>

// Component with typed handlers
interface ButtonProps {
  onClick: (event: React.MouseEvent<HTMLButtonElement>) => void;
  onDoubleClick?: (event: React.MouseEvent<HTMLButtonElement>) => void;
}

function Button({ onClick, onDoubleClick }: ButtonProps) {
  return (
    <button 
      onClick={onClick}
      onDoubleClick={onDoubleClick}
    >
      Click
    </button>
  );
}
```

### Keyboard Events

```typescript
// Input keyboard events
function handleKeyDown(event: React.KeyboardEvent<HTMLInputElement>) {
  if (event.key === "Enter") {
    console.log("Enter pressed");
  }
}

// Textarea keyboard events
function handleTextAreaKeyPress(event: React.KeyboardEvent<HTMLTextAreaElement>) {
  console.log(event.key);
}

// Generic keyboard handler
function handleKeyUp(event: React.KeyboardEvent) {
  console.log(event.code, event.key, event.keyCode);
}

// Component with keyboard handler
interface SearchProps {
  onSearch: (query: string) => void;
}

function Search({ onSearch }: SearchProps) {
  const [query, setQuery] = useState("");

  const handleKeyDown = (event: React.KeyboardEvent<HTMLInputElement>) => {
    if (event.key === "Enter") {
      onSearch(query);
    }
  };

  return (
    <input
      value={query}
      onChange={(e) => setQuery(e.target.value)}
      onKeyDown={handleKeyDown}
    />
  );
}
```

### Form Events

```typescript
// Submit event
function handleSubmit(event: React.FormEvent<HTMLFormElement>) {
  event.preventDefault();
  console.log("Form submitted");
}

// Change event for input
function handleInputChange(event: React.ChangeEvent<HTMLInputElement>) {
  console.log(event.target.value);
}

// Change event for select
function handleSelectChange(event: React.ChangeEvent<HTMLSelectElement>) {
  console.log(event.target.value);
}

// Change event for textarea
function handleTextAreaChange(event: React.ChangeEvent<HTMLTextAreaElement>) {
  console.log(event.target.value);
}

// Focus events
function handleFocus(event: React.FocusEvent<HTMLInputElement>) {
  console.log("Input focused");
}

function handleBlur(event: React.FocusEvent<HTMLInputElement>) {
  console.log("Input blurred");
}
```

### Event Handler Types

```typescript
// Common event handler types
type ClickHandler = React.MouseEventHandler<HTMLButtonElement>;
type ChangeHandler = React.ChangeEventHandler<HTMLInputElement>;
type SubmitHandler = React.FormEventHandler<HTMLFormElement>;
type KeyHandler = React.KeyboardEventHandler<HTMLInputElement>;

// Using the types
interface FormProps {
  onSubmit: SubmitHandler;
  onChange: ChangeHandler;
}

// Creating reusable handler types
type InputChangeHandler = (value: string) => void;

interface InputProps {
  value: string;
  onChange: InputChangeHandler;
}

function Input({ value, onChange }: InputProps) {
  const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    onChange(event.target.value);
  };

  return <input value={value} onChange={handleChange} />;
}
```

---

## Forms and Inputs

### Controlled Components

```typescript
// Basic controlled input
function LoginForm() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");

  const handleEmailChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    setEmail(event.target.value);
  };

  const handlePasswordChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    setPassword(event.target.value);
  };

  const handleSubmit = (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();
    console.log({ email, password });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={email}
        onChange={handleEmailChange}
      />
      <input
        type="password"
        value={password}
        onChange={handlePasswordChange}
      />
      <button type="submit">Login</button>
    </form>
  );
}

// Form with typed state
interface FormData {
  username: string;
  email: string;
  age: number;
  bio: string;
  country: string;
  subscribe: boolean;
}

function RegistrationForm() {
  const [formData, setFormData] = useState<FormData>({
    username: "",
    email: "",
    age: 0,
    bio: "",
    country: "",
    subscribe: false
  });

  const handleInputChange = (
    event: React.ChangeEvent<HTMLInputElement | HTMLTextAreaElement | HTMLSelectElement>
  ) => {
    const { name, value, type } = event.target;
    const checked = (event.target as HTMLInputElement).checked;

    setFormData(prev => ({
      ...prev,
      [name]: type === "checkbox" ? checked : value
    }));
  };

  return (
    <form>
      <input
        name="username"
        value={formData.username}
        onChange={handleInputChange}
      />
      <input
        name="email"
        type="email"
        value={formData.email}
        onChange={handleInputChange}
      />
      <input
        name="age"
        type="number"
        value={formData.age}
        onChange={handleInputChange}
      />
      <textarea
        name="bio"
        value={formData.bio}
        onChange={handleInputChange}
      />
      <select
        name="country"
        value={formData.country}
        onChange={handleInputChange}
      >
        <option value="">Select...</option>
        <option value="US">United States</option>
        <option value="UK">United Kingdom</option>
      </select>
      <input
        name="subscribe"
        type="checkbox"
        checked={formData.subscribe}
        onChange={handleInputChange}
      />
    </form>
  );
}
```

### Uncontrolled Components with Refs

```typescript
function UncontrolledForm() {
  const nameRef = useRef<HTMLInputElement>(null);
  const emailRef = useRef<HTMLInputElement>(null);
  const fileRef = useRef<HTMLInputElement>(null);

  const handleSubmit = (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();

    const name = nameRef.current?.value;
    const email = emailRef.current?.value;
    const files = fileRef.current?.files;

    console.log({ name, email, files });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input ref={nameRef} type="text" />
      <input ref={emailRef} type="email" />
      <input ref={fileRef} type="file" />
      <button type="submit">Submit</button>
    </form>
  );
}
```

### Form Validation

```typescript
interface FormErrors {
  [key: string]: string;
}

interface LoginFormData {
  email: string;
  password: string;
}

function LoginFormWithValidation() {
  const [formData, setFormData] = useState<LoginFormData>({
    email: "",
    password: ""
  });
  const [errors, setErrors] = useState<FormErrors>({});

  const validate = (data: LoginFormData): FormErrors => {
    const errors: FormErrors = {};

    if (!data.email) {
      errors.email = "Email is required";
    } else if (!/\S+@\S+\.\S+/.test(data.email)) {
      errors.email = "Email is invalid";
    }

    if (!data.password) {
      errors.password = "Password is required";
    } else if (data.password.length < 6) {
      errors.password = "Password must be at least 6 characters";
    }

    return errors;
  };

  const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = event.target;
    setFormData(prev => ({ ...prev, [name]: value }));
    
    // Clear error for this field
    if (errors[name]) {
      setErrors(prev => ({ ...prev, [name]: "" }));
    }
  };

  const handleSubmit = (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();
    
    const validationErrors = validate(formData);
    
    if (Object.keys(validationErrors).length === 0) {
      console.log("Form is valid", formData);
    } else {
      setErrors(validationErrors);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <input
          name="email"
          type="email"
          value={formData.email}
          onChange={handleChange}
        />
        {errors.email && <span>{errors.email}</span>}
      </div>
      <div>
        <input
          name="password"
          type="password"
          value={formData.password}
          onChange={handleChange}
        />
        {errors.password && <span>{errors.password}</span>}
      </div>
      <button type="submit">Submit</button>
    </form>
  );
}
```

---

## Context API

### Basic Context

```typescript
// Create context with type
interface ThemeContextType {
  theme: "light" | "dark";
  toggleTheme: () => void;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

// Provider component
function ThemeProvider({ children }: { children: React.ReactNode }) {
  const [theme, setTheme] = useState<"light" | "dark">("light");

  const toggleTheme = () => {
    setTheme(prev => prev === "light" ? "dark" : "light");
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

// Custom hook for using context
function useTheme() {
  const context = useContext(ThemeContext);
  
  if (context === undefined) {
    throw new Error("useTheme must be used within a ThemeProvider");
  }
  
  return context;
}

// Using the context
function ThemedButton() {
  const { theme, toggleTheme } = useTheme();
  
  return (
    <button onClick={toggleTheme}>
      Current theme: {theme}
    </button>
  );
}
```

### Context with Complex State

```typescript
// User context types
interface User {
  id: number;
  name: string;
  email: string;
  role: "admin" | "user" | "guest";
}

interface AuthContextType {
  user: User | null;
  login: (email: string, password: string) => Promise<void>;
  logout: () => void;
  isAuthenticated: boolean;
  isLoading: boolean;
}

const AuthContext = createContext<AuthContextType | undefined>(undefined);

// Provider with async operations
function AuthProvider({ children }: { children: React.ReactNode }) {
  const [user, setUser] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    // Check for existing session
    const checkAuth = async () => {
      try {
        const response = await fetch("/api/auth/me");
        if (response.ok) {
          const userData = await response.json();
          setUser(userData);
        }
      } catch (error) {
        console.error(error);
      } finally {
        setIsLoading(false);
      }
    };

    checkAuth();
  }, []);

  const login = async (email: string, password: string) => {
    setIsLoading(true);
    try {
      const response = await fetch("/api/auth/login", {
        method: "POST",
        body: JSON.stringify({ email, password })
      });
      
      if (response.ok) {
        const userData = await response.json();
        setUser(userData);
      }
    } catch (error) {
      throw error;
    } finally {
      setIsLoading(false);
    }
  };

  const logout = () => {
    setUser(null);
  };

  const value: AuthContextType = {
    user,
    login,
    logout,
    isAuthenticated: user !== null,
    isLoading
  };

  return (
    <AuthContext.Provider value={value}>
      {children}
    </AuthContext.Provider>
  );
}

function useAuth() {
  const context = useContext(AuthContext);
  
  if (context === undefined) {
    throw new Error("useAuth must be used within an AuthProvider");
  }
  
  return context;
}
```

### Context with Reducer

```typescript
// State and actions
interface CartItem {
  id: number;
  name: string;
  price: number;
  quantity: number;
}

interface CartState {
  items: CartItem[];
  total: number;
}

type CartAction =
  | { type: "ADD_ITEM"; payload: CartItem }
  | { type: "REMOVE_ITEM"; payload: number }
  | { type: "UPDATE_QUANTITY"; payload: { id: number; quantity: number } }
  | { type: "CLEAR_CART" };

// Reducer
function cartReducer(state: CartState, action: CartAction): CartState {
  switch (action.type) {
    case "ADD_ITEM":
      const existingItem = state.items.find(item => item.id === action.payload.id);
      
      if (existingItem) {
        return {
          ...state,
          items: state.items.map(item =>
            item.id === action.payload.id
              ? { ...item, quantity: item.quantity + 1 }
              : item
          )
        };
      }
      
      return {
        ...state,
        items: [...state.items, action.payload]
      };

    case "REMOVE_ITEM":
      return {
        ...state,
        items: state.items.filter(item => item.id !== action.payload)
      };

    case "UPDATE_QUANTITY":
      return {
        ...state,
        items: state.items.map(item =>
          item.id === action.payload.id
            ? { ...item, quantity: action.payload.quantity }
            : item
        )
      };

    case "CLEAR_CART":
      return { items: [], total: 0 };

    default:
      return state;
  }
}

// Context type
interface CartContextType {
  state: CartState;
  addItem: (item: CartItem) => void;
  removeItem: (id: number) => void;
  updateQuantity: (id: number, quantity: number) => void;
  clearCart: () => void;
}

const CartContext = createContext<CartContextType | undefined>(undefined);

// Provider
function CartProvider({ children }: { children: React.ReactNode }) {
  const [state, dispatch] = useReducer(cartReducer, { items: [], total: 0 });

  const addItem = (item: CartItem) => {
    dispatch({ type: "ADD_ITEM", payload: item });
  };

  const removeItem = (id: number) => {
    dispatch({ type: "REMOVE_ITEM", payload: id });
  };

  const updateQuantity = (id: number, quantity: number) => {
    dispatch({ type: "UPDATE_QUANTITY", payload: { id, quantity } });
  };

  const clearCart = () => {
    dispatch({ type: "CLEAR_CART" });
  };

  return (
    <CartContext.Provider value={{ state, addItem, removeItem, updateQuantity, clearCart }}>
      {children}
    </CartContext.Provider>
  );
}

function useCart() {
  const context = useContext(CartContext);
  
  if (context === undefined) {
    throw new Error("useCart must be used within a CartProvider");
  }
  
  return context;
}
```

---

## Advanced Patterns

### Generic Components

```typescript
// Generic list component
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
  keyExtractor: (item: T) => string | number;
}

function List<T>({ items, renderItem, keyExtractor }: ListProps<T>) {
  return (
    <ul>
      {items.map(item => (
        <li key={keyExtractor(item)}>
          {renderItem(item)}
        </li>
      ))}
    </ul>
  );
}

// Usage
interface User {
  id: number;
  name: string;
}

<List<User>
  items={users}
  renderItem={(user) => <span>{user.name}</span>}
  keyExtractor={(user) => user.id}
/>

// Generic table component
interface Column<T> {
  header: string;
  accessor: keyof T;
  render?: (value: T[keyof T]) => React.ReactNode;
}

interface TableProps<T> {
  data: T[];
  columns: Column<T>[];
}

function Table<T>({ data, columns }: TableProps<T>) {
  return (
    <table>
      <thead>
        <tr>
          {columns.map(col => (
            <th key={String(col.accessor)}>{col.header}</th>
          ))}
        </tr>
      </thead>
      <tbody>
        {data.map((row, idx) => (
          <tr key={idx}>
            {columns.map(col => (
              <td key={String(col.accessor)}>
                {col.render
                  ? col.render(row[col.accessor])
                  : String(row[col.accessor])}
              </td>
            ))}
          </tr>
        ))}
      </tbody>
    </table>
  );
}

// Generic select component
interface Option<T> {
  value: T;
  label: string;
}

interface SelectProps<T> {
  options: Option<T>[];
  value: T;
  onChange: (value: T) => void;
}

function Select<T extends string | number>({
  options,
  value,
  onChange
}: SelectProps<T>) {
  const handleChange = (event: React.ChangeEvent<HTMLSelectElement>) => {
    const selectedValue = event.target.value as T;
    onChange(selectedValue);
  };

  return (
    <select value={String(value)} onChange={handleChange}>
      {options.map(option => (
        <option key={String(option.value)} value={String(option.value)}>
          {option.label}
        </option>
      ))}
    </select>
  );
}
```

### Higher-Order Components (HOCs)

```typescript
// Basic HOC
function withLoading<P extends object>(
  Component: React.ComponentType<P>
): React.FC<P & { isLoading: boolean }> {
  return ({ isLoading, ...props }: P & { isLoading: boolean }) => {
    if (isLoading) {
      return <div>Loading...</div>;
    }
    return <Component {...(props as P)} />;
  };
}

// Usage
interface UserProfileProps {
  user: User;
}

const UserProfile: React.FC<UserProfileProps> = ({ user }) => (
  <div>{user.name}</div>
);

const UserProfileWithLoading = withLoading(UserProfile);

// HOC with additional props
interface WithAuthProps {
  isAuthenticated: boolean;
  user: User | null;
}

function withAuth<P extends WithAuthProps>(
  Component: React.ComponentType<P>
): React.FC<Omit<P, keyof WithAuthProps>> {
  return (props: Omit<P, keyof WithAuthProps>) => {
    const { user, isAuthenticated } = useAuth();

    if (!isAuthenticated) {
      return <div>Please log in</div>;
    }

    return <Component {...(props as P)} isAuthenticated={isAuthenticated} user={user} />;
  };
}

// HOC with generics
function withData<T, P extends { data: T }>(
  Component: React.ComponentType<P>,
  fetchData: () => Promise<T>
): React.FC<Omit<P, "data">> {
  return (props: Omit<P, "data">) => {
    const [data, setData] = useState<T | null>(null);
    const [loading, setLoading] = useState(true);

    useEffect(() => {
      fetchData().then(result => {
        setData(result);
        setLoading(false);
      });
    }, []);

    if (loading || !data) {
      return <div>Loading...</div>;
    }

    return <Component {...(props as P)} data={data} />;
  };
}
```

### Render Props Pattern

```typescript
// Basic render props
interface MousePosition {
  x: number;
  y: number;
}

interface MouseTrackerProps {
  render: (position: MousePosition) => React.ReactNode;
}

function MouseTracker({ render }: MouseTrackerProps) {
  const [position, setPosition] = useState<MousePosition>({ x: 0, y: 0 });

  const handleMouseMove = (event: React.MouseEvent) => {
    setPosition({ x: event.clientX, y: event.clientY });
  };

  return (
    <div onMouseMove={handleMouseMove}>
      {render(position)}
    </div>
  );
}

// Usage
<MouseTracker
  render={({ x, y }) => (
    <div>Mouse position: {x}, {y}</div>
  )}
/>

// Generic render props
interface DataFetcherProps<T> {
  url: string;
  render: (data: T | null, loading: boolean, error: Error | null) => React.ReactNode;
}

function DataFetcher<T>({ url, render }: DataFetcherProps<T>) {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(json => {
        setData(json);
        setLoading(false);
      })
      .catch(err => {
        setError(err);
        setLoading(false);
      });
  }, [url]);

  return <>{render(data, loading, error)}</>;
}

// Usage
<DataFetcher<User[]>
  url="/api/users"
  render={(data, loading, error) => {
    if (loading) return <div>Loading...</div>;
    if (error) return <div>Error: {error.message}</div>;
    if (!data) return null;
    return <ul>{data.map(user => <li key={user.id}>{user.name}</li>)}</ul>;
  }}
/>
```

### Compound Components

```typescript
// Tabs compound component
interface TabsContextType {
  activeTab: string;
  setActiveTab: (id: string) => void;
}

const TabsContext = createContext<TabsContextType | undefined>(undefined);

interface TabsProps {
  defaultTab: string;
  children: React.ReactNode;
}

function Tabs({ defaultTab, children }: TabsProps) {
  const [activeTab, setActiveTab] = useState(defaultTab);

  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      <div className="tabs">{children}</div>
    </TabsContext.Provider>
  );
}

interface TabListProps {
  children: React.ReactNode;
}

function TabList({ children }: TabListProps) {
  return <div className="tab-list">{children}</div>;
}

interface TabProps {
  id: string;
  children: React.ReactNode;
}

function Tab({ id, children }: TabProps) {
  const context = useContext(TabsContext);
  if (!context) throw new Error("Tab must be used within Tabs");

  const { activeTab, setActiveTab } = context;

  return (
    <button
      className={activeTab === id ? "active" : ""}
      onClick={() => setActiveTab(id)}
    >
      {children}
    </button>
  );
}

interface TabPanelProps {
  id: string;
  children: React.ReactNode;
}

function TabPanel({ id, children }: TabPanelProps) {
  const context = useContext(TabsContext);
  if (!context) throw new Error("TabPanel must be used within Tabs");

  const { activeTab } = context;

  if (activeTab !== id) return null;

  return <div className="tab-panel">{children}</div>;
}

// Compound exports
Tabs.List = TabList;
Tabs.Tab = Tab;
Tabs.Panel = TabPanel;

// Usage
<Tabs defaultTab="home">
  <Tabs.List>
    <Tabs.Tab id="home">Home</Tabs.Tab>
    <Tabs.Tab id="profile">Profile</Tabs.Tab>
    <Tabs.Tab id="settings">Settings</Tabs.Tab>
  </Tabs.List>

  <Tabs.Panel id="home">Home Content</Tabs.Panel>
  <Tabs.Panel id="profile">Profile Content</Tabs.Panel>
  <Tabs.Panel id="settings">Settings Content</Tabs.Panel>
</Tabs>
```

### Discriminated Unions

```typescript
// API response types
interface SuccessResponse<T> {
  status: "success";
  data: T;
}

interface ErrorResponse {
  status: "error";
  error: string;
}

interface LoadingResponse {
  status: "loading";
}

type ApiResponse<T> = SuccessResponse<T> | ErrorResponse | LoadingResponse;

// Component using discriminated union
interface DataDisplayProps<T> {
  response: ApiResponse<T>;
  renderData: (data: T) => React.ReactNode;
}

function DataDisplay<T>({ response, renderData }: DataDisplayProps<T>) {
  switch (response.status) {
    case "loading":
      return <div>Loading...</div>;
    case "error":
      return <div>Error: {response.error}</div>;
    case "success":
      return <div>{renderData(response.data)}</div>;
  }
}

// Component state with discriminated unions
type FormState =
  | { type: "idle" }
  | { type: "submitting" }
  | { type: "success"; message: string }
  | { type: "error"; error: string };

function ContactForm() {
  const [formState, setFormState] = useState<FormState>({ type: "idle" });

  const handleSubmit = async (data: FormData) => {
    setFormState({ type: "submitting" });
    
    try {
      await submitForm(data);
      setFormState({ type: "success", message: "Form submitted!" });
    } catch (error) {
      setFormState({ type: "error", error: "Submission failed" });
    }
  };

  return (
    <div>
      {formState.type === "submitting" && <div>Submitting...</div>}
      {formState.type === "success" && <div>{formState.message}</div>}
      {formState.type === "error" && <div>{formState.error}</div>}
    </div>
  );
}
```

---

## Utility Types

### Built-in TypeScript Utility Types

```typescript
// Partial - makes all properties optional
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

function updateUser(id: number, updates: Partial<User>) {
  // Can pass any subset of User properties
}

updateUser(1, { name: "John" }); // Valid
updateUser(1, { email: "john@example.com", age: 30 }); // Valid

// Required - makes all properties required
interface PartialUser {
  id?: number;
  name?: string;
  email?: string;
}

type CompleteUser = Required<PartialUser>;
// All properties are now required

// Readonly - makes all properties readonly
type ReadonlyUser = Readonly<User>;

const user: ReadonlyUser = {
  id: 1,
  name: "John",
  email: "john@example.com",
  age: 30
};

// user.name = "Jane"; // Error: cannot assign to readonly property

// Record - creates object type with specific keys and values
type UserRole = "admin" | "user" | "guest";

const permissions: Record<UserRole, string[]> = {
  admin: ["read", "write", "delete"],
  user: ["read", "write"],
  guest: ["read"]
};

// Pick - selects specific properties
type UserPreview = Pick<User, "id" | "name">;
// { id: number; name: string; }

// Omit - removes specific properties
type UserWithoutId = Omit<User, "id">;
// { name: string; email: string; age: number; }

// Exclude - removes types from union
type Status = "idle" | "loading" | "success" | "error";
type NonIdleStatus = Exclude<Status, "idle">;
// "loading" | "success" | "error"

// Extract - keeps only specific types from union
type SuccessOrError = Extract<Status, "success" | "error">;
// "success" | "error"

// NonNullable - removes null and undefined
type MaybeString = string | null | undefined;
type DefiniteString = NonNullable<MaybeString>;
// string

// ReturnType - gets return type of function
function getUser() {
  return { id: 1, name: "John" };
}

type UserType = ReturnType<typeof getUser>;
// { id: number; name: string; }

// Parameters - gets parameter types of function
function createUser(name: string, age: number) {
  return { name, age };
}

type CreateUserParams = Parameters<typeof createUser>;
// [name: string, age: number]
```

### React-Specific Utility Types

```typescript
// ComponentProps - extracts props from component
const Button = (props: { label: string; onClick: () => void }) => (
  <button onClick={props.onClick}>{props.label}</button>
);

type ButtonProps = React.ComponentProps<typeof Button>;
// { label: string; onClick: () => void; }

// ElementType - accepts any valid React element type
interface PolymorphicProps {
  as?: React.ElementType;
  children: React.ReactNode;
}

function Polymorphic({ as: Component = "div", children }: PolymorphicProps) {
  return <Component>{children}</Component>;
}

// ReactElement vs ReactNode
type ElementOnly = React.ReactElement; // Only React elements
type NodeAllowed = React.ReactNode; // Elements, strings, numbers, null, etc.

// PropsWithChildren - adds children prop
type CardProps = React.PropsWithChildren<{
  title: string;
}>;
// { title: string; children?: ReactNode; }

// ComponentPropsWithoutRef - component props without ref
type DivProps = React.ComponentPropsWithoutRef<"div">;

// ComponentPropsWithRef - component props with ref
type ButtonPropsWithRef = React.ComponentPropsWithRef<"button">;
```

### Custom Utility Types

```typescript
// Make specific properties optional
type PartialBy<T, K extends keyof T> = Omit<T, K> & Partial<Pick<T, K>>;

interface Product {
  id: number;
  name: string;
  price: number;
  description: string;
}

type ProductInput = PartialBy<Product, "id" | "description">;
// id and description are optional, name and price are required

// Make specific properties required
type RequiredBy<T, K extends keyof T> = Omit<T, K> & Required<Pick<T, K>>;

// Nullable type
type Nullable<T> = T | null;

type NullableUser = Nullable<User>;

// Maybe type (null or undefined)
type Maybe<T> = T | null | undefined;

// Deep Partial
type DeepPartial<T> = {
  [P in keyof T]?: T[P] extends object ? DeepPartial<T[P]> : T[P];
};

interface NestedObject {
  user: {
    profile: {
      name: string;
      age: number;
    };
  };
}

type PartialNested = DeepPartial<NestedObject>;
// All nested properties are optional

// Function type helpers
type AsyncFunction<T> = (...args: any[]) => Promise<T>;

type VoidFunction = () => void;

// Ensure all properties are functions
type Functionalize<T> = {
  [P in keyof T]: T[P] extends (...args: any[]) => any ? T[P] : never;
};

// Ensure all properties are NOT functions
type NoFunctions<T> = {
  [P in keyof T]: T[P] extends Function ? never : T[P];
};
```

---

## Best Practices

### Type vs Interface

```typescript
// Use interface for object shapes
interface User {
  id: number;
  name: string;
}

// Use type for unions, intersections, primitives
type Status = "idle" | "loading" | "success" | "error";
type ID = string | number;
type Point = { x: number; y: number };

// Interfaces can be extended
interface Employee extends User {
  department: string;
}

// Types can use intersections
type Admin = User & { role: "admin"; permissions: string[] };

// Interfaces can be merged (declaration merging)
interface Window {
  customProperty: string;
}

interface Window {
  anotherProperty: number;
}
// Window now has both properties

// Prefer interface for public APIs
// Prefer type for internal complex types
```

### Avoid Type Assertions

```typescript
// Bad - type assertion without validation
const user = JSON.parse(data) as User;

// Good - validate the data
function isUser(obj: any): obj is User {
  return (
    typeof obj === "object" &&
    typeof obj.id === "number" &&
    typeof obj.name === "string"
  );
}

const parsed = JSON.parse(data);
if (isUser(parsed)) {
  const user = parsed; // TypeScript knows it's a User
}

// Bad - unnecessary assertion
const element = document.getElementById("myId") as HTMLElement;

// Good - handle null case
const element = document.getElementById("myId");
if (element) {
  // element is HTMLElement here
}
```

### Proper Null Handling

```typescript
// Use optional chaining
const userName = user?.profile?.name;

// Use nullish coalescing
const displayName = user?.name ?? "Guest";

// Type guards for null checks
function processUser(user: User | null) {
  if (user === null) {
    return;
  }
  // user is User here
  console.log(user.name);
}

// Non-null assertion (use sparingly)
const element = document.getElementById("myId")!; // Tells TS it's not null
```

### Discriminated Unions for Complex State

```typescript
// Bad - separate booleans
interface State {
  isLoading: boolean;
  isError: boolean;
  data: Data | null;
  error: Error | null;
}

// Good - discriminated union
type State =
  | { status: "idle" }
  | { status: "loading" }
  | { status: "success"; data: Data }
  | { status: "error"; error: Error };

// Safer to use
function Component({ state }: { state: State }) {
  switch (state.status) {
    case "loading":
      return <div>Loading...</div>;
    case "error":
      return <div>{state.error.message}</div>; // error is available
    case "success":
      return <div>{state.data.title}</div>; // data is available
    default:
      return null;
  }
}
```

### Const Assertions

```typescript
// Without const assertion
const colors = ["red", "blue", "green"];
// Type: string[]

// With const assertion
const colors = ["red", "blue", "green"] as const;
// Type: readonly ["red", "blue", "green"]

// Useful for component props
const BUTTON_SIZES = ["small", "medium", "large"] as const;
type ButtonSize = (typeof BUTTON_SIZES)[number];
// "small" | "medium" | "large"

interface ButtonProps {
  size: ButtonSize;
}
```

### Generics Best Practices

```typescript
// Use descriptive generic names
function map<TInput, TOutput>(
  items: TInput[],
  fn: (item: TInput) => TOutput
): TOutput[] {
  return items.map(fn);
}

// Constrain generics when needed
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

// Default generic types
interface ApiResponse<T = unknown> {
  data: T;
  status: number;
}

// Multiple generic constraints
function merge<T extends object, U extends object>(obj1: T, obj2: U): T & U {
  return { ...obj1, ...obj2 };
}
```

### Component Organization

```typescript
// 1. Type definitions at top
interface TodoItemProps {
  todo: Todo;
  onToggle: (id: number) => void;
  onDelete: (id: number) => void;
}

// 2. Component definition
function TodoItem({ todo, onToggle, onDelete }: TodoItemProps) {
  // 3. Hooks
  const [isHovered, setIsHovered] = useState(false);

  // 4. Callbacks
  const handleToggle = useCallback(() => {
    onToggle(todo.id);
  }, [todo.id, onToggle]);

  const handleDelete = useCallback(() => {
    onDelete(todo.id);
  }, [todo.id, onDelete]);

  // 5. Render
  return (
    <div
      onMouseEnter={() => setIsHovered(true)}
      onMouseLeave={() => setIsHovered(false)}
    >
      <input
        type="checkbox"
        checked={todo.completed}
        onChange={handleToggle}
      />
      <span>{todo.title}</span>
      {isHovered && <button onClick={handleDelete}>Delete</button>}
    </div>
  );
}

// 6. Export
export default TodoItem;
```

### Avoid `any` Type

```typescript
// Bad
function processData(data: any) {
  return data.value;
}

// Good - use unknown for truly unknown data
function processData(data: unknown) {
  if (typeof data === "object" && data !== null && "value" in data) {
    return (data as { value: string }).value;
  }
  throw new Error("Invalid data");
}

// Better - define the expected shape
interface DataShape {
  value: string;
}

function processData(data: DataShape) {
  return data.value;
}

// Use generics for flexible types
function identity<T>(value: T): T {
  return value;
}
```

---

## Conclusion

This guide covered the essential and advanced TypeScript concepts in React:

- **Core Concepts**: Understanding basic types and why TypeScript enhances React
- **Component Typing**: Properly typing function components and their props
- **Hooks**: Using TypeScript with all React hooks
- **Events**: Handling mouse, keyboard, and form events with proper types
- **Context API**: Creating type-safe context providers
- **Advanced Patterns**: Generics, HOCs, render props, and compound components
- **Utility Types**: Leveraging built-in and custom utility types
- **Best Practices**: Writing maintainable, type-safe React code

The key to mastering TypeScript in React is understanding that types are documentation, error prevention, and development tools all in one. Start with basic typing and gradually adopt more advanced patterns as your application grows in complexity.

Remember: TypeScript is there to help you, not hinder you. When types feel too complex, it's often a sign to simplify your code structure rather than bypass the type system.
