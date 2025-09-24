# React 19 - Detailed Notes

## React Compiler

The React Compiler is one of the most significant additions to React 19. It automatically optimizes your React code without requiring manual memoization.

### Key Benefits
- Automatic memoization of components and values
- Eliminates the need for `useMemo`, `useCallback`, and `memo` in most cases
- Optimizes re-renders automatically
- Works with existing code without breaking changes

### How it Works
- Analyzes your components at build time
- Identifies optimization opportunities
- Generates optimized code that maintains React's semantics
- Preserves debugging experience

## New Hooks

### `use()` Hook
- Can unwrap promises and context values
- Works inside conditionals and loops (unlike other hooks)
- Simplifies async data fetching patterns

```javascript
function MyComponent() {
  const data = use(fetchData()); // Can be used conditionally
  return <div>{data.name}</div>;
}
```

### `useActionState()` Hook
- Manages state for server actions and form submissions
- Provides pending state and error handling
- Replaces the experimental `useFormState`

```javascript
function MyForm() {
  const [state, formAction, isPending] = useActionState(submitAction, initialState);
  
  return (
    <form action={formAction}>
      <input name="name" disabled={isPending} />
      <button type="submit" disabled={isPending}>
        {isPending ? 'Submitting...' : 'Submit'}
      </button>
    </form>
  );
}
```

### `useFormStatus()` Hook
- Provides status information about form submissions
- Must be used within a form component
- Returns pending state and form data

```javascript
function SubmitButton() {
  const { pending, data, method, action } = useFormStatus();
  
  return (
    <button type="submit" disabled={pending}>
      {pending ? 'Submitting...' : 'Submit'}
    </button>
  );
}
```

### `useOptimistic()` Hook
- Enables optimistic UI updates
- Immediately shows expected result while async operation is in progress
- Automatically reverts if operation fails

```javascript
function TodoList({ todos, addTodo }) {
  const [optimisticTodos, addOptimisticTodo] = useOptimistic(
    todos,
    (state, newTodo) => [...state, newTodo]
  );
  
  const handleSubmit = async (formData) => {
    const newTodo = { id: Date.now(), text: formData.get('todo') };
    addOptimisticTodo(newTodo);
    await addTodo(newTodo);
  };
  
  return (
    <div>
      {optimisticTodos.map(todo => <div key={todo.id}>{todo.text}</div>)}
      <form action={handleSubmit}>
        <input name="todo" />
        <button type="submit">Add</button>
      </form>
    </div>
  );
}
```

## Actions

Actions provide a new way to handle user interactions and async operations.

### Server Actions
- Functions that run on the server
- Can be called directly from client components
- Automatic serialization and error handling

### Form Actions
- Simplified form handling without controlled components
- Built-in pending states and error management
- Progressive enhancement friendly

```javascript
async function createPost(formData) {
  'use server';
  
  const title = formData.get('title');
  const content = formData.get('content');
  
  // Server-side logic
  await savePost({ title, content });
}

function CreatePostForm() {
  return (
    <form action={createPost}>
      <input name="title" placeholder="Post title" />
      <textarea name="content" placeholder="Post content" />
      <button type="submit">Create Post</button>
    </form>
  );
}
```

## Document Metadata

React 19 provides native support for document metadata without requiring external libraries.

### Features
- Direct rendering of `<title>`, `<meta>`, and `<link>` tags
- Automatic deduplication
- Server-side rendering support

```javascript
function BlogPost({ post }) {
  return (
    <div>
      <title>{post.title}</title>
      <meta name="description" content={post.excerpt} />
      <meta property="og:title" content={post.title} />
      <link rel="canonical" href={`/posts/${post.slug}`} />
      
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </div>
  );
}
```

## Web Components Integration

Improved support for custom elements and web components.

### Enhancements
- Better prop passing to custom elements
- Improved event handling
- Automatic property vs attribute detection

```javascript
function MyComponent() {
  return (
    <my-custom-element
      stringProp="hello"
      numberProp={42}
      objectProp={{ key: 'value' }}
      onCustomEvent={handleEvent}
    />
  );
}
```

## Asset Loading

Built-in support for preloading resources and managing asset dependencies.

### Resource Preloading
- `preload()` - Preload resources that will be needed soon
- `preinit()` - Load and initialize resources immediately
- `prefetchDNS()` - Prefetch DNS for external domains

```javascript
import { preload, preinit } from 'react-dom';

function MyComponent() {
  // Preload a resource
  preload('/api/data.json', { as: 'fetch' });
  
  // Load and initialize a stylesheet
  preinit('/styles/component.css', { as: 'style' });
  
  return <div>My Component</div>;
}
```

## Breaking Changes

### Removed Features
- `defaultProps` for function components (use default parameters instead)
- Some legacy context APIs
- Deprecated React DOM APIs

### Stricter Hydration
- More strict matching between server and client
- Better error messages for mismatches
- Improved debugging experience

### TypeScript Updates
- Updated type definitions
- Better inference for new hooks
- Stricter typing for some APIs

## Migration Guide

### From React 18 to React 19

1. **Update Dependencies**
   ```bash
   npm install react@19 react-dom@19
   ```

2. **Review Breaking Changes**
   - Replace `defaultProps` with default parameters
   - Update any deprecated API usage
   - Test hydration in SSR applications

3. **Adopt New Features Gradually**
   - Start with the React Compiler for automatic optimizations
   - Migrate forms to use Actions where appropriate
   - Use new hooks for better UX patterns

4. **Update TypeScript**
   ```bash
   npm install @types/react@19 @types/react-dom@19
   ```

## Performance Considerations

### React Compiler Benefits
- Reduced bundle size through better tree shaking
- Fewer re-renders due to automatic memoization
- Better runtime performance

### New Patterns
- Actions reduce client-side JavaScript for form handling
- Optimistic updates improve perceived performance
- Better resource loading reduces time to interactive

## Best Practices

1. **Embrace the React Compiler**
   - Remove manual memoization where the compiler can handle it
   - Trust the compiler's optimizations
   - Focus on writing clean, readable code

2. **Use Actions for Forms**
   - Prefer server actions over client-side form handling
   - Use `useActionState` for complex form logic
   - Implement optimistic updates for better UX

3. **Leverage New Hooks**
   - Use `use()` for cleaner async patterns
   - Implement `useOptimistic()` for responsive interfaces
   - Utilize `useFormStatus()` for better form UX

4. **Document Metadata**
   - Replace third-party solutions with native support
   - Use for better SEO and social sharing
   - Take advantage of automatic deduplication

## Resources

- [React 19 Official Documentation](https://react.dev/)
- [React Compiler Documentation](https://react.dev/reference/react/use)
- [Migration Guide](https://react.dev/blog/2024/12/05/react-19)
- [Community Examples and Discussions](https://github.com/facebook/react/discussions)