---
title: Bundling
template: doc.hbs
url: /docs/bundling/
---

# Bundling

One of the benefits of the Juno framework is the integrated bundling solution. Rather than having to bundle all possible assets, scripts, and styles into a large set that covers every possible client state, Juno bundles only the required assets to successfully present the requested view.

The Juno bundler is a custom Webpack implementation named Revelation. Revelation automatically links the JavaScript, Dust, and Stylus files together into one JavaScript file and one CSS for per ViewComponent. Bundles are created for every ViewComponent in a given site. When a given page is requested, the scripts and styles for only the ViewComponents on the page is delivered to client.

Revelation detect and resolves all implicit dependencies behind the scenes, and using the bundler doesn’t require developer configuration or programmatic changes. The perscriptive component-based structure of Juno applications allows Revelation to perform all of the bundling automatically.

The `gulp` scripts tie into liveReload while developing to provide instant reloads when developing Juno experiences.

<visual><break></break></visual>

## Technical Overview

Revelation is built on webpack, which implements a CommonJS loader. Our babel transpiler will also convert ES2015 `import` and `export` declarations into CommonJS. We recommend using ES2015 syntax.

Under the hood, we traverse the file system for all ViewComponent JavaScript files and then inject `require` statements for the associated Stylus and Dust files. A custom loader (`component-loader`) then looks for implicit dependencies on other ViewComponents based on the `{@ViewComponent /}` Dust helper and injects `require` statements for those ViewComponents.

When the page is assembled, the shell's header (as built by the `{@shellHead /}` helper) will include the relevant bundles, and SPA navigation will resolve the required resources for the next route via AJAX requests.
