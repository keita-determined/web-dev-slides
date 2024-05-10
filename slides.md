---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background:
# some information about your slides, markdown enabled
title: Web Development 101
info: |
  ## Web Development 101
  Presentation slides for Determined AI Web learners.

# apply any unocss classes to the current slide
class: text-center
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# https://sli.dev/guide/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/guide/syntax#mdc-syntax
mdc: true
---

# Web Development 101

Keita Tomato

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---

# What is Web Development?

<div v-click>
<img
  id="cake"
  class="w-80 opacity-100 text-center"
  src="/cake.png"
  alt=""
/>
</div>

<v-switch transition="true">
<template #1><div class="text-center text-3xl">It's a cake!</div></template>
<template #2><div class="text-center text-3xl">It's a piece of cake! 🍰🍰🍰</div></template>
</v-switch>

<!--
You can have `style` tag in markdown to override the style for the current page.
Learn more: https://sli.dev/guide/syntax#embedded-styles
-->

<!--
Here is another comment.
-->

<style>
#cake {
  margin: auto;
}
</style>

---

# Tech Stack

|                                                           |                                    |
| --------------------------------------------------------- | ---------------------------------- |
| [**Typescript**](https://www.typescriptlang.org/)         | Programming Language               |
| [**React**](https://react.dev/?uwu=true)                  | JS Framework                       |
| [**Ant Design**](https://ant.design/components/overview/) | UI Library                         |
| [**Testing Library**](https://testing-library.com/)       | Testing Library                    |
| [**Hew**](https://github.com/determined-ai/hew)           | UI Kit for HPE AI at Scale Project |

---

# Folder Structure

In `webui/react`

```txt {all|8,13,15}
├── public
│   ├── determined
│   └── hpe
├── scripts
└── src
    ├── __mocks__
    ├── assets
    ├── components
    ├── constants
    ├── e2e
    ├── ee
    ├── fixtures
    ├── hooks
    ├── omnibar
    ├── pages
    ├── routes
    ├── services
    ├── stores
    ├── styles
    ├── utils
    └── vendor
```

---

# How to Write React Component

<div>A Component is a reusable piece of code.</div>

````md magic-move
```tsx {all|1|2|3|6}
// components/Counter.tsx
const Counter = (): JSX.Element => { // Component name. Function component
  return (); // write JSX
};

export default Counter; // export component to use somewhere outside of this file
```

```tsx {all|3-10}
// components/Counter.tsx
const Counter = (): JSX.Element => {
  return (
    // JSX
    <div>
      <h1>Counter</h1>
      <button>Counter</button>
      <div data-testid="counter-text">Counter: 0</div>
    </div>
  );
};

export default Counter;
```

```tsx {all|2,5-6,12}
// components/Counter.tsx
import { useState } from 'react';

const Counter = (): JSX.Element => {
  // [the current state, the set function to update the state]
  const [counter, setCouner] = useState<number>(0); // React hooks

  return (
    <div>
      <h1>Counter</h1>
      <button>Counter</button>
      <div data-testid="counter-text">Counter: {counter}</div>
    </div>
  );
};

export default Counter;
```

```tsx {all|7-11,16|all}
// components/Counter.tsx
import { useCallback, useState } from 'react';

const Counter = (): JSX.Element => {
  const [counter, setCouner] = useState<number>(0);

  const onUpdateCount = useCallback(() => {
    setCouner((prev) => prev + 1); // update counter state
    // ❌ DON'T DO this because it won't trigger re-rendering (not update the counter state)
    // counter += 1
  }, []);

  return (
    <div>
      <h1>Counter</h1>
      <button onClick={onUpdateCount}>Counter</button>
      <div data-testid="counter-text">Counter: {counter}</div>
    </div>
  );
};

export default Counter;
```
````

---

# How to Use the Component?

```tsx
import Counter from 'components/Counter'; // Here!

const WhateverOtherComponent = (): JSX.Element => {
  // some code

  return (
    // some code
    <Counter /> // Here!
  );
};
```

<div v-click>
<div class="text-center">A Component is a <span v-mark.circle.orange="1">reusable</span> piece of code.</div>
<div class="text-center text-3xl" >Demo Time!</div>
</div>

---

# How to Debug React Code?

- Code Search in the Editor
- Browser Inspector
  - Elements
  - Console
  - Network
  - Components
- `console.log()` in code

<div v-click>
<div class="text-center text-3xl" >Demo Time!</div>
</div>

---

# How to Write Test

#### [Testing Library](https://testing-library.com/) for Unit and Component Tests

```tsx {all|1|2-5|7-12|14-16|18|20-23|24-27|29|30|32-41}{maxHeight:'400px'}
// components/Counter.test.tsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

import Counter from 'components/Counter';

const setup = () => {
  render(<Counter />); // render arbitrary components
  const user = userEvent.setup(); // simulates user interactions by dispatching the events

  return { user };
};

describe('Counter', () => {
  it('should have a button with Counter text', () => {
    setup();

    expect(screen.getAllByText('Counter')).toHaveLength(2);

    // use queryBy if the element potentially does not exist
    // https://testing-library.com/docs/dom-testing-library/cheatsheet/#queries
    expect(screen.queryByText('Ebichu')).not.toBeInTheDocument();

    // Error since there are multiple elements with `Counter`
    // expect(screen.getByText('Counter')).toBeInTheDocument();
    expect(screen.getByRole('button', { name: 'Counter' })).toBeInTheDocument();
  });

  it('should be clickable', async () => {
    const { user } = setup();

    const button = screen.getByRole('button', { name: 'Counter' });

    for (let i = 0; i < 10; i++) {
      // why findby? Read https://testing-library.com/docs/queries/about/
      expect(await screen.findByTestId('counter-text')).toHaveTextContent(
        `Counter: ${i}`
      );
      // await until promise is resolved
      await user.click(button);
    }
  });
});
```

---

# Useful Links

|                                                                                                            |                                          |
| ---------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
| [**Mozilla**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) | Mozilla as Web Docs (async for example!) |
| [**React**](https://react.dev/learn?uwu=true)                                                              | Good to Read All Tutorials               |
| [**Typescript**](https://www.typescriptlang.org/docs/handbook/typescript-from-scratch.html)                | TS for the New Programmer                |
| [**Testing Library**](https://testing-library.com/docs/recipes)                                            | Testing Library Example                  |
| [**Tomato**](https://hpe.enterprise.slack.com/archives/D02Q0SG1UF4)                                        | DM Keita Tomato to Ketchup               |

---

# Thank you

<div class="w-60 relative">
  <div class="relative w-40 h-40">
    <img
      v-motion
      :initial="{ x: 800, y: -100, scale: 1.5, rotate: -50 }"
      :enter="final"
      class="absolute inset-0"
      src="/tomato-paste.png"
      alt=""
    />
  </div>

  <div
    class="text-5xl absolute top-14 left-40 text-[#2B90B6] -z-1"
    v-motion
    :initial="{ x: -80, opacity: 0}"
    :enter="{ x: 0, opacity: 1, transition: { delay: 2000, duration: 1000 } }">
    Any Questions?
  </div>
</div>

<!-- vue script setup scripts can be directly used in markdown, and will only affects current page -->
<script setup lang="ts">
const final = {
  x: 0,
  y: 0,
  rotate: 0,
  scale: 1,
  transition: {
    type: 'spring',
    damping: 10,
    stiffness: 20,
    mass: 2
  }
}
</script>
