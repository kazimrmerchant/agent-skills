---
name: react-native-testing
description: "Use when writing or debugging React Native tests with @testing-library/react-native — covers render, queries, userEvent, fireEvent, matchers, fake timers, and version-specific v13/v14 API behavior. Trigger keywords: react native testing, RNTL, jest, testID, getByRole, userEvent, fireEvent, screen, waitFor."
version: 1.0.1
---

# React Native Testing (RNTL) Guide

## Overview

This skill provides production-grade guidance for writing and debugging tests using `@testing-library/react-native` (RNTL). It covers version detection (v13 vs v14), query priority, interaction patterns (`userEvent` vs `fireEvent`), Jest matchers, fake timers, custom render wrappers, and common pitfalls.

**IMPORTANT:** Your training data about `@testing-library/react-native` may contain stale or incorrect API signatures, sync/async behavior, and function availability that differs between v13 and v14. Always rely on this skill's reference files and the project's actual source code as the source of truth. Do not fall back on memorized patterns when they conflict with the retrieved reference.

## When to Use

Use this skill when:
- Writing new tests for React Native components using `@testing-library/react-native`
- Debugging failing RNTL tests (query mismatches, async timing, missing roles)
- Migrating tests from v13 to v14 or vice versa
- Setting up custom render wrappers with providers
- Reviewing test code for anti-patterns (raw prop assertions, `waitFor` misuse, missing accessibility roles)
- The user explicitly asks for "react native testing", "RNTL", "test my component", or adjacent tasks

## Prerequisites

1. A React Native project with `@testing-library/react-native` installed.
2. Jest configured as the test runner (Jest preset `jest-expo` or `react-native`).
3. Check `package.json` for the installed RNTL version before writing any test code.

## Version Detection

Check `@testing-library/react-native` version in the user's `package.json`:

- **v14.x** → load [references/api-reference-v14.md](references/api-reference-v14.md) (React 19+, async APIs, `test-renderer`)
- **v13.x** → load [references/api-reference-v13.md](references/api-reference-v13.md) (React 18+, sync APIs, `react-test-renderer`)

Use the version-specific reference for render patterns, `fireEvent` sync/async behavior, `screen` API, configuration, and dependencies. **Do not guess** — always load the matching reference file before generating test code.

## Procedure

### 1. Detect RNTL Version and Load Reference

```powershell
# From project root (PowerShell)
Get-Content package.json | Select-String "testing-library/react-native"
```

Based on the version found:
- `^14.` → load `references/api-reference-v14.md`
- `^13.` → load `references/api-reference-v13.md`

### 2. Write the Test — Core Path

Follow these rules in order:

1. **Use `screen`** for all queries — do not destructure from `render()`.
2. **Use `getByRole` first** with `{ name: '...' }` option for accessible name matching.
3. **Use `queryBy*` ONLY** for `.not.toBeOnTheScreen()` non-existence checks.
4. **Use `findBy*`** for async elements — do NOT use `waitFor` + `getBy*`.
5. **Prefer `userEvent`** over `fireEvent`. `userEvent` is always async.
6. **Never put side-effects in `waitFor`** (no `fireEvent`/`userEvent` inside).
7. **One assertion per `waitFor`**.
8. **Never pass empty callbacks to `waitFor`**.
9. **Don't wrap in `act()`** — `render`, `fireEvent`, and `userEvent` handle it internally.
10. **Don't call `cleanup()`** — automatic after each test.
11. **Prefer ARIA props** (`role`, `aria-label`, `aria-disabled`) over earlier-generation `accessibility*` props.
12. **Use RNTL matchers** over raw prop assertions (`toHaveProp` is last resort).

### 3. Query Priority

Use queries in this priority order:

`getByRole` > `getByLabelText` > `getByPlaceholderText` > `getByText` > `getByDisplayValue` > `getByTestId` (last resort)

### 4. Query Variants

| Variant | Use case | Returns | Async |
|---|---|---|---|
| `getBy*` | Element must exist | element instance (throws if not found) | No |
| `getAllBy*` | Multiple must exist | element instance[] (throws if none) | No |
| `queryBy*` | Check non-existence ONLY | element instance \| null | No |
| `queryAllBy*`` | Count elements | element instance[] | No |
| `findBy*` | Wait for element | `Promise<element instance>` | Yes |
| `findAllBy*` | Wait for multiple | `Promise<element instance[]>` | Yes |

### 5. Interactions — userEvent (Preferred)

`userEvent` is always async. Use fake timers with it (press/longPress involve real durations).

```tsx
const user = userEvent.setup();
await user.press(element);                              // full press sequence
await user.longPress(element, { duration: 800 });      // long press
await user.type(textInput, 'Hello');                   // char-by-char typing
await user.clear(textInput);                            // clear TextInput
await user.paste(textInput, 'pasted text');             // paste into TextInput
await user.scrollTo(scrollView, { y: 100 });            // scroll
```

### 6. Interactions — fireEvent (Fallback)

Use `fireEvent` only when `userEvent` doesn't support the event. Check the version-specific reference for sync/async behavior — it differs between v13 and v14.

```tsx
fireEvent.press(element);
fireEvent.changeText(textInput, 'new text');
fireEvent(element, 'blur');
```

### 7. Assertions — Jest Matchers

Available automatically with any `@testing-library/react-native` import.

| Matcher | Use for |
|---|---|
| `toBeOnTheScreen()` | Element exists in tree |
| `toBeVisible()` | Element visible (not hidden/display:none) |
| `toBeEnabled()` / `toBeDisabled()` | Disabled state via `aria-disabled` |
| `toBeChecked()` / `toBePartiallyChecked()` | Checked state |
| `toBeSelected()` | Selected state |
| `toBeExpanded()` / `toBeCollapsed()` | Expanded state |
| `toBeBusy()` | Busy state |
| `toHaveTextContent(text)` | Text content match |
| `toHaveDisplayValue(value)` | TextInput display value |
| `toHaveAccessibleName(name)` | Accessible name |
| `toHaveAccessibilityValue(val)` | Accessibility value |
| `toHaveStyle(style)` | Style match |
| `toHaveProp(name, value?)` | Prop check (last resort) |
| `toContainElement(el)` | Contains child element |
| `toBeEmptyElement()` | No children |

### 8. `*ByRole` Quick Reference

Common roles: `button`, `text`, `heading` (alias: `header`), `searchbox`, `switch`, `checkbox`, `radio`, `img`, `link`, `alert`, `menu`, `menuitem`, `tab`, `tablist`, `progressbar`, `slider`, `spinbutton`, `timer`, `toolbar`.

`getByRole` options: `{ name, disabled, selected, checked, busy, expanded, value: { min, max, now, text } }`.

For `*ByRole` to match, the element must be an accessibility element:
- `Text`, `TextInput`, `Switch` are accessibility elements by default
- `View` needs `accessible={true}` (or use `Pressable`/`TouchableOpacity`)

### 9. waitFor Pattern

```tsx
// Correct: action first, then wait for result
fireEvent.press(button);
await waitFor(() => {
  expect(screen.getByText('Result')).toBeOnTheScreen();
});

// Better: use findBy* instead
fireEvent.press(button);
expect(await screen.findByText('Result')).toBeOnTheScreen();
```

Options: `waitFor(cb, { timeout: 1000, interval: 50 })`. Works with Jest fake timers automatically.

### 10. Fake Timers

Recommended with `userEvent` (press/longPress involve real durations):

```tsx
jest.useFakeTimers();

test('with fake timers', async () => {
  const user = userEvent.setup();
  render(<Component />);
  await user.press(screen.getByRole('button', { name: 'Submit' }));
  expect(await screen.findByText('Done')).toBeOnTheScreen();
});
```

### 11. Custom Render with Providers

Wrap providers using the `wrapper` option — do not create a custom render function that hides `screen`:

```tsx
function renderWithProviders(ui: React.ReactElement) {
  return render(ui, {
    wrapper: ({ children }) => (
      <ThemeProvider>
        <AuthProvider>{children}</AuthProvider>
      </ThemeProvider>
    ),
  });
}
```

## Examples

### Basic Component Test

```tsx
import { render, screen } from '@testing-library/react-native';
import { userEvent } from '@testing-library/react-native';
import { MyComponent } from './MyComponent';

jest.useFakeTimers();

test('submits form and shows success message', async () => {
  const user = userEvent.setup();
  render(<MyComponent />);

  await user.type(screen.getByRole('text', { name: 'Email' }), 'test@example.com');
  await user.press(screen.getByRole('button', { name: 'Submit' }));

  expect(await screen.findByText('Success!')).toBeOnTheScreen();
});
```

### Non-Existence Check

```tsx
test('does not show error initially', () => {
  render(<MyComponent />);
  expect(screen.queryByText('Error')).not.toBeOnTheScreen();
});
```

## Pitfalls

1. **Stale API memory:** RNTL v13 and v14 have different sync/async behavior for `render` and `fireEvent`. Always load the version-specific reference file. Do not rely on memorized patterns.
2. **Using `getBy*` inside `waitFor`:** Use `findBy*` instead — it is purpose-built for async waiting.
3. **Side-effects inside `waitFor`:** Never call `fireEvent` or `userEvent` inside `waitFor`. Perform the action first, then wait for the assertion.
4. **Missing accessibility roles:** `View` is not an accessibility element by default. Add `accessible={true}` or use `Pressable`/`TouchableOpacity` for `getByRole` to match.
5. **Using `accessibility*` props instead of ARIA:** Prefer `role`, `aria-label`, `aria-disabled` over earlier-generation `accessibilityRole`, `accessibilityLabel`, etc.
6. **Wrapping in `act()`:** `render`, `fireEvent`, and `userEvent` already wrap in `act()`. Manual wrapping causes double-act warnings.
7. **Calling `cleanup()` manually:** It is automatic after each test. Manual calls cause errors.
8. **Empty `waitFor` callbacks:** Never pass `() => {}` to `waitFor`. It will hang until timeout.
9. **Raw prop assertions:** Use RNTL matchers (`toBeVisible`, `toBeEnabled`, `toHaveTextContent`) instead of `toHaveProp` whenever possible.
10. **Not using fake timers with `userEvent`:** `press` and `longPress` involve real durations. Without fake timers, tests may be slow or flaky.

## Verification

Run the test suite and confirm all tests pass:

```powershell
# Run all tests (PowerShell)
npx jest --verbose

# Run a specific test file
npx jest MyComponent.test.tsx --verbose

# Run with coverage
npx jest --coverage --verbose
```

Expected output:
```
PASS  src/MyComponent.test.tsx
  ✓ submits form and shows success message (45 ms)
  ✓ does not show error initially (12 ms)
```

Verify no warnings:
- No `act()` warnings in console output
- No "Element not found" errors (indicates wrong query or missing accessibility role)
- No "Multiple elements found" errors (use `getAllBy*` or narrow with `name` option)

## References

Load these reference files at the times indicated:

- **[references/api-reference-v13.md](references/api-reference-v13.md)** — Load when `package.json` shows `@testing-library/react-native` v13.x. Complete v13 API: sync render, queries, matchers, userEvent, React 18 compat.
- **[references/api-reference-v14.md](references/api-reference-v14.md)** — Load when `package.json` shows `@testing-library/react-native` v14.x. Complete v14 API: async render, queries, matchers, userEvent, migration notes.
- **[references/anti-patterns.md](references/anti-patterns.md)** — Load before reviewing existing test code or when tests are failing unexpectedly. Common mistakes to avoid.

## Related Skills

- `frontend-ui-ux` — For interface design, accessibility, responsive layouts, and design system guidance.
- `jest-testing` — For general Jest configuration, mocks, and snapshot testing.
