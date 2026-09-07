---
name: a11y-review
description: >
  Review frontend code for accessibility issues using WCAG 2.2 Level AA, native HTML semantics, WAI-ARIA, keyboard interaction, focus management, accessible names, forms, and dynamic content. Use when reviewing or fixing accessibility issues in HTML, CSS, JavaScript, TypeScript, Angular, React, Vue, web components, or other web frontend technologies.
---

**Version:** 0.1.0  
**Target standard:** WCAG 2.2 Level AA

---

# 🔹 Accessibility Review

Systematically review frontend code for accessibility issues.

The goal is not to produce a generic accessibility checklist. The goal is to identify real accessibility barriers, explain why they are problems, relate them to the appropriate accessibility requirement when possible, and recommend the smallest appropriate fix.

Prioritize correctness over the number of findings.

Do not invent accessibility issues to make the review appear comprehensive.

---

# 🔹 Scope

This skill currently focuses on web frontend accessibility.

Supported areas in v0.1:

- HTML semantics
- interactive elements
- accessible names and descriptions
- WAI-ARIA roles, states, and properties
- keyboard interaction
- focus management
- forms and validation
- dynamic content and announcements
- common accessibility issues in frontend frameworks
- custom elements and web components

Supported technologies include:

- HTML
- CSS
- JavaScript
- TypeScript
- Angular
- React
- Vue
- web components
- other browser-based frontend technologies

The primary conformance target is: **WCAG 2.2 Level AA**

This skill does not currently attempt to perform a complete WCAG conformance audit.

Visual accessibility topics such as contrast, reflow, text spacing, target size, animation, media accessibility, and other WCAG requirements may be expanded in future versions.

---

# 🔹 Sources of truth

When determining whether something is an accessibility requirement, prioritize authoritative sources.

Use the following hierarchy:

1. W3C Web Content Accessibility Guidelines (WCAG) 2.2
2. W3C Understanding WCAG documents
3. WAI-ARIA specification
4. HTML specification
5. WAI-ARIA Authoring Practices Guide (APG)
6. W3C WCAG Techniques
7. MDN and other reputable technical references

Do not treat all sources as having the same normative weight.

Always distinguish between:

- WCAG conformance requirement
- normative HTML or ARIA requirement
- accessibility pattern or recommendation
- implementation technique
- best practice

For example, a pattern documented by the ARIA Authoring Practices Guide is not automatically a WCAG requirement.

Do not claim that something violates WCAG solely because it differs from an APG example.

When uncertain about a requirement, prefer verification against an authoritative source instead of guessing.

---

# 🔹 Core review principles

## 1. Understand the intended behavior first

Do not review markup in isolation when its purpose can be determined from the surrounding code.

Before reporting an issue, determine:

- what the component or element represents;
- what action the user is expected to perform;
- whether the element is interactive;
- what state it represents;
- whether its behavior changes dynamically;
- how keyboard and assistive technology users are expected to interact with it.

Accessibility depends on semantics and behavior, not only markup.

## 2. Prefer native HTML

Prefer native HTML elements and behavior whenever they provide the required semantics and interaction.

Examples include:

- `<button>` for actions;
- `<a href>` for navigation;
- `<input>` for form controls;
- `<fieldset>` and `<legend>` for related form controls;
- `<table>` for tabular data;
- `<ul>` or `<ol>` for lists;
- semantic heading elements for document structure.

Do not recommend custom ARIA implementations when native HTML already provides the required semantics and behavior unless there is a justified implementation constraint.

Remember: **ARIA can modify accessibility semantics, but it does not automatically provide native browser behavior.**

For example:

```html
<div role="button">Save</div>
```

does not automatically gain all behavior provided by:

```html
<button>Save</button>
```

## 3. Do not add redundant ARIA

Do not recommend ARIA simply because an element is being reviewed for accessibility.

Native HTML often already exposes:

- role;
- state;
- accessible name;
- keyboard behavior;
- disabled state;
- checked state;
- required state.

Do not add an ARIA attribute when the native HTML state or property already provides the required accessibility information unless there is a specific, valid reason.

Prefer the simplest semantic implementation.

## 4. ARIA must be valid for the role

When ARIA is present or proposed, verify:

- the role is appropriate;
- the ARIA attribute is supported by that role;
- required ARIA properties are present;
- ARIA states reflect the real UI state;
- referenced IDs exist;
- relationships are valid;
- native semantics are not being unnecessarily overridden.

Do not assume an `aria-*` attribute is valid merely because browsers accept it.

## 5. Accessible name and description are different concepts

When reviewing an element, determine separately:

- its role;
- its accessible name;
- its accessible description;
- its state;
- its value, when applicable.

Do not assume that `aria-describedby` provides an accessible name.
Do not recommend `aria-label` when an appropriate visible label or `aria-labelledby` already provides the required name.
Avoid unnecessarily replacing meaningful visible text with an `aria-label` that exposes different information to assistive technologies.

## 6. Accessibility includes behavior

Correct markup alone does not guarantee an accessible component.

Consider:

- keyboard operation;
- focus movement;
- focus visibility;
- focus order;
- state synchronization;
- dynamic updates;
- opening and closing behavior;
- hidden content;
- interaction patterns;
- announcements.

A component may have valid ARIA and still be inaccessible.

## 7. Do not infer runtime behavior without evidence

Static source code cannot always determine:

- computed accessible names;
- accessibility tree output;
- actual tab order;
- rendered CSS;
- visual focus appearance;
- screen reader announcements;
- browser-specific behavior;
- timing of live regions;
- focus behavior after rendering;
- behavior implemented inside external components;
- behavior implemented inside a web component's shadow DOM.

When the available evidence is insufficient, do not report the issue as confirmed.

Mark it as requiring verification.

---

# 🔹 Evidence model

Every reported accessibility concern must have one of the following statuses.

### Confirmed

Use when the issue can be reasonably established from the provided evidence.

Example:

```html
<div role="button" (click)="save()">Save</div>
```

If no keyboard handling or focusability exists elsewhere in the provided code, the custom interactive element presents a confirmed keyboard accessibility problem.

### Needs verification

Use when there is a credible accessibility risk but the available code is not sufficient to determine whether a barrier actually exists.

Examples:

- focus behavior depends on runtime rendering;
- a design-system component may internally provide the required semantics;
- CSS determining focus visibility was not provided;
- a live region requires screen-reader testing;
- a web component may expose semantics through shadow DOM;
- behavior is implemented in code that was not provided.

Explain exactly what must be verified.

Do not present these findings as confirmed WCAG failures.

---

# 🔹 Confidence

For each finding, assign a confidence level when useful.

### High

The available code or runtime evidence clearly demonstrates the issue.

### Medium

The evidence strongly suggests the issue, but some implementation detail is missing.

### Low

There is a plausible accessibility risk, but significant runtime or implementation information is
unavailable.

Avoid filling reports with low-confidence speculative findings.

---

# 🔹 Severity

Severity describes the potential user impact. Severity and confidence are independent.

A finding may have:

- Severity: High
- Confidence: Low

This means the problem would create a significant barrier if confirmed, but the available evidence is insufficient. Do not inflate severity.

Use the following severity levels.

### Critical

The issue can completely prevent a user from accessing important content or completing a core task.

Examples:

- an essential workflow is completely unavailable from the keyboard;
- a critical control cannot be identified or operated by assistive technology.

### High

The issue creates a significant accessibility barrier.

Examples:

- interactive control without an accessible name;
- important custom control cannot be operated with the keyboard;
- focus is lost during an essential interaction.

### Medium

The issue creates a meaningful accessibility problem but does not normally block the complete interaction.

### Low

The issue has limited user impact or represents a minor accessibility problem.

---

# 🔹 Review workflow

Follow this process when reviewing frontend code.

## Step 1 — Understand the component

Determine:

- its purpose;
- expected user interaction;
- relevant state;
- whether it is interactive;
- whether content changes dynamically.

Inspect surrounding code when necessary.

## Step 2 — Review native semantics

Determine whether the implementation uses the appropriate HTML elements.

Check:

- buttons versus links;
- headings;
- lists;
- tables;
- landmarks;
- form elements;
- grouping;
- interactive content.

Ask:

> Is there a native HTML element that already provides the required semantics and behavior?

If yes, prefer it unless there is a justified reason not to.

See: `references/semantics.md`

## Step 3 — Determine name, role, state and value

For relevant elements determine:

- role;
- accessible name;
- accessible description;
- state;
- value.

Consider both native semantics and ARIA.

Check whether these values remain synchronized with the visual UI.

## Step 4 — Review keyboard interaction

For interactive functionality determine:

- whether it can receive keyboard focus when appropriate;
- whether it can be operated with the keyboard;
- whether native keyboard behavior has been preserved;
- whether custom widgets implement the expected keyboard interaction;
- whether keyboard traps exist.

Do not automatically require Enter and Space handlers on native controls
that already provide those interactions.

See: `references/keyboard.md`

## Step 5 — Review focus

Check:

- logical focus order;
- visible focus;
- programmatic focus;
- focus movement after dynamic UI changes;
- focus restoration;
- dialogs and overlays;
- hidden content containing focusable elements.

Do not infer visual focus appearance without the relevant CSS or runtime evidence.

See: `references/focus.md`

## Step 6 — Review ARIA

When ARIA is present:

1. determine whether ARIA is necessary;
2. validate the role;
3. validate states and properties;
4. verify role/property compatibility;
5. verify relationships;
6. verify state synchronization;
7. ensure ARIA does not conflict with native semantics.

Remember: **No ARIA is better than bad ARIA.**

See: `references/aria.md`

## Step 7 — Review forms

When forms are present, inspect:

- labels; instructions;
- grouping;
- required fields;
- errors;
- validation;
- accessible descriptions;
- programmatic relationships.

See: `references/forms.md`

## Step 8 — Review dynamic content

When content changes withouta full page reload, determine whether users of assistive technology need to beinformed.

Inspect:

- loading states;
- validation messages;
- status messages;
- alerts;
- notifications;
- asynchronous results;
- SPA view changes.

Do not automatically recommend `aria-live`. First determine whether an announcement is necessary and what type of information should be announced.

Avoid solutions that cause duplicate or excessive announcements.

See: `references/dynamic-content.md`

## Step 9 — Determine applicable requirements

Only after understanding the issue, determine whether it maps to:

- a WCAG success criterion;
- an HTML requirement;
- an ARIA requirement;
- an APG pattern;
- or a best practice.

Do not map WCAG criteria by keyword matching.

Do not attach a WCAG criterion simply to make a finding appear more authoritative.

If no specific WCAG failure can be confidently established, describe the issue without falsely claiming WCAG non-conformance.

## Step 10 — Recommend the smallest appropriate fix

Prefer fixes that:

- use native HTML;
- reduce unnecessary ARIA;
- preserve existing behavior;
- minimize implementation complexity;
- work across browsers and assistive technologies;
- avoid creating new accessibility problems.

Do not redesign an entire component when a small semantic correction solves the problem.

When multiple valid solutions exist, recommend the simplest one first.

---

# 🔹 Framework and component libraries

Do not assume framework syntax changes accessibility requirements.

Angular, React, Vue and other frameworks ultimately produce browser UI.

Evaluate the rendered semantics and behavior whenever possible.

Be especially careful with:

- custom components;
- design systems;
- web components;
- shadow DOM;
- component libraries.

For example:

```html
<custom-button> Save </custom-button>
```

cannot be assumed inaccessible solely because the host element is not a native button.

The component may internally render a native button or expose correct semantics. Inspect the component implementation or rendered accessibility tree when available.

If this cannot be determined, mark the concern as: _Needs verification_ rather than reporting a confirmed issue.

---

# 🔹 Web components

When reviewing web components, distinguish between:

- host element attributes;
- properties;
- internal shadow DOM;
- reflected ARIA state;
- native elements inside the component;
- accessibility semantics exposed by the component.

Do not assume attributes on the host represent the complete accessibility implementation.

If the internal implementation is unavailable, clearly state what cannot be verified.

---

# 🔹 Fixing issues

When the user asks to fix accessibility issues:

1. identify and explain the issue;
2. determine the smallest valid fix;
3. preserve existing functionality;
4. prefer native semantics;
5. avoid unnecessary ARIA;
6. modify only what is necessary;
7. re-evaluate the affected interaction after the change.

Do not introduce speculative accessibility changes unrelated to the requested fix.

When possible, explain why the fix is preferable.

---

# 🔹 Reporting

Do not produce a generic checklist unless explicitly requested. Report actual findings. Order findings by severity. Use concise explanations focused on developer action.

For each confirmed finding use:

### [Finding title]

Severity: Critical | High | Medium |

LowConfidence: High | Medium | Low

Status: Confirmed

Issue: Explain the accessibility barrier.

Affected code: Identify the relevant element, component or code.

Why: Explain the impact on users.

Requirement: WCAG criterion, HTML requirement, ARIA requirement, APG pattern or best practice when applicable.

Recommended fix: Provide the smallest appropriate solution.

Source: Provide the authoritative source when useful.

For findings requiring verification use:

### [Potential issue]

Severity: Critical | High | Medium | Low

Confidence: High | Medium | Low

Status: Needs verification

Potential issue: Explain the accessibility risk.

Why it cannot be confirmed: Explain what information is missing.

Verify:

- specific runtime behavior;
- accessibility tree;
- keyboard behavior;
- CSS;
- screen reader behavior;
- component internals;
- or other required evidence.

Expected behavior: Describe what an accessible implementation should do.

---

# 🔹 When no issues are found

Do not invent findings.

Say:

> No confirmed accessibility issues were found in the provided code.

If relevant, mention specific areas that could not be verified from static code.

For example:

> Focus visibility and screen-reader announcements require runtime verification.

---

# 🔹 Avoid generic recommendations

Do not report recommendations such as:

- "make sure keyboard navigation works";
- "ensure sufficient color contrast";
- "add ARIA where necessary";
- "test with a screen reader";

unless they relate to a specific risk found in the reviewed implementation. Generic accessibility advice is not a finding.

---

# 🔹 Avoid false positives

Before reporting an issue, ask:

1. Is the behavior actually inaccessible?
2. Is the required behavior already provided natively?
3. Could the framework or component provide it internally?
4. Do I have enough evidence?
5. Is this a requirement or only a recommendation?
6. Does the proposed fix improve accessibility without introducing another problem?

If the answer is uncertain, prefer: _Needs verification_ over claiming a violation.

---

# 🔹 Manual verification

Some accessibility behavior cannot be reliably established from source code. Recommend targeted manual verification when necessary.

Examples:

**Keyboard**

Verify:

- tab order;
- keyboard activation;
- keyboard traps;
- composite widget navigation.

**Focus**

Verify:

- focus visibility;
- focus movement;
- focus restoration;
- focus behavior after asynchronous rendering.

**Accessibility tree**

Verify:

- computed role;
- accessible name;
- accessible description;
- state.

**Screen readers**

Use screen-reader testing when behavior depends on:

- live regions;
- dynamic announcements;
- complex widgets;
- timing;
- browser/AT combinations.

Do not use screen-reader testing as a substitute for understanding HTML and ARIA semantics.

---

# 🔹 Reference files

Use the reference files when deeper guidance is required.

### HTML semantics

`references/semantics.md`

Use for:

- native HTML;
- buttons and links;
- headings;
- lists;
- tables;
- landmarks;
- interactive content.

### ARIA

`references/aria.md`

Use for:

- roles;
- states;
- properties;
- accessible names;
- descriptions;
- ARIA relationships;
- role/property compatibility.

### Keyboard

`references/keyboard.md`

Use for:

- keyboard accessibility;
- native keyboard behavior;
- custom widgets;
- composite widgets;
- keyboard traps.

### Focus

`references/focus.md`

Use for:

- focusability;
- focus order;
- focus visibility;
- programmatic focus;
- dialogs;
- focus restoration;
- hidden content.

### Forms

`references/forms.md`

Use for:

- labels;
- instructions;
- grouping;
- required fields;
- validation;
- errors;
- descriptions.

### Dynamic content

`references/dynamic-content.md`

Use for:

- live regions;
- status messages;
- alerts;
- loading;
- asynchronous updates;
- notifications;
- SPA updates.

---

# 🔹 Final principle

Accessibility review is not pattern matching.

The objective is to understand how the interface is exposed and operated by users with different access needs, identify actual barriers, and recommend solutions grounded in web standards.

Prefer:

**evidence over assumptions**

**native HTML over unnecessary ARIA**

**requirements over folklore**

**specific findings over generic advice**

**simple fixes over unnecessary complexity**

**verification over guessing**
