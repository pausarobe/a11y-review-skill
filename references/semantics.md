# HTML Semantics Reference

Use this reference when reviewing the semantic structure of web interfaces.

The objective is to determine whether the HTML represents the meaning and
behavior of the interface correctly.

Prefer native HTML semantics whenever they provide the required meaning and
behavior.

Do not evaluate semantics based on visual appearance alone.

---

# Native HTML first

Before recommending ARIA or custom keyboard behavior, determine whether a
native HTML element already represents the intended interface.

Native HTML should normally be preferred because it can provide:

- semantics;
- keyboard behavior;
- focus behavior;
- form behavior;
- states and properties;
- browser interoperability;
- assistive technology support.

Prefer the simplest native element that correctly represents the intended
behavior.

For example, prefer:

```html
<button type="button">Save</button>
```

over:

```html
<div role="button" tabindex="0" onclick="save()" onkeydown="...">Save</div>
```

The second implementation may be made accessible, but it requires recreating
behavior that the native button already provides.

Do not recommend replacing a custom implementation with native HTML solely
because native HTML exists.

First determine whether:

- the native element represents the intended behavior;
- the existing implementation already exposes equivalent semantics;
- there are implementation constraints that justify the custom control.

When the implementation of a custom component is unavailable, do not assume
that it lacks native semantics internally.

---

# Semantics are determined by purpose

Choose elements according to what the content or control means, not according
to how it looks.

CSS can make:

- a button look like a link;
- a link look like a button;
- a heading look like normal text;
- a list look like a group of cards.

Visual appearance does not determine semantics.

When reviewing an element, ask:

1. What does this element represent?
2. What happens when the user interacts with it?
3. Is there a native HTML element for that purpose?
4. Does the current implementation expose that meaning programmatically?

---

# Buttons and links

Buttons and links have different purposes.

## Buttons

Use a button for actions that operate on the current interface or application.

Typical examples include:

- submitting a form;
- opening a dialog;
- closing a dialog;
- toggling content;
- expanding or collapsing a section;
- deleting an item;
- saving data;
- starting or stopping an operation;
- opening a menu;
- changing an application state.

Prefer:

```html
<button type="button" onclick="openFilters()">Open filters</button>
```

over:

```html
<a href="#" onclick="openFilters()"> Open filters </a>
```

when no navigation occurs.

Use the appropriate button type.

Inside a form:

```html
<button type="submit">Save</button>
```

submits the form.

```html
<button type="button">Cancel</button>
```

does not.

Remember that a `<button>` associated with a form defaults to submit behavior
when its `type` is omitted.

Do not report the absence of `type="button"` as an accessibility issue without
considering the surrounding form context and actual behavior.

It may instead be a functional or robustness problem.

---

## Links

Use links for navigation to another resource or location.

Typical examples include:

- another page;
- another route;
- another document;
- a section identified by a fragment;
- an external website;
- a downloadable resource when navigation/download is the intended action.

Prefer:

```html
<a href="/account/details"> Account details </a>
```

for navigation.

Do not remove `href` from a link and recreate navigation entirely through
JavaScript without a justified reason.

A real `href` provides native browser behavior such as:

- keyboard activation;
- link semantics;
- opening in a new tab or window;
- copying the link address;
- browser context-menu functionality;
- expected browser navigation behavior.

For SPA navigation, preserving a meaningful `href` is generally preferable even
when JavaScript or a router intercepts navigation.

For example, a framework component that performs client-side routing may still
expose a real link destination.

Evaluate the rendered element and behavior rather than framework syntax alone.

---

## Button versus link decision

When uncertain, use this question:

> Does activation primarily perform an action or navigate to a resource?

If it performs an action:

```html
<button></button>
```

is normally appropriate.

If it navigates:

```html
<a href="..."></a>
```

is normally appropriate.

Do not decide based on styling.

---

# Generic containers

`<div>` and `<span>` are generic elements.

They are appropriate when no specific semantic element is required.

Do not report every `<div>` or `<span>` as an accessibility issue.

A generic container is not inherently inaccessible.

Report a problem only when meaningful semantics are missing or incorrect.

For example:

```html
<div class="card-layout">...</div>
```

may be completely appropriate.

However:

```html
<div onclick="save()">Save</div>
```

is suspicious because the element is being used as an interactive control
without native interactive semantics.

Prefer:

```html
<button type="button" onclick="save()">Save</button>
```

when the intended behavior is a button.

---

# Interactive content

Interactive functionality should normally use elements designed for
interaction.

Common native interactive elements include:

- `<button>`;
- `<a href>`;
- `<input>`;
- `<select>`;
- `<textarea>`;
- `<details>` / `<summary>` where appropriate.

Avoid recreating native controls from generic elements unless necessary.

If a generic element implements an interactive role, verify separately:

- semantic role;
- accessible name;
- focusability;
- keyboard behavior;
- state;
- expected interaction pattern.

See:

- `aria.md`
- `keyboard.md`
- `focus.md`

Do not assume that adding `role` makes a generic element equivalent to its
native HTML counterpart.

---

# Nested interactive content

Avoid nesting interactive controls in ways that create invalid or confusing
interaction.

For example, do not place a button inside a button:

```html
<button>
  Product
  <button type="button">Delete</button>
</button>
```

Likewise, be cautious when nesting interactive content inside links or other
interactive controls.

Problems can include:

- invalid HTML;
- ambiguous activation;
- confusing focus order;
- unexpected assistive technology output;
- events triggering multiple actions.

When reviewing nested controls, determine whether each interactive element
represents an independent action.

If multiple independent actions exist, structure them as separate controls
rather than making one interactive control contain another.

For example:

```html
<article>
  <a href="/product/123"> Product name </a>

  <button type="button">Delete</button>
</article>
```

may be preferable to making the entire container one interactive element that
contains additional controls.

---

# Headings

Use heading elements to represent document and section structure.

Available levels are:

```html
<h1>
  <h2>
    <h3>
      <h4>
        <h5>
          <h6></h6>
        </h5>
      </h4>
    </h3>
  </h2>
</h1>
```

Choose heading levels according to structural hierarchy, not visual size.

Prefer:

```html
<h2>Recent transactions</h2>
```

over:

```html
<div class="heading-large">Recent transactions</div>
```

when the text identifies a section.

CSS should control visual presentation.

---

## Heading hierarchy

Heading levels should represent the hierarchy of the content.

For example:

```html
<h1>Account details</h1>

<h2>Balance</h2>

<h2>Transactions</h2>

<h3>Pending transactions</h3>
```

Do not mechanically report every skipped heading level as a WCAG failure.

A heading sequence must be evaluated in the context of the document structure.

Prefer a logical hierarchy because it improves navigation and comprehension,
particularly for screen reader users.

Do not choose heading levels merely to obtain a particular visual style.

---

## Multiple h1 elements

HTML can technically contain multiple `<h1>` elements.

Do not automatically report multiple `<h1>` elements as a WCAG failure.

However, a clear primary page heading and understandable heading hierarchy are
usually easier for users to navigate.

Evaluate the actual document structure rather than applying a rule such as
"exactly one h1 is always required."

---

## Elements with heading semantics

Custom components may expose heading semantics internally.

For example:

```html
<design-system-section-title heading-level="2" heading-text="Transactions">
</design-system-section-title>
```

cannot be judged solely from the host element.

Inspect its rendered output or accessibility tree when possible.

If the component correctly renders or exposes an `h2`, do not recommend adding
another heading around it.

If its implementation is unavailable, mark the semantics as requiring
verification.

---

# Lists

Use lists when content represents a collection of related items.

Use:

```html
<ul></ul>
```

for unordered collections.

Use:

```html
<ol></ol>
```

when sequence or order is meaningful.

List items should normally use:

```html
<li></li>
```

For example:

```html
<ul>
  <li>Checking account</li>
  <li>Savings account</li>
  <li>Credit card</li>
</ul>
```

Do not require list markup merely because several visually similar components
appear next to each other.

Ask whether the content conceptually forms a list.

For example, several unrelated dashboard regions may not need list semantics.

---

## Navigation lists

Navigation links are often represented as a list inside a navigation landmark:

```html
<nav aria-label="Primary">
  <ul>
    <li><a href="/accounts">Accounts</a></li>
    <li><a href="/cards">Cards</a></li>
    <li><a href="/transfers">Transfers</a></li>
  </ul>
</nav>
```

The list communicates the relationship between navigation items.

However, do not report every `<nav>` without a `<ul>` as inaccessible.

HTML does not require navigation links to be wrapped in a list.

Evaluate whether list semantics provide meaningful structural information.

---

# Landmarks and page regions

Use semantic sectioning and landmark elements when they represent meaningful
page regions.

Common elements include:

```html
<header>
  <nav>
    <main>
      <aside>
        <footer></footer>
      </aside>
    </main>
  </nav>
</header>
```

These can help assistive technology users understand and navigate the page.

Do not add landmark roles unnecessarily when native HTML already provides the
appropriate semantics.

Prefer:

```html
<nav></nav>
```

over:

```html
<div role="navigation"></div>
```

when native HTML can be used.

---

## Main content

Use `<main>` for the primary content of the document.

Normally, a page should expose one primary main landmark.

Be careful with SPAs where multiple views may temporarily exist in the DOM.

Hidden or inactive content should not result in multiple simultaneously exposed
main landmarks.

---

## Multiple landmarks of the same type

When multiple landmarks of the same type are present, accessible names may be
needed to distinguish them.

For example:

```html
<nav aria-label="Primary">...</nav>

<nav aria-label="Account">...</nav>
```

Do not add labels to landmarks unnecessarily when there is only one and its
purpose is already clear.

---

## Header and footer context

`<header>` and `<footer>` semantics can depend on where they occur.

Do not assume every `<header>` or `<footer>` creates a page-level landmark.

Evaluate its context in the document structure.

---

# Section and article

Use `<section>` for a meaningful thematic grouping when the content forms a
section of the document.

A section should generally have an identifiable purpose, commonly expressed
through a heading.

Do not replace every `<div>` with `<section>`.

`<section>` is not a generic styling container.

Use `<article>` when the content represents a self-contained composition that
could reasonably stand independently or be reused/distributed separately.

Examples may include:

- news articles;
- forum posts;
- comments;
- independently meaningful content entries.

Do not mechanically use `<article>` for every card.

A visual "card" is a design pattern, not an HTML semantic.

---

# Tables

Use `<table>` for tabular data: information whose meaning depends on
relationships between rows and columns.

Do not use tables purely for visual layout.

Prefer:

```html
<table>
  <caption>
    Recent transactions
  </caption>
  <thead>
    <tr>
      <th scope="col">Date</th>
      <th scope="col">Description</th>
      <th scope="col">Amount</th>
    </tr>
  </thead>
  <tbody>
    ...
  </tbody>
</table>
```

when the data is genuinely tabular.

---

## Table headers

Use `<th>` for header cells.

For simple tables, `scope` can make relationships explicit:

```html
<th scope="col">Amount</th>
```

or:

```html
<th scope="row">January</th>
```

Complex tables may require more explicit header relationships.

Do not automatically require `scope` on every `<th>` when browser and assistive
technology header associations are already unambiguous.

Evaluate the table structure.

---

## Table caption

A `<caption>` can identify the purpose of a table.

Other valid naming mechanisms may also exist depending on the implementation.

Do not automatically report the absence of `<caption>` as a failure if the
table already has an appropriate accessible name or surrounding context.

Determine whether users can identify what the table represents.

---

## Responsive tables

Responsive design must not destroy meaningful table relationships.

If tabular data is visually transformed into cards on small screens, determine
whether the rendered semantics still communicate the relationships correctly.

Do not judge this solely from CSS class names.

Inspect the rendered DOM and accessibility tree when necessary.

---

# Forms and native controls

Prefer native form elements for user input:

```html
<input>
<select>
<textarea>
<button>
```

Use appropriate native input types where they represent the expected data or
interaction.

Examples include:

```html
<input type="email" />
<input type="tel" />
<input type="number" />
<input type="checkbox" />
<input type="radio" />
```

Native controls provide semantics and behavior that should not be recreated
without reason.

Detailed form accessibility guidance belongs in:

`forms.md`

---

# Fieldset and legend

Use `<fieldset>` and `<legend>` when a group of form controls requires a shared
group label.

A common example is a radio group:

```html
<fieldset>
  <legend>Preferred contact method</legend>

  <label>
    <input type="radio" name="contact" value="email" />
    Email
  </label>

  <label>
    <input type="radio" name="contact" value="phone" />
    Phone
  </label>
</fieldset>
```

Do not require `<fieldset>` for every collection of inputs.

Use it when the grouping relationship and group label are meaningful to
understanding the controls.

---

# Images

Use `<img>` when an image is content.

The semantics of the image and its alternative text depend on its purpose.

For example:

```html
<img src="warning.svg" alt="Warning" />
```

may be appropriate when the image itself communicates meaningful information.

Decorative images generally should not expose redundant information to
assistive technologies.

Alternative text decisions require understanding the image's purpose and
surrounding content.

Do not determine alt text solely from the filename.

---

# Navigation versus JavaScript routing

Frontend frameworks often perform navigation without full page reloads.

The use of JavaScript routing does not change the semantic distinction between
an action and navigation.

If activating a control navigates to another application route, link semantics
are generally appropriate.

For example, prefer an implementation that ultimately exposes:

```html
<a href="/documents"> Shared documents </a>
```

even if the router intercepts the activation and performs client-side
navigation.

A click handler does not automatically mean the element should be a button.

Determine what the interaction actually does.

---

# Click handlers do not determine semantics

Do not classify elements based solely on the presence of a click handler.

For example:

```html
<a href="/documents" onclick="navigateWithRouter(event)"> Shared documents </a>
```

can still correctly represent navigation.

Likewise:

```html
<button onclick="deleteItem()">Delete</button>
```

represents an action.

The intended user-facing behavior determines semantics.

---

# Disabled controls

Native controls should normally use their native disabled mechanism when the
control is genuinely unavailable.

For example:

```html
<button disabled>Save</button>
```

Do not treat a visually disabled control as functionally disabled without
checking its actual behavior.

Likewise, do not assume `aria-disabled="true"` prevents interaction.

ARIA communicates semantics; it does not automatically block events or provide
native disabled behavior.

Detailed state behavior belongs in:

`aria.md`

---

# Hidden content

Different hiding techniques have different semantic and interaction effects.

Examples include:

```css
display: none;
visibility: hidden;
```

the HTML `hidden` attribute, `inert`, visually-hidden techniques, and
`aria-hidden`.

Do not treat these mechanisms as interchangeable.

In particular:

- visually hidden content may intentionally remain available to assistive
  technologies;
- `aria-hidden="true"` affects the accessibility tree but does not itself make
  descendants unfocusable;
- `inert` prevents user interaction and removes the subtree from sequential
  focus navigation and the accessibility tree in supporting browsers.

Evaluate both visibility and interaction behavior.

See:

- `focus.md`
- `aria.md`

---

# Custom components

Custom component syntax does not reveal its final accessibility semantics.

For example:

```html
<eb-button> Save </eb-button>
```

must not automatically be reported as missing button semantics.

The component may:

- render a native `<button>`;
- use shadow DOM;
- expose semantics through its host;
- implement keyboard behavior internally.

When possible, inspect:

1. component implementation;
2. rendered DOM;
3. shadow DOM;
4. accessibility tree;
5. keyboard behavior.

If these are unavailable, state what requires verification.

Do not recommend wrapping a custom interactive component in a native interactive
element without understanding its implementation.

For example, blindly changing:

```html
<eb-button>Save</eb-button>
```

to:

```html
<button>
  <eb-button>Save</eb-button>
</button>
```

may create nested interactive controls and make accessibility worse.

---

# Web components and Shadow DOM

When reviewing web components, distinguish between the custom element host and
its internal implementation.

For example:

```html
<my-checkbox checked> Accept terms </my-checkbox>
```

does not provide enough information by itself to determine:

- exposed role;
- accessible name;
- checked state;
- keyboard behavior;
- focusability.

Inspect the accessibility tree or component implementation when available.

If unavailable, classify the relevant concern as:

**Needs verification**

rather than assuming the component is inaccessible.

---

# Framework templates

Framework-specific syntax does not change HTML semantics.

Examples include:

```html
@if (visible) {
<button>Save</button>
}
```

or:

```jsx
{
  visible && <button>Save</button>;
}
```

Review the interface that the framework ultimately renders.

Do not report framework syntax itself as an accessibility problem unless it
causes an accessibility barrier in the rendered UI.

---

# DOM order and visual order

Semantic structure follows the DOM and accessibility tree, not merely the
visual arrangement.

CSS can visually reorder content.

When visual order differs from DOM order, determine whether this causes:

- confusing reading order;
- confusing focus order;
- incorrect relationships;
- loss of meaning.

Do not automatically report CSS reordering as inaccessible.

Evaluate whether the resulting reading and interaction order remains logical.

Focus-specific guidance belongs in:

`focus.md`

---

# Semantic structure versus styling

Do not choose semantic elements for their default browser styles.

Likewise, do not avoid semantic elements because their default styles differ
from the design.

CSS should control presentation.

HTML should represent meaning.

Prefer:

```html
<h2 class="section-title">Transactions</h2>
```

over:

```html
<div class="section-title">Transactions</div>
```

when the content is actually a heading.

---

# ARIA does not replace HTML behavior

Adding a role changes exposed semantics but does not necessarily provide the
behavior of the corresponding native element.

For example:

```html
<div role="button" tabindex="0">Save</div>
```

may expose button semantics and be focusable, but keyboard activation behavior
must still be implemented correctly.

Prefer native HTML where practical.

Detailed ARIA guidance belongs in:

`aria.md`

Keyboard behavior belongs in:

`keyboard.md`

---

# Do not over-semanticize

More semantics are not automatically better.

Avoid:

- unnecessary roles;
- unnecessary landmarks;
- unnecessary headings;
- unnecessary sections;
- unnecessary list structures;
- redundant ARIA.

The goal is not to maximize the number of semantic elements.

The goal is to represent the actual structure and behavior accurately.

---

# Review decision process

When reviewing semantic HTML, follow this reasoning:

1. Determine what the content or control represents.
2. Determine whether it is interactive.
3. Determine what happens when it is activated.
4. Identify the native HTML element, if any, that represents that purpose.
5. Inspect the semantics currently exposed.
6. Determine whether native browser behavior is being unnecessarily recreated.
7. Consider framework, design-system, or web-component implementation details.
8. Determine whether there is sufficient evidence of an accessibility barrier.
9. Only then report a finding.

Do not use simplistic rules such as:

> Every clickable div is automatically a WCAG violation.

Instead determine:

- whether it is actually interactive;
- whether semantics are provided elsewhere;
- whether it is keyboard accessible;
- whether the implementation is visible in the provided code;
- what accessibility requirement is affected.

---

# Common review mistakes

Avoid these mistakes when reviewing semantics.

## Mistake: assuming custom elements are inaccessible

Wrong reasoning:

> `<eb-button>` is not a native `<button>`, therefore it is inaccessible.

Correct approach:

Inspect what the component renders or exposes.

---

## Mistake: deciding button versus link from appearance

Wrong reasoning:

> It looks like a button, therefore it must use `<button>`.

Correct approach:

Determine whether activation performs an action or navigation.

---

## Mistake: requiring ARIA on native HTML

Wrong reasoning:

```html
<button role="button"></button>
```

is more accessible than:

```html
<button></button>
```

It is not.

Native semantics already provide the role.

---

## Mistake: requiring lists everywhere

Repeated visual elements do not automatically require list semantics.

Determine whether they form a meaningful collection.

---

## Mistake: treating heading level rules mechanically

Do not report a heading solely because its numeric level differs from the
previous heading.

Evaluate the document hierarchy and actual structural meaning.

---

## Mistake: treating best practice as WCAG failure

A semantic improvement may be useful without constituting WCAG
non-conformance.

Clearly distinguish:

- confirmed WCAG failure;
- HTML or ARIA conformance issue;
- recommended accessibility pattern;
- best practice.

---

# Related references

Use these references for topics that extend beyond semantic HTML:

- `aria.md` — roles, states, properties, accessible names and ARIA relationships.
- `keyboard.md` — native and custom keyboard interaction.
- `focus.md` — focusability, focus order and focus management.
- `forms.md` — labels, validation, errors and form relationships.
- `dynamic-content.md` — live regions, asynchronous updates and announcements.

---

# Final principle

Semantic HTML should describe what the interface means and what its controls
do.

When reviewing semantics:

**understand purpose before choosing an element**

**prefer native HTML when it provides the required semantics and behavior**

**do not judge custom components from their tag name alone**

**do not confuse visual appearance with semantic meaning**

**do not add semantics that the browser already provides**

**do not report a violation without sufficient evidence**
