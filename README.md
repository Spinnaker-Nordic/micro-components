# Micro-components

Micro-components is a lightweight framework that allows code to be mounted on DOM elements.

## Installation

```bash
$ yarn add @spinnakernordic/micro-components
```

## Components

### Creating a component

```typescript
import type { ComponentType } from '@spinnakernordic/micro-components';

const Component: ComponentType = (node, { app, emitter }) => {
  console.log(node); // The DOM element
  console.log(app); // The app instance
  console.log(emitter); // Event emitter
};

export default Component;
```

### Registering a component

```typescript
import { App } from '@spinnakernordic/micro-components';

const app = new App({
  // Asynchronous, loads component with custom loaders/event listeners
  'my-component': ['mouseenter', () => import('components/my-component')],

  // Synchronous, loads component immediately
  'my-component': require('components/my-component'),
});

app.mount();
```

Create DOM element(s) with the attribute `data-component` containing the name of your component

```html
<div data-component="my-component"></div>
```

## Creating custom loaders

When default DOM events are not sufficient to load your component, you may want to create a custom loader.

```typescript
import type { ComponentLoaderType } from '@spinnakernordic/micro-components';

const Loader: ComponentLoaderType = ({ node, emitter }, load) => {
  // In this example we call load() on a custom event listener
  document.addEventListener('my-custom-event', load);

  // Return a function which disconnects the listener
  return () => {
    document.removeEventListener('my-custom-event', load);
  };
};

export default Loader;
```

### Register the loader on a component

```typescript
import customLoader from 'loaders/custom-loader',

const app = new App({
  'my-component': [customLoader, () => import('components/my-component')],
});

app.mount();
```

### Using multiple loaders

It is very simple to add multiple loaders for a component.
In cases where only regular events that are emitted from the node itself is used, you can pass a single string containing these.
If you want to combine multiple custom loaders, you can use an array.

```typescript
import customLoader from 'loaders/custom-loader',
import otherCustomLoader from 'loaders/other-custom-loader',

const app = new App({
  // Multiple regular events
  'my-component': ['mouseenter click', () => import('components/my-component')],
  
  // Combine multiple custom loaders
  'my-component': [
    [customLoader, otherCustomLoader, 'mouseenter'],
    () => import('components/my-component'),
  ],
});

app.mount();
```
