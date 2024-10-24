---
layout: post
short_title: Fixing Node's Relative Module Imports
title: Fixing Node's Relative Module Imports
author: YourOrdinaryCat
---

I bet you hate how this looks as much as I do:

```js
import { thing, weee } from "../../../../utils/index.js";

// Or, if you're not using ESM just yet
const { thing, weee } = require("../../../../utils");
```

If you don't want to read 2 paragraphs of incoherent rambling, [skip to the solution](#the-solution).

Say what you will about how hard this is to refactor, I just despise how it looks. Then you look up a solution, and it requires installing an npm package with 73 dependencies doing silly strange things, just to be able to write something that is natively supported on your frontend thanks to one of its 143897 dependencies:

```js
import { thing, weee } from "@/utils";
```

We won't write exactly that, but it _is_ how imports could look if you were using the built-in support for this in [Next.js](https://nextjs.org/docs/pages/building-your-application/configuring/absolute-imports-and-module-aliases), and it's a good approximation of what we want. Of course, we're not insane enough to use Next.js for our backend, we ran out of insanity using it for the frontend!

## The Solution

Anyways, we'll fix the example from the start of the article. On `package.json`:

```json
{
  "imports": {
    "#utils/*": "./dist/utils/*"
  }
}
```

Now, we can import like this:

```js
import { thing, weee } from "#utils/index.js";

// TODO: switch to ESM already!
const { thing, weee } = require("#utils");
```

Of course, I'm assuming you have that folder structure - you should change it based on yours, of course.

## TypeScript Support

What's that? You want to use TypeScript? Of course - having this in your `tsconfig.json` should do:

```json
{
  "compilerOptions": {
    "paths": {
      "#utils/*": ["./src/utils/*"]
    }
  }
}
```

The same import will work with TSC and the language server.

This also works pretty well with TSX, and CommonJS - you can read up on `imports` [here](https://nodejs.org/api/packages.html#imports), and `paths` [here](https://www.typescriptlang.org/tsconfig/#paths). And you don't need to add a single dependency!
