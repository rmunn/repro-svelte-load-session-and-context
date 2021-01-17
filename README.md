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
