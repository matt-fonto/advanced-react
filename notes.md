# Advanced React

1. Intro to re-renders
2. Elements, children and Re-renders
3. Components as props
4. Render props
5. Mastering memoization
6. Masting Reconcilation
7. Making sense of Higher Order Component (HOC)

## React Lifecyle

1. Mounting: Component is added to the DOM. Instance + State + DOM init

- Instance creation: the component is initialized
- State initialization: states are setup
- DOM creation: React renders the component and inserts them into the DOM

2. Unmounting: Component is removed from the DOM

- Clean up logic: clearing timers, unsubscribing from listeners

3. Re-rendering

- Triggered by state change
- Faster than mounting through reuse: Making re-renders is faster than mounting. React reuses as much existing DOM elements where possible
- State update is triggered, the component re-renders
- Everything below the component is re-rendered
- The component itself and all the other components inside it will re-render
- The solution is to move state down to the lowest level possible
- The state should be as local as possible

### Composition trick to reduce re-renders | Container-Presenter Pattern

- The pattern below (Container-Presenter Pattern | Wrapper Component Pattern | Children-as-a-Function pattern) refers to children being passed directly and not manipulated by the parent
- This pattern avoids unnecessary re-renders because of how React handles children components and state updates in the parent

#### What is happening?

1. Children are rendered once

   - The components are passed as children. These children aren't directly tied to the parent's state
   - When the state `position` updates, React doesn't re-rendered the children because they aren't dependent on the parent's state

```tsx
export default function App() {

  return (
    <ScrollableWithMovingBlock> // parent -> it has the state, but it doesn't influence the children
    children -> we don't need to pass it as props, only wrap it
        <VerySlowComponent/> // these components (children) are ignorant to the scrolling logic and aren't influenced by it
        <BunchOfStuff/>
        <OtherStuffAlsoCompliated>
    </ScrollableWithMovingBlock>
)
}
```

```tsx
function ScrollableWithMovingBlock({ content }: { content: ReactNode }) {
  const [position, setPosition] = useState(150);

  const onScroll = (e) => {
    const calculated = getPosition(e.target.scrollTop);
    setPosition(calculated);
  };

  return (
    <div className="scrollable" onScroll={onScroll}>
        <MovingBlock position={position}>
        {content}
    </div>
  );
}
```

## React components

- A React component is JS function that returns a React Element

```tsx
function ChildComponent(){
    return ...
}

function Parent(){
  return React.createElement(ChildComponent, null, null);
}
```

- If we log a component, we'll see something like:

```tsx
{
    type: ChildComponent,
    props: {
        content: {
            // children or other props
            ...
        }
    }
    ... // React stuff
}
```

### The danger of custom hooks

- The issue is that even though the state is in the hook and not the component anymore, this will still cause re-render, however, it will be more difficult to identify it
- The component will re-render as though the state, which was updated, was part of it
- Props change causes re-render: MYTH
- State was changed, React will re-render the component and all its children, regardless of the props being passed
-

## Components as Props

- Generic components

```tsx
// As a button
const App = () => {
  return (
    <>
      <Button icon={<Icon />} />
      <Button icon={<LoadingIcon />} />
    </>
  );
};

// As a modal
const App = () => {
  return (
    <>
      <ModalDialog
        footer={
          <>
            <Button>Cancel</Button>
            <Button>Cancel</Button>
          </>
        }
      />
    </>
  );
};
```

### Principles

#### Component Design and composition

1. Single responsibility: Each component does one thing and one thing well
2. Composition over inheritance: Build components by combining smaller components
3. Reusable components: Components that are reusable and modular.

#### State management

1. Optimize state placement: Keep state as local as possible
2. Avoid prop drilling
