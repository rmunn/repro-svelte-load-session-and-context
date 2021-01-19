# Bug reproduction

A reproduction of a bug with `load()` and its `context` and `session` parameters.

## Repro steps

1. Run `yarn`
1. Run `yarn dev`
1. Point your browser at http://localhost:3000/
1. Notice the page contains "Info received from top-level layout: undefined"
1. Edit `src/routes/$layout.svelte`
1. Change the `load()` parameters from `load({ page, context })` to `load({ page, context, session })`
1. Save the file and look at the browser
1. Notice the page contains "Info received from top-level layout: Info from layout load() function"
1. Wonder why adding a `session` parameter caused the *context* to be successfully passed down

## Explanation

Here's what is happening here:

1. The load() function in `$layout.svelte` returns some context data, which is then received by the load() function in `index.svelte` via its `context` parameter.
2. The load() function in `index.svelte` also takes a `session` parameter. This is enough to access the `session` getter and thus set `node.uses.session` to `true` in the `Renderer.hydrate` method in Svelte-Kit's `start.js` module.
3. The `index.svelte` route has an onMount() function that sets a value in the `$session` store. (I do it in `onMount()` because that ensures it doesn't run during SSR: the session store is readonly during SSR.)
4. When the session changes, Svelte-Kit notices that the `index.svelte` component depends on the session value, and so it re-runs the logic in the `if (changed_since_last_render)` block in the hydrate() method... but only for the `index.svelte` component. Because the `$layout.svelte` component does *not* depend on the session value (nothing in its `load()` function or anywhere else accessed the `session` getter, so its `node.uses.session` value was `false`), it was not re-run by the change to the session store, and thus its `load()` function did not fire.
5. Because the `$layout.svelte` load() function did not re-run, it did not populate the context received by `index.svelte`, unlike the first time it ran. (Check the console log for lines starting with "Received context" to verify that).
6. If you edit `$layout.svelte` and have it access the `session` parameter (by destructuring it) in load(), then suddenly the context will get passed through to `index.svelte`.

Result: very unexpected behavior from a user's perspective. Changing whether or not `$layout.svelte`'s load() function takes a *session* argument has changed what *context* value is received by `index.svelte`'s load() function. I suspect that what's actually desired here is that if a component's load() function uses `context`, and any of its ancestor components returned a `context` output from their load() functions, then those ancestor components need to be re-run any time the descendant needs to be re-run.
