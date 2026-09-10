# Design System
<!-- Owner: UI/UX Designer. Produced in Phase 2. Omit this whole file for backend-only projects. -->

## Design Tokens
- Colors: Primary `#`, Secondary `#`, Error `#`, Warning `#`, Success `#`, Neutral scale (5-7 steps)
- Typography: font family, a fixed type scale (e.g. 12/14/16/20/24/32px), weight tokens
- Spacing: a fixed scale (e.g. 4/8/12/16/24/32/48px) — Frontend Engineer must not invent
  one-off values outside this scale.
- Breakpoints: a fixed set (e.g. mobile <640px / tablet 640-1024px / desktop >1024px) — every
  component's Responsive behavior references these, never a one-off pixel value.
- Motion: a fixed set of transition durations and easings (e.g. fast 100ms, base 200ms, slow
  300ms; ease-out for entrances, ease-in for exits) — every hover/loading/transition references
  these, never an invented duration.

## Accessibility Baseline (non-negotiable, not aspirational)
- WCAG 2.2 AA minimum: 4.5:1 text contrast, all interactive elements keyboard-reachable, visible
  focus states, form errors announced to screen readers.

## Components
<!-- Include every component a Core User Flow needs, PLUS the cross-cutting feedback components
     every flow implicitly relies on even if no flow names them: toast/inline notification, error
     banner, offline/connectivity indicator. -->
### [Component name]
- Variants: [list]
- States: default, hover, focus, disabled, error, loading, empty (for any list/collection/table
  component — what shows with 0 items, not a spinner forever) — specify each that applies
- Responsive behavior: [behavior at each Breakpoints token, if it changes layout not just size]

---
### Example: Button
Variants: primary, secondary, danger. Sizes: sm/md/lg. States: default, hover, focus-visible
(2px outline, offset 2px), disabled (50% opacity, not-allowed cursor), loading (spinner replaces
label, width does not change to avoid layout shift).
