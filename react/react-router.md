**Table of Contents**

- [React Router](#react-router)
- [Navigation menu](#react-router--nav-menu)
- [Outlets](#react-router--outlet)
- [Dynamic Routes](#react-router--dynamic)
- [Protected Routes](#react-router--protected)

---

<a name="react-router" id="react-router"></a>

# React Router

A router in React handles navigation between different views or pages without reloading the whole browser page. It matches the current URL to a specific component and renders it, giving the app the feel of multiple pages while staying a single-page application. Several libraries exist to add this functionality, since React itself does not include routing.

[React Router](https://reactrouter.com/home) is primarily a fully-featured routing solution for React apps. It offers pre-developed components, Hooks, and utility functions to create modern routing strategies. React Router is notable because it also uses the full-stack framework Remix. As a result, React Router has a wide range of use cases that span from simple routing to a full-stack framework.

<a name="react-router--nav-menu" id="react-router--nav-menu"></a>

## Adding a navigation menu

The way to navigate between different webpages in HTML is to use an anchor tag, as shown below:

```tsx
<a href="/about">Some Link Name</a>
```

But using this approach in a React app is going to lead to refreshing the entire webpage each time the user clicks a link. This is not the advantage you are looking for when using a library like React. To avoid refreshing the webpages, React Router provides the `Link` and `NavLink` component.

The `Link` component works very similarly to the HTML anchor tag. It has a `to` prop that just like `href`, accepts a route to link to. `NavLink` on the other hand, is an extension of `Link` component that can change styles if the route it links to is active.

```tsx
<NavLink
  to={ROUTES.SOME_WHERE}
  className="..."
>
  Somewhere
</NavLink>
```

<a name="react-router--outlet" id="react-router--outlet"></a>

## `<Outlet/>`

`<Outlet />` is a React Router component used to render the component associated with a child route inside a parent route. It is commonly used for nested routing. The parent component defines the shared layout or UI, while `<Outlet />` determines where the currently matched child route will be rendered.

If no child route matches, `<Outlet />` renders nothing unless a default/index route is configured.

```tsx
import { Outlet } from "react-router-dom";

function DashboardLayout() {
  return (
    <div>
      <nav>Dashboard Navigation</nav>

      <main>
        <Outlet />
      </main>
    </div>
  );
}

const routes = [
  {
    path: "/dashboard",
    element: <DashboardLayout />,
    children: [
      {
        path: "profile",
        element: <Profile />,
      },
      {
        path: "settings",
        element: <Settings />,
      },
    ],
  },
];

```

When the user visits `/dashboard/profile`, the resulting structure is effectively:
```
DashboardLayout
├── Navigation
└── Profile
```

The `<Outlet />` inside `DashboardLayout` is where `<Profile />` is rendered. Similarly, `/dashboard/settings` renders `<Settings />` in the same `<Outlet />` location.

<a name="react-router--dynamic" id="react-router--dynamic"></a>

## Dynamic Routes

Dynamic routes allow a route to match different URL values using dynamic segments. A dynamic segment is defined by placing a parameter name after a colon (`:`), such as `:id`.

React Router extracts the value from the URL and makes it available through the `useParams()` hook. Dynamic routes are useful for pages such as user profiles, product details, blog posts, or any resource identified by an ID.

```tsx
import { useParams } from "react-router-dom";

function UserProfile() {
  const { id } = useParams();

  return <h1>User ID: {id}</h1>;
}
```

```tsx
const routes = [
  {
    path: "/users/:id",
    element: <UserProfile />,
  },
];
```

Now different URLs will use the same component:

```
/users/123  → User ID: 123
/users/456  → User ID: 456
/users/789  → User ID: 789
```

Here, `:id` is the dynamic route parameter, and its value can be retrieved using `useParams()`.

<a name="react-router--protected" id="react-router--protected"></a>

## Protected Routes

Protected routes are routes that should only be accessible to **authenticated or authorized** users. They are typically implemented by creating a wrapper component that checks whether the user is authenticated. If the user is authenticated, the wrapper renders the requested route using `<Outlet />`. Otherwise, it redirects the user to a login page using `<Navigate />.`

```tsx
import { Navigate, Outlet } from "react-router-dom";

function ProtectedRoute() {
  const isAuthenticated = true; // Replace with your authentication logic

  return isAuthenticated ? <Outlet /> : <Navigate to="/login" replace />;
}
```

**OR**

```tsx
function ProtectedRoute({ user, children }:{
  user: User;
  children: React.ReactNode;
}) {
  const navigate = useNavigate();
  useEffect(() => {
    if (!user) {
      navigate("/login");
    }
  });

  return children;
}
```

The protected routes can then be grouped under the `ProtectedRoute`:

```tsx
const routes = [
  {
    element: <ProtectedRoute />,
    children: [
      {
        path: "/dashboard",
        element: <Dashboard />,
      },
      {
        path: "/profile",
        element: <Profile />,
      },
    ],
  },
  {
    path: "/login",
    element: <Login />,
  },
];
```