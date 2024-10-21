---
layout: post
title:  "Writing Reusable Components in React: Best Practices"
date:   2024-10-20 21:30:41 -0400
categories: ReactJS
---

Reusable components are the cornerstone of a scalable React application. They allow you to encapsulate logic and UI into self-contained units that can be easily shared across your app. However, writing a truly reusable component requires some careful consideration to ensure it can be easily adapted to various contexts without being tightly coupled to specific application logic.

In this post, we will explore some best practices for writing reusable React components. Specifically, we’ll focus on avoiding direct access to state management (like React Context, Redux, or Zustand) within the component itself. Instead, we’ll see how to build wrappers around your component to inject dependencies like a context.

### Best Practices for Writing Reusable Components

1. **Separation of Concerns**
   Reusable components should focus on rendering UI and handling generic behavior. Any logic tied to a specific application state or business logic should be abstracted out. For instance, instead of accessing Redux or React Context directly within the component, pass the necessary data and handlers through `props`. This separation ensures the component remains decoupled and can be easily reused in other parts of the application or even across projects.

2. **Use `props` for Customization**
   A reusable component should accept `props` for dynamic data and configuration. This way, the component can behave differently based on the data passed to it. By relying on `props`, the component remains agnostic to the broader state management solution in use (e.g., React Context, Redux, or Zustand).

   ```jsx
   const Button = ({ label, onClick, style }) => {
     return <button onClick={onClick} style={style}>{label}</button>;
   };
   ```

   In the example above, the `Button` component doesn’t care about where the `label` or `onClick` handler comes from. It just receives them via `props` and renders accordingly.

3. **Avoid Side Effects in the Component**
   Reusable components should avoid performing side effects (like making API calls or updating global state). If side effects are necessary, such as fetching data or interacting with a state management library, move those operations into the wrapper or parent component.

4. **Provide Default Values for Props**
   To make components more flexible and reduce the need for repetitive configuration, provide sensible default values for props. This ensures the component works even with minimal configuration.

   ```jsx
   const Button = ({ label = "Click Me", onClick = () => {} }) => {
     return <button onClick={onClick}>{label}</button>;
   };
   ```

5. **Composition Over Inheritance**
   React encourages the use of composition over inheritance. When building reusable components, leverage composition to create complex UIs by combining smaller, simpler components.

   ```jsx
   const Card = ({ title, children }) => {
     return (
       <div className="card">
         <h2>{title}</h2>
         <div>{children}</div>
       </div>
     );
   };
   ```

   In this case, the `Card` component is reusable and composable. It can accept different types of content via the `children` prop.

---

### Do Not Access React Context, Redux, or Zustand Directly in the Component

When creating reusable components, **do not directly access React Context, Redux, or Zustand state inside the component**. Tying a component directly to these state management solutions limits its reusability since it becomes dependent on a specific global state.

#### Why Avoid Direct State Access?

- **Tight coupling**: Accessing context or Redux directly in the component couples the component with the app’s state management strategy, making it harder to use that component in a different project or environment.
- **Reduced flexibility**: If your component relies on specific state structures (like Redux state), it becomes harder to adapt that component to another state structure.
- **Impedes testing**: Components that access global state directly can be harder to test in isolation since they rely on specific providers being present.

Instead, the component should receive the data it needs via props, and the logic for accessing state should be moved to a **wrapper component** or **parent component**.

---

### Build a Wrapper Around the Component to Use React Context

Instead of accessing context or state management libraries directly, you can build a wrapper that injects the necessary data and handlers into the reusable component. This allows the component to remain simple and generic while still being integrated with the larger application state when needed.

#### Example: Wrapping a Component to Use React Context

Let’s say we have a `ThemeContext` that provides the current theme for the app (e.g., `light` or `dark`). Instead of making the reusable component aware of the context, we can create a wrapper to inject the context data.

1. **The Reusable Component (Button)**

   ```jsx
   const Button = ({ label, theme, onClick }) => {
     return (
       <button onClick={onClick} className={`btn-${theme}`}>
         {label}
       </button>
     );
   };
   ```

   Here, the `Button` component is completely unaware of any global state or context. It simply takes `theme` as a prop.

2. **ThemeContext**

   ```jsx
   const ThemeContext = React.createContext();
   ```

3. **Wrapper Component (ThemedButton)**

   Now we’ll create a wrapper component that connects the reusable `Button` component to the `ThemeContext`.

   ```jsx
   const ThemedButton = ({ label, onClick }) => {
     const theme = React.useContext(ThemeContext);

     return <Button label={label} theme={theme} onClick={onClick} />;
   };
   ```

   The `ThemedButton` component handles the logic of fetching the current theme from the context. It passes the `theme` prop down to the reusable `Button` component, allowing the `Button` to remain decoupled from the app’s state management.

### Isolated Testing

Since the reusable component is unaware of any global state, you can test it in complete isolation. You simply need to provide the necessary props and verify that it behaves as expected. There is no need to mock global state providers like React Context or Redux in your tests.

---

### Conclusion

Writing reusable components in React is essential for building scalable and maintainable applications. By adhering to best practices such as passing data through props, avoiding side effects, and separating logic from UI, you can ensure that your components are flexible and reusable across different parts of your app.

Remember, do not access React Context, Redux, or Zustand directly inside the component. Instead, create wrapper components that handle the state management logic and pass the necessary data as props. This approach keeps your components clean, reusable, and easy to maintain.
