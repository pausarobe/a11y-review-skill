# Forms Accessibility Reference

Use this reference when reviewing or implementing accessible forms.

The objective is to ensure that users can understand, complete, review, and
correct form input using different input methods and assistive technologies.

Prefer native form semantics whenever possible.

Do not treat form accessibility as only a labeling problem.

Accessible forms also require:

- understandable instructions;
- meaningful grouping;
- predictable validation;
- programmatically associated errors;
- appropriate required-state communication;
- usable keyboard interaction;
- sensible focus behavior.

---

# Native form controls first

Prefer native form controls when they represent the required interaction.

Examples include:

- `<input>`;
- `<textarea>`;
- `<select>`;
- `<button>`;
- `<fieldset>`;
- `<legend>`;
- `<label>`.

Native controls provide browser behavior and semantics that custom widgets must
otherwise reproduce.

Prefer:

```html
<label for="email">Email</label> <input id="email" type="email" />
```

over:

```html
<div>Email</div>
<div role="textbox" contenteditable="true"></div>
```

unless a custom control is genuinely required.

---

# Every control needs an understandable purpose

When reviewing a form control, determine whether users can understand:

- what information is requested;
- whether it is required;
- any formatting constraints;
- any relevant instructions;
- its current state;
- validation errors;
- how to correct an error.

Do not assume a placeholder or visual layout provides enough information.

---

# Native labels

Use `<label>` for native form controls when possible.

Preferred pattern:

```html
<label for="email"> Email </label>

<input id="email" type="email" />
```

The `for` value must match the control's `id`.

Another valid native pattern is wrapping the control:

```html
<label>
  Email
  <input type="email" />
</label>
```

Use project conventions consistently.

---

# Visible labels are preferable

Visible labels usually provide the best experience because they benefit:

- screen reader users;
- voice-control users;
- users with cognitive disabilities;
- users reviewing completed forms;
- sighted keyboard users.

Do not hide a useful visible label merely because `aria-label` can provide an
accessible name.

Prefer:

```html
<label for="account"> Account number </label>

<input id="account" />
```

over:

```html
<input aria-label="Account number" />
```

when a visible label is practical.

---

# Placeholder is not a label

Do not use placeholder text as the only identification of a form control.

Avoid:

```html
<input type="email" placeholder="Email" />
```

without an associated label or equivalent accessible name.

Placeholder text may:

- disappear when users type;
- have poor contrast;
- make reviewing entered data harder;
- be inconsistently announced.

Use placeholder text only for supplementary examples or hints when appropriate.

---

# aria-label and aria-labelledby

When a native visible label cannot be used, other accessible naming mechanisms
may be valid.

Examples include:

```html
<input aria-label="Search" />
```

or:

```html
<span id="search-label"> Search transactions </span>

<input aria-labelledby="search-label" />
```

Do not automatically add ARIA naming when a native `<label>` already provides
the correct accessible name.

See:

`aria.md`

---

# Labels for custom form components

Custom components may expose their own labeling API.

For example:

```html
<eb-input-text label="Email"> </eb-input-text>
```

may internally render a native label and input.

Do not assume a missing outer `<label>` makes the component inaccessible.

Inspect:

- component documentation;
- rendered DOM;
- accessibility tree;
- shadow DOM where relevant.

If the internal labeling cannot be determined, classify the concern as:

**Needs verification**

---

# Multiple labels and accessible name conflicts

Be cautious when a control has multiple naming mechanisms such as:

- `<label>`;
- `aria-label`;
- `aria-labelledby`.

These can conflict or override each other.

Do not add multiple accessible-name sources without understanding the resulting
name computation.

Prefer one clear source of truth.

---

# Form control grouping

Related controls may require programmatic grouping.

Common examples include:

- radio groups;
- related checkboxes;
- groups of fields sharing one question;
- grouped contact information where a shared label is necessary.

Native grouping is often provided with:

```html
<fieldset>
  <legend>Preferred contact method</legend>

  ...
</fieldset>
```

---

# fieldset and legend

Use `<fieldset>` and `<legend>` when a set of controls shares a common question
or meaning.

Example:

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

The legend provides the group-level context.

Do not require `<fieldset>` for every visual grouping of fields.

Use it when the controls need a shared programmatic label.

---

# Radio groups

Prefer native radio buttons where practical.

Example:

```html
<fieldset>
  <legend>Payment method</legend>

  <label>
    <input type="radio" name="payment" value="card" />
    Card
  </label>

  <label>
    <input type="radio" name="payment" value="transfer" />
    Bank transfer
  </label>
</fieldset>
```

Native radio buttons provide:

- radio semantics;
- checked state;
- grouping through `name`;
- keyboard behavior.

Do not add redundant:

```html
role="radio"
```

or:

```html
aria-checked
```

to native radio inputs without a specific reason.

---

# Checkbox groups

Checkboxes may also need a group label when they answer one shared question.

Example:

```html
<fieldset>
  <legend>Select notification channels</legend>

  <label>
    <input type="checkbox" />
    Email
  </label>

  <label>
    <input type="checkbox" />
    SMS
  </label>
</fieldset>
```

Do not require a fieldset if each checkbox represents an independent setting
and no shared group question is necessary.

---

# Instructions

Provide instructions before users need them.

Examples include:

- password rules;
- date format;
- allowed file types;
- expected account-number format;
- required selections.

Prefer visible instructions.

Where necessary, associate instructions programmatically with the control.

Example:

```html
<label for="password"> Password </label>

<input id="password" type="password" aria-describedby="password-help" />

<p id="password-help">Use at least 12 characters.</p>
```

Do not put essential instructions only inside placeholder text.

---

# Accessible descriptions

Use `aria-describedby` for supplementary instructions or descriptions when
appropriate.

For example:

```html
<input id="username" aria-describedby="username-help" />

<p id="username-help">Use 4 to 20 characters.</p>
```

Do not use `aria-describedby` as a substitute for the control's primary label.

Name and description serve different purposes.

See:

`aria.md`

---

# Required fields

Users must be able to determine which fields are required.

For native controls, prefer:

```html
<input required />
```

when appropriate.

Do not add:

```html
aria-required="true"
```

solely to duplicate native required semantics.

However, programmatic required state alone may not be sufficient for all sighted
users.

Provide a visible indication when needed.

---

# Visible required indication

If required fields are marked visually, the meaning must be understandable.

For example:

```html
<label for="name"> Name <span aria-hidden="true">*</span> </label>

<p id="required-note">* Required field</p>
```

can be valid when the overall form makes the meaning clear.

Do not rely on color alone to communicate required state.

Do not add unnecessary screen-reader text if the native required state and
visible wording already provide sufficient information.

---

# Optional fields

In some forms, marking optional fields can be clearer than marking every
required field.

For example:

```html
<label for="middle-name"> Middle name (optional) </label>
```

The choice depends on the form.

Do not enforce one visual convention universally.

The important requirement is that users can understand what is required before
submission.

---

# Input type

Use the native input type that best represents the expected value.

Examples include:

```html
<input type="email" />
<input type="tel" />
<input type="url" />
<input type="date" />
<input type="number" />
```

Appropriate types can improve:

- native validation;
- mobile keyboard layout;
- autofill;
- semantics.

Do not choose an input type solely for visual appearance.

For example, `type="number"` may be inappropriate for identifiers that are
numeric-looking but are not mathematical values.

---

# Inputmode

`inputmode` may be used to suggest an appropriate virtual keyboard.

Example:

```html
<input inputmode="numeric" />
```

may be appropriate for numeric-looking identifiers where `type="number"` would
be semantically wrong.

Do not treat `inputmode` as a substitute for proper labeling or validation.

---

# autocomplete

Use appropriate `autocomplete` tokens for fields collecting user information
when applicable.

Examples include:

```html
autocomplete="name" autocomplete="email" autocomplete="tel"
autocomplete="street-address" autocomplete="postal-code"
autocomplete="current-password" autocomplete="new-password"
```

Correct autocomplete values can improve accessibility and reduce cognitive
effort.

Do not disable autocomplete without a justified reason.

---

# Do not block paste unnecessarily

Avoid preventing users from pasting into fields such as:

- passwords;
- confirmation fields;
- security codes;

unless there is a strong, justified reason.

Blocking paste can interfere with:

- password managers;
- assistive technology;
- users with motor or cognitive disabilities.

Do not assume retyping improves security.

---

# Accessible authentication

Authentication flows should not rely solely on cognitive-function tests such as:

- memorizing complex passwords;
- transcribing inaccessible challenges;
- solving puzzles.

Allow appropriate mechanisms such as:

- password managers;
- paste;
- passkeys;
- one-time codes;
- alternative authentication methods.

When reviewing authentication, consider applicable WCAG 2.2 requirements.

---

# Validation timing

Do not assume validation must occur either only on submit or immediately while
typing.

Both patterns can be valid.

Evaluate whether validation:

- is understandable;
- is predictable;
- avoids interrupting users unnecessarily;
- communicates errors clearly;
- allows correction.

Avoid aggressively announcing errors on every keystroke unless the interaction
requires it.

---

# Native validation

Native browser validation may provide useful behavior.

However, custom applications often implement their own validation.

Do not assume native validation alone satisfies every accessibility need.

Inspect:

- error identification;
- programmatic association;
- focus behavior;
- announcement behavior;
- recovery path.

---

# Error identification

When a validation error occurs, users need to understand:

- which field is invalid;
- what is wrong;
- how to correct it when possible.

Avoid vague messages such as:

```text
Invalid input
```

when more specific guidance is available.

Prefer:

```text
Enter an email address in the format name@example.com.
```

when appropriate.

---

# Associate errors with controls

Where possible, associate an error message programmatically with the invalid
control.

Example:

```html
<label for="email"> Email </label>

<input
  id="email"
  type="email"
  aria-invalid="true"
  aria-describedby="email-error"
/>

<p id="email-error">Enter a valid email address.</p>
```

The relationship helps assistive technologies expose the error in the context
of the field.

---

# aria-invalid

Use:

```html
aria-invalid="true"
```

when the current value has been determined to be invalid and exposing this state
is appropriate.

Do not leave the attribute stale after the value becomes valid.

Do not set every field to invalid immediately when the page first loads unless
that behavior is intentionally required.

See:

`aria.md`

---

# Error descriptions and existing help text

A control may already use `aria-describedby` for help text.

When adding an error, ensure the description relationship still exposes all
necessary information.

Example:

```html
<input id="password" aria-describedby="password-help password-error" />
```

can associate both instructions and the current error.

Do not accidentally replace useful help text with only the error message unless
that is intended.

---

# aria-errormessage

`aria-errormessage` may be used where appropriate, but do not automatically
prefer it over established `aria-describedby` patterns.

Consider:

- browser support;
- assistive technology support;
- project conventions;
- implementation complexity.

Use the most interoperable solution for the project's supported environment.

---

# Error messages should be visible

Do not provide validation feedback only through ARIA.

Sighted users also need to perceive errors.

An error may use:

- visible text;
- iconography;
- color;
- borders.

Do not rely on color alone.

---

# Error summary

For larger forms, an error summary can help users identify multiple errors.

Example:

```html
<div tabindex="-1">
  <h2>There are 3 errors</h2>

  <ul>
    <li>
      <a href="#email"> Enter a valid email address. </a>
    </li>
  </ul>
</div>
```

The summary may receive programmatic focus after failed submission.

The exact pattern depends on the form.

See:

`focus.md`

---

# Focus after validation failure

Possible approaches include:

- move focus to the error summary;
- move focus to the first invalid field;
- keep focus on the submit button and announce the result.

Do not assume one approach is always correct.

Choose the pattern that best supports the form structure and user task.

---

# Errors and live regions

Dynamic error messages may need announcement.

Do not automatically add:

```html
role="alert"
```

to every field error.

Too many alerts can create noisy or repeated announcements.

Determine:

- whether the error appears after user action;
- whether focus moves to the field;
- whether the error will already be included in the field description;
- whether a summary announces the errors.

See:

`dynamic-content.md`

---

# Error correction

Where possible, provide guidance that helps users correct mistakes.

For example:

```text
Card number must contain 16 digits.
```

is more useful than:

```text
Invalid card number.
```

Do not provide correction suggestions when doing so would create security or
privacy problems.

---

# Confirmation for important actions

For actions that create significant legal, financial, or irreversible
consequences, users may need an opportunity to:

- review;
- correct;
- confirm;

their information before final submission.

Evaluate applicable WCAG requirements for error prevention when relevant.

---

# Redundant entry

Within a single process, avoid requiring users to re-enter information they have
already provided when the information can reasonably be reused.

Examples include:

- entering the same address twice;
- repeatedly entering an account identifier;
- repeating personal details across sequential steps.

Where appropriate, provide:

- pre-filled values;
- selection from previous entries;
- copy mechanisms.

Evaluate applicable WCAG 2.2 requirements.

---

# Disabled submit buttons

Be cautious when disabling a form's submit button until all fields are valid.

This can make it difficult for users to discover:

- which fields are incomplete;
- what validation rules failed;
- how to proceed.

A disabled submit button is not automatically inaccessible.

However, evaluate whether users receive enough information before the control
becomes available.

In some cases, allowing submission and then exposing clear validation errors is
more understandable.

Do not report disabled submit controls as a blanket violation.

---

# aria-disabled submit controls

If a custom submit control uses:

```html
aria-disabled="true"
```

remember that ARIA alone does not block activation.

Verify that:

- keyboard activation is prevented;
- pointer activation is prevented;
- disabled semantics match the real behavior.

See:

`aria.md`

---

# Readonly fields

Readonly controls and disabled controls represent different states.

Example:

```html
<input value="ES123456789" readonly />
```

may remain focusable so users can:

- inspect;
- select;
- copy;

the value.

Do not replace readonly with disabled unless the intended semantics genuinely
mean unavailable.

---

# Disabled controls

Native disabled controls generally:

- cannot be edited;
- cannot be activated;
- are removed from sequential focus navigation;
- may not be submitted with the form.

This behavior can be appropriate.

Do not report native disabled behavior as inaccessible by itself.

Evaluate whether users can still understand why the control is unavailable when
that explanation is necessary.

---

# Dynamic enablement

When a control becomes enabled after another selection, ensure the dependency is
understandable.

Example:

```text
Select "Other" to enable the details field.
```

Do not make required interaction relationships discoverable only by trial and
error.

---

# Conditional form fields

When fields appear or disappear dynamically:

- maintain logical reading order;
- ensure new fields have correct labels;
- avoid unexpected focus movement;
- ensure hidden controls are not focusable;
- expose validation only for relevant active fields.

Do not validate hidden or inactive fields unless the application genuinely
requires their values.

---

# Multi-step forms

For multi-step forms or wizards, users should understand:

- current step;
- total or remaining steps when useful;
- required actions;
- validation errors;
- navigation between steps.

When moving to a new step, consider appropriate focus management.

See:

`focus.md`

---

# Form progress

Progress indicators may expose the current step semantically.

For example:

```html
<ol>
  <li aria-current="step">Personal details</li>
  ...
</ol>
```

may be appropriate.

Do not use `aria-selected` when the concept is current step.

See:

`aria.md`

---

# Search forms

Search inputs should have an accessible name.

Example:

```html
<label for="site-search"> Search </label>

<input id="site-search" type="search" />
```

A visually hidden label may be valid when the visible interface already makes
the purpose obvious.

Do not assume `type="search"` itself provides a meaningful accessible name.

---

# Icon-only form controls

Icon-only controls such as:

- clear search;
- show password;
- calendar trigger;
- remove item;

need an accessible name.

Example:

```html
<button type="button" aria-label="Clear search">
  <svg aria-hidden="true">...</svg>
</button>
```

The accessible name should describe the action.

Do not rely only on the icon's visual meaning.

---

# Show/hide password controls

A password visibility toggle should be an actual interactive control.

Example:

```html
<button type="button" aria-pressed="false">Show password</button>
```

or another appropriate pattern.

Ensure:

- accessible name is understandable;
- state is exposed when needed;
- keyboard operation works;
- toggling does not unexpectedly move focus.

---

# Date inputs

Date-entry accessibility depends on the chosen control.

Native:

```html
<input type="date" />
```

may provide platform-specific behavior.

Custom date pickers are substantially more complex.

If a custom date picker is used, inspect:

- input labeling;
- popup semantics;
- keyboard navigation;
- focus management;
- selected/current date state;
- format instructions.

Do not assume a visual calendar is accessible solely because it has ARIA roles.

---

# File inputs

File upload controls should expose:

- what file is requested;
- accepted types when relevant;
- size constraints;
- upload status;
- validation errors.

Prefer native file input behavior when possible.

If a custom drop zone is used, provide an equivalent non-drag mechanism.

Do not require users to drag files as the only upload method.

---

# Drag-and-drop form interactions

If form completion requires dragging, consider applicable pointer accessibility
requirements.

Provide an alternative when required.

Keyboard users should not be blocked from completing the same task.

---

# Select controls

Prefer native:

```html
<select></select>
```

when it satisfies the interaction requirements.

Native select elements provide established semantics and keyboard behavior.

Custom selects, comboboxes, and listboxes require careful implementation.

See:

- `aria.md`
- `keyboard.md`
- `focus.md`

---

# Textarea

Use `<textarea>` for multi-line text input.

Do not replace it with generic contenteditable elements without a justified
need.

Native textarea semantics and behavior are broadly supported.

---

# Contenteditable

`contenteditable` can introduce complex accessibility behavior.

If used as a form control, verify:

- appropriate role;
- accessible name;
- keyboard behavior;
- current value exposure;
- validation;
- focus behavior.

Prefer native text inputs when they satisfy the requirement.

---

# Form submission

Form submission should normally use native form mechanisms when possible.

Example:

```html
<form>
  ...
  <button type="submit">Save</button>
</form>
```

Native submit behavior supports:

- Enter submission in appropriate contexts;
- form validation;
- predictable browser behavior.

Do not replace submit behavior with click-only JavaScript unnecessarily.

---

# Multiple submit buttons

A form may legitimately contain multiple submit buttons.

Example:

```html
<button type="submit" name="action" value="save">Save</button>

<button type="submit" name="action" value="save-and-close">
  Save and close
</button>
```

The `value` can help distinguish which submit control initiated submission when
used with `name`.

Do not report multiple submit buttons as an accessibility issue by itself.

Ensure each action has a clear accessible name.

---

# Buttons inside forms

Remember that:

```html
<button></button>
```

inside a form defaults to submit behavior unless another type is specified.

Use:

```html
<button type="button"></button>
```

for non-submit actions.

Accidental submission may be a functional problem and can create confusing form
behavior.

Evaluate whether it creates an accessibility barrier in context rather than
mechanically labeling it a WCAG failure.

---

# Enter key behavior in forms

Native form controls may submit the form when Enter is pressed depending on
context.

Do not add custom key handlers solely to recreate native form submission.

See:

`keyboard.md`

---

# Focus and form controls

Do not manually add:

```html
tabindex="0"
```

to ordinary native form controls.

They are already focusable.

Use custom tabindex only when implementing a justified focus pattern.

See:

`focus.md`

---

# Label click behavior

Native `<label>` association allows users to activate or focus the corresponding
control by interacting with the label.

This is particularly useful for:

- checkboxes;
- radios;
- small form targets.

Do not replace proper label association with visually adjacent text only.

---

# Target size

Form controls and associated labels should provide usable pointer targets.

Labels associated with checkbox and radio controls can increase the effective
clickable area.

Target-size requirements may be covered more fully in a future visual/pointer
reference.

Do not infer target-size failures from markup alone when rendered dimensions are
unknown.

---

# Form layout and reading order

Visual form layout should preserve a logical reading and focus order.

Be cautious with:

- multi-column forms;
- responsive reordering;
- CSS Grid placement.

Do not assume a two-column form is inaccessible.

Verify that DOM order remains understandable.

---

# Error text placement

Place error text where users can associate it visually with the relevant field.

Programmatic relationships should reinforce, not replace, understandable visual
placement.

Do not place all field errors far from their controls without an effective
navigation or association mechanism.

---

# Success messages

After successful form submission, users may need clear confirmation.

Depending on the workflow, this may involve:

- navigation to a confirmation page;
- status message;
- heading focus;
- live-region announcement.

Do not automatically move focus to every success message.

See:

- `focus.md`
- `dynamic-content.md`

---

# Loading and submission state

During asynchronous submission, communicate relevant state when necessary.

Possible approaches include:

- disabled submit control;
- visible loading text;
- `aria-busy`;
- status announcement.

Do not rely solely on a spinner with no accessible meaning.

See:

`dynamic-content.md`

---

# Repeated submission prevention

If submission is temporarily blocked to prevent duplicates, ensure the state is
understandable.

For example:

```text
Submitting…
```

can be clearer than silently disabling the button.

Do not create a state where users cannot determine whether their action was
accepted.

---

# Form reset

Reset actions can cause substantial data loss.

If a reset control exists, ensure its purpose is clear.

Avoid accidental placement or ambiguous labels such as:

```text
Clear
```

when:

```text
Clear all form fields
```

would better communicate the consequence.

Do not automatically require confirmation for every reset action, but evaluate
risk.

---

# Autofill

Do not design forms in ways that unnecessarily prevent browser autofill.

Autofill can significantly improve accessibility for users with:

- motor disabilities;
- cognitive disabilities;
- memory difficulties.

Use appropriate field semantics and `autocomplete`.

---

# Autocorrect and input behavior

Attributes such as:

```html
autocapitalize autocorrect spellcheck
```

may affect user input.

Use them according to the data being collected.

For identifiers, codes, email addresses, and similar values, unwanted
autocorrection may create errors.

Do not treat these attributes as accessibility requirements by default, but
consider usability impact.

---

# Error persistence

Errors should remain available long enough for users to understand and correct
them.

Avoid transient error messages that disappear automatically before users can
read them.

Do not require users to reproduce the error simply to read the message again.

---

# Accessible names should remain stable

Avoid changing a control's accessible name unnecessarily during interaction.

For example, a submit button changing from:

```text
Submit
```

to:

```text
Loading
```

may be valid, but ensure the resulting control remains understandable.

When state is temporary, sometimes preserving the action name while exposing
busy state separately is clearer.

Evaluate context.

---

# Language

Labels, instructions, and error messages should use the appropriate document or
element language.

If part of a form uses a different language, use appropriate language metadata
where necessary.

---

# Custom validation components

Design-system components may implement:

- labels;
- descriptions;
- invalid states;
- error relationships;
- required semantics;

internally.

Do not duplicate ARIA externally without checking the component API.

Example:

```html
<eb-input-text
  label="Email"
  error-message="Enter a valid email"
  [invalid]="true"
>
</eb-input-text>
```

cannot be judged from host attributes alone.

Inspect rendered behavior or documentation.

---

# Custom component error APIs

Prefer the design system's supported error and description APIs when they expose
correct semantics.

Do not bypass them with unrelated DOM manipulation unless necessary.

If the component does not expose the required accessibility relationship, that
may be a design-system issue rather than something every application should
patch individually.

---

# Readonly display versus form control

Sometimes an interface displays data in a form-like layout without allowing
editing.

Do not automatically use disabled input controls simply to preserve the visual
design.

If the information is static content, semantic text may be more appropriate.

Choose semantics according to user interaction.

---

# Review decision process

When reviewing a form, follow this reasoning:

1. Determine the purpose of each control.
2. Verify that an appropriate native control is used when possible.
3. Determine the accessible name.
4. Verify visible labeling where appropriate.
5. Check grouping and shared instructions.
6. Determine required/optional state.
7. Inspect input type and autocomplete where relevant.
8. Review validation timing.
9. Inspect error identification.
10. Verify programmatic error relationships.
11. Check focus behavior after validation.
12. Check dynamic error announcements.
13. Inspect disabled and readonly behavior.
14. Review conditional fields and multi-step interactions.
15. Inspect custom component APIs before adding external ARIA.
16. Determine whether runtime verification is required.
17. Report only confirmed or credible accessibility barriers.

---

# Common review mistakes

## Mistake: treating placeholder as a label

Wrong:

```html
<input placeholder="Email" />
```

with no accessible label.

Correct approach:

Provide a persistent accessible name.

---

## Mistake: adding aria-label to every field

Wrong reasoning:

> Every input needs `aria-label`.

Correct approach:

Prefer native labels when available.

---

## Mistake: marking required only with color

Required state must not depend solely on color perception.

---

## Mistake: adding role="alert" to every error

Too many alerts can create noisy or duplicate announcements.

Determine whether an alert is actually necessary.

---

## Mistake: disabling submit without explaining why

Users need a predictable way to understand incomplete or invalid input.

---

## Mistake: treating readonly and disabled as equivalent

Readonly values may remain focusable and usable for copying.

Disabled controls represent unavailable interaction.

---

## Mistake: requiring fieldset for every form layout

Use fieldset when controls need a meaningful shared group label.

---

## Mistake: judging custom fields from host markup alone

Design-system components may provide internal labeling and validation semantics.

Inspect or verify before reporting.

---

## Mistake: replacing native form behavior with JavaScript unnecessarily

Native form submission, labeling, and validation behavior should be preserved
when suitable.

---

# Related references

Use these references for adjacent topics:

- `semantics.md` — native form elements and structural semantics.
- `aria.md` — accessible names, descriptions, required and invalid states.
- `keyboard.md` — keyboard operation and form submission behavior.
- `focus.md` — validation focus, error summaries, and dynamic focus changes.
- `dynamic-content.md` — error announcements, submission status, and async updates.

---

# Final principle

Accessible forms should make it clear what information is required, how to
provide it, what went wrong, and how to recover.

When reviewing forms:

**prefer native form controls**

**provide persistent and understandable labels**

**do not use placeholder as the only label**

**group related controls when shared context matters**

**communicate required state clearly**

**associate errors with the controls they describe**

**do not overuse live alerts**

**preserve predictable native submission behavior**

**inspect design-system form semantics before duplicating them**

**verify runtime validation and focus behavior instead of guessing**
