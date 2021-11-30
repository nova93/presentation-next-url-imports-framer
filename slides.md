---
theme: seriph
background: https://source.unsplash.com/collection/94734566/1920x1080
class: text-center
highlighter: shiki
lineNumbers: false
info: |
  ## Slidev Starter Template
  Presentation slides for developers.
drawings:
  persist: false
title: Next.js 12 URL Imports and Framer
---

# Next.js 12 URL Imports

# &

# Framer

<div class="abs-br m-6 flex gap-2">
  <a href="https://github.com/nova93/presentation-next-url-imports-framer" target="_blank" alt="GitHub"
    class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

<!--
- Next.js 12 release
- Middleware on-edge functions
- Rust compiler
- AND URL IMPORTS
- which enable some proper awesome tooling in Framer

oh and by the way, this whole presentation is an SPA, just in case there wasn't enough cool stuff to cover in this presentation
-->

---

# URL Imports

Next.js >= v12.0.0

URL imports are an experimental feature that allows you to import modules directly from external servers (instead of from the local disk).

To opt-in, add the allowed URL prefixes inside `next.config.js`:

```ts
module.exports = {
  experimental: {
    urlImports: ["https://example.com/modules/"],
  },
};
```

Then, you can import modules directly from URLs:

```jsx
import { a, b, c } from "https://example.com/modules/some/module.js";
```

<!--
Here's a blurb from next documentations.

As you can see, it's trivial to use this feature. We could quite easily implement this across our apps.

However, there are a few considerations:
1. Any imported package has to be an ESM module
2. `next.lock` files
-->

---

# Let's see if it works

A new app, imports Vehicle Search Micro-app

<br />
<br />
<br />

```jsx
// pages/index.js
import Vs from "http://127.0.0.1:8887/marketplace-vehicle-search-micro-app.esm.js";

export default function Home() {
  const router = useRouter();

  return (
    <Wrapper>
      <Vs basePath={"/"} router={router} />
      <p>ble</p>
    </Wrapper>
  );
}
```

<!--
Following the documentation, I've added our existing Vehicle Search Micro-app to a new next.js app.

Seems easy enough, just a URL import, and normal usage, as if it was any other component/package.

There was a bit more config needed, due to styled-components
-->

---

```js {all|4-6|7-16}
// next.config.js
module.exports = {
  reactStrictMode: true,
  experimental: {
    urlImports: ["http://127.0.0.1:8887"],
  },
  webpack: (config) => {
    config.resolve.alias = {
      ...config.resolve.alias,
      "styled-components": require.resolve(
        "./node_modules/styled-components/dist/styled-components.esm.js"
      ),
    };

    return config;
  },
  publicRuntimeConfig: {
    isStaging: true,
  },
};
```

<small><https://github.com/vercel/next.js/issues/31088></small>

<!--
At the top, we have typical `next.config.js` setup, including the experimental URL imports allowlist.

The custom webpack alias was required to get it sort of working. Notice it's resolving ESM package. CommonJS packages don't work with URL Imports.

Sort of, because it's still not working fully, but at least with this, we are getting the app to start.
-->

---

# Well

We could maybe fix this, but it's clear at this point that this is not a production ready feature

<img src="/url-imports-outcome.png" />

<small><https://github.com/oodlefinance/federated-modules-poc/tree/main/next-shell></small>

<!--
As I was working through the ever increasing array of errors, it became very obvious that this is not quite production ready yet, at least for complex systems, such as micro-apps

Next.js considers this feature experimental, and notes security concerns with the current implementation.

However, there is a way to use this really cool feature...
-->

---

# Enter Framer

An awesome tool, with even cooler feature > <strong><u>Handshake</u></strong>

Get started: <https://www.framer.com/docs/guides/handshake/>

<br />

```jsx
import Button from "https://framer.com/m/Button-Vuaa.js@6PVVpQlmjSnjigo6pxzB";

const Component = () => (
  <Button tap={() => console.log("pressed")} text="hello there" />
);
```

<br />
<br />
<br />

- Designer created component, which can be directly implemented
- Can use variables (props)
- You can create Components using Code inside Framer!

<!--
Framer is a design tool, which you can use in browser, but they also offer installable apps.

From design side of things, they offer similar functionality to all the other design tools.

With the launch of URL Imports in Next.js, they've also launched handshake, a very neat tool, which allows devs to use components directly in code, without the need to rebuild them.

As this example shows, and I will show you a live one in a second, they can also take props, which opens up a huge range of possibilities.

Also, as an added bonus, you can actually write Framer components in code, directly in Figma.

Now, let's see how it works on a real project
-->
