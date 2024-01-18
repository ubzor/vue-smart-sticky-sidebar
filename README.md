# Vue Smart Sticky Sidebar

Vue 3 sidebar component that sticks to top or bottom of the screen when page scrolls, based on scroll direction.

## Installation

```sh
yarn add vue-smart-sticky-sidebar
```

## Usage

Import `SmartStickySidebar` component in script section of SFC:

```ts
import { SmartStickySidebar } from 'vue-smart-sticky-sidebar'
```

Then in template section:

```html
<!-- element with display: flex -->
<div class="wrapper">
    <SmartStickySidebar>
        <!-- sidebar content here -->
    </SmartStickySidebar>

    <main class="content">
        <!-- main content there -->
    </main>
</div>
```

## Props

### container-class

type: `any`
default: `''`

Accepts any valid expression for outer (static) element's `:class` attribute

### content-class

type: `any`
default: `''`

Accepts any valid expression for inner (moving) element's `:class` attribute

### top-spacing

type: `number`
default: `0`

Distance in pixels between sidebar content sticked to the top and window top

### bottom-spacing

type: `number`
default: `0`

Distance in pixels between sidebar content sticked to the bottom and window bottom

## Dev Playground

Run in dev mode:

```sh
yarn dev
```

Open link from console in browser to see Playground

## TODO

-   ~~Basic component functionality~~
-   ~~Basic dev plauground~~
-   ~~Build as a lib~~
-   ~~This readme~~
-   Implement testing
-   Improve dev playground
-   New features
    -   disabling sticky behavior
    -   emit events, expose state
    -   ???

---

ubzor (c) 2024
