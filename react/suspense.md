**Table of Contents**

- [Suspense](#suspense)
- [How it works with server-side rendering](#suspense--how-ssr)
- [Data fetching patterns in React](#suspense--patterns)
- - [Fetch on render](#suspense--f-o-r)
- - [Fetch then render](#suspense--f-t-r)
- - [Render while fetching](#suspense--r-w-f)
- [Suspense use cases](#suspense--cases)
- - [Data fetching](#suspense--fetching)
- - [Lazy loading](#suspense--lazy)
- - [Handling multiple async operations](#suspense--multi-async)
- - [SSR](#suspense--ssr)

[<sub>Source</sub>](https://hygraph.com/blog/react-suspense)

---

<a name="suspense" id="suspense"></a>

# Suspense

**React Suspense** is a built-in feature that simplifies managing asynchronous operations in your React applications.

Unlike data-fetching libraries like Axios or state management tools like Redux, Suspense focuses solely on managing what is displayed while your components wait for asynchronous tasks to complete.

When React encounters a Suspense component, it checks if any child components are waiting for a promise to resolve. If so, React "suspends" the rendering of those components and displays a fallback UI, such as a loading spinner or message, until the promise is resolved.

```tsx
<Suspense fallback={<div>Loading books...</div>}>
  <Books />
</Suspense>
```

In this code snippet, until the data for `Books` is ready, the `Suspense` component displays a fallback UI, in this case, a loading message. This clarifies to the user that the content is being fetched, providing a more seamless experience.

<a name="suspense--how-ssr" id="suspense--how-ssr"></a>

# How it works with server-side rendering

React Suspense also enhances server-side rendering (SSR) by allowing you to render parts of your application progressively.

With SSR, you can use renderToPipeableStream to load essential parts of your page first and progressively load the remaining parts as they become available. Suspense manages the fallbacks during this process, improving performance, user experience, and SEO.

<a name="suspense--patterns" id="suspense--patterns"></a>

# Data fetching patterns in React

When a React component needs data from an API, there are three common data fetching patterns: **fetch on render**, **fetch then render**, and **render as you fetch** (which is what React Suspense facilitates). Each pattern has its strengths and weaknesses.

<a name="suspense--f-o-r" id="suspense--f-o-r"></a>

## Fetch on render

In this approach, the network request is triggered inside the component after it has mounted. This straightforward pattern can lead to performance issues, especially with nested components making similar requests.

```tsx
const UserProfile = () => {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch('/api/user')
      .then(response => response.json())
      .then(data => setUser(data));
  }, []);

  if (!user) return <p>Loading user profile...</p>;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.bio}</p>
    </div>
  );
};
```

In this example, the `fetch` request is triggered in the `useEffect` hook after the component mounts. The loading state is managed by checking if the `user` data is available. This approach can lead to a network waterfall effect, where each subsequent component waits for the previous one to fetch data, causing delays.

<a name="suspense--f-t-r" id="suspense--f-t-r"></a>

## Fetch then render

The fetch-then-render approach initiates the network request before the component mounts, ensuring data is available as soon as the component renders. This pattern helps avoid the network waterfall problem seen in the on-render approach.

```tsx
const fetchUserData = () => {
  return fetch('/api/user')
    .then(response => response.json());
};

const App = () => {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetchUserData().then(data => setUser(data));
  }, []);

  if (!user) return <p>Loading user data...</p>;

  return (
    <div>
      <UserProfile user={user} />
    </div>
  );
};

const UserProfile = ({ user }) => (
  <div>
    <h1>{user.name}</h1>
    <p>{user.bio}</p>
  </div>
);
```

In this example, the `fetchUserData` function is called before the component mounts, and the data is set in the `useEffect` hook. The loading state is managed similarly by checking if the `user` data is available.

This method starts fetching early but still waits for all promises to be resolved before rendering useful data, which can lead to delays if one request is slow.

<a name="suspense--r-w-f" id="suspense--r-w-f"></a>

## Render as you fetch

React Suspense introduces the render-as-you-fetch pattern, allowing components to render immediately after initiating a network request. This improves user experience by rendering UI elements as soon as data is available, without waiting for all data to be fetched.

```tsx
const fetchUserData = () => {
  let data;
  let promise = fetch('/api/user')
    .then(response => response.json())
    .then(json => { data = json });

  return {
    read() {
      if (!data) {
        throw promise;
      }
      return data;
    }
  };
};

const resource = fetchUserData();

const App = () => (
  <Suspense fallback={<p>Loading user profile...</p>}>
    <UserProfile />
  </Suspense>
);

const UserProfile = () => {
  const user = resource.read();
  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.bio}</p>
    </div>
  );
};
```

In this example, the `fetchUserData` function starts fetching data immediately and returns an object with a `read` method. The `read` method throws a promise if the data isn't ready, which triggers Suspense to show the fallback UI. Once available, `read` returns the data, and the component renders it.

This pattern allows each component to manage its loading state independently, reducing wait times and improving the application's overall responsiveness.

<a name="suspense--cases" id="suspense--cases"></a>

# Use cases of Suspense

<a name="suspense--fetching" id="suspense--fetching"></a>

## 1. Data fetching

As shown above.

<a name="suspense--lazy" id="suspense--lazy"></a>

## 2. Lazy loading components

Suspense works seamlessly with React's `lazy()` function to load components only when needed, reducing your application's initial load time. This is especially useful for large applications where not all components are required immediately.

For example, here is a LazyComponent that is dynamically imported and lazy-loaded using `React.lazy()`:

```tsx
const LazyComponent = React.lazy(() => import('./LazyComponent'));

const App = () => (
  <Suspense fallback={<p>Loading component...</p>}>
    <LazyComponent />
  </Suspense>
);
```

In this code, the `<Suspense>` component specifies a fallback message ("Loading component...") to display while the `LazyComponent` is being fetched and loaded.

<a name="suspense--multi-async" id="suspense--multi-async"></a>

## 3. Handling multiple asynchronous operations

Suspense can manage multiple asynchronous operations, ensuring that each part of the UI displays its loading state independently. This is useful in scenarios where different parts of the application fetch data from different sources.

```tsx
const fetchUserData = () => {
  let data;
  let promise = fetch('/api/user')
    .then(response => response.json())
    .then(json => { data = json });

  return {
    read() {
      if (!data) {
        throw promise;
      }
      return data;
    }
  };
};

const fetchPostsData = () => {
  let data;
  let promise = fetch('/api/posts')
    .then(response => response.json())
    .then(json => { data = json });

  return {
    read() {
      if (!data) {
        throw promise;
      }
      return data;
    }
  };
};

const userResource = fetchUserData();
const postsResource = fetchPostsData();

const App = () => (
  <div>
    <Suspense fallback={<p>Loading user...</p>}>
      <UserProfile />
    </Suspense>
    <Suspense fallback={<p>Loading posts...</p>}>
      <Posts />
    </Suspense>
  </div>
);

const UserProfile = () => {
  const user = userResource.read();
  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.bio}</p>
    </div>
  );
};

const Posts = () => {
  const posts = postsResource.read();
  return (
    <ul>
      {posts.map(post => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
};
```

You can also nest `<Suspense>` components to manage rendering order with Suspense:

```tsx
const App = () => (
  <div>
    <Suspense fallback={<p>Loading user profile...</p>}>
      <UserProfile />

      <Suspense fallback={<p>Loading posts...</p>}>
        <Posts />
      </Suspense>
    </Suspense>
  </div>
);
```

<a name="suspense--ssr" id="suspense--ssr"></a>

## 4. Server-side rendering (SSR)

Suspense can improve SSR by allowing you to specify which parts of the app should be rendered on the server and which should wait until the client has more data. This can significantly enhance the performance and SEO of your web application.

```tsx
import { renderToPipeableStream } from 'react-dom/server';
    
const App = () => (
  <Suspense fallback={<p>Loading...</p>}>
    <MainComponent />
  </Suspense>
);

// Server-side rendering logic
const { pipe } = renderToPipeableStream(<App />);
```

The `renderToPipeableStream` function from `react-dom/server` handles the server-side rendering, ensuring that the initial HTML sent to the client is rendered quickly and additional data is loaded progressively.