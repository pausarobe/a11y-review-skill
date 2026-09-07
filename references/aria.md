# ARIA Reference

Use this reference when reviewing or implementing WAI-ARIA roles, states,
properties, accessible names, descriptions, and relationships.

ARIA should be used to improve accessibility semantics when native HTML is not
sufficient.

ARIA should not be added automatically.

Prefer native HTML whenever it already provides the required semantics and
behavior.

---

# First rule of ARIA

Prefer native HTML over ARIA when a native element already represents the
required role, state, or behavior.

Prefer:

```html
<button type="button">Save</button>
```

over:

```html
<div role="button" tabindex="0">Save</div>
```

when the intended control is simply a button.

Likewise, prefer:

```html
<input type="checkbox" />
```

over a custom implementation using:

```html
<div role="checkbox" aria-checked="false"></div>
```

unless there is a justified reason to implement a custom widget.

ARIA can expose semantics, but it does not automatically reproduce native
browser behavior.

---

# ARIA changes semantics, not behavior

ARIA affects how an element is exposed to accessibility APIs.

It does not automatically provide:

- focusability;
- keyboard interaction;
- click behavior;
- form submission;
- disabled behavior;
- checked behavior;
- focus management.

For example:

```html
<div role="button">Save</div>
```

may expose button semantics, but it is not automatically focusable or operable
with Enter and Space.

Keyboard behavior belongs in:

`keyboard.md`

Focus behavior belongs in:

`focus.md`

---

# Do not add redundant ARIA

Do not add roles or ARIA states when native HTML already exposes equivalent
semantics.

Avoid:

```html
<button role="button">Save</button>
```

Prefer:

```html
<button>Save</button>
```

Avoid unnecessary duplication such as:

```html
<input type="checkbox" aria-checked="true" checked />
```

when the native `checked` state already exposes the required information.

Do not assume redundant ARIA is harmless.

Redundant or conflicting semantics can make implementations harder to maintain
and may produce inconsistent accessibility output.

---

# ARIA must match actual UI state

ARIA states and properties must reflect the actual state of the interface.

For example:

```html
<button aria-expanded="true" aria-controls="filters">Filters</button>
```

must only expose:

```text
aria-expanded="true"
```

when the controlled content is actually expanded.

Do not leave stale ARIA state after the UI changes.

State synchronization is essential.

---

# Roles

A role communicates what an element represents.

Examples include:

- `button`;
- `checkbox`;
- `radio`;
- `tab`;
- `tabpanel`;
- `dialog`;
- `alert`;
- `status`;
- `navigation`;
- `menu`;
- `menuitem`;
- `listbox`;
- `option`;
- `combobox`.

Do not choose roles based on visual appearance.

Choose the role according to the intended semantics and interaction pattern.

---

# Role validity

When reviewing a role, verify:

1. whether the role is necessary;
2. whether the role is valid for the element;
3. whether native semantics would be preferable;
4. whether required ARIA states and properties are present;
5. whether prohibited or unsupported ARIA attributes are being used;
6. whether keyboard behavior matches the role.

Do not assume:

```html
role="..."
```

is valid merely because the browser accepts it.

---

# Role and property compatibility

Not every ARIA attribute is supported by every role.

Before recommending an ARIA attribute, verify that it is:

- supported by the role;
- required by the role when applicable;
- valid for the element;
- meaningful for the intended interaction.

For example, do not recommend:

```html
aria-readonly
```

for a role that does not support that state.

Do not infer compatibility from attribute naming alone.

When uncertain, verify against the WAI-ARIA specification.

---

# Required ARIA properties

Some ARIA roles require particular states or properties.

When a custom widget uses such a role, verify that all required attributes are
present.

For example, a custom checkbox commonly requires a valid checked state such as:

```html
<div role="checkbox" aria-checked="false" tabindex="0">
  Receive notifications
</div>
```

Do not report missing required properties without confirming the role
requirements in the ARIA specification.

---

# Accessible name

Many interactive elements require an accessible name.

The accessible name may come from:

- visible text content;
- associated native labels;
- `aria-labelledby`;
- `aria-label`;
- other native naming mechanisms.

Do not assume every interactive element needs `aria-label`.

First determine whether it already has an accessible name.

---

# Prefer visible labels

When possible, prefer visible text that also provides the accessible name.

For example:

```html
<button>Save</button>
```

is preferable to:

```html
<button aria-label="Save">
  <span aria-hidden="true">💾</span>
</button>
```

if visible text can reasonably be provided.

Visible labels benefit:

- screen reader users;
- voice-control users;
- users with cognitive disabilities;
- all sighted users.

---

# aria-label

Use `aria-label` when an element needs an accessible name and an appropriate
visible or referenced label is unavailable.

Example:

```html
<button aria-label="Close">
  <svg aria-hidden="true">...</svg>
</button>
```

This may be appropriate for an icon-only button.

Do not add `aria-label` when meaningful visible text already provides the
correct accessible name.

For example, avoid unnecessary duplication:

```html
<button aria-label="Save">Save</button>
```

unless there is a specific reason.

---

# aria-label can override visible text

Be careful when `aria-label` and visible text differ.

For example:

```html
<button aria-label="Submit form">Send</button>
```

may expose an accessible name different from the visible label.

This can create problems for voice-control users who attempt to activate the
control by saying the text they see.

Prefer accessible names that include or match the visible label when practical.

Do not change the accessible name unnecessarily.

---

# aria-labelledby

Use `aria-labelledby` when the accessible name should come from one or more
existing elements.

Example:

```html
<h2 id="dialog-title">Delete account</h2>

<div role="dialog" aria-labelledby="dialog-title">...</div>
```

Verify that:

- every referenced ID exists;
- referenced elements contain appropriate text;
- references are unique where expected;
- the resulting name is meaningful.

Do not assume broken references are harmless.

---

# aria-label versus aria-labelledby

When both are present, the accessible-name computation determines which source
takes precedence.

Avoid providing multiple competing naming mechanisms unless there is a clear
reason.

Prefer one clear source of truth.

When visible text already exists elsewhere, `aria-labelledby` is often
preferable to duplicating that text in `aria-label`.

---

# aria-describedby

Use `aria-describedby` to associate supplementary descriptive information with
an element.

Example:

```html
<input id="password" type="password" aria-describedby="password-help" />

<p id="password-help">Minimum 12 characters.</p>
```

`aria-describedby` provides a description, not the primary accessible name.

Do not use it as a replacement for a required label.

---

# Name and description are different

For a form control:

```html
<label for="email"> Email </label>

<input id="email" type="email" aria-describedby="email-help" />

<p id="email-help">We will send the receipt to this address.</p>
```

the intended model is:

```text
Name: Email
Description: We will send the receipt to this address.
```

Do not collapse these concepts into one.

---

# aria-hidden

`aria-hidden="true"` removes an element and its descendants from the
accessibility tree.

Example:

```html
<svg aria-hidden="true">...</svg>
```

may be appropriate for a decorative icon.

Do not use `aria-hidden="true"` on meaningful content that users of assistive
technology need.

---

# aria-hidden does not prevent focus

Important:

`aria-hidden="true"` does not automatically prevent keyboard focus.

For example:

```html
<div aria-hidden="true">
  <button>Hidden action</button>
</div>
```

can create a problematic state if the button remains focusable.

A keyboard user may focus something that is hidden from the accessibility tree.

When hiding interactive subtrees, evaluate both:

- accessibility-tree exposure;
- focusability and interaction.

Consider appropriate mechanisms such as:

- `hidden`;
- `display: none`;
- `inert`;
- conditional rendering;

depending on the intended behavior.

---

# Do not apply aria-hidden to focusable elements

Avoid:

```html
<button aria-hidden="true">Save</button>
```

if the button remains interactive or focusable.

An element should not generally be hidden from assistive technology while still
being available for keyboard interaction.

If such a pattern appears, determine whether it is intentional and whether it
creates inconsistent access.

---

# aria-disabled

`aria-disabled="true"` communicates that a control is disabled or unavailable.

It does not automatically:

- prevent clicks;
- prevent keyboard activation;
- remove the control from the tab order;
- block JavaScript handlers;
- apply native disabled styling.

For example:

```html
<div role="button" aria-disabled="true" tabindex="0">Save</div>
```

still requires implementation logic to prevent activation if the control is
truly disabled.

---

# Prefer native disabled when available

For native form controls, prefer the native `disabled` attribute when its
behavior is appropriate.

Example:

```html
<button disabled>Save</button>
```

Native `disabled` provides browser behavior that `aria-disabled` alone does not.

However, native disabled controls are generally removed from sequential keyboard
focus.

In some interfaces, retaining a disabled control in the tab sequence may be an
intentional design choice.

Evaluate requirements before mechanically replacing one mechanism with the
other.

---

# aria-expanded

Use `aria-expanded` on controls that expand or collapse associated content.

Example:

```html
<button aria-expanded="false" aria-controls="filters">Filters</button>
```

When expanded:

```html
<button aria-expanded="true" aria-controls="filters">Filters</button>
```

The value must match the real expanded state.

Do not use `aria-expanded` merely because an element has a submenu-like visual
appearance.

Use it when the element actually controls expandable content.

---

# aria-controls

`aria-controls` identifies an element or elements controlled by the current
element.

Example:

```html
<button aria-expanded="false" aria-controls="filters-panel">Filters</button>

<div id="filters-panel">...</div>
```

Verify that referenced IDs exist.

Do not assume `aria-controls` creates behavior or relationships in the DOM.

It only exposes the relationship semantically.

Do not add it if it provides no meaningful accessibility benefit.

---

# aria-selected

Use `aria-selected` for roles where selection is part of the widget semantics,
such as tabs or options where appropriate.

Example:

```html
<div role="tablist">
  <button role="tab" aria-selected="true">Overview</button>

  <button role="tab" aria-selected="false">Transactions</button>
</div>
```

Do not confuse:

- selected;
- checked;
- current;
- expanded.

Each state represents a different concept.

---

# aria-current

Use `aria-current` to indicate the current item within a set of related items.

Common use cases include:

- current page;
- current step;
- current date;
- current location.

Example:

```html
<nav aria-label="Breadcrumb">
  <a href="/">Home</a>
  <a href="/accounts">Accounts</a>
  <a href="/accounts/current" aria-current="page"> Current account </a>
</nav>
```

Do not use `aria-selected` when the meaning is actually "current page" or
"current step."

---

# aria-checked

Use `aria-checked` with roles whose semantics support a checked state.

Examples include custom:

- checkboxes;
- radio buttons;
- switches;
- menuitemcheckbox;
- menuitemradio.

For a custom checkbox:

```html
<div role="checkbox" aria-checked="false" tabindex="0">
  Receive notifications
</div>
```

The ARIA state must remain synchronized with the actual UI.

---

# Native checked controls

Do not add `aria-checked` unnecessarily to native checked controls.

Prefer:

```html
<input type="checkbox" checked />
```

over:

```html
<input type="checkbox" checked aria-checked="true" />
```

when the native state already exposes the required semantics.

Likewise for native radio controls.

---

# Mixed checkbox state

Custom or native tri-state checkboxes may expose a mixed state where
appropriate.

For ARIA widgets:

```html
aria-checked="mixed"
```

can represent partial selection.

Do not use `mixed` unless the widget semantics genuinely support an
indeterminate or partially checked state.

---

# aria-readonly

`aria-readonly` communicates that a value cannot be modified while remaining
otherwise perceivable or operable according to the role semantics.

Do not assume it is valid for every widget role.

Before recommending it:

1. verify that the role supports `aria-readonly`;
2. determine whether readonly semantics match the intended behavior;
3. distinguish readonly from disabled.

Readonly and disabled are not equivalent.

A readonly control may still be focusable and its value may still be available
for selection or reading.

A disabled control communicates that the control is unavailable.

---

# aria-required

Use `aria-required="true"` when required semantics are not already provided by
native HTML and the role supports it.

For native form controls, prefer the native `required` attribute when
appropriate:

```html
<input type="email" required />
```

Do not add:

```html
aria-required="true"
```

solely to duplicate the native required state.

Visible indication of required fields must also be considered for sighted
users.

Detailed form guidance belongs in:

`forms.md`

---

# aria-invalid

Use `aria-invalid="true"` to communicate that the current value is invalid when
appropriate.

Example:

```html
<input
  id="email"
  type="email"
  aria-invalid="true"
  aria-describedby="email-error"
/>

<p id="email-error">Enter a valid email address.</p>
```

Do not set `aria-invalid="true"` before validation has determined the field to
be invalid unless the interaction design explicitly requires it.

Keep the state synchronized with validation.

---

# aria-errormessage

`aria-errormessage` may associate an input with an error message where supported
and appropriate.

However, do not automatically prefer it over established patterns such as
`aria-describedby`.

Browser and assistive technology support and project conventions should be
considered.

Avoid introducing a less interoperable mechanism without a clear benefit.

---

# aria-live

Use live regions only when dynamic content needs to be announced without moving
focus.

Common values include:

```html
aria-live="polite"
```

and:

```html
aria-live="assertive"
```

Do not add live regions to every dynamically changing element.

First determine:

- whether the update needs to be announced;
- how urgent it is;
- whether focus movement already communicates the change;
- whether another live region already announces it.

Detailed guidance belongs in:

`dynamic-content.md`

---

# role="status"

Use `role="status"` for important but non-urgent status information.

Examples may include:

- operation completed;
- search results updated;
- item added to cart;
- background process finished.

`status` generally behaves like a polite live region.

Do not use it for every piece of changing text.

---

# role="alert"

Use `role="alert"` for important information requiring immediate user
attention.

Examples may include:

- critical validation feedback after submission;
- urgent application errors.

Alerts can interrupt current screen reader output.

Do not use `role="alert"` for routine status information.

Overuse can create a noisy and disruptive experience.

---

# role="dialog"

A dialog role communicates that content represents a dialog window.

Example:

```html
<div role="dialog" aria-modal="true" aria-labelledby="dialog-title">
  <h2 id="dialog-title">Delete account</h2>

  ...
</div>
```

ARIA semantics alone are not sufficient.

An accessible dialog also requires appropriate:

- focus placement;
- keyboard interaction;
- focus containment where applicable;
- Escape behavior when appropriate;
- focus restoration.

See:

- `focus.md`
- `keyboard.md`

Prefer the native `<dialog>` element when it meets project requirements and is
implemented accessibly.

Do not assume native `<dialog>` removes the need for testing.

---

# aria-modal

Use `aria-modal="true"` only when the interface actually behaves modally.

If content outside the dialog remains available for normal interaction, then
the dialog is not truly modal.

Do not use `aria-modal="true"` merely because the element visually resembles a
modal.

Semantic state must match interaction behavior.

---

# Tabs

ARIA tabs form a composite widget.

Typical semantics include:

```html
<div role="tablist">
  <button
    role="tab"
    aria-selected="true"
    aria-controls="panel-overview"
    id="tab-overview"
  >
    Overview
  </button>
</div>

<div role="tabpanel" id="panel-overview" aria-labelledby="tab-overview">
  ...
</div>
```

Do not review tab semantics without also reviewing expected keyboard behavior.

See:

`keyboard.md`

Do not add `role="tab"` to controls unless the interface genuinely follows a
tabbed-interface pattern.

A group of ordinary navigation links is not automatically a tablist.

---

# Menus

Do not use ARIA menu roles for ordinary website navigation simply because the
UI visually looks like a menu.

Roles such as:

```text
menu
menubar
menuitem
```

represent application-style menu interaction patterns and bring specific
keyboard expectations.

A standard site navigation is often better represented with:

```html
<nav>
  <a href="...">...</a>
</nav>
```

or equivalent native structure.

Use ARIA menu roles only when the interaction actually follows that widget
pattern.

---

# Comboboxes

Comboboxes are complex widgets.

Do not infer that a text input with suggestions automatically requires every
combobox-related ARIA attribute.

When reviewing a combobox, inspect:

- role;
- expanded state;
- controlled popup;
- active option;
- option semantics;
- keyboard interaction;
- focus model.

Prefer established accessible component implementations over hand-rolling a
complex ARIA combobox when practical.

---

# aria-activedescendant

`aria-activedescendant` can be used in composite widgets where DOM focus remains
on one element while another descendant is exposed as the active item.

Do not recommend it unless the widget pattern requires that focus model.

Verify:

- referenced ID exists;
- active descendant belongs to the appropriate owned or controlled structure;
- the state updates with keyboard navigation.

This is an advanced ARIA pattern and should not be added casually.

---

# aria-owns

Use `aria-owns` with caution.

It can alter accessibility-tree relationships and is often unnecessary.

Prefer natural DOM relationships when possible.

Do not use `aria-owns` merely to describe a visual relationship.

If a simpler DOM structure or `aria-controls` is sufficient, prefer that.

---

# aria-haspopup

Use `aria-haspopup` only when a control opens a popup of the corresponding type.

Possible semantic values include popup types such as:

- menu;
- listbox;
- tree;
- grid;
- dialog.

Do not set `aria-haspopup="true"` mechanically on every control that reveals
content.

Simple disclosure content does not necessarily require `aria-haspopup`.

---

# aria-pressed

Use `aria-pressed` for toggle buttons.

Example:

```html
<button type="button" aria-pressed="false">Bold</button>
```

When active:

```html
aria-pressed="true"
```

Use it when the button itself represents an on/off pressed state.

Do not confuse it with:

- `aria-selected`;
- `aria-checked`;
- `aria-expanded`.

---

# Relationships must be valid

ARIA relationships based on IDs must reference real elements.

Examples include:

- `aria-labelledby`;
- `aria-describedby`;
- `aria-controls`;
- `aria-owns`;
- `aria-activedescendant`.

Check:

- ID existence;
- uniqueness;
- lifecycle;
- conditional rendering.

Frameworks can make these relationships especially fragile when IDs are
generated or elements are conditionally rendered.

---

# Conditional rendering and stale references

Be cautious when framework templates conditionally render referenced elements.

For example:

```html
<button aria-describedby="error-message">Submit</button>
```

is problematic if:

```html
<p id="error-message"></p>
```

does not exist when the relationship is expected.

Do not assume framework bindings always keep relationships valid.

Inspect rendered state where necessary.

---

# Duplicate IDs

ARIA relationships depend on unique element IDs.

Duplicate IDs can make accessible naming and description relationships
ambiguous or incorrect.

When reviewing components rendered repeatedly, check whether IDs are generated
uniquely.

Do not report duplicate-ID risk without evidence that the component can
actually produce duplicates.

---

# Dynamic ARIA state

Framework bindings commonly control ARIA state.

Examples:

```html
<button [attr.aria-expanded]="isOpen"></button>
```

or:

```jsx
<button aria-expanded={isOpen}>
```

The framework syntax itself is not the accessibility concern.

Determine whether the rendered attribute value accurately reflects the UI
state.

---

# Boolean ARIA values

ARIA boolean states are represented as string values in rendered HTML.

For example:

```html
aria-expanded="true"
```

and:

```html
aria-expanded="false"
```

Do not assume presence alone means true.

Be careful with framework templates that accidentally render invalid values such
as:

```html
aria-expanded=""
```

or:

```html
aria-expanded="null"
```

Evaluate the actual rendered DOM where necessary.

---

# Removing ARIA attributes

For optional ARIA attributes, removing the attribute may differ semantically
from setting it to `"false"`.

For example:

```text
aria-expanded absent
```

does not always mean the same thing as:

```text
aria-expanded="false"
```

Understand the semantics of each property before deciding whether to remove or
set a value.

---

# Custom elements and design systems

Do not judge a custom component solely from attributes visible on its host.

Example:

```html
<eb-checkbox checked label="Receive notifications"> </eb-checkbox>
```

may internally expose correct:

- checkbox role;
- accessible name;
- checked state;
- keyboard interaction.

Inspect:

1. implementation;
2. rendered DOM;
3. shadow DOM;
4. accessibility tree;
5. keyboard behavior.

If these are unavailable, mark relevant concerns as:

**Needs verification**

Do not add ARIA to the host element blindly if the component already manages
its own accessibility semantics.

---

# Avoid overriding component semantics

When using an accessible design-system component, be cautious about externally
adding:

- `role`;
- `tabindex`;
- `aria-*`;

that the component already manages internally.

External overrides can create conflicting semantics.

Before modifying ARIA on a custom component, determine its public accessibility
API and rendered behavior.

---

# Presentational roles

Roles such as:

```html
role="presentation"
```

or:

```html
role="none"
```

remove certain semantic meaning.

Do not apply them to meaningful interactive elements or structural content
without understanding the impact.

They should not be used merely to suppress an accessibility warning.

---

# Native semantics can override or constrain ARIA

Not every ARIA role is valid on every native HTML element.

Avoid using ARIA to force an element into a semantic role that conflicts with
its native behavior.

When changing semantics significantly, prefer choosing the correct HTML element
instead.

---

# ARIA and CSS visibility

ARIA state does not necessarily match visual visibility automatically.

For example:

```html
aria-expanded="false"
```

does not hide content.

Likewise:

```html
aria-hidden="true"
```

does not visually hide content.

Semantic state and visual/interaction state must be coordinated by the
implementation.

---

# ARIA and events

Do not infer correct interaction from ARIA alone.

For example:

```html
<div role="checkbox" aria-checked="false">Accept terms</div>
```

may expose semantics but still fail keyboard interaction.

Review event handling separately.

See:

`keyboard.md`

---

# ARIA and focus

ARIA does not control focus unless a specific pattern such as
`aria-activedescendant` is used.

Adding:

```html
role="dialog"
```

does not move focus into the dialog.

Adding:

```html
role="tab"
```

does not implement roving tabindex.

Review focus behavior separately.

See:

`focus.md`

---

# Do not use ARIA to hide implementation problems

Avoid adding ARIA as a patch for incorrect native structure.

For example, do not prefer:

```html
<div role="heading" aria-level="2">Transactions</div>
```

over:

```html
<h2>Transactions</h2>
```

when native heading markup can be used.

ARIA is not a substitute for semantic HTML.

---

# Review decision process

When reviewing ARIA, follow this reasoning:

1. Determine the intended semantics.
2. Determine whether native HTML already provides them.
3. Determine whether ARIA is necessary.
4. Validate the role.
5. Validate each ARIA state and property.
6. Verify role/property compatibility.
7. Verify required states and properties.
8. Verify accessible naming and description.
9. Verify referenced IDs.
10. Verify state synchronization.
11. Check whether ARIA conflicts with native semantics.
12. Consider keyboard and focus behavior.
13. Determine whether the issue is confirmed or requires runtime verification.
14. Only then report a finding.

---

# Common review mistakes

## Mistake: adding ARIA to make native HTML "more accessible"

Wrong:

```html
<button role="button" aria-label="Save">Save</button>
```

Correct approach:

Use native semantics and add ARIA only when it provides necessary information.

---

## Mistake: assuming aria-disabled blocks interaction

Wrong reasoning:

> `aria-disabled="true"` means the click cannot fire.

Correct reasoning:

ARIA communicates disabled semantics but interaction must still be prevented by
the implementation.

---

## Mistake: assuming aria-hidden removes keyboard focus

Wrong reasoning:

> Descendants of `aria-hidden="true"` cannot receive focus.

Correct reasoning:

`aria-hidden` affects accessibility-tree exposure, not focusability by itself.

---

## Mistake: using aria-label as the default naming strategy

Wrong reasoning:

> Every icon, input, or button should receive `aria-label`.

Correct reasoning:

First determine whether native labels, visible text, or `aria-labelledby`
already provide the accessible name.

---

## Mistake: treating selected, checked, current and expanded as equivalent

These states represent different concepts.

Use the state appropriate to the widget semantics.

---

## Mistake: inventing unsupported ARIA

Do not recommend an ARIA state simply because its name seems semantically
related.

Verify role support in the WAI-ARIA specification.

---

## Mistake: using application widget roles for ordinary web content

Examples include using:

```text
menu
menuitem
tab
```

when the interface is actually ordinary navigation.

ARIA widget roles bring specific semantic and keyboard expectations.

---

## Mistake: assuming ARIA guarantees screen reader output

Actual announcements may depend on:

- browser;
- accessibility API;
- assistive technology;
- DOM timing;
- focus;
- live-region behavior.

When necessary, require runtime verification rather than making absolute claims.

---

# Related references

Use these references for adjacent topics:

- `semantics.md` — native HTML and semantic structure.
- `keyboard.md` — expected keyboard interaction.
- `focus.md` — focusability and focus management.
- `forms.md` — labels, validation, errors and form semantics.
- `dynamic-content.md` — live regions, alerts, status messages and updates.

---

# Final principle

ARIA should communicate semantics that the browser cannot already provide
appropriately.

When reviewing ARIA:

**use native HTML first**

**do not add ARIA without a semantic reason**

**validate role and property compatibility**

**keep ARIA synchronized with the real interface**

**distinguish name, description, state and value**

**do not assume ARIA provides behavior**

**do not modify design-system semantics blindly**

**verify uncertain behavior instead of guessing**
