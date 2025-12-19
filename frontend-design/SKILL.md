---
name: frontend-design
description: Expert frontend UI/UX design skill for creating modern, accessible, and beautiful web interfaces. Use when designing UI, creating components, building layouts, styling applications, or when user mentions "design", "UI", "UX", "frontend", "CSS", "styling", or "layout".
allowed-tools: Read, Write, Edit, Bash, Glob
---

# Expert Frontend Design

## Overview
This skill enables Claude to design and implement modern, accessible, and visually appealing frontend interfaces using 2025 best practices.

## Core Design Principles

### 1. Clarity & Simplicity
Keep designs clean and straightforward. Users should instantly understand where to go and what to do.

```css
/* Clean, readable styling */
.card {
  padding: 1.5rem;
  background: white;
  border-radius: 0.5rem;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

/* Clear visual hierarchy */
.card-title {
  font-size: 1.25rem;
  font-weight: 600;
  margin-bottom: 0.5rem;
}

.card-body {
  font-size: 1rem;
  color: #64748b;
  line-height: 1.6;
}
```

### 2. Visual Hierarchy
Guide users' attention through intentional design choices.

**Priority Levels:**
```
1. Primary Action   → Large, colored button, prominent position
2. Secondary Action → Outlined or muted button
3. Tertiary Action  → Text link or icon
```

**Size & Weight:**
```css
/* Heading hierarchy */
h1 { font-size: 2.5rem; font-weight: 700; }
h2 { font-size: 2rem; font-weight: 600; }
h3 { font-size: 1.5rem; font-weight: 600; }
h4 { font-size: 1.25rem; font-weight: 500; }
body { font-size: 1rem; font-weight: 400; }
small { font-size: 0.875rem; font-weight: 400; }
```

### 3. Consistency
Use consistent patterns throughout the application.

```css
/* Design tokens - define once, use everywhere */
:root {
  /* Colors */
  --color-primary: #3b82f6;
  --color-primary-hover: #2563eb;
  --color-secondary: #64748b;
  --color-success: #22c55e;
  --color-warning: #f59e0b;
  --color-error: #ef4444;

  /* Spacing scale */
  --space-xs: 0.25rem;
  --space-sm: 0.5rem;
  --space-md: 1rem;
  --space-lg: 1.5rem;
  --space-xl: 2rem;
  --space-2xl: 3rem;

  /* Border radius */
  --radius-sm: 0.25rem;
  --radius-md: 0.5rem;
  --radius-lg: 1rem;
  --radius-full: 9999px;

  /* Shadows */
  --shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05);
  --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1);
}
```

### 4. Mobile-First & Responsive Design
Design for mobile first, then scale up.

```css
/* Mobile first approach */
.container {
  padding: 1rem;
  width: 100%;
}

/* Tablet */
@media (min-width: 768px) {
  .container {
    padding: 2rem;
    max-width: 768px;
    margin: 0 auto;
  }
}

/* Desktop */
@media (min-width: 1024px) {
  .container {
    max-width: 1024px;
  }
}

/* Large desktop */
@media (min-width: 1280px) {
  .container {
    max-width: 1280px;
  }
}
```

### 5. Accessibility (A11y)
Accessibility is a core requirement, not optional.

**Contrast Ratios:**
- Body text: 4.5:1 minimum
- Large/bold text: 3:1 minimum

**Keyboard Navigation:**
```css
/* Visible focus states */
button:focus-visible,
a:focus-visible,
input:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

/* Skip link for keyboard users */
.skip-link {
  position: absolute;
  top: -100%;
  left: 0;
  padding: 1rem;
  background: var(--color-primary);
  color: white;
  z-index: 100;
}

.skip-link:focus {
  top: 0;
}
```

**ARIA Labels:**
```html
<!-- Icon-only button needs label -->
<button aria-label="Close dialog">
  <svg>...</svg>
</button>

<!-- Form inputs need labels -->
<label for="email">Email address</label>
<input id="email" type="email" aria-describedby="email-hint" />
<span id="email-hint">We'll never share your email</span>
```

## Modern Design Styles (2025)

### Glassmorphism
Translucent, frosted-glass effect.

```css
.glass-card {
  background: rgba(255, 255, 255, 0.25);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.18);
  border-radius: 1rem;
  padding: 2rem;
}
```

### Neumorphism
Soft, extruded 3D look.

```css
.neumorph-card {
  background: #e0e5ec;
  border-radius: 1rem;
  padding: 2rem;
  box-shadow:
    9px 9px 16px rgba(163, 177, 198, 0.6),
    -9px -9px 16px rgba(255, 255, 255, 0.5);
}

.neumorph-button-pressed {
  box-shadow:
    inset 6px 6px 10px rgba(163, 177, 198, 0.6),
    inset -6px -6px 10px rgba(255, 255, 255, 0.5);
}
```

### Neo-Brutalism
Bold, raw, unapologetic design.

```css
.brutalist-card {
  background: #fff;
  border: 3px solid #000;
  box-shadow: 8px 8px 0 #000;
  padding: 2rem;
}

.brutalist-button {
  background: #ffde59;
  border: 3px solid #000;
  box-shadow: 4px 4px 0 #000;
  font-weight: 700;
  text-transform: uppercase;
  padding: 1rem 2rem;
  cursor: pointer;
  transition: all 0.1s ease;
}

.brutalist-button:hover {
  transform: translate(2px, 2px);
  box-shadow: 2px 2px 0 #000;
}

.brutalist-button:active {
  transform: translate(4px, 4px);
  box-shadow: none;
}
```

### Minimalist
Clean, simple, less is more.

```css
.minimal-card {
  background: #fafafa;
  border: 1px solid #e5e5e5;
  border-radius: 0.25rem;
  padding: 1.5rem;
}

.minimal-button {
  background: transparent;
  border: 1px solid currentColor;
  padding: 0.75rem 1.5rem;
  font-weight: 500;
  transition: all 0.2s ease;
}

.minimal-button:hover {
  background: #000;
  color: #fff;
}
```

## Component Patterns

### Button Hierarchy
```css
/* Primary - main actions */
.btn-primary {
  background: var(--color-primary);
  color: white;
  padding: 0.75rem 1.5rem;
  border-radius: var(--radius-md);
  font-weight: 500;
  border: none;
  cursor: pointer;
  transition: background 0.2s;
}

.btn-primary:hover {
  background: var(--color-primary-hover);
}

/* Secondary - alternative actions */
.btn-secondary {
  background: transparent;
  color: var(--color-primary);
  border: 1px solid var(--color-primary);
  padding: 0.75rem 1.5rem;
  border-radius: var(--radius-md);
}

/* Ghost - subtle actions */
.btn-ghost {
  background: transparent;
  color: var(--color-secondary);
  border: none;
  padding: 0.75rem 1.5rem;
}

.btn-ghost:hover {
  background: rgba(0, 0, 0, 0.05);
}
```

### Form Inputs
```css
.input-group {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.input-label {
  font-size: 0.875rem;
  font-weight: 500;
  color: #374151;
}

.input-field {
  padding: 0.75rem 1rem;
  border: 1px solid #d1d5db;
  border-radius: var(--radius-md);
  font-size: 1rem;
  transition: border-color 0.2s, box-shadow 0.2s;
}

.input-field:focus {
  outline: none;
  border-color: var(--color-primary);
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
}

.input-error {
  border-color: var(--color-error);
}

.input-hint {
  font-size: 0.75rem;
  color: var(--color-secondary);
}

.input-error-message {
  font-size: 0.75rem;
  color: var(--color-error);
}
```

### Card Layout
```css
.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 1.5rem;
}

.card {
  display: flex;
  flex-direction: column;
  background: white;
  border-radius: var(--radius-lg);
  overflow: hidden;
  box-shadow: var(--shadow-md);
  transition: transform 0.2s, box-shadow 0.2s;
}

.card:hover {
  transform: translateY(-4px);
  box-shadow: var(--shadow-lg);
}

.card-image {
  width: 100%;
  height: 200px;
  object-fit: cover;
}

.card-content {
  padding: 1.5rem;
  flex: 1;
}

.card-footer {
  padding: 1rem 1.5rem;
  border-top: 1px solid #e5e5e5;
  background: #fafafa;
}
```

## Layout Patterns

### Flexbox Utilities
```css
.flex { display: flex; }
.flex-col { flex-direction: column; }
.items-center { align-items: center; }
.justify-center { justify-content: center; }
.justify-between { justify-content: space-between; }
.gap-sm { gap: 0.5rem; }
.gap-md { gap: 1rem; }
.gap-lg { gap: 1.5rem; }
```

### CSS Grid Layouts
```css
/* Two-column layout */
.two-column {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 2rem;
}

/* Sidebar layout */
.sidebar-layout {
  display: grid;
  grid-template-columns: 280px 1fr;
  min-height: 100vh;
}

/* Holy grail layout */
.holy-grail {
  display: grid;
  grid-template-rows: auto 1fr auto;
  grid-template-columns: 200px 1fr 200px;
  min-height: 100vh;
}
```

## Performance Best Practices

### Lazy Loading Images
```html
<img
  src="placeholder.jpg"
  data-src="actual-image.jpg"
  loading="lazy"
  alt="Description"
/>
```

### Efficient Animations
```css
/* Use transform and opacity for smooth animations */
.animate-in {
  animation: fadeIn 0.3s ease-out;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Use will-change sparingly */
.heavy-animation {
  will-change: transform;
}
```

## Design System Checklist

When designing interfaces, ensure:

- [ ] **Color palette** defined with semantic names
- [ ] **Typography scale** with clear hierarchy
- [ ] **Spacing system** using consistent units
- [ ] **Component library** for reusable elements
- [ ] **Responsive breakpoints** defined
- [ ] **Accessibility** tested (contrast, keyboard, screen reader)
- [ ] **Loading states** for async operations
- [ ] **Error states** for form validation
- [ ] **Empty states** for no-data scenarios
- [ ] **Dark mode** support (if applicable)

## Instructions for Claude

When helping with frontend design:

1. **Ask about the project context:**
   - Tech stack (React, Vue, vanilla, etc.)
   - CSS approach (Tailwind, CSS Modules, styled-components)
   - Design system (if existing)
   - Target audience

2. **Consider accessibility first:**
   - Semantic HTML
   - ARIA labels where needed
   - Keyboard navigation
   - Color contrast

3. **Apply appropriate design style:**
   - Match existing brand/style if present
   - Suggest modern styles for new projects
   - Keep consistency throughout

4. **Optimize for performance:**
   - Efficient CSS selectors
   - Minimal JavaScript for animations
   - Lazy loading for images/components

5. **Provide complete, production-ready code:**
   - Include hover/focus states
   - Add transitions for polish
   - Handle edge cases (long text, empty states)

## Sources

This skill is based on research from:
- [Modern Front-End Design: 18 Essential Principles for 2025](https://www.index.dev/blog/top-front-end-design-principles)
- [UI Design Best Practices for 2025](https://www.webstacks.com/blog/ui-design-best-practices)
- [Web App UI/UX Best Practices 2025](https://cygnis.co/blog/web-app-ui-ux-best-practices-2025/)
- [10 UI Design Principles for 2025](https://www.lyssna.com/blog/ui-design-principles/)
- [Frontend Development Best Practices 2025](https://www.jalasoft.com/blog/best-practices-for-frontend-development)
- [Toptal Front-end Design Principles](https://www.toptal.com/front-end/front-end-design-principles)
