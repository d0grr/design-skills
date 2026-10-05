# The switcher

The control that flips states. It sits over the section being worked on, so its appearance is not a design decision. Build the spec below and leave it alone.

## Deliberately outside the design system

Never style the switcher with the project's tokens, fonts or colors. One that looks native to the product becomes part of what you are looking at.

One dark neutral surface, the system font stack and no project variables. Dark reads as chrome over both light and dark pages, so it does not follow the theme.

## Behavior

- It sets the `__state` search param and reads the active state back from it. The URL is the source of truth, so every state is a link.
- Left and right arrows step through the states. Number keys jump to one directly.
- `H` hides and shows the switcher, for screenshots and screen recordings.
- The active item carries `aria-current="true"`, and the container carries a label.
- Switching is instant, with no transition, and keeps the scroll position.
- Key handling ignores events from inputs, textareas and contenteditable elements, so typing in the section never flips the state.

## Structure

One button per state, in the order step 2 of [SKILL.md](SKILL.md) listed them.

```html
<nav class="state-switcher" aria-label="States">
  <button type="button" data-state="loading">Loading</button>
  <button type="button" data-state="empty" aria-current="true">Empty</button>
  <button type="button" data-state="enterprise">Enterprise</button>
</nav>
```

## Placement and styling

Fixed, bottom centre, above everything the page can stack. On a narrow viewport it stays 16px from each edge and scrolls sideways, so every state stays reachable. Arrow keys scroll the active button into view. Where the section sits at the bottom of the viewport, move it to top centre.

```css
.state-switcher {
  position: fixed;
  bottom: 24px;
  left: 50%;
  translate: -50% 0;
  z-index: 2147483647;
  display: flex;
  gap: 2px;
  max-width: calc(100vw - 32px);
  overflow-x: auto;
  scrollbar-width: none;
  padding: 4px;
  border-radius: 999px;
  background: rgb(20 20 20 / 0.9);
  box-shadow: inset 0 0 0 1px rgb(255 255 255 / 0.1), 0 8px 24px rgb(0 0 0 / 0.25);
  font: 13px/1 -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
  user-select: none;
}

.state-switcher button {
  flex-shrink: 0;
  padding: 7px 14px;
  border: 0;
  border-radius: 999px;
  background: none;
  color: rgb(255 255 255 / 0.6);
  cursor: pointer;
}

.state-switcher button:hover {
  color: rgb(255 255 255 / 0.85);
}

.state-switcher button[aria-current="true"] {
  background: rgb(255 255 255 / 0.14);
  color: rgb(255 255 255);
}

.state-switcher button:focus-visible {
  outline: 2px solid rgb(255 255 255 / 0.7);
  outline-offset: 2px;
}
```

In a framework, keep the class names and the structure and change only the rendering syntax. The switcher is its own client file in the fixture folder from step 3, separate from the fixture data, and renders behind the same dev check.
