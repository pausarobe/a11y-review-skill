# Dynamic Content Accessibility Reference

Use this reference when reviewing or implementing dynamic content that changes
without a full page reload.

The objective is to ensure that users, including screen reader users, are made
aware of important changes without creating unnecessary interruptions,
duplicate announcements, or disruptive focus movement.

Dynamic content does not automatically require a live region.

First determine:

- whether the change is important;
- whether the user already receives the information through focus;
- whether the update is expected;
- whether the update is urgent;
- whether another accessibility mechanism already communicates it.

Prefer the least disruptive solution.

---

# Dynamic content includes more than live regions

Examples of dynamic content include:

- validation messages;
- loading indicators;
- search results;
- filtering results;
- notifications;
- toast messages;
- status updates;
- progress updates;
- async operation results;
- SPA route changes;
- expanded or collapsed content;
- content inserted after user interaction;
- background updates.

Do not assume all of these need `aria-live`.

---

# Determine whether an announcement is necessary

Before adding a live region, ask:

1. Does the user need to know that something changed?
2. Is the changed content already the current focus target?
3. Is the change obvious from the user's current interaction?
4. Does the update affect task completion?
5. Is the information urgent?
6. Would an announcement duplicate information already exposed elsewhere?

Only add an announcement when it improves access.

---

# Focus versus announcement

Focus movement and live-region announcements solve different problems.

Use focus when the user's interaction context has changed and they need to
continue from a new location.

Use a live region when information changes but focus should remain where it is.

For example:

- opening a modal usually requires focus movement;
- "3 search results found" usually does not;
- a background save confirmation usually does not;
- a new wizard step may require focus movement.

Do not use both focus movement and a live-region announcement automatically.

This can cause duplicate or confusing output.

See:

`focus.md`

---

# aria-live

`aria-live` identifies a region whose updates may be announced by assistive
technologies.

Common values include:

```html
aria-live="polite"
```

and:

```html
aria-live="assertive"
```

Use `polite` for non-urgent updates.

Use `assertive` only for information requiring immediate attention.

Do not use `assertive` by default.

---

# aria-live="polite"

Use polite announcements for information that is important but does not need to
interrupt the user's current activity.

Examples may include:

- search result count;
- successful save;
- item added;
- filtering complete;
- background operation finished.

Example:

```html
<div aria-live="polite" aria-atomic="true">5 results found.</div>
```

Do not use a polite live region for every text change on the page.

---

# aria-live="assertive"

Use assertive announcements sparingly.

They may interrupt current screen reader output.

Possible use cases include:

- critical errors;
- urgent session expiration warnings;
- important blocking failures.

Do not use assertive announcements for routine feedback such as:

- saved successfully;
- filters updated;
- item selected.

Prefer `polite` when interruption is unnecessary.

---

# role="status"

`role="status"` is appropriate for important but non-urgent status messages.

It generally provides polite live-region behavior.

Example:

```html
<div role="status">Profile saved successfully.</div>
```

Use it for status information that users should know while keeping focus in the
current task.

Do not use it for every piece of dynamic text.

---

# role="alert"

`role="alert"` is appropriate for urgent information requiring immediate
attention.

Example:

```html
<div role="alert">Payment could not be processed.</div>
```

Do not use `role="alert"` merely because content contains an error.

Some validation errors may be better exposed through:

- `aria-describedby`;
- an error summary;
- focus movement;
- a polite announcement.

Overusing alerts can make interfaces noisy and disruptive.

---

# status versus alert

Use this distinction:

```text
Important but not urgent
        ↓
      status

Immediate attention required
        ↓
       alert
```

Do not choose based on visual styling.

A red message is not automatically an alert.

---

# Pre-existing live regions

Live regions generally work most reliably when the live-region container exists
before the content update occurs.

A common pattern is:

```html
<div aria-live="polite" aria-atomic="true"></div>
```

and later:

```html
<div aria-live="polite" aria-atomic="true">Saved successfully.</div>
```

Be cautious when dynamically inserting both:

- the live-region element;
- and its populated message;

at the same time.

Some browser and assistive technology combinations may not announce such
updates consistently.

When reliable announcement is important, prefer keeping the live-region
container present and updating its content.

---

# Do not add arbitrary delays by default

Do not automatically recommend:

```javascript
setTimeout(...)
```

to make live regions work.

Announcement timing can depend on:

- DOM insertion;
- browser;
- assistive technology;
- rendering;
- framework lifecycle.

Prefer a stable live-region architecture before adding timing hacks.

If timing remains uncertain, require runtime testing.

---

# aria-atomic

`aria-atomic` controls whether assistive technologies should present the whole
region or only changed content.

Example:

```html
<div aria-live="polite" aria-atomic="true">5 results found.</div>
```

Use:

```html
aria-atomic="true"
```

when the complete message provides necessary context.

For example, if only the number changes:

```text
5 results found
```

announcing only:

```text
5
```

may be insufficient.

Do not add `aria-atomic="true"` mechanically to every live region.

---

# aria-relevant

`aria-relevant` can indicate which types of changes are relevant to a live
region.

Possible values include concepts such as:

- additions;
- removals;
- text.

Do not configure `aria-relevant` unless the default behavior is insufficient
for the interaction.

Overconfiguration can make live-region behavior harder to predict.

---

# aria-busy

Use:

```html
aria-busy="true"
```

to indicate that a region is currently being updated when this information is
useful to assistive technologies.

Example:

```html
<section aria-busy="true">...</section>
```

After loading:

```html
<section aria-busy="false">...</section>
```

Do not assume `aria-busy` automatically announces:

> Loading

or:

> Finished loading

It communicates state, not necessarily a user-facing status message.

Use visible or live-region feedback where users need explicit information.

---

# Loading states

Loading indicators should communicate meaningful state.

Avoid relying only on:

```html
<spinner></spinner>
```

with no accessible meaning.

Possible approaches include:

- visible "Loading…" text;
- `role="status"`;
- `aria-busy`;
- disabled controls where appropriate.

The right choice depends on the interaction.

---

# Do not announce every loading state

Very short or trivial loading operations may not require announcement.

Repeated announcements such as:

```text
Loading
Loaded
Loading
Loaded
```

can become distracting.

Consider:

- expected duration;
- task importance;
- whether the UI becomes temporarily unavailable;
- whether the user needs confirmation that processing started.

---

# Loading and focus

Do not move focus to a loading indicator by default.

Usually, focus should remain on the user's current control while loading occurs.

Move focus only when the interaction context genuinely changes.

See:

`focus.md`

---

# Search results

When filtering or searching updates results dynamically, users may need to know
that results changed.

A concise status message may be appropriate:

```html
<div role="status" aria-atomic="true">12 results found.</div>
```

Do not announce the entire result list after every keystroke.

That can create excessive output.

---

# Search-as-you-type

For search-as-you-type interfaces, avoid overly frequent announcements.

Potential problems include announcing:

```text
18 results
17 results
14 results
9 results
```

while the user is still typing.

Consider:

- debounce behavior;
- announcing only after results stabilize;
- announcing only meaningful changes;
- whether users actually need the result count during typing.

Do not introduce arbitrary debounce values solely for accessibility without
considering the existing interaction.

---

# Filtering

When filters update content dynamically, users may need confirmation that the
result set changed.

A status message such as:

```text
8 products shown.
```

may be useful.

Do not move focus to the updated results automatically if the user is still
interacting with filter controls.

Preserving focus usually allows continued filtering.

---

# Sorting

Sorting a table or list may require feedback.

Possible methods include:

- updating the sortable control's accessible state;
- visible text indicating sort direction;
- a status message when necessary.

Do not automatically announce the entire reordered content.

The sort control itself may already expose enough information.

---

# Pagination

When pagination updates content without a full navigation, users need to
understand that the content has changed.

Possible approaches include:

- focus management;
- status announcements;
- changing a heading;
- route navigation semantics.

The correct solution depends on whether pagination behaves like navigation or a
dynamic update.

Do not apply a live region automatically.

---

# Infinite scrolling

Infinite scrolling can create accessibility challenges.

Consider:

- whether users know more content has loaded;
- whether keyboard position remains stable;
- whether content loading unexpectedly shifts focus;
- whether users can reach content after the continuously expanding region;
- whether an alternative pagination mechanism is needed.

A live status such as:

```text
20 more results loaded.
```

may be useful.

Do not announce every individual item.

---

# Notifications

Notifications may include:

- success messages;
- warnings;
- errors;
- informational updates.

Choose announcement behavior based on importance, not visual component type.

A notification component is not automatically:

```html
role="alert"
```

---

# Toast notifications

Toast notifications generally should not receive focus automatically.

They may use a live region when the information is important.

Example:

```html
<div role="status">Changes saved.</div>
```

Be cautious with toasts that:

- disappear too quickly;
- contain essential information;
- contain interactive controls;
- use `role="alert"` unnecessarily.

If users need to interact with the notification, ensure the actions are
reachable predictably.

---

# Toast duration

Do not assume a fixed timeout is accessible.

If a toast contains information users need to read or act on, it should remain
available long enough.

Critical information should not disappear before users can perceive it.

Do not use live-region announcements as justification for removing important
visible content too quickly.

---

# Interactive notifications

If a notification contains actions such as:

```text
Undo
View
Retry
```

do not automatically move focus into it.

Consider whether users can reach the action through normal keyboard navigation.

If the action is time-sensitive, the interaction may need additional design
consideration.

---

# Form validation messages

Dynamic form errors may need announcement.

However, do not add:

```html
role="alert"
```

to every field error.

First determine:

- whether focus remains on the invalid field;
- whether the error is referenced through `aria-describedby`;
- whether the control exposes `aria-invalid`;
- whether an error summary is announced;
- whether the message appeared after a user action.

See:

`forms.md`

---

# Inline validation

Inline validation while typing can create excessive announcements.

Avoid announcing every partial invalid state.

For example, while typing an email:

```text
Invalid
Invalid
Invalid
Invalid
Valid
```

would be disruptive.

Validate at a meaningful interaction point such as:

- blur;
- submit;
- completed input;

depending on the form design.

---

# Error summaries

A form error summary may use:

- programmatic focus;
- a live-region announcement;
- both in carefully designed cases.

Do not automatically combine:

```text
focus move + role=alert + individual alerts
```

because this may cause repeated announcements.

Choose one coherent strategy.

---

# Successful submission

After a successful form submission, users should receive clear confirmation.

Possible patterns include:

- navigating to a confirmation page;
- moving focus to a confirmation heading;
- announcing a status message;
- updating existing content.

Do not automatically use a live region if navigation or focus already provides
clear context.

---

# Background save

Autosave or background-save functionality often benefits from concise status
feedback.

Example:

```html
<div role="status">Saved.</div>
```

Avoid repeatedly announcing:

```text
Saving
Saved
Saving
Saved
```

for every minor change unless users genuinely need that feedback.

---

# Progress updates

Progress updates may need to be communicated when users are waiting for a
long-running operation.

Examples include:

- file upload;
- data processing;
- installation;
- report generation.

Use appropriate native or ARIA semantics.

For determinate progress, native:

```html
<progress value="60" max="100">60%</progress>
```

may be appropriate.

Do not create a live-region announcement for every percentage change.

---

# Progress announcement frequency

For long-running operations, announcing:

```text
1%
2%
3%
4%
...
```

can be excessive.

Consider announcing significant milestones such as:

```text
25%
50%
75%
Complete
```

or only status transitions.

The correct frequency depends on the operation.

---

# Async button actions

When a button starts an async action, users may need to know that the action was
accepted.

Possible approaches include:

- changing visible button text;
- disabling the button;
- exposing busy state;
- adding status text.

Example:

```html
<button type="submit" disabled>Saving…</button>
```

with an appropriate visible status may be sufficient.

Do not automatically add a live region if the button's state already clearly
communicates progress.

---

# Avoid ambiguous spinner-only states

A visual spinner alone may not communicate:

- what is loading;
- whether the action succeeded;
- whether users should wait;
- whether interaction is blocked.

Pair visual indicators with meaningful text or semantics when necessary.

---

# Button accessible names during loading

Be cautious when changing:

```text
Save
```

to:

```text
Loading
```

because the control may lose its action context.

Sometimes:

```text
Saving…
```

provides a clearer continuation of the original action.

Do not enforce a single naming pattern.

Evaluate whether the resulting name remains understandable.

---

# SPA route changes

Single-page applications can change the primary view without a full browser
navigation.

Screen reader and keyboard users may not automatically know that the page
context changed.

Possible strategies include:

- updating `<title>`;
- moving focus to the new page heading;
- focusing the main content;
- announcing the route change.

Do not automatically use all strategies at once.

Choose a coherent approach.

---

# Document title

When SPA navigation represents a new page or view, update the document title
appropriately.

A meaningful title helps users identify the current context.

Do not rely solely on a live-region announcement while leaving an outdated page
title.

---

# Route change announcements

If using a route-change live region, keep announcements concise.

Example:

```text
Account details page
```

may be sufficient.

Avoid reading the entire new page automatically.

In many cases, moving focus to the new page heading is more useful.

See:

`focus.md`

---

# Dynamic headings

When content changes significantly, updating a heading may provide context.

However, changing heading text alone does not necessarily cause an announcement.

If users need immediate awareness of the update, consider whether focus or a
status message is appropriate.

---

# Expand and collapse

Expanding content does not normally require a live-region announcement if the
trigger already exposes:

```html
aria-expanded
```

and focus remains on the trigger.

Example:

```html
<button aria-expanded="true" aria-controls="details">Hide details</button>
```

The changed state may already communicate the interaction.

Do not add a live region merely because content appeared.

---

# Accordion panels

For accordions, state changes are usually communicated through:

- button semantics;
- `aria-expanded`;
- controlled panel relationship.

Do not announce the entire panel contents automatically after expansion.

Users can navigate into the revealed content normally.

---

# Tabs

Switching tabs does not normally require a live-region announcement if:

- tab semantics are correct;
- `aria-selected` updates;
- focus remains on the active tab;
- the corresponding panel is exposed appropriately.

Do not announce the entire tabpanel automatically.

See:

- `aria.md`
- `keyboard.md`
- `focus.md`

---

# Dialogs

Opening a dialog usually requires focus movement into the dialog.

Because focus provides the context change, an additional live-region
announcement is often unnecessary.

Do not automatically combine:

```text
role=dialog
+
focus movement
+
role=alert
```

unless there is a specific reason.

---

# Menus and popovers

Temporary interface layers usually communicate their presence through:

- focus movement;
- expanded state;
- appropriate semantics.

Do not automatically add live regions when a menu or popover opens.

---

# Dynamic counters

Counters such as:

```text
Cart: 3 items
Unread messages: 5
```

may need announcement when they change as a direct result of user action.

Consider whether users need immediate confirmation.

Example:

```text
Item added. Cart now contains 3 items.
```

may be more useful than announcing only:

```text
3
```

Use atomic messages where context matters.

---

# Character counters

Character counters may update frequently while users type.

Do not announce every character count change.

Possible approaches include announcing only:

- when approaching a limit;
- when the limit is reached;
- at meaningful thresholds.

Example:

```text
10 characters remaining.
```

may be useful.

Avoid excessive live output.

---

# Session timeout warnings

Session-expiration warnings are important dynamic content.

Users should receive enough notice and an accessible way to extend the session
when applicable.

An assertive announcement may be justified if expiration is imminent.

Do not rely solely on a visual countdown.

---

# Countdown timers

Do not announce every second of a countdown.

That would overwhelm screen reader output.

Announce meaningful milestones when necessary.

For example:

```text
2 minutes remaining.
30 seconds remaining.
```

depending on the task.

---

# Content removed dynamically

When content is removed, determine whether users need confirmation.

For example, after deleting an item:

```text
Transaction removed.
```

may be useful.

If the deleted item held focus, focus restoration is also required.

See:

`focus.md`

---

# Content added dynamically

When adding content such as:

- new messages;
- comments;
- rows;
- list items;

determine whether users need immediate notification.

Do not announce every addition automatically.

Background updates may be intentionally non-interruptive.

---

# Real-time feeds

Frequently updating interfaces require special care.

Examples include:

- chat;
- stock data;
- sports scores;
- monitoring dashboards.

Avoid live regions that announce every update indiscriminately.

Consider:

- update frequency;
- user control;
- paused announcements;
- relevance;
- explicit notification preferences.

---

# Chat interfaces

Chat applications may need to announce newly received messages.

Be careful not to announce:

- messages the user is currently composing;
- large message histories;
- duplicate content;
- updates when the user is reviewing older messages.

Announcements should preserve user context.

---

# Streaming content

For streamed or progressively generated content, do not assume every token or
small text update should be live-announced.

Frequent partial updates can create unusable screen reader output.

Consider:

- chunked announcements;
- sentence-level updates;
- completion announcements;
- user-controlled reading.

This requires runtime testing.

---

# Background updates

Not every background update should interrupt the user.

Examples include:

- timestamps;
- analytics counters;
- decorative status changes;
- non-essential dashboard refreshes.

Leave these silent unless users need the information to complete their task.

---

# Duplicate announcements

Duplicate announcements are a common accessibility problem.

They may occur when the same update is communicated through multiple mechanisms.

For example:

```text
focus moved to message
+
role="alert"
+
aria-live region
```

may result in repeated output.

Before adding an announcement, identify every existing semantic channel.

Prefer one clear source of truth.

---

# Nested live regions

Avoid unnecessarily nesting live regions.

For example:

```html
<div aria-live="polite">
  <div role="status">Saved.</div>
</div>
```

may create redundant or unpredictable behavior.

Prefer one appropriate live-region mechanism.

---

# Multiple simultaneous live regions

Interfaces with many independent live regions can become noisy.

Review whether each region genuinely needs to announce updates.

Consolidate status messages where practical.

Do not create separate live regions for every small component by default.

---

# Repeating the same message

Some assistive technology combinations may not announce identical text when the
same message is set repeatedly.

For example:

```text
Copied
```

followed later by:

```text
Copied
```

may not always be re-announced depending on implementation.

If repeated identical announcements are essential, runtime behavior may need
special handling.

Do not introduce workarounds without testing.

---

# Clearing live regions

Some implementations clear a live region after an announcement.

This can be valid.

However, avoid clearing content so quickly that it affects announcement
reliability.

Prefer simple stable behavior and test with supported assistive technologies.

---

# Visually hidden live regions

A live region may be visually hidden while remaining available to assistive
technologies.

Use an established visually-hidden utility.

Do not use:

```css
display: none;
```

or:

```css
visibility: hidden;
```

for content that must remain exposed to assistive technologies.

See:

`semantics.md`

---

# Live regions must remain in the accessibility tree

Do not place important live-region content inside:

```html
aria-hidden="true"
```

or another mechanism that removes it from the accessibility tree.

Verify the actual rendered state.

---

# Hidden parent containers

A live region inside a hidden ancestor may not be exposed.

For example:

```html
<div hidden>
  <div role="status">Saved.</div>
</div>
```

cannot be expected to announce while hidden.

Evaluate the complete ancestor chain.

---

# Framework conditional rendering

Frameworks may create and destroy live-region containers conditionally.

Examples include:

```html
@if (message) {
<div role="status">{{ message }}</div>
}
```

or equivalent React/Vue patterns.

Do not assume this is always reliable for announcement.

If consistent live announcements are important, a persistent empty region whose
content changes may be more robust.

Runtime testing is recommended.

---

# Angular and live regions

Angular change detection can update live-region content asynchronously.

Do not add arbitrary Angular-specific delays unless actual announcement problems
have been observed.

Prefer:

- stable live-region containers;
- predictable state changes;
- clear message updates.

If timing depends on rendering order, verify at runtime.

---

# React and live regions

React may mount and update live-region elements according to component state.

Avoid relying on a live region that only exists at the exact moment the message
is inserted if reliable announcement is required.

A persistent component that updates its text can be easier to reason about.

---

# Design-system notification components

Custom components may already implement:

- `role="status"`;
- `role="alert"`;
- `aria-live`;
- focus behavior.

For example:

```html
<eb-notification type="success"> Changes saved. </eb-notification>
```

cannot be judged from its host markup alone.

Inspect:

- component documentation;
- rendered DOM;
- accessibility tree;
- runtime announcement.

Do not add external live-region semantics blindly.

---

# Do not duplicate design-system announcements

If a design-system component already announces itself, wrapping it in another:

```html
<div aria-live="polite"></div>
```

may create duplicate output.

Understand the component's accessibility contract first.

---

# Custom event names do not indicate announcement behavior

Events such as:

```text
success
loaded
updated
validationChange
```

do not reveal whether assistive technologies are informed.

Review the rendered UI and semantics.

---

# Visual changes and announcements

A visual state change does not automatically require screen reader announcement.

Examples include:

- icon changes;
- background color changes;
- animation completion;
- decorative transitions.

Only announce changes users need to know.

---

# Screen-reader testing

Live-region behavior can vary between:

- NVDA;
- JAWS;
- VoiceOver;
- TalkBack;
- browsers;
- operating systems.

When a critical workflow depends on dynamic announcements, runtime testing is
important.

Do not make absolute claims about exact spoken output from source code alone.

---

# Do not test only one screen reader when risk is high

For critical interactions, consider testing relevant browser/AT combinations
supported by the product.

However, do not require exhaustive combinations for every minor update.

Prioritize based on risk.

---

# Accessibility tree inspection

Developer tools can help inspect whether:

- live-region roles exist;
- regions are hidden;
- names and states are exposed.

However, accessibility-tree inspection does not fully prove announcement timing.

Use it together with runtime testing.

---

# Announcements should be concise

Live-region messages should generally be short and meaningful.

Prefer:

```text
5 results found.
```

over:

```text
The search operation has successfully completed and the application has
returned a total number of five matching search results.
```

Avoid verbose repetitive announcements.

---

# Announce outcomes, not implementation details

Prefer user-facing information.

Good:

```text
Payment failed. Try another card.
```

Avoid:

```text
HTTP request returned status code 402.
```

unless the technical detail is meaningful to the intended user.

---

# Avoid redundant prefixes

If every message begins with:

```text
Notification:
```

or:

```text
Status:
```

consider whether that repetition adds value.

Assistive technology may already communicate the role.

Keep messages focused on the information itself.

---

# Dynamic state and accessible names

Sometimes dynamic state is better communicated through an existing control
rather than a live region.

For example:

```html
<button aria-expanded="true">Filters</button>
```

already exposes expanded state.

Do not announce:

```text
Filters expanded
```

separately unless there is a specific need.

---

# Toggle buttons

A toggle button may expose its state with:

```html
aria-pressed
```

rather than announcing a separate status message.

Example:

```html
<button aria-pressed="true">Bold</button>
```

Do not duplicate state through a live region by default.

---

# Selected and current states

Changes to:

```text
aria-selected
aria-current
aria-checked
```

may already be exposed by assistive technologies.

Do not add separate live-region announcements for every state change without
testing whether additional feedback is needed.

---

# State synchronization

When dynamic UI changes, ARIA state must remain synchronized.

Examples include:

- `aria-expanded`;
- `aria-selected`;
- `aria-checked`;
- `aria-current`;
- `aria-busy`.

Do not treat live-region announcements as a substitute for correct state
semantics.

See:

`aria.md`

---

# Avoid announcing hidden implementation text

Do not populate live regions with internal debugging or implementation state.

For example:

```text
loading=true
```

is not meaningful user feedback.

Use user-facing language.

---

# Localization

Dynamic announcements must be localized consistently with the interface.

Do not hard-code announcement text in a different language from the visible UI.

Assistive technologies rely on correct language metadata for pronunciation.

---

# Dynamic language changes

If a live-region message uses a different language from the page, appropriate
language semantics may be necessary.

Example:

```html
<span lang="en"> Payment successful. </span>
```

when embedded in another language context.

---

# User control over frequent updates

For very active interfaces, users may benefit from controls to:

- pause updates;
- mute announcements;
- filter updates;
- choose notification priority.

This is particularly relevant for:

- feeds;
- dashboards;
- chats;
- monitoring tools.

Do not treat every real-time interface as requiring constant announcements.

---

# Motion and dynamic content

Animation may accompany dynamic updates.

Do not rely on motion alone to communicate that state changed.

Users who reduce motion should still receive equivalent state information.

---

# Error recovery

Dynamic failure messages should tell users:

- what happened;
- whether their action completed;
- what they can do next.

Prefer:

```text
Transfer could not be completed. Try again.
```

over:

```text
Error.
```

when more information is available.

---

# Retry states

If a failed async action can be retried, ensure:

- retry control is accessible;
- status is understandable;
- repeated errors are not announced excessively;
- focus remains predictable.

---

# Offline states

Applications that detect connectivity changes may need to inform users.

Examples:

```text
You are offline.
Connection restored.
```

These may be suitable status messages.

Do not repeatedly announce every connectivity fluctuation if it creates noise.

---

# Autosuggest and autocomplete updates

Custom autocomplete interfaces may dynamically update suggestions.

Do not use a general live region to read every option as it appears.

Use the appropriate combobox/listbox pattern.

Relevant state may be exposed through:

- `aria-expanded`;
- `aria-activedescendant`;
- option semantics.

See:

`aria.md`

and:

`keyboard.md`

---

# Result count for autocomplete

A concise result count may sometimes be useful:

```text
4 suggestions available.
```

Avoid repeating it on every minor update unless necessary.

---

# Carousel updates

Automatic carousels can change content dynamically.

Users need control over movement where applicable.

Do not automatically announce every slide change, especially when changes are
automatic.

This can interrupt unrelated reading.

If slide changes are user-triggered, existing control state and focus may be
sufficient.

---

# Timer updates

Do not place continuously changing timers in assertive live regions.

Frequent time updates can overwhelm users.

Announce only meaningful thresholds when necessary.

---

# Stock prices and rapidly changing data

Rapidly changing values should not all be automatically announced.

Users should be able to decide what information is important.

Use live regions carefully in data-heavy dashboards.

---

# Accessibility announcements are not logs

Do not use a live region as an application event log.

Announcements should support the user's current task, not expose every internal
state transition.

---

# Review decision process

When reviewing dynamic content, follow this reasoning:

1. Identify what changes dynamically.
2. Determine what triggers the change.
3. Determine whether users need to know about it.
4. Check whether focus already communicates the change.
5. Check whether semantic state already communicates the change.
6. Determine urgency.
7. Choose between no announcement, polite status, alert, or focus movement.
8. Check for duplicate announcement mechanisms.
9. Verify the live-region container exists at the right time.
10. Review `aria-atomic`, `aria-busy`, and related states only when needed.
11. Check update frequency.
12. Check whether messages are concise and meaningful.
13. Inspect custom component behavior.
14. Determine whether runtime screen-reader verification is required.
15. Report only confirmed or credible barriers.

---

# Common review mistakes

## Mistake: adding aria-live to every dynamic element

Wrong reasoning:

> Content changes, therefore it needs `aria-live`.

Correct approach:

First determine whether users need an announcement.

---

## Mistake: using role="alert" for every error

Alerts are interruptive.

Use them only when urgency justifies interruption.

---

## Mistake: combining focus movement and live announcements automatically

This can cause duplicate output.

Choose the mechanism that best communicates the context change.

---

## Mistake: announcing expanded content

A disclosure may already expose its state through `aria-expanded`.

Do not announce the entire revealed content automatically.

---

## Mistake: announcing every search result change

Frequent updates can overwhelm screen reader users.

Prefer concise, meaningful status messages.

---

## Mistake: assuming aria-busy says "Loading"

`aria-busy` exposes state.

It does not automatically provide a useful visible or spoken message.

---

## Mistake: dynamically creating a live region and expecting guaranteed output

Announcement behavior can depend on timing and browser/AT combinations.

Persistent regions are often more reliable.

---

## Mistake: adding arbitrary delays immediately

First fix the live-region architecture.

Use timing workarounds only when tested and necessary.

---

## Mistake: wrapping an accessible design-system notification in another live region

This may produce duplicate announcements.

Inspect the component first.

---

## Mistake: using live regions instead of semantic state

Prefer:

```html
aria-expanded aria-selected aria-checked aria-current
```

when those states accurately represent the interaction.

---

## Mistake: announcing implementation details

Messages should describe outcomes users understand.

---

# Related references

Use these references for adjacent topics:

- `semantics.md` — semantic structure and native elements.
- `aria.md` — live-region roles, states, and ARIA properties.
- `keyboard.md` — keyboard interaction with dynamically changing widgets.
- `focus.md` — focus movement after context changes.
- `forms.md` — validation messages, errors, and submission feedback.

---

# Final principle

Dynamic accessibility is about communicating meaningful change without taking
control away from the user.

When reviewing dynamic content:

**do not announce every change**

**use focus for context changes and live regions for non-focus updates**

**prefer polite announcements unless urgency requires interruption**

**avoid duplicate announcements**

**keep messages concise and user-facing**

**use semantic state before adding separate announcements**

**avoid excessive updates**

**inspect design-system behavior before adding external live regions**

**verify critical screen-reader behavior at runtime instead of guessing**
