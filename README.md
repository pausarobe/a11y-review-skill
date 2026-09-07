# A11y Review Skill

Version: 0.1.0

The A11y Review Skill helps review web frontend code for accessibility issues using an evidence-based process. Its primary target is WCAG 2.2 Level AA, while also distinguishing WCAG conformance requirements from HTML, WAI-ARIA, APG patterns, implementation techniques, and best practices.

The skill is designed for accessibility reviews and focused fixes across HTML, CSS, JavaScript, TypeScript, Angular, React, Vue, web components, and other browser-based frontend technologies. It does not attempt to be a complete WCAG conformance audit.

## Main Review Areas

- HTML semantics and native interactive elements
- Accessible names, descriptions, roles, states, and values
- WAI-ARIA roles, states, properties, and relationships
- Keyboard operability and widget interaction patterns
- Focus order, visibility, programmatic focus, and focus restoration
- Form labels, grouping, instructions, validation, and errors
- Dynamic content, status messages, announcements, and SPA updates
- Frontend framework, design-system, custom element, and web component behavior

## Repository Structure

- `SKILL.md`: skill metadata, scope, review workflow, reporting model, and core principles.
- `references/`: focused guidance for deeper review areas.
- `references/semantics.md`: native HTML semantics and structural review.
- `references/aria.md`: ARIA roles, states, properties, names, descriptions, and relationships.
- `references/keyboard.md`: keyboard operability and expected interaction patterns.
- `references/focus.md`: focusability, focus order, focus visibility, and focus management.
- `references/forms.md`: form labeling, grouping, validation, and error handling.
- `references/dynamic-content.md`: live regions, status updates, announcements, and asynchronous UI changes.

## Review Philosophy

Accessibility review here is not checklist matching. The skill first tries to understand the component's purpose, behavior, exposed semantics, and available evidence. It prioritizes real user barriers, native HTML where appropriate, valid and necessary ARIA, targeted verification when runtime behavior is uncertain, and the smallest fix that resolves the problem without introducing new accessibility issues.

This skill evolves incrementally as new accessibility cases and implementation patterns are reviewed.
