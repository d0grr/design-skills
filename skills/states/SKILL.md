---
name: states
description: Renders every state of a section you choose on its real page, with mock data and a switcher, so you can work on each state.
disable-model-invocation: true
---

# States

This skill takes one section of the product and makes every state it can be in reachable on demand. Mock data feeds the section on its real route, a switcher flips between states and the whole setup is removed in one step when you are done.

It is a workbench, not a review. The user iterates on the visuals with every state one keypress away. Stress testing a component against hostile content is `break`, exploring alternative designs is `variant` and reviewing finished work is `interface-review`.

Where `break` isolates one component on a scratch page, this skill stays on the real route. The section keeps its real neighbours, providers, data hooks and layout, because those decide what each state actually looks like.

## 1. Scope one section

One section per run: the audit log table, the network panel, the billing card. "The settings page" spans several, so list them and ask which one.

Restate it in one sentence: what the section shows, which route it lives on and where its data comes from.

## 2. Find the states in the code

The states are the branches the section already has. Read the component and its data hooks for every one:

| Kind | States |
| --- | --- |
| Data | Loading, empty, error, one item, a typical set, a set long enough to scroll or paginate, some requests resolved and others pending, stale data refetching |
| Account | Plan tier, role and permission checks, an owner versus a member |
| Feature | Flags, trials, limits reached, a disabled or locked feature |

A state the code cannot reach is not a state. Do not add a branch to render one. Where the design shows a state the code lacks, list it as missing and leave it out.

Write the set down before building, one line each, named the way the product talks about it: `empty`, `enterprise`, `member-no-access`. Say which kinds you dropped and why in one line. Combine kinds only where the code branches on the combination, such as `member-empty`, never as a cross product.

## 3. Mock at the data boundary

Swap the data where the section receives it: the query result, the hook's return or the loader. Leave the components below it untouched. A fixture threaded through props five levels down tests a path production never takes.

All mock code lives in one place that production cannot reach:

- One folder next to the section and clearly named, such as `audit-log.states/`. The fixture data and the switcher are separate files in it. Where the data boundary runs on the server, the switcher is client code, so keeping them apart keeps each in its own module graph.
- Imported only through a dynamic `import()` inside the project's dev check, such as `import.meta.env.DEV` or `process.env.NODE_ENV !== "production"`.
- The section's real code gains two guarded lines, one that swaps in the fixture when the `__state` search param is set and one that mounts the switcher. Nothing else changes.

The double underscore keeps the param clear of the route's own query params, which may already use `state` for a filter, a tab or an OAuth callback. Read the route's existing params first and pick another name if even `__state` is taken.

Make the data look like the product. Real-shaped names, timestamps, amounts and the item counts users actually have. Three rows of "Test item" make every state look fine.

Loading and pending states hold still. Keep them pending until the switcher moves, rather than resolving after a timeout.

## 4. Add the switcher

A fixed control flips the `__state` search param, so every state is a link. [switcher.md](switcher.md) holds the spec.

## 5. Confirm every state renders, then hand over

Load the page once in a browser already at hand and flip through every state. The section shows the mock data, not the real data and not a blank region. Mock data that never appears is the common failure here. Live sync, a cache that wins over the override or a server boundary dropping the fixture all cause it. Fix the plumbing before handing over.

With no browser at hand, say so and hand the URL over for the user to check.

Then hand over the URL, the state list and the switcher keys. Stop there. Changing how a state looks is the user's next request, not part of this one.

## 6. Keep it up while the user iterates

The setup stays while the user works on the visuals. After a visual change, flip through every state again, since a fix for one state often breaks another.

## 7. Remove it in one step

On the user's word, delete the fixture folder and the two guarded lines. Then search the codebase for the folder name and the `__state` param, and check that the diff touches nothing else of the setup. Commit only when asked.

## Before you finish

| Mistake | Fix |
| --- | --- |
| Rendered on a blank scratch route | Keep the section on its real page |
| Fixtures passed as props deep in the tree | Swap the data where the section receives it |
| A branch added to the section to show a state | Leave out states the code cannot reach, and list missing ones |
| Mock code reachable in production | One fixture folder, imported only behind the dev check |
| Fixture data and switcher in one module | Separate files, so server data never enters the client bundle |
| "Item 1", "Test user", three rows | Product-shaped data in real quantities |
| Loading resolves after a timeout | Hold it until the switcher moves |
| Handed over without loading each state | Load every state; a blank or real-data state means broken plumbing |
| Switcher styled with the project's tokens | Keep it visibly outside the design system |
| Visuals changed unasked | Hand over and wait |
| Setup deleted in the same turn it was built | Remove it only on the user's word |
| Fixture names or the param left behind | Search for both after removal |
