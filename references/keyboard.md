# Keyboard Accessibility Reference

Use this reference when reviewing keyboard operability and keyboard interaction
patterns in web interfaces.

The objective is to determine whether users can operate interactive
functionality using the keyboard and whether the interaction matches the
semantics of the control.

Do not require custom keyboard handlers when native HTML already provides the
correct behavior.

---

# Native keyboard behavior first

Prefer native interactive elements because browsers already provide keyboard
behavior for them.

Examples include:

- `<button>`;
- `<a href>`;
- `<input>`;
- `<select>`;
- `<textarea>`;
- `<details>` / `<summary>` where appropriate.

For example:

```html
<button type="button">Save</button>
```

already provides keyboard activation.

Do not add custom Enter or Space handlers unless there is a specific reason.

---

# Do not duplicate native activation behavior

Avoid patterns such as:

```html
<button type="button" onclick="save()" onkeydown="handleKeydown(event)">
  Save
</button>
```

when `handleKeydown` manually triggers the same activation for Enter or Space
that the native button already provides.

Duplicating native behavior can cause:

- double activation;
- inconsistent behavior across browsers;
- unnecessary complexity;
- difficult-to-maintain code.

Use native behavior whenever possible.

---

# Click events and keyboard activation

Native buttons and links can generate click activation from keyboard input.

For example:

```html
<button onclick="save()">Save</button>
```

can activate through:

- pointer input;
- Enter;
- Space.

Therefore, a framework listener such as:

```html
<button (click)="save()">Save</button>
```

does not require separate:

```html
(keydown.enter)
```

or:

```html
(keydown.space)
```

handlers solely to support keyboard activation.

The browser maps native keyboard interaction to activation behavior.

---

# The event may still be a click event

When a native control is activated with the keyboard, the application may still
receive a `click` event.

Do not assume that keyboard activation must produce a `keydown` event in the
application handler.

For example:

```html
<button (click)="save($event)">Save</button>
```

can receive a click event when activated using the keyboard.

This is expected native behavior.

---

# Buttons

Native buttons are activated using the expected browser keyboard behavior.

Normally:

- Enter activates the button;
- Space activates the button.

Do not manually reproduce this behavior on native `<button>` elements unless
there is a justified implementation requirement.

---

# Links

Native links with an `href` are normally activated using Enter.

Example:

```html
<a href="/accounts"> Accounts </a>
```

Do not require Space activation for ordinary links.

Button and link keyboard behavior are not identical.

This is another reason not to use one role when the intended semantics are the
other.

---

# Custom button implementations

If a native button cannot be used and an element implements button semantics,
verify all required behavior.

For example:

```html
<div role="button" tabindex="0">Save</div>
```

requires custom keyboard interaction.

The control must be operable using the expected button keys.

Typically:

- Enter activates;
- Space activates.

Do not assume:

```html
role="button"
```

provides this behavior automatically.

---

# Prefer invoking the same activation path

When custom keyboard behavior is necessary, keyboard and pointer activation
should generally reach the same logical action.

Avoid maintaining separate business logic such as:

```text
click -> saveFromMouse()
Enter -> saveFromKeyboard()
```

unless the behaviors genuinely need to differ.

Prefer a single logical action.

For example:

```typescript
save(): void {
  ...
}
```

called from both interaction paths if custom handling is required.

---

# Calling click() from keyboard handlers

For a custom element that is intended to behave like a native control, calling
the element's `click()` method from a keyboard handler can sometimes be a valid
way to reuse the same activation path.

For example:

```typescript
onKeyDown(event: KeyboardEvent): void {
  if (event.key === 'Enter' || event.key === ' ') {
    event.preventDefault();
    this.element.click();
  }
}
```

However, do not use this pattern automatically.

First ask:

> Could this control simply be a native `<button>`?

If yes, prefer the native button.

Do not create unnecessary custom keyboard emulation around generic elements.

---

# Space key behavior

Space often has default browser behavior, such as scrolling the page.

When implementing a custom control that uses Space for activation, it may be
necessary to prevent the default scrolling behavior.

Example:

```typescript
if (event.key === " ") {
  event.preventDefault();
  activate();
}
```

Do not call `preventDefault()` globally or on unrelated key events.

Only suppress native behavior when required by the widget interaction.

---

# Keydown versus keyup

Native controls do not necessarily activate on the same keyboard event.

For example, browser button activation behavior for Space may involve keydown
and keyup semantics.

When recreating native controls, avoid relying on assumptions about event timing
without testing.

Prefer native elements instead of attempting to reproduce all browser behavior.

If a custom widget requires precise event timing, verify behavior in supported
browsers.

---

# Keyboard operability

All functionality that can be operated through a pointer should generally have
an equivalent keyboard method unless the functionality fundamentally depends on
a path-based or analog input.

When reviewing an interaction, ask:

1. Can the user reach the control with the keyboard when appropriate?
2. Can the user activate or manipulate it?
3. Can the user leave the interaction?
4. Is the interaction understandable without a pointer?
5. Does it follow the expected widget pattern?

---

# Keyboard focusability

Interactive elements that participate in normal keyboard interaction should
generally be reachable through sequential keyboard navigation.

Native interactive elements are usually focusable automatically.

Custom controls often require:

```html
tabindex="0"
```

when they participate in the normal tab sequence.

Do not add `tabindex="0"` to non-interactive elements without a semantic reason.

Focus behavior is covered in more detail in:

`focus.md`

---

# Avoid positive tabindex

Avoid positive tabindex values such as:

```html
tabindex="1" tabindex="2" tabindex="3"
```

for normal page navigation.

Positive tabindex creates a custom focus order that can easily become
inconsistent with:

- DOM order;
- visual order;
- expected reading order.

Prefer DOM order and native focus order.

Use:

```html
tabindex="0"
```

to place an element in the natural tab sequence when necessary.

Use:

```html
tabindex="-1"
```

when the element should be programmatically focusable but not part of normal
sequential navigation.

Detailed focus guidance belongs in:

`focus.md`

---

# Keyboard traps

Users must be able to move focus away from components using the keyboard unless
the interface intentionally contains focus within a modal interaction and
provides an appropriate way to exit.

A keyboard trap occurs when focus enters an interface and cannot leave through
normal keyboard interaction.

Examples may include:

- custom widgets intercepting Tab indefinitely;
- embedded content capturing keyboard focus;
- modal interfaces without a close mechanism;
- JavaScript repeatedly returning focus to the same element.

Do not report a keyboard trap from static code unless the behavior can be
determined.

When uncertain, classify it as:

**Needs verification**

---

# Do not intercept Tab unnecessarily

In normal document interaction, allow the browser to handle Tab and Shift+Tab.

Avoid custom code that manually calculates the next focusable element unless
the widget pattern genuinely requires focus management.

Composite widgets may manage internal focus, but this should follow the
appropriate interaction pattern.

---

# Composite widgets

Some ARIA widgets are composite controls.

Typical examples include:

- tabs;
- menus;
- listboxes;
- tree views;
- grids;
- radio groups in custom implementations.

These widgets often use arrow-key navigation internally rather than placing
every internal item in the page Tab order.

Do not require every element inside a composite widget to have:

```html
tabindex="0"
```

That may produce an incorrect interaction model.

Instead, verify the expected pattern.

---

# Roving tabindex

Roving tabindex is a common technique for composite widgets.

Typically:

- one item has `tabindex="0"`;
- other items have `tabindex="-1"`;
- arrow keys move the active item;
- tabindex values update as focus moves.

Example:

```html
<div role="tablist">
  <button role="tab" tabindex="0" aria-selected="true">Overview</button>

  <button role="tab" tabindex="-1" aria-selected="false">Transactions</button>
</div>
```

Do not recommend roving tabindex for ordinary groups of buttons or links.

Use it only when the widget pattern requires composite keyboard navigation.

---

# aria-activedescendant focus model

Some composite widgets keep DOM focus on a container or input while using:

```html
aria-activedescendant
```

to expose the active option.

In these cases, keyboard navigation may not move DOM focus between every item.

Do not report this as incorrect merely because focus stays on the container.

Verify that:

- `aria-activedescendant` points to the active item;
- the active item changes correctly;
- expected keyboard commands work;
- visual indication matches the active item.

See:

`aria.md`

---

# Tabs

ARIA tabs have specific keyboard expectations.

A typical tablist supports:

- Tab to enter or leave the tab interface;
- Left Arrow and Right Arrow to move between tabs in a horizontal tablist;
- Up Arrow and Down Arrow where appropriate for vertical orientation;
- Home to move to the first tab;
- End to move to the last tab.

Depending on activation mode:

- focus movement may automatically activate the tab;
- or Enter/Space may activate the focused tab.

Do not assume one activation model is always required.

Automatic activation is generally appropriate when panel content can be shown
without noticeable delay.

Manual activation may be preferable when activation causes significant latency
or expensive operations.

---

# Tabs and Tab key

Do not make every tab stop independently in the normal page Tab sequence.

A typical composite tab pattern has one active or selected tab in the sequential
focus order, while arrow keys move between tabs.

Tab then proceeds to content or the next interactive element according to the
pattern.

Avoid:

```html
tabindex="0"
```

on every tab when implementing roving tabindex.

---

# Overflow tabs and "more" controls

If a tab interface moves some tabs into an overflow or "more options" control,
do not automatically treat the overflow button as another tab.

Determine the interaction model.

A separate button that opens a list or menu of hidden tabs may be more
appropriate than assigning:

```html
role="tab"
```

to the overflow trigger.

The hidden destination items must remain operable and understandable.

Keyboard behavior must match the semantics actually chosen for the overflow
control.

---

# Menus and menubars

ARIA menus and menubars use application-style keyboard interaction.

Typical behavior may include:

- arrow keys to move between items;
- Enter or Space to activate;
- Escape to close;
- Home/End in some implementations;
- character navigation in some patterns.

Do not apply this behavior to ordinary website navigation unless the interface
is genuinely an ARIA menu or menubar.

Normal navigation links generally use native browser keyboard behavior.

---

# Disclosure controls

A disclosure button that expands or collapses content should normally use a
native button.

Example:

```html
<button type="button" aria-expanded="false" aria-controls="details">
  More details
</button>
```

Native button keyboard behavior handles Enter and Space automatically.

Do not add separate keyboard handlers just because `aria-expanded` is present.

---

# Dialogs

Dialogs require keyboard interaction beyond semantics.

When a modal dialog opens, verify that:

- keyboard focus enters the dialog appropriately;
- keyboard users can reach its controls;
- focus does not unintentionally escape into inert background content;
- Escape closes the dialog when that behavior is appropriate;
- focus is restored appropriately after closing.

Detailed focus behavior belongs in:

`focus.md`

Do not assume:

```html
role="dialog"
```

implements any keyboard behavior.

---

# Escape key

Escape is commonly used to dismiss temporary interface layers such as:

- dialogs;
- menus;
- popovers;
- some disclosures.

Do not require Escape for every interactive component.

Determine whether dismissal is expected for that pattern.

When Escape is implemented, avoid triggering unrelated parent handlers that
cause multiple layers to close unexpectedly unless that behavior is intended.

---

# Enter and Space are not universal

Do not create a generic rule such as:

> Every clickable element must support Enter and Space.

The expected keys depend on the semantics of the control.

Examples:

- button: Enter and Space;
- link: Enter;
- checkbox: typically Space;
- radio: arrow navigation within a group and Space in relevant contexts;
- tab: arrow navigation plus activation according to the selected activation
  model;
- menuitem: widget-specific behavior.

First identify the role, then evaluate the expected keyboard interaction.

---

# Checkboxes

Native checkboxes already implement keyboard interaction.

Prefer:

```html
<input type="checkbox" />
```

Custom checkbox implementations must reproduce the expected interaction.

Typically:

- the checkbox is focusable;
- Space changes the checked state;
- exposed `aria-checked` state remains synchronized.

Do not add custom key handling to native checkboxes without reason.

---

# Radio buttons

Native radio groups provide browser keyboard behavior.

Prefer native:

```html
<input type="radio" />
```

where practical.

For custom radio groups, keyboard behavior normally involves arrow-key movement
between radio items and appropriate checked-state synchronization.

Do not treat each custom radio as an unrelated button.

Review the radio group as a composite interaction.

---

# Select and combobox patterns

Native `<select>` controls already provide keyboard interaction.

Do not replace their behavior with custom handlers unnecessarily.

Custom comboboxes and listboxes are complex.

Keyboard expectations can include:

- Arrow Down / Arrow Up;
- Enter;
- Escape;
- Home / End;
- character input;
- autocomplete-specific behavior.

Do not infer a complete keyboard model merely from:

```html
role="combobox"
```

Inspect the actual widget pattern.

---

# Tree views

Tree widgets typically use arrow keys for hierarchical navigation.

Possible behavior includes:

- Right Arrow to expand or move into children;
- Left Arrow to collapse or move to the parent;
- Up/Down Arrow to move between visible items;
- Home/End for boundaries.

Do not apply tree keyboard behavior to ordinary nested lists.

Only use it when the interface exposes tree semantics.

---

# Grids

ARIA grids may implement spreadsheet-like keyboard navigation.

This is an advanced widget pattern.

Do not assume a data table should become a keyboard-managed ARIA grid.

A semantic `<table>` often requires no custom arrow-key navigation.

Use grid semantics only when cells themselves support interactive,
application-style navigation.

---

# Arrow keys and page scrolling

Arrow keys normally scroll pages or affect native controls.

When implementing a composite widget that consumes arrow keys, prevent default
browser behavior only when the key has a defined function within that widget.

Do not suppress arrow-key behavior globally.

---

# Modifier keys

Be cautious with keyboard shortcuts involving:

- Ctrl;
- Alt;
- Shift;
- Meta.

Avoid overriding common browser, operating-system, or assistive-technology
shortcuts.

When custom shortcuts are necessary, ensure they do not interfere with standard
interaction.

---

# Single-character shortcuts

Single-character keyboard shortcuts can interfere with:

- speech input;
- screen reader commands;
- accidental key presses.

If an interface provides shortcuts triggered by a single printable character,
review the applicable WCAG requirements.

Users may need a way to:

- disable the shortcut;
- remap it;
- or limit it to when the relevant control has focus.

Do not confuse ordinary typing inside an input with a global character shortcut.

---

# Keyboard shortcuts should not be the only method

A custom keyboard shortcut may improve efficiency, but essential functionality
should normally remain available through standard interface controls.

Do not require users to discover or remember undocumented shortcuts in order to
complete core tasks.

---

# Pointer-only interactions

Be cautious with functionality triggered exclusively by:

- hover;
- drag;
- swipe;
- pointer position.

For hover-revealed content, keyboard focus should generally provide equivalent
access where relevant.

For drag-based functionality, verify whether an alternative interaction is
required under applicable WCAG criteria.

Do not treat all drag interactions as keyboard issues; pointer accessibility
may belong to a separate review area.

---

# Hover and focus parity

If important interactive content appears on hover, determine whether keyboard
users can reveal and interact with the same content.

For example, if hovering a card reveals action buttons, keyboard focus should
not leave those actions unavailable.

Do not blindly duplicate CSS `:hover` rules with `:focus`.

Evaluate the interaction and intended focus target.

---

# Disabled controls

Native disabled controls normally cannot be activated with keyboard or pointer
input.

For custom controls using:

```html
aria-disabled="true"
```

verify that keyboard activation is also blocked when the control is intended to
be unavailable.

Do not allow:

```text
Enter
Space
click
```

to continue performing the disabled action solely because ARIA indicates the
disabled state visually or semantically.

See:

`aria.md`

---

# Readonly controls

Readonly and disabled interactions differ.

Do not automatically block all keyboard access to readonly controls.

Readonly content may still need to support:

- focus;
- text selection;
- navigation;
- copying.

Evaluate the native behavior of the corresponding control.

---

# Hidden content

Keyboard users should not be able to tab into controls that are intended to be
fully hidden or inactive.

Be cautious with:

```html
aria-hidden="true"
```

because it does not itself remove descendants from keyboard focus.

When content is hidden, verify both:

- accessibility-tree exposure;
- focusability.

See:

- `aria.md`
- `focus.md`

---

# Event propagation

Keyboard handlers may interact with parent components.

Do not automatically add:

```typescript
event.stopPropagation();
```

or:

```typescript
event.preventDefault();
```

to solve event conflicts.

Each changes behavior differently.

`preventDefault()` prevents the browser's default behavior.

`stopPropagation()` prevents the event from continuing through the event
propagation path.

Use them only when the interaction pattern requires it.

---

# Simulated clicks

When a keyboard handler calls:

```typescript
element.click();
```

consider whether:

- it may cause duplicate activation;
- the element is already a native interactive control;
- click handlers distinguish pointer and keyboard events;
- default browser behavior also produces a click.

Do not add simulated clicks to native buttons or links merely to support
keyboard users.

Native controls already handle keyboard activation.

---

# Custom events

Design-system and framework components may expose custom events such as:

```text
ebClick
change
selectionChange
```

Do not judge keyboard accessibility solely from the event name.

A custom component may internally convert pointer and keyboard activation into
the same public event.

Inspect its behavior or implementation when possible.

If unavailable, mark keyboard behavior as:

**Needs verification**

rather than assuming it only supports mouse input.

---

# Component libraries

When reviewing a design-system component, inspect:

1. rendered element;
2. keyboard behavior;
3. exposed role;
4. focusability;
5. component documentation;
6. public events.

For example:

```html
<eb-button (ebClick)="save()"> Save </eb-button>
```

does not require:

```html
(keydown.enter)="save()"
```

merely because the application template only listens to `ebClick`.

The component may already normalize all activation internally.

Do not duplicate keyboard behavior outside the component without evidence that
it is missing.

---

# Framework event modifiers

Framework syntax may provide key filtering.

Examples include patterns such as:

```html
(keydown.enter)="activate()"
```

or equivalent framework APIs.

Do not judge accessibility from the syntax alone.

Determine:

- whether the target is native or custom;
- whether the key handler is necessary;
- whether native behavior is duplicated;
- whether default behavior must be prevented.

---

# Do not make non-interactive content focusable without purpose

Avoid:

```html
<div tabindex="0">Static information</div>
```

unless there is a clear interaction or navigation reason.

Adding focusability to static content can:

- increase the number of Tab stops;
- make navigation slower;
- create confusing expectations of interactivity.

Screen reader users can navigate static content without every element being
keyboard focusable.

---

# Programmatic focus is different from keyboard navigation

An element may legitimately use:

```html
tabindex="-1"
```

to receive programmatic focus without entering the normal Tab order.

This pattern is useful for:

- route-change focus;
- validation summaries;
- dialog headings;
- dynamically inserted content.

Do not report `tabindex="-1"` as inaccessible merely because the element cannot
be reached through Tab.

See:

`focus.md`

---

# Visual focus belongs to focus review

Keyboard operability and visible focus are related but distinct.

A control may be keyboard operable but still fail to provide an adequately
visible focus indicator.

Evaluate focus appearance in:

`focus.md`

Do not assume the focus indicator is missing from HTML alone when CSS is not
available.

---

# Testing keyboard interaction

When runtime testing is possible, test using keyboard only.

Depending on the interface, verify:

- Tab;
- Shift+Tab;
- Enter;
- Space;
- Escape;
- arrow keys;
- Home;
- End.

Do not mechanically test every key against every component.

Test the keys expected for the semantic pattern.

---

# Review decision process

When reviewing keyboard accessibility, follow this reasoning:

1. Determine whether the element is interactive.
2. Identify its native element or ARIA role.
3. Determine the expected keyboard behavior for that semantic role.
4. Determine whether native HTML already provides that behavior.
5. If custom behavior exists, inspect its key handling.
6. Verify focusability when appropriate.
7. Verify that activation does not require pointer input.
8. Check for duplicate native/custom activation.
9. Check for keyboard traps.
10. Inspect composite-widget navigation where applicable.
11. Check whether disabled states actually prevent activation.
12. Determine whether available code is sufficient to confirm behavior.
13. Require runtime verification when necessary.
14. Report only actual or credible accessibility barriers.

---

# Common review mistakes

## Mistake: adding Enter and Space to every click handler

Wrong reasoning:

> If `(click)` exists, add `(keydown.enter)` and `(keydown.space)`.

Correct approach:

First determine whether the target is a native interactive element.

Native buttons already provide keyboard activation.

---

## Mistake: requiring Space on links

Normal links are primarily activated with Enter.

Do not impose button behavior on links.

---

## Mistake: assuming a custom event is mouse-only

An event named `click`, `ebClick`, or similar does not reveal how the underlying
component handles keyboard interaction.

Inspect the component.

---

## Mistake: making everything focusable

Do not add `tabindex="0"` to static content simply to make it "accessible."

Keyboard focus is for interactive controls and specific programmatic focus
patterns.

---

## Mistake: positive tabindex to repair focus order

Do not use positive tabindex values to manually reorder the page.

Fix the DOM structure where possible.

---

## Mistake: implementing native button behavior manually

Avoid recreating Enter and Space handling when `<button>` can be used.

---

## Mistake: using arrow keys on ordinary groups of links

Arrow-key navigation belongs to specific composite widgets.

Ordinary navigation links normally remain in standard Tab navigation.

---

## Mistake: reporting missing keyboard support from template syntax alone

Custom components may implement keyboard interaction internally.

Inspect or verify before reporting.

---

## Mistake: preventing default on every key

`preventDefault()` should only be used when the widget needs to replace a
specific native behavior.

---

# Related references

Use these references for adjacent topics:

- `semantics.md` — choosing the correct native element and role.
- `aria.md` — roles, states, and ARIA properties.
- `focus.md` — focusability, focus order, visual focus, and programmatic focus.
- `forms.md` — keyboard behavior of form controls and validation interactions.
- `dynamic-content.md` — announcements and asynchronous interaction feedback.

---

# Final principle

Keyboard accessibility should follow the semantics of the interface.

When reviewing keyboard interaction:

**prefer native keyboard behavior**

**do not duplicate browser activation**

**determine expected keys from the control's semantics**

**use custom keyboard handling only when native behavior is insufficient**

**do not make static content focusable without reason**

**do not confuse keyboard operability with focus management**

**inspect design-system components before adding external handlers**

**verify complex runtime behavior instead of guessing**
