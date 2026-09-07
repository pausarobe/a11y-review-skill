# Focus Management Reference

Use this reference when reviewing focusability, focus order, focus visibility,
programmatic focus, focus restoration, and focus behavior in dynamic web
interfaces.

Focus management should help keyboard and assistive technology users understand
where they are and continue interacting predictably.

Do not move focus unless there is a clear user-experience or accessibility
reason.

---

# Native focus behavior first

Native interactive elements usually provide appropriate focus behavior.

Examples include:

- `<button>`;
- `<a href>`;
- `<input>`;
- `<select>`;
- `<textarea>`;
- `<summary>`.

Prefer native interactive controls when possible.

Do not add:

```html
tabindex="0"
```

to native controls that are already focusable unless there is a specific reason.

---

# Focusability and interactivity

Not every element should be focusable.

An element normally belongs in the sequential Tab order when users are expected
to interact with it directly.

Static content generally should not be made focusable solely for accessibility.

Avoid:

```html
<div tabindex="0">Account balance: €1,200</div>
```

unless there is a specific interaction or navigation requirement.

Screen reader users can navigate static content without every element being a
Tab stop.

---

# tabindex values

## tabindex="0"

Use:

```html
tabindex="0"
```

when a custom interactive element must participate in the natural sequential
focus order.

Example:

```html
<div role="button" tabindex="0">Save</div>
```

However, first determine whether a native `<button>` would be preferable.

---

## tabindex="-1"

Use:

```html
tabindex="-1"
```

when an element should be focusable programmatically but should not normally
participate in sequential Tab navigation.

Typical uses include:

- route-change focus targets;
- headings receiving focus after navigation;
- error summaries;
- dialog content;
- dynamically inserted content;
- composite widget descendants managed through roving tabindex.

Do not report `tabindex="-1"` as inaccessible merely because Tab does not reach
the element.

---

## Positive tabindex

Avoid:

```html
tabindex="1" tabindex="2" tabindex="3"
```

for ordinary page navigation.

Positive tabindex creates a custom focus order that can diverge from:

- DOM order;
- reading order;
- visual order;
- responsive layouts.

Prefer fixing DOM structure instead.

---

# Focus order

Sequential focus order should generally follow a logical and predictable order.

In most interfaces, this should correspond closely to DOM order.

When reviewing focus order, consider:

- visual layout;
- reading order;
- interaction sequence;
- dynamically inserted content;
- hidden content;
- overlays;
- responsive layouts.

Do not report focus order issues solely from CSS class names.

Runtime verification may be required.

---

# DOM order versus visual order

CSS can visually reorder content using techniques such as:

- Flexbox `order`;
- CSS Grid placement;
- absolute positioning.

Visual order and DOM order do not always need to be identical.

The question is whether the resulting reading and focus sequence remains
logical.

Do not automatically report every CSS reorder as inaccessible.

Verify whether users encounter content in an understandable sequence.

---

# Focus visibility

Keyboard users need a visible indication of which element currently has focus.

Do not remove the browser focus indicator without providing an adequate
replacement.

Avoid:

```css
*:focus {
  outline: none;
}
```

unless an equivalent or better focus indicator is provided.

Do not infer missing focus visibility from HTML alone when relevant CSS is not
available.

Mark it as:

**Needs verification**

when necessary.

---

# :focus and :focus-visible

Modern interfaces may use:

```css
:focus-visible;
```

to show focus indicators primarily when the browser determines that keyboard
focus indication is appropriate.

This can be valid.

Do not require identical visual treatment for:

```css
:hover;
```

and:

```css
:focus-visible;
```

They may look the same or different.

The accessibility requirement is that keyboard focus is visible and
sufficiently clear.

---

# Hover and focus styles

Hover and focus states do not need to be visually different from each other.

They may share the same styling when the focus indicator remains clearly
perceivable.

Do not report:

> hover and focus look identical

as an accessibility issue by itself.

Evaluate whether focus is actually visible.

---

# Focus not obscured

When an element receives focus, it should not be completely hidden by
author-created content such as:

- sticky headers;
- sticky footers;
- cookie banners;
- overlays;
- fixed navigation.

WCAG 2.2 includes Focus Not Obscured requirements.

When relevant, verify that focused controls remain visible.

CSS techniques such as:

```css
scroll-margin-top
```

may help when sticky elements cover focused content.

Do not infer this issue from the existence of a sticky header alone.

Runtime verification is usually required.

---

# Programmatic focus

Programmatic focus is appropriate when a user action causes a meaningful
context change and moving focus helps users understand or continue the
interaction.

Examples include:

- opening a modal dialog;
- navigating to a new SPA view;
- revealing a validation error summary;
- moving to a newly active step in a wizard.

Do not move focus merely because content changed.

Excessive focus movement can be disorienting.

---

# Preserve user context

Before moving focus programmatically, ask:

1. Did the user trigger a significant context change?
2. Would the user's current focus become invalid or misleading?
3. Is there a logical destination for focus?
4. Will moving focus help the user continue?
5. Can the same information be communicated without moving focus?

Prefer the least disruptive approach.

---

# Focus after opening a dialog

When a modal dialog opens, focus should move into the dialog.

The exact initial focus target depends on the content.

Possible targets include:

- the first meaningful interactive control;
- the dialog heading;
- a safe default action;
- a descriptive static element with `tabindex="-1"` when appropriate.

Do not always focus the first button mechanically.

For example, when a dialog contains substantial explanatory content, focusing
the heading may provide better context.

---

# Dialog focus containment

Modal dialogs should prevent users from unintentionally interacting with
background content while the dialog is active.

This may involve:

- native `<dialog>` behavior;
- focus trapping;
- `inert` on background content;
- an accessible component library.

Do not assume:

```html
role="dialog"
```

or:

```html
aria-modal="true"
```

implements focus containment.

Verify actual behavior.

---

# Focus restoration after closing

When a temporary interface such as a dialog closes, focus should usually return
to a logical location.

Often this is the element that opened it.

For example:

```text
Open settings button
        ↓
Dialog opens
        ↓
User closes dialog
        ↓
Focus returns to Open settings button
```

However, if the trigger no longer exists or the interaction changes context,
choose another logical destination.

Do not restore focus blindly to an element removed from the DOM.

---

# Focus after deleting content

If an action removes the currently focused element, focus must not be left in an
undefined or confusing state.

For example, after deleting an item from a list, an appropriate destination may
be:

- the next item;
- the previous item;
- the list container;
- an action related to the remaining content.

The correct choice depends on context.

Do not always force focus to the top of the page.

---

# Focus after dynamic insertion

Dynamically inserted content does not automatically need focus.

For example, adding a status message should often use an announcement mechanism
rather than moving focus.

Move focus only when the new content becomes the user's primary interaction
context.

See:

`dynamic-content.md`

---

# Focus and validation

When form validation fails, consider how users will locate errors.

Possible approaches include:

- focus the first invalid field;
- focus an error summary;
- preserve focus and announce validation results.

The best pattern depends on the form and interaction.

Do not automatically move focus to every error message.

Detailed form behavior belongs in:

`forms.md`

---

# SPA route changes

Client-side navigation does not automatically reset focus like a full page
navigation.

After a significant SPA route change, consider whether focus should move to a
logical page-level target such as:

- the main heading;
- `<main>`;
- another meaningful initial context element.

A common pattern is:

```html
<h1 tabindex="-1">Account details</h1>
```

and programmatically focusing it after navigation.

Do not add route-change focus automatically without understanding how the
application currently handles navigation and announcements.

---

# Route-change focus target

A route-change focus target should provide useful context.

Good candidates include:

- page heading;
- main content container;
- primary content region.

Avoid focusing arbitrary layout containers with no useful accessible name or
context.

---

# Focus after asynchronous rendering

Frameworks may render the intended focus target after an asynchronous change.

Examples include:

- Angular conditional rendering;
- React state updates;
- lazy-loaded dialogs;
- route transitions.

Programmatic focus should occur only after the target exists and is ready.

Do not use arbitrary delays unless necessary.

Prefer framework lifecycle or rendering synchronization mechanisms.

When timing behavior cannot be determined statically, mark it as:

**Needs verification**

---

# Multiple requestAnimationFrame calls

Repeated `requestAnimationFrame` calls may sometimes appear in focus-management
code.

Do not assume that waiting multiple animation frames is inherently wrong.

However, repeated frames can indicate that focus depends on uncertain rendering
timing.

Prefer deterministic synchronization with the framework or component API when
possible.

If focus only becomes visually correct after several frames, investigate:

- CSS transition timing;
- component rendering;
- shadow DOM focus delegation;
- lifecycle timing;
- browser behavior.

Do not remove such code without verifying runtime behavior.

---

# Focus and hidden content

Users should not be able to focus controls that are intended to be fully hidden
or inactive.

Be cautious with:

```html
aria-hidden="true"
```

because it does not itself prevent keyboard focus.

Also inspect:

- `hidden`;
- `display: none`;
- `visibility: hidden`;
- `inert`;
- conditional rendering;
- off-screen positioning.

Different mechanisms behave differently.

---

# inert

The `inert` attribute can be useful when a subtree should be temporarily
non-interactive.

It typically prevents:

- sequential keyboard focus;
- pointer interaction;
- exposure in the accessibility tree.

Example:

```html
<div inert>...</div>
```

may be appropriate for background content while a modal interaction is active.

Do not add `inert` mechanically.

Determine whether the content should genuinely be unavailable.

---

# aria-hidden and focus

Avoid exposing states where content is hidden from assistive technologies but
still reachable by keyboard.

For example:

```html
<div aria-hidden="true">
  <button>Action</button>
</div>
```

can be problematic if the button remains focusable.

Evaluate semantic visibility and keyboard accessibility together.

See:

`aria.md`

---

# Focus in collapsed content

Controls inside collapsed or inactive content should generally not remain in the
sequential focus order.

For example, an inactive tab panel should not expose interactive descendants to
Tab navigation if the panel is meant to be unavailable.

Appropriate techniques may include:

- conditional rendering;
- `hidden`;
- `inert`;
- browser-native component behavior.

Do not rely on `aria-hidden` alone to remove focusability.

---

# Focus in off-screen content

Content positioned off-screen may still be focusable.

Do not assume visual invisibility means focus inaccessibility.

When off-screen techniques are used for:

- menus;
- drawers;
- carousels;
- visually hidden content;

verify whether focusable descendants are appropriately available or
unavailable.

---

# Visually hidden content

Visually hidden content intentionally remains available to assistive
technologies.

A typical visually-hidden utility should not itself become a focus target unless
the element is interactive.

For example, skip links are often visually hidden until focused.

Do not confuse:

```text
visually hidden
```

with:

```text
removed from the accessibility tree
```

---

# Skip links

Skip links allow keyboard users to bypass repeated navigation.

A common pattern is:

```html
<a href="#main-content" class="skip-link"> Skip to main content </a>
```

The link should become visible when focused.

The destination must support meaningful navigation behavior.

Depending on browser behavior and implementation, the target may need to be
programmatically focusable.

Do not assume an anchor fragment alone always results in appropriate focus
movement in every application.

---

# Composite widgets

Composite widgets often manage focus internally.

Examples include:

- tabs;
- menus;
- listboxes;
- grids;
- tree views;
- custom radio groups.

These may use:

- roving tabindex;
- `aria-activedescendant`.

Do not require every child to be in the page Tab sequence.

See:

`keyboard.md`

---

# Roving tabindex

In a roving tabindex pattern:

- one item has `tabindex="0"`;
- other items have `tabindex="-1"`;
- keyboard navigation moves focus between items;
- tabindex values update.

Example:

```html
<button role="tab" tabindex="0">Overview</button>

<button role="tab" tabindex="-1">Transactions</button>
```

This can be correct.

Do not report `tabindex="-1"` on inactive composite items as inaccessible.

---

# aria-activedescendant

When a component uses `aria-activedescendant`, DOM focus may remain on a
container while the active descendant changes.

This is a valid focus model for certain widgets.

Do not require DOM focus to move to each item when this pattern is correctly
implemented.

See:

`aria.md`

---

# Focus and custom components

Custom components may manage focus internally.

For example:

```html
<eb-input-text></eb-input-text>
```

may delegate focus to an internal native input.

Do not judge focusability from the custom element host alone.

Inspect:

1. public focus API;
2. rendered DOM;
3. shadow DOM;
4. focus delegation;
5. actual keyboard behavior.

If unavailable, classify the concern as:

**Needs verification**

---

# setFocus-style component APIs

Design systems may expose methods such as:

```typescript
component.setFocus();
```

Prefer the component's documented focus API when it correctly targets the
internal interactive element.

Do not bypass component encapsulation with:

```typescript
document.querySelector(...)
```

unless there is a justified reason.

A public focus API can be more robust for components using Shadow DOM or
internal wrappers.

---

# document.activeElement

Runtime inspection of:

```javascript
document.activeElement;
```

can help verify focus.

With Shadow DOM, `document.activeElement` may point to the host element while the
actual focused element exists deeper in the shadow tree.

Do not assume the top-level active element always reveals the complete focus
target.

---

# Shadow DOM focus

Web components may use:

- internal focusable elements;
- `delegatesFocus`;
- component focus APIs.

When reviewing focus in a web component, inspect actual behavior rather than
host markup alone.

If inaccessible internals cannot be inspected, report what requires
verification.

---

# Focus and disabled native controls

Native disabled controls are normally removed from sequential keyboard focus.

For example:

```html
<button disabled>Save</button>
```

is not usually reachable by Tab.

This is expected native behavior.

Do not report it as a focus issue merely because the disabled control cannot be
focused.

---

# aria-disabled and focus

Controls using:

```html
aria-disabled="true"
```

may remain focusable.

This can be intentional.

For example, retaining focusability can allow users to discover that an action
exists but is currently unavailable.

If `aria-disabled` is used, ensure:

- disabled semantics are exposed;
- activation is actually prevented;
- the interaction is understandable.

See:

`aria.md`

---

# Focus and readonly controls

Readonly controls may remain focusable.

This can allow users to:

- inspect values;
- select text;
- copy content.

Do not remove readonly controls from focus solely because they cannot be edited.

Readonly and disabled semantics are different.

---

# Focus on headings

Headings are not normally focusable.

However, they may legitimately receive programmatic focus using:

```html
<h1 tabindex="-1"></h1>
```

for patterns such as:

- SPA navigation;
- dialog context;
- step transitions.

Do not add headings to the normal Tab sequence with `tabindex="0"` unless there
is a specific reason.

---

# Focus on error summaries

An error summary may receive programmatic focus after form submission so users
are informed that validation failed.

Example:

```html
<div role="alert" tabindex="-1">Please correct the following errors.</div>
```

Whether `role="alert"` is appropriate depends on the interaction.

The summary may instead use another announcement mechanism.

See:

- `forms.md`
- `dynamic-content.md`

---

# Focus stealing

Avoid moving focus unexpectedly while the user is interacting with another
control.

Examples of problematic focus stealing include:

- background refresh moving focus;
- auto-updating widgets moving focus without user action;
- validation moving focus while the user is typing;
- notifications receiving focus automatically.

Dynamic updates should not take control away from the user unless necessary.

---

# Autofocus

Use autofocus cautiously.

Automatically focusing a control on page load can:

- skip introductory content;
- scroll the page unexpectedly;
- open a mobile keyboard;
- disorient screen reader users.

Do not report every use of autofocus as inaccessible.

Evaluate the context.

A focused field at the start of a narrowly scoped interaction may be reasonable.

---

# HTML autofocus attribute

Native:

```html
<input autofocus />
```

moves focus automatically when the document loads under supported conditions.

In SPAs and dynamically rendered interfaces, framework-managed focus may be more
predictable.

Do not add `autofocus` solely to compensate for missing focus management after
dynamic navigation.

---

# Focus indicator and component states

Focused controls may also be:

- hovered;
- selected;
- expanded;
- invalid;
- disabled.

Focus styling should remain discernible alongside these states.

Do not assume a focus indicator is sufficient merely because one CSS rule
exists.

Runtime visual verification may be necessary.

---

# Focus under zoom and responsive layouts

At high zoom or narrow viewport widths, focused controls may move or become
obscured.

When relevant, test focus behavior at responsive breakpoints.

Do not infer failure solely from responsive CSS.

---

# Focus and scroll

Calling:

```javascript
element.focus();
```

may scroll the element into view.

Options such as:

```javascript
element.focus({ preventScroll: true });
```

may change this behavior.

Use `preventScroll` only when keeping the current scroll position is
intentional and the focused element remains perceivable.

Do not focus an off-screen element while preventing users from locating it.

---

# Scroll after focus

If programmatic focus causes an unexpected scroll jump, consider whether:

- the focus target is appropriate;
- layout has stabilized;
- sticky elements obscure the target;
- scrolling should be coordinated explicitly.

Do not remove focus solely to avoid scroll movement.

Fix the underlying context if focus is still necessary.

---

# Focus after opening menus and popovers

Whether focus should move into a popup depends on the interaction pattern.

For example:

- ARIA menus commonly move focus into menu items;
- simple non-modal informational popovers may not;
- disclosure content may leave focus on the trigger.

Do not use one focus rule for every overlay.

Identify the actual widget pattern first.

---

# Focus after expanding content

A disclosure button that reveals content generally keeps focus on the trigger.

Example:

```text
Focus: "Show details" button
User activates
Details become visible
Focus remains on "Show details"
```

Do not automatically move focus into every newly expanded panel.

Only move focus when the interaction requires it.

---

# Focus after tab activation

When switching tabs through the standard tab pattern, focus normally remains on
the active tab while the corresponding tabpanel becomes available.

Do not automatically move focus into the tabpanel on each tab change.

This would interfere with arrow-key navigation.

---

# Focus after pagination

When pagination changes a substantial content region, determine whether users
need focus repositioning.

Possible approaches include:

- keeping focus on the activated pagination control;
- moving focus to the updated content heading;
- announcing the updated result state.

The correct approach depends on the interface.

Do not move focus automatically without considering user continuity.

---

# Focus after sorting or filtering

Updating a list after:

- filtering;
- sorting;
- selecting options;

does not necessarily require moving focus.

Usually, preserving focus on the control that triggered the update is less
disruptive.

Use status announcements where appropriate.

See:

`dynamic-content.md`

---

# Focus after loading states

Do not move focus to loading indicators by default.

A loading state usually needs state communication rather than focus movement.

After loading completes, move focus only if the user's interaction context has
fundamentally changed.

---

# Focus and toasts

Toast notifications generally should not steal focus.

If the toast contains interactive actions that the user must access, the design
should provide a predictable way to reach them.

Do not automatically focus transient notifications.

Use appropriate announcements where necessary.

See:

`dynamic-content.md`

---

# Focus traps versus modal containment

A focus trap can be appropriate inside a true modal interaction.

A focus trap is inappropriate when users need to interact with background
content.

Do not infer modality solely from visual presentation.

Ensure semantic and interaction models agree.

---

# Escape and focus restoration

When Escape closes a temporary interface, verify that focus returns to an
appropriate location.

Do not only test visual closing.

Keyboard users should not be left with focus on removed content.

---

# Focus when trigger disappears

If the element that opened a component no longer exists after the interaction,
focus must move elsewhere.

For example, after confirming deletion:

```text
Delete button no longer exists
```

Focus may need to move to:

- next logical item;
- previous logical item;
- section heading;
- status region;
- another meaningful control.

Context determines the best destination.

---

# Browser focus outline versus custom styles

Native browser focus outlines are valid accessibility mechanisms.

Do not require a custom outline merely because the design system has not
overridden browser defaults.

Evaluate whether the resulting focus indicator is sufficiently perceivable.

---

# Do not focus non-existent targets

Framework code may attempt to focus elements before they are rendered.

For example:

```typescript
this.showInput = true;
this.input.setFocus();
```

may fail if the input is created asynchronously.

Use lifecycle or rendering coordination where appropriate.

Do not hide such failures behind arbitrary timeouts without understanding the
rendering sequence.

---

# Avoid arbitrary setTimeout for focus

Patterns such as:

```typescript
setTimeout(() => element.focus(), 500);
```

can be fragile.

Prefer deterministic approaches tied to:

- component lifecycle;
- render completion;
- framework scheduling;
- documented component APIs.

However, do not automatically remove an existing timeout without testing.

There may be integration constraints.

---

# Focus and animation

Animations or transitions can delay the moment when an element is visually ready
for focus.

If focus is moved before the element becomes visible, users may lose context.

Coordinate focus with the actual interaction state.

Avoid unnecessary delays that make the interface feel unresponsive.

---

# Reduced motion does not remove focus requirements

Respecting:

```css
prefers-reduced-motion
```

may change transition timing or disable animations.

Focus behavior must remain correct regardless of motion preference.

Do not couple essential focus logic exclusively to animation-end events unless
a fallback exists.

---

# Testing focus

When runtime testing is possible, verify:

- initial focus;
- Tab and Shift+Tab order;
- visible focus;
- focus after opening overlays;
- focus after closing overlays;
- focus after content deletion;
- focus after navigation;
- hidden content;
- responsive behavior.

Use browser developer tools when helpful to inspect:

```javascript
document.activeElement;
```

and the accessibility tree.

---

# Screen reader focus and DOM focus

Screen readers may distinguish between:

- keyboard/DOM focus;
- virtual cursor or reading position.

Do not assume moving DOM focus is always necessary merely because screen reader
users need to discover new content.

For non-interactive updates, live-region announcements may be more appropriate.

See:

`dynamic-content.md`

---

# Review decision process

When reviewing focus behavior, follow this reasoning:

1. Determine whether the element should be focusable.
2. Determine whether native behavior already handles focus.
3. Inspect sequential focus order.
4. Inspect `tabindex`.
5. Determine whether focus is visible.
6. Check whether hidden content can receive focus.
7. Identify context changes that may require programmatic focus.
8. Check dialog and overlay focus behavior.
9. Check focus restoration.
10. Check whether dynamically removed elements can retain focus.
11. Consider framework rendering timing.
12. Inspect custom-component focus APIs.
13. Determine whether runtime verification is required.
14. Report only confirmed or credible focus barriers.

---

# Common review mistakes

## Mistake: making headings part of the normal Tab order

Wrong:

```html
<h1 tabindex="0"></h1>
```

solely so keyboard users can reach the heading.

Correct approach:

Static headings do not normally belong in sequential focus navigation.

Use `tabindex="-1"` only when programmatic focus is needed.

---

## Mistake: removing tabindex="-1"

Wrong reasoning:

> If users cannot Tab to it, it is inaccessible.

Correct reasoning:

`tabindex="-1"` is intentionally used for programmatic focus.

---

## Mistake: using positive tabindex to repair layout

Wrong:

```html
tabindex="5"
```

to force a desired navigation order.

Correct approach:

Prefer logical DOM order.

---

## Mistake: assuming aria-hidden blocks focus

`aria-hidden="true"` does not by itself prevent keyboard focus.

---

## Mistake: moving focus to every dynamic update

Most asynchronous updates should not steal focus.

Use announcements when appropriate.

---

## Mistake: always focusing the first control in a dialog

Initial focus depends on dialog content and user task.

---

## Mistake: forgetting focus restoration

Closing an overlay is not complete if focus is left on removed or hidden
content.

---

## Mistake: assuming custom components are not focusable

Inspect the component API and rendered implementation.

---

## Mistake: assuming focus and hover must look different

They may share styling.

The requirement is that focus remains clearly visible.

---

## Mistake: judging focus visibility without CSS

If styling is unavailable, require verification rather than claiming a failure.

---

# Related references

Use these references for adjacent topics:

- `semantics.md` — native interactive elements and structural semantics.
- `aria.md` — roles, states, hidden content, and ARIA relationships.
- `keyboard.md` — keyboard activation and composite-widget interaction.
- `forms.md` — validation focus and error handling.
- `dynamic-content.md` — announcements and updates that should not steal focus.

---

# Final principle

Focus should help users understand where they are and continue interacting
predictably.

When reviewing focus:

**keep the natural focus model when possible**

**do not make static content focusable without reason**

**avoid positive tabindex**

**move focus only for meaningful context changes**

**restore focus after temporary interactions**

**do not allow hidden content to remain unexpectedly focusable**

**inspect design-system focus APIs before bypassing them**

**verify visual and runtime focus behavior instead of guessing**
