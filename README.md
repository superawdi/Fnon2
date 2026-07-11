# Fnon.js v2.0.4 🚀

**Lightweight modal, toast, spinner, date picker, and context menu library.**  
No dependencies. 42 KB minified (12 KB gzip). Works in all modern browsers.

---
**Fnon v2.0** has been completely rebuilt from scratch! Here is what you need to know before updating:

### 🔄 Upgrading to v2.0
* **Familiar Syntax:** I have done my absolute best to keep the code and API as close to the old version as possible, ensuring a smoother transition for everyone.
* **Check Your Code:** Because this is a major rewrite, please **review your existing implementation** before upgrading to version 2.0 to ensure everything continues to run smoothly in your project.

### 📌 About the Project & Support
* **Personal Use:** I originally built this library for myself to use across my own projects, but decided to share it openly so others could benefit from it too.
* **Contributions & Support:** Because this is a personal passion project, **I am not accepting feature requests.** However, if you run into any legitimate bugs, feel free to report them and I will do my best to get them fixed when I can!

---

> ### [Live Demo](https://superawdi.github.io/Fnon2/)

## Table of Contents

- [Installation](#installation)
- [Fnon Global API](#fnon-global-api)
- [Modal System](#modal-system)
  - [Alert](#alert)
  - [Ask](#ask)
  - [Dialogue](#dialogue)
  - [Breaking Bar](#breaking-bar)
  - [Modal Options](#modal-options)
  - [Button Definition](#button-definition)
  - [Lifecycle Hooks](#lifecycle-hooks)
  - [Layout System](#layout-system)
  - [Width Variants](#width-variants)
- [Hint / Toast System](#hint--toast-system)
  - [Hint Options](#hint-options)
  - [Hint Themes](#hint-themes)
- [Spinner System](#spinner-system)
  - [Wait (Full-page)](#wait-full-page)
  - [Box (Container)](#box-container)
  - [Spinner Options](#spinner-options)
  - [Available Spinners](#available-spinners)
- [DatePicker](#datepicker)
  - [DatePicker Options](#datepicker-options)
  - [Return Value](#return-value)
  - [CSS Custom Properties](#datepicker-css-properties)
- [Context Menu](#context-menu)
  - [ContextMenuItem](#contextmenuitem)
  - [Keyboard Navigation](#context-menu-keyboard)
- [Dark Mode](#dark-mode)
- [Theme System](#theme-system)
  - [Built-in Themes](#built-in-themes)
  - [Custom Themes](#custom-themes)
  - [Theme Colors](#theme-colors)
- [CSS Custom Properties](#css-custom-properties)
- [CSS Classes Reference](#css-classes-reference)
- [React Integration](#react-integration)
  - [enableReactRendering](#enablereactrendering)
  - [useFnon Hook](#usefnon-hook)
- [Renderer Hook System](#renderer-hook-system)
- [Browser Support](#browser-support)

---

### Default theme names: 
![Primary](https://placehold.co/80x30/029eff/ffffff?text=Primary)
![Success](https://placehold.co/80x30/39DA8A/ffffff?text=Success)
![Warning](https://placehold.co/80x30/fdd347/000000?text=Warning)
![Info](https://placehold.co/80x30/48dbfb/000000?text=Info)
![Light](https://placehold.co/80x30/f8f9fa/000000?text=Light)
![Danger](https://placehold.co/80x30/ff6b6b/000000?text=Danger)
![Dark](https://placehold.co/80x30/222f3e/ffffff?text=Dark)

---

# ✪ Installation

Copy `fnon.min.js` and `fnon.min.css` to your project.

```html
<link rel="stylesheet" href="path/to/fnon.min.css">
<script src="path/to/fnon.min.js"></script>
<script>
  // Alert
  Fnon.Alert.Primary('Hello World!', 'Welcome');

  // Ask (returns Promise)
  const confirmed = await Fnon.Ask.Primary.Async('Delete?', 'Confirm');

  // Toast
  Fnon.Hint.Success('File saved successfully!');

  // Spinner
  Fnon.Wait.Spin('Loading...');
  setTimeout(() => Fnon.Wait.Remove(), 2000);

  // DatePicker
  const date = await Fnon.DatePicker({ initDate: new Date() });

  // Context Menu
  Fnon.ContextMenu(event, [
    { text: 'Open', onClick: () => open() },
    { text: 'More', items: [
      { text: 'Sub item', onClick: () => sub() }
    ]}
  ]);

  // Set global defaults
  Fnon.Init({ isDark: true, dir: 'rtl', fontFamily: 'Arial, sans-serif' });
</script>
```

---

# Fnon Global API

The `Fnon` object is the single entry point. It exposes:

| Property | Type | Description |
|----------|------|-------------|
| `Alert` | object | Pre-themed alert factories (7 built-in themes + custom) |
| `Ask` | object | Pre-themed ask/confirm factories |
| `Dialogue` | object | Pre-themed dialogue factories |
| `Breaking` | object | Pre-themed breaking bar factories |
| `Hint` | object | Pre-themed hint/toast factories |
| `Wait` | object | Full-page spinner methods |
| `Box` | object | Container-targeted spinner methods |
| `DatePicker(options?)` | function | Date picker modal (alias: `DP`) |
| `ContextMenu(event, items, onClick?)` | function | Context menu (alias: `CM`) |
| `AddLogo(name, html)` | function | Register custom spinner |
| `AddTheme(name, config)` | function | Register custom theme |
| `Init(config)` | function | Set shared defaults across all subsystems |
| `setRenderer(fn)` | function | Register custom content renderer (for React etc.) |
| `registerCleanup(id, fn)` | function | Register modal cleanup callback |
| `registerReactRoot(id, root)` | function | Store React root for unmounting |

---

# Modal System

# ⏹️ Alert


A single-OK-button modal.

```ts
Fnon.Alert.ThemeName(
  first: FnonContent | ModalOptions,
  title?: string,
  btnOkText?: string,
  callback?: () => void,
  closedCallback?: () => void
): void

Fnon.Alert.Primary("message","title","ok text",()=> console.log('closed'))
Fnon.Alert.Warning("message","title")
Fnon.Alert.Info("message")
Fnon.Alert.Success('Operation completed.', 'Success');
Fnon.Alert.Danger('Something went wrong.', 'Error', 'Retry', () => retry());
```

**Async variant** (returns a Promise that resolves when the user clicks OK):

```ts
Fnon.Alert.ThemeName.Async(
  first: FnonContent | ModalOptions,
  title?: string,
  btnOkText?: string
): Promise<void>

Fnon.Alert.Info.Async("message","title").then(()=> console.log('A'))

// Async
await Fnon.Alert.Primary.Async('Continue?', 'Confirm');
console.log('User clicked OK');
```

# ⏹️ Ask 

A two-button (OK / Cancel) confirmation modal.

```ts
Fnon.Ask.ThemeName(
  first: FnonContent | ModalOptions,
  title?: string,
  btnOkText?: string,
  btnCancelText?: string,
  callback?: (result: boolean) => void,
  cancelCallback?: (result: boolean) => void,
  closedCallback?: () => void
): void
```

**Async variant** (resolves `true` on OK, `false` on Cancel):

```ts
Fnon.Ask.ThemeName.Async(
  first: FnonContent | ModalOptions,
  title?: string,
  btnOkText?: string,
  btnCancelText?: string
): Promise<boolean>
```

**Example:**

```ts
Fnon.Ask.Warning('Are you sure?', 'Confirm Delete', 'Delete', 'Cancel',
  (ok) => { if (ok) deleteItem(); }
);

// Async
if (await Fnon.Ask.Primary.Async('Proceed?', 'Confirm')) {
  proceed();
}
```

### Dialogue

A fully customizable modal with direct access to the modal element and close function.

```ts
Fnon.Dialogue.ThemeName(
  first: FnonContent | ModalOptions,
  title?: string,
  btnOkText?: string,
  btnCancelText?: string,
  callback?: (close: () => void, html: HTMLElement, data: Record<string, unknown>) => boolean | void,
  cancelCallback?: (close: () => void, html: HTMLElement, data: Record<string, unknown>) => boolean | void,
  closedCallback?: () => void
): { close: () => void; modalEl: HTMLElement; id: string }
```

Return `false` from `callback` or `cancelCallback` to **prevent** the modal from closing.

**Async variant** (resolves `true` on OK, `false` on Cancel, `null` if closed via X):

```ts
Fnon.Dialogue.ThemeName.Async(
  first: FnonContent | ModalOptions,
  title?: string,
  btnOkText?: string,
  btnCancelText?: string
): Promise<boolean | null>
```

**Direct call** (without theme prefix):

```ts
Fnon.Dialogue(options: ModalOptions): { close: () => void; modalEl: HTMLElement; id: string }
```

**Example:**

```ts
Fnon.Dialogue.Primary('Choose an option:', 'Custom Dialog',
  'Save', 'Discard',
  (close, el, data) => {
    save();
    close();  // must call close() explicitly
  },
  (close) => {
    close();
  }
);
```

### Breaking Bar

A fixed-position breaking news bar anchored to top or bottom.

```ts
Fnon.Breaking.ThemeName(
  message?: FnonContent,
  titleOrOptions?: string | BreakingOptions
): { close: () => void; el: HTMLElement; id: string }
```

**Direct call:**

```ts
Fnon.Breaking(options: BreakingOptions): { close: () => void; el: HTMLElement; id: string }
```

**Async variant** (resolves when bar closes):

```ts
Fnon.Breaking.ThemeName.Async(
  message?: FnonContent,
  titleOrOptions?: string | BreakingOptions
): Promise<void>
```

**BreakingOptions:**

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `message` | `FnonContent` | — | Body text |
| `headline` | `string` | `'BREAKING'` | Headline badge text |
| `theme` | `ThemeName` | `'Danger'` | Theme to apply |
| `anchor` | `'top' \| 'bottom'` | `'top'` | Bar position |
| `buttons` | `ButtonDef[]` | — | Custom action buttons |
| `timeout` | `number` | `5000` | Auto-dismiss (ms), 0 = no dismiss |
| `closable` | `boolean` | `true` | Show close button |
| `onClose` | `() => void` | — | Close callback |
| `appendTarget` | `string \| HTMLElement` | `'body'` | Target container |
| `fontFamily` | `string` | `'Quicksand', sans-serif` | Font family |

### Modal Options

All options accepted by `createModal()` (the core modal builder used by Alert, Ask, Dialogue):

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `message` | `FnonContent` | — | Modal body content |
| `title` | `FnonContent` | — | Title bar content (string, HTML string, or HTMLElement) |
| `buttons` | `ButtonDef[]` | — | Custom button definitions |
| `btnOkText` | `string` | — | Text for OK button |
| `btnCancelText` | `string` | — | Text for Cancel button |
| `btnOkStyle` | `string` | — | CSS class suffix for OK button (e.g. `'Primary'`, `'Danger'`) |
| `btnCancelStyle` | `string` | — | CSS class suffix for Cancel button |
| `showClose` | `boolean` | `true` | Show X close button in title bar |
| `closeButton` | `boolean` | `true` | Legacy alias for `showClose` |
| `showMin` | `boolean` | `false` | Show minimize button |
| `showMaxMin` | `boolean` | `false` | Show maximize/restore buttons |
| `data` | `Record<string, unknown>` | — | Shared mutable data passed to all callbacks |
| `userData` | `Record<string, unknown>` | — | Legacy alias for `data` |
| `autoFocus` | `boolean` | `false` | Auto-focus first focusable element |
| `autoFocusTag` | `string` | `'input:not([disabled])'` | Selector for auto-focus target |
| `autoFocusDelay` | `number` | `0` | Delay before auto-focus (ms) |
| `appendTarget` | `string \| HTMLElement` | `'body'` | Where to append the modal |
| `layout` | `string` | `'hbf'` | Section order: `h`=title, `b`=body, `f`=footer |
| `width` | `string` | — | Width variant (`'sm'`, `'nl'`, `'lg'`, `'xl'`, `'fl'`) |
| `anchor` | `'top' \| 'bottom'` | — | Vertical anchoring |
| `fontFamily` | `string` | `'Quicksand', sans-serif` | Font family |
| `animation` | `string` | `'slide-top'` | Entrance animation |
| `icon` | `string` | — | Icon HTML (shown in body) |
| `iconColor` | `string` | — | Icon color |
| `showIcon` | `boolean` | `false` | Show icon section |
| `titleColor` | `string` | — | Title text color |
| `titleBackground` | `string` | — | Title bar background |
| `color` | `string` | `'#2b2b2b'` | Body text color |
| `background` | `string` | `'rgba(0,0,0,0.4)'` | Overlay background |
| `zIndex` | `number` | `4000` | CSS z-index for overlay |
| `btnOkColor` | `string` | — | OK button text color |
| `btnOkBackground` | `string` | — | OK button background |
| `btnOkShadow` | `string` | — | OK button box-shadow |
| `btnOkBorderColor` | `string` | — | OK button border color |
| `btnCancelColor` | `string` | — | Cancel button text color |
| `btnCancelBackground` | `string` | — | Cancel button background |
| `btnCancelShadow` | `string` | — | Cancel button box-shadow |
| `btnCancelBorderColor` | `string` | — | Cancel button border color |
| `delButtons` | `boolean` | `false` | Remove all default buttons |
| `isDark` | `boolean \| (() => boolean)` | — | Enable dark mode |
| `darkBgColor` | `string` | `'#1a202c'` | Dark mode background color |
| `dir` | `'ltr' \| 'rtl'` | `'ltr'` | Text direction |

### Button Definition

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `text` | `string` | — | Button label |
| `style` | `string` | `'Primary'` | CSS class: `'Primary'`, `'Danger'`, `'Success'`, etc. |
| `callback` | `(close, html, data) => boolean \| void` | — | Click handler. Return `false` to prevent modal from closing |
| `color` | `string` | — | Custom text color |
| `background` | `string` | — | Custom background |
| `boxShadow` | `string` | — | Custom box-shadow |
| `shadow` | `string` | — | Alias for `boxShadow` |
| `borderColor` | `string` | — | Custom border color |
| `css` | `string` | — | Additional CSS classes |

**Custom buttons example:**

```ts
Fnon.Dialogue({
  message: 'What would you like to do?',
  buttons: [
    { text: 'Save', style: 'Success', callback: (close) => { save(); close(); } },
    { text: 'Delete', style: 'Danger', callback: (close) => { deleteItem(); close(); } },
    { text: 'Cancel', style: 'Light' },  // calls close() automatically
  ]
});
```

When `delButtons: true` is set in modal options, only custom buttons are shown (default OK/Cancel are removed).

### Modal Data (Shared State)

The `data` option lets you pass a mutable object into the modal that is accessible across all lifecycle hooks and button callbacks. This is useful for passing state between your code and the modal's event cycle.

```ts
const myData = { userId: 42, mode: 'edit' };

Fnon.Ask.Primary('Update this record?', 'Confirm', 'Yes', 'No',
  (result) => {
    // result is true/false (Ask behavior)
  },
  null,
  null,
  { data: myData }  // <-- last argument is ModalOptions
);

// Or via Dialogue for full access:
Fnon.Dialogue({
  message: 'Edit user?',
  data: { userId: 42, timestamp: Date.now() },
  buttons: [
    {
      text: 'Save',
      callback: (close, html, data) => {
        // data = { userId: 42, timestamp: ... }
        console.log('Saving user', data.userId);
        close();
      }
    }
  ],
  onOpen: (el, data) => {
    console.log('Modal opened for user', data.userId);
  },
  onClosed: (data) => {
    console.log('Modal closed for user', data.userId);
  }
});
```

The `data` object is shared — mutations made in one callback are visible in all subsequent callbacks.

### Lifecycle Hooks

| Hook | Signature | Timing |
|------|-----------|--------|
| `onInit` | `(modalEl, data) => void` | After element creation, before DOM append |
| `onOpen` | `(modalEl, data) => void` | After DOM append and animation start |
| `onClose` | `(data) => void` | When close is triggered |
| `onClosing` | `(modalEl, data) => void` | During close animation |
| `onClosed` | `(data) => void` | After element removed from DOM |
| `beforeShow` | `(modalEl, data) => void` | Before animation starts |
| `afterShow` | `(modalEl, data) => void` | After animation completes |
| `defaultBefore` | `(modalEl, data) => void` | Universal pre-show hook |
| `defaultAfter` | `(modalEl, data) => void` | Universal post-show hook |
| `callback` | varies | Theme-specific (OK button click) |
| `cancelCallback` | varies | Theme-specific (Cancel button click) |

### Layout System

Control the order of title bar (`h`), body (`b`), and footer (`f`) via the `layout` string.  
Default: `'hbf'`. Example: `'fbh'` puts footer first.

### Width Variants

| Class | max-width |
|-------|-----------|
| `.fnon-modal-sm` | 360px |
| `.fnon-modal-nl` | 480px (default) |
| `.fnon-modal-lg` | 640px |
| `.fnon-modal-xl` | 800px |
| `.fnon-modal-fl` | Full-width (fixed) |

---

## Hint / Toast System

A notification toast that auto-dismisses. Positioned via named positions.

```ts
Fnon.Hint.ThemeName(
  message: string,
  titleOrOptions?: string | HintOptions
): { close: () => void }
```

**Async variant:**

```ts
Fnon.Hint.ThemeName.Async(
  message: string,
  titleOrOptions?: string | HintOptions
): Promise<void>
```

**Built-in positions:**

`right-top` (default), `right-center`, `right-bottom`, `left-top`, `left-center`, `left-bottom`, `center-top`, `center-center`, `center-bottom`

**Example:**

```ts
Fnon.Hint.Success('Saved!', 'Success');
Fnon.Hint.Danger('Connection lost.', { position: 'center-center', timeOut: 0 });
```

### Hint Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `type` | `string` | `'Primary'` | Theme type |
| `title` | `FnonContent` | — | Title content |
| `position` | `string` | `'right-top'` | Screen position |
| `animation` | `string` | `'slide-left'` | Entrance animation |
| `closable` | `boolean` | `true` | Show close button |
| `timeOut` | `number` | `4000` | Auto-dismiss (ms), 0 = no dismiss |
| `appendTo` | `string` | — | Container selector |
| `onClose` | `() => void` | — | Dismiss callback |
| `onclosed` | `() => void` | — | Post-removal callback |
| `fontFamily` | `string` | `'Quicksand', sans-serif` | Font |
| `spacing` | `string` | `'8px'` | Margin between hints |
| `svgSize` | `{ w: number, h: number }` | `{ w: 20, h: 20 }` | Icon dimensions |
| `fontSize` | `string` | `'0.88rem'` | Text font size |
| `width` | `string` | `'360px'` | Hint width |
| `animationDuration` | `string` | `'0.3s'` | Animation speed |
| `progressColor` | `string` | — | Progress bar color |
| `isDark` | `boolean \| (() => boolean)` | — | Dark mode |
| `darkBgColor` | `string` | — | Dark background |
| `dir` | `'ltr' \| 'rtl'` | `'ltr'` | Text direction |

### Hint Themes

Built-in hint themes and their colors:

| Theme | Title Color | Background | Border |
|-------|------------|------------|--------|
| Primary | `#029eff` | `#ebf8ff` | `#029eff` |
| Success | `#39DA8A` | `#f0fff4` | `#39DA8A` |
| Warning | `#fdd347` | `#fffbe5` | `#fdd347` |
| Danger | `#ff6b6b` | `#fff5f5` | `#ff6b6b` |
| Info | `#48dbfb` | `#e5faff` | `#48dbfb` |
| Light | `#2d3748` | `#f8f9fa` | `#ced4da` |
| Dark | `#fff` | `#222f3e` | `#222f3e` |

Add custom hint themes via `Fnon.AddTheme(name, config)` — hint theming is derived automatically from the core theme.

---

## Spinner System

### Wait (Full-page)

A centered overlay spinner covering the entire viewport. Only one active at a time.

```ts
Fnon.Wait.SpinnerName(
  text?: string,
  options?: SpinnerOptions
): void
```

**Async variant** (resolves when `Wait.Remove()` is called):

```ts
Fnon.Wait.SpinnerName.Async(
  text?: string,
  options?: SpinnerOptions
): Promise<boolean>
```

**Control methods:**

| Method | Description |
|--------|-------------|
| `Fnon.Wait.Remove(delay?: number)` | Remove overlay (with optional delay in ms) |
| `Fnon.Wait.Change(text: string)` | Update the displayed text |

**Example:**

```ts
Fnon.Wait.Orbit('Connecting...');
await fetch(url);
Fnon.Wait.Remove();

// Async
const result = await Fnon.Wait.Spin.Async('Working...');
// result = true when Remove() is called
```

### Box (Container)

A spinner overlay inside a specific container element.

```ts
Fnon.Box.SpinnerName(
  selector: string | HTMLElement,
  text?: string,
  options?: BoxOptions
): void
```

**Async variant:**

```ts
Fnon.Box.SpinnerName.Async(
  selector: string | HTMLElement,
  text?: string,
  options?: BoxOptions
): Promise<boolean>
```

**Control methods:**

| Method | Description |
|--------|-------------|
| `Fnon.Box.Remove(selector: string \| HTMLElement)` | Remove overlay from container |

**Example:**

```ts
Fnon.Box.Spin('#my-div', 'Loading...');

// Remove later
Fnon.Box.Remove('#my-div');
```

### Spinner Options

| Option | Type | Wait Default | Box Default | Description |
|--------|------|-------------|-------------|-------------|
| `text` | `string` | `''` | `''` | Display text below spinner |
| `clickToClose` | `boolean` | `false` | — | Click overlay to close |
| `svgColor` | `string` | `'#029eff'` | `'#029eff'` | Spinner color |
| `appendTarget` | `string` | — | — | Where to append (Wait only) |
| `fontFamily` | `string` | `'Quicksand', sans-serif` | `'Quicksand', sans-serif` | Font |
| `containerSize` | `string` | — | — | Spinner container dimensions |
| `svgSize` | `{ w, h }` | — | — | SVG dimensions (Box only) |
| `isDark` | `boolean \| (() => boolean)` | — | — | Dark mode |
| `darkBgColor` | `string` | — | — | Dark background |
| `dir` | `'ltr' \| 'rtl'` | — | — | Text direction |

### Available Spinners

| Name | Type | Description |
|------|------|-------------|
| `Orbit` | CSS-only | Orbiting dot with ring animation |
| `Spin` | CSS-only | Rotating ring |
| `Infinity` | SVG | Figure-eight infinite loop path |
| `Ripple` | SVG | Expanding concentric circles |
| `Typing` | SVG | Bouncing typing indicator dots |

**Custom spinners** via `Fnon.AddLogo(name, htmlString)`:

```ts
Fnon.AddLogo('MyLogo', '<svg>...</svg>');
Fnon.Wait.MyLogo('Custom spinner');
Fnon.Box.MyLogo('#target', 'Custom');
```

---

## DatePicker

A calendar modal that returns a Promise with the selected date(s).

```ts
Fnon.DatePicker(options?: DatePickerOptions): Promise<Date | { start: Date; end: Date } | null>
```

**Alias:** `Fnon.DP(options?)`

**Usage:**

```ts
// Single date
const date = await Fnon.DatePicker({ initDate: new Date() });
if (date) console.log('Selected:', date);

// Date range (dateRange: true)
const range = await Fnon.DatePicker({ dateRange: true });
if (range) console.log('From:', range.start, 'To:', range.end);

// With time picker
const dt = await Fnon.DatePicker({ withTime: true });

// With constraints
const d = await Fnon.DatePicker({
  minDate: new Date(2024, 0, 1),
  maxDate: new Date(2025, 11, 31)
});
```

The Promise resolves `null` if the user closes the modal without selecting (X button / Escape).

### DatePicker Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `initDate` | `Date` | `new Date()` | Initial focused date |
| `dateRange` | `boolean` | `false` | Enable date range selection (returns `{ start, end }`) |
| `withTime` | `boolean` | `false` | Show time input (ignored if `dateRange` is `true`) |
| `minDate` | `Date` | — | Earliest selectable date |
| `maxDate` | `Date` | — | Latest selectable date |
| `title` | `FnonContent` | — | Modal title |
| `isDark` | `boolean \| (() => boolean)` | — | Dark mode |
| `darkBgColor` | `string` | — | Dark background |
| `dir` | `'ltr' \| 'rtl'` | `'ltr'` | Text direction |

### Return Value

| Mode | Resolves to | Rejects when |
|------|-------------|--------------|
| Single date | `Date \| null` | Modal closed without selection |
| Date range | `{ start: Date, end: Date } \| null` | Modal closed without selection |

### DatePicker CSS Properties

All `--dp-*` variables are scoped to `.fnon-dp-popup` (light and dark variants).

| Property | Light Default | Dark Default |
|----------|--------------|--------------|
| `--dp-accent` | `#029eff` | `#0284c7` |
| `--dp-bg` | `#fff` | `#0f172a` |
| `--dp-text` | `#2d3748` | `#e2e8f0` |
| `--dp-border` | `#e2e8f0` | `#334155` |
| `--dp-hover` | `#ebf8ff` | `#1e293b` |
| `--dp-other` | `#a0aec0` | `#64748b` |
| `--dp-disabled` | `#e2e8f0` | `#334155` |
| `--dp-selected` | `#029eff` | `#0284c7` |
| `--dp-selected-text` | `#fff` | `#fff` |
| `--dp-range` | `#ebf8ff` | `#1e3a5f` |
| `--dp-popup-bg` | `#fff` | `#1e293b` |
| `--dp-popup-shadow` | `0 8px 25px rgba(0,0,0,.12)` | `0 8px 25px rgba(0,0,0,.4)` |
| `--dp-today-ring` | `#029eff` | `#38bdf8` |

---

## Context Menu

A right-click (or click) context menu with nested submenus, keyboard navigation, and Chrome-like styling.

```ts
Fnon.ContextMenu(
  event: MouseEvent | { x: number; y: number },
  items: ContextMenuItem[],
  globalOnClick?: (item: ContextMenuItem, event: MouseEvent) => void
): { close: () => void }
```

**Alias:** `Fnon.CM(event, items, onClick?)`

### ContextMenuItem

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `text` | `FnonContent` | — | Display content (string, HTML string, or HTMLElement) |
| `onClick` | `(item, event) => void` | — | Click handler (required if no submenu `items`) |
| `items` | `ContextMenuItem[]` | — | Submenu items (takes priority over `onClick`) |
| `className` | `string` | — | Additional CSS class |
| `disabled` | `boolean` | `false` | Disabled state (dimmed, no click) |

**`globalOnClick`** fires only for items that do **not** have their own `onClick`.

**Examples:**

```ts
document.addEventListener('contextmenu', (e) => {
  Fnon.ContextMenu(e, [
    { text: 'Open', onClick: () => open() },
    { text: 'Edit', onClick: () => edit() },
    { text: 'Share', items: [
      { text: 'Email', onClick: () => share('email') },
      { text: 'Copy Link', onClick: () => share('link') },
    ]},
    { text: 'Delete', disabled: true },
  ]);
});

// Left-click trigger (use an anchor point)
Fnon.ContextMenu({ x: 100, y: 200 }, [
  { text: 'Option 1', onClick: () => fn1() },
  { text: 'Option 2', onClick: () => fn2() },
], (item) => console.log('Global click:', item.text));
```

### Context Menu Keyboard Navigation

| Key | Action |
|-----|--------|
| `ArrowDown` | Next item |
| `ArrowUp` | Previous item |
| `ArrowRight` | Open submenu |
| `ArrowLeft` | Close submenu |
| `Enter` / `Space` | Select item (or open submenu) |
| `Escape` | Close submenu (or whole menu) |

Click outside, right-click outside, scroll, and resize all dismiss the menu.

---

## Dark Mode

Every subsystem supports dark mode via the `isDark` option and `Fnon.Init()`.

### Enabling dark mode

```ts
// Globally via Init()
Fnon.Init({ isDark: true });

// Per-instance
Fnon.Alert.Primary('Message', 'Title', { isDark: true });
Fnon.Hint.Success('Done', { isDark: true });
Fnon.DatePicker({ isDark: true });
```

The `isDark` can also be a **function** `() => boolean` — evaluated once at creation time:

```ts
Fnon.Init({ isDark: () => localStorage.getItem('theme') === 'dark' });
```

### Dark behavior by subsystem

| Subsystem | Dark effect |
|-----------|-------------|
| Modals | Background set to `darkBgColor` (#1a202c), text to `#e2e8f0`, footer border to `#2d3748` |
| Hint | Background set to `darkBgColor`, text color adapted |
| Wait/Box | Overlay gets darker opacity, inner container uses `darkBgColor` |
| DatePicker | All `--dp-*` vars switch to dark palette |
| ContextMenu | `.fnon-cm-dark` class applied with dark colors |
| Breaking | Excluded from dark mode (theme-driven) |

**Important:** Only wrapper backgrounds are changed — user content inside modals is not force-colored.

---

## RTL Support

Every subsystem supports right-to-left text direction via the `dir` option. Set it globally via `Fnon.Init()` or per-instance.

```ts
// Globally via Init()
Fnon.Init({ dir: 'rtl' });

// Per-instance
Fnon.Alert.Primary('Message', 'Title', { dir: 'rtl' });
Fnon.Hint.Success('Done', { dir: 'rtl' });
Fnon.DatePicker({ dir: 'rtl' });
```

### What gets flipped

| Subsystem | RTL effect |
|-----------|------------|
| Modals | Controls (min/max/close) order reversed, drag uses `right` instead of `left` |
| Hints | Positions `right-*` ↔ `left-*`, border accent on right side, progress bar anchors right, slide animations reverse |
| ContextMenu | Submenus open to the left, arrow icons point left, keyboard arrows swap (`ArrowRight` closes, `ArrowLeft` opens) |
| DatePicker | Navigation chevrons swap direction |

`Fnon.Init({ dir: 'rtl' })` sets `document.documentElement.dir = "rtl"`, which cascades to all components.

---

## Theme System

Seven built-in themes, each with defined colors for title bar, buttons, and backgrounds.

### Built-in Themes

| Theme | titleBackground | titleColor | btnOkBackground | btnOkColor | backgroundColor |
|-------|---------------|-----------|----------------|-----------|----------------|
| Primary | `#029eff` | `#fff` | `#029eff` | `#fff` | `#fff` |
| Success | `#39DA8A` | `#fff` | `#39DA8A` | `#fff` | `#fff` |
| Warning | `#fdd347` | `#2d3748` | `#fdd347` | `#2d3748` | `#fff` |
| Danger | `#ff6b6b` | `#fff` | `#ff6b6b` | `#fff` | `#fff` |
| Info | `#48dbfb` | `#2d3748` | `#48dbfb` | `#2d3748` | `#fff` |
| Light | `#f8f9fa` | `#2d3748` | `#f8f9fa` | `#2d3748` | `#fff` |
| Dark | `#222f3e` | `#fff` | `#222f3e` | `#fff` | `#fff` |

### Custom Themes

```ts
Fnon.AddTheme('MyTheme', {
  titleBackground: '#7c3aed',
  titleColor: '#fff',
  btnOkBackground: '#7c3aed',
  btnOkColor: '#fff',
  btnOkBorderColor: '#7c3aed',
  backgroundColor: '#faf5ff',
});

// Then use it:
Fnon.Alert.MyTheme('Custom themed modal!');
Fnon.Ask.MyTheme.Async('Confirm?');
```

This automatically registers the theme for: Alert, Ask, Dialogue, Breaking, and Hint.

### Theme Colors

| Option | Type | Description |
|--------|------|-------------|
| `titleBackground` | `string` | Title bar background color |
| `titleColor` | `string` | Title bar text color |
| `btnOkBackground` | `string` | OK button background color |
| `btnOkColor` | `string` | OK button text color |
| `btnOkBorderColor` | `string` | OK button border color |
| `backgroundColor` | `string` | Modal body background color |
| `[key: string]` | `string` | Any additional custom values |

---

## CSS Custom Properties

### Root-level

Set these on `:root` to customize globally:

| Property | Default | Description |
|----------|---------|-------------|
| `--fnon-font` | `'Quicksand', sans-serif` | Base font |
| `--fnon-z-overlay` | `4000` | Overlay z-index |
| `--fnon-z-modal` | `4001` | Modal z-index |
| `--fnon-z-hint` | `5000` | Hint z-index |
| `--fnon-z-wait` | `6000` | Wait overlay z-index |
| `--fnon-radius` | `10px` | Border radius |
| `--fnon-transition` | `0.25s ease` | Default transition |

### Theme-injected (per instance via CSS vars on modal element)

| Property | Source |
|----------|--------|
| `--fnon-title-background` | `theme.titleBackground` |
| `--fnon-title-color` | `theme.titleColor` |
| `--fnon-btn-ok-background` | `theme.btnOkBackground` |
| `--fnon-btn-ok-color` | `theme.btnOkColor` |
| `--fnon-btn-ok-border-color` | `theme.btnOkBorderColor` |
| `--fnon-background-color` | `theme.backgroundColor` |
| `--fnon-body-color` | `opts.color` override |
| `--fnon-bg` | Dark mode `darkBgColor` |

### Spinner

| Property | Description |
|----------|-------------|
| `--fnon-sp-color` | Spinner color |
| `--fnon-sp-size` | Spinner container size (default 48px) |

### Hint

| Property | Description |
|----------|-------------|
| `--fnon-icon-w` | Hint icon width |
| `--fnon-icon-h` | Hint icon height |
| `--display-dur` | Progress bar animation duration |

### Box

| Property | Description |
|----------|-------------|
| `--fnon-box-svg-w` | Box SVG width |
| `--fnon-box-svg-h` | Box SVG height |

---

## CSS Classes Reference

### Overlay & Modal

| Class | Purpose |
|-------|---------|
| `.fnon-overlay` | Modal backdrop |
| `.fnon-overlay-hidden` | Hidden overlay (minimized) |
| `.fnon-modal` | Modal dialog |
| `.fnon-modal.fnon-active` | Visible modal |
| `.fnon-modal.fnon-maximized` | Fullscreen |
| `.fnon-modal.fnon-minimized` | Hidden (minimized) |
| `.fnon-modal-title` | Title bar |
| `.fnon-modal-title-text` | Title text span |
| `.fnon-modal-controls` | Window control buttons |
| `.fnon-modal-body` | Body content |
| `.fnon-modal-icon` | Icon div |
| `.fnon-modal-footer` | Footer (buttons) |
| `.fnon-dragging` | Applied during drag |
| `.fnon-theme-{name}` | Theme class |
| `.fnon-task-bar` | Minimized modal task bar |
| `.fnon-task-bar-btn` | Task bar restore button |

### Buttons

| Class | Purpose |
|-------|---------|
| `.fnon-btn` | Base button |
| `.fnon-btn-light` | Light theme |
| `.fnon-btn-danger` | Danger theme |
| `.fnon-btn-success` | Success theme |
| `.fnon-btn-warning` | Warning theme |
| `.fnon-btn-info` | Info theme |
| `.fnon-btn-dark` | Dark theme |

### Breaking Bar

| Class | Purpose |
|-------|---------|
| `.fnon-breaking` | Breaking bar |
| `.fnon-breaking-top` | Anchored to top |
| `.fnon-breaking-bottom` | Anchored to bottom |
| `.fnon-breaking-active` | Visible state |
| `.fnon-breaking-inner` | Flex container |
| `.fnon-breaking-headline` | Headline badge |
| `.fnon-breaking-body` | Message body |
| `.fnon-breaking-buttons` | Buttons container |
| `.fnon-breaking-close` | Close button |

### Hint / Toast

| Class | Purpose |
|-------|---------|
| `.fnon-hint-container` | Positioned container |
| `.fnon-hint-container.{position}` | e.g. `right-top` |
| `.fnon-hint` | Hint element |
| `.fnon-hint-active` | Visible state |
| `.fnon-hint-icon` | Icon container |
| `.fnon-hint-content` | Text content |
| `.fnon-hint-title` | Title text |
| `.fnon-hint-message` | Message text |
| `.fnon-hint-close` | Close button |
| `.fnon-hint-progress` | Progress bar |

### Wait / Box

| Class | Purpose |
|-------|---------|
| `.fnon-wait-overlay` | Full-page overlay |
| `.fnon-wait-active` | Visible overlay |
| `.fnon-wait-box` | Spinner container |
| `.fnon-wait-text` | Text below spinner |
| `.fnon-box-overlay` | Container overlay |
| `.fnon-box-inner` | Box spinner container |
| `.fnon-box-label` | Box text label |

### CSS-only Spinners

| Class | Purpose |
|-------|---------|
| `.fnon-sp-orbit` | Orbit spinner |
| `.fnon-sp-orbit-dot` | Orbit dot |
| `.fnon-sp-spin` | Spin spinner |
| `.fnon-sp-spin-ring` | Spin ring |

### DatePicker

| Class | Purpose |
|-------|---------|
| `.fnon-dp` | DatePicker container |
| `.fnon-dp-dark` | Dark mode |
| `.fnon-dp-header` | Header row |
| `.fnon-dp-nav` | Nav button |
| `.fnon-dp-nav-disabled` | Disabled nav |
| `.fnon-dp-label` | Month/year label |
| `.fnon-dp-time` | Time row |
| `.fnon-dp-time-input` | Time input |
| `.fnon-dp-grid` | Day grid |
| `.fnon-dp-dow` | Day-of-week header |
| `.fnon-dp-day` | Day cell |
| `.fnon-dp-other` | Other month |
| `.fnon-dp-today` | Today |
| `.fnon-dp-disabled` | Disabled |
| `.fnon-dp-selected` | Selected |
| `.fnon-dp-range` | Range |
| `.fnon-dp-range-start` | Range start |
| `.fnon-dp-range-end` | Range end |
| `.fnon-dp-range-preview` | Hover preview |
| `.fnon-dp-actions` | Action buttons |
| `.fnon-dp-btn` | Action button |
| `.fnon-dp-btn-primary` | Primary action |
| `.fnon-dp-popup` | Year/month popup |
| `.fnon-dp-popup-nav` | Popup nav bar |
| `.fnon-dp-popup-nav-btn` | Popup nav button |
| `.fnon-dp-popup-nav-label` | Popup nav label |
| `.fnon-dp-popup-row` | Popup grid row |
| `.fnon-dp-popup-cell` | Popup cell |
| `.fnon-dp-popup-active` | Active cell |

### Context Menu

| Class | Purpose |
|-------|---------|
| `.fnon-cm` | Context menu |
| `.fnon-cm-dark` | Dark mode |
| `.fnon-cm-item` | Menu item |
| `.fnon-cm-item-disabled` | Disabled |
| `.fnon-cm-item-parent` | Has submenu |
| `.fnon-cm-item-text` | Text span |
| `.fnon-cm-arrow` | Submenu arrow |
| `.fnon-cm-submenu` | Submenu |

### Utility

| Class | Purpose |
|-------|---------|
| `.fnon-hidden` | `display: none !important` |

---

## React Integration

Fnon provides a React adapter for rendering React components inside modals and a hook for portal-aware modal factories.

### enableReactRendering

```ts
import { enableReactRendering } from 'fnon';
// or via CDN: Fnon.setRenderer(...)

enableReactRendering(ReactDOM);
// ReactDOM = { createRoot: ReactDOM.createRoot }
```

After calling this, any `FnonContent` that is a React VNode (object with `$$typeof` or `type`) will be rendered via `createRoot()` instead of `innerHTML`. The root is automatically unmounted when the modal closes.

### useFnon Hook

```ts
import { useFnon } from 'fnon';

function App() {
  const { Alert, Ask, Dialogue } = useFnon({ mountRoot: '#fnon-root' });

  return (
    <div id="fnon-root">
      <button onClick={() => Alert.Primary('Hello from React!')}>
        Show Alert
      </button>
    </div>
  );
}
```

Returns modal factories with `appendTarget` automatically set to the portal root, so modals render into a controlled container rather than `document.body`.

---

## Renderer Hook System

Low-level hooks for custom content rendering (used internally by the React adapter).

```ts
Fnon.setRenderer((content, container, modal) => {
  // Return true to indicate you handled rendering
  return false; // fall back to innerHTML
});

Fnon.registerCleanup(modalId, () => {
  // Cleanup logic when modal closes
});

Fnon.registerReactRoot(modalId, root);
```

| Function | Description |
|----------|-------------|
| `setRenderer(fn)` | Register renderer (or `null` to clear) |
| `registerCleanup(id, fn)` | Register per-modal cleanup |
| `registerReactRoot(id, root)` | Store React root for unmounting |

**`RendererFn` signature:**

```ts
(content: FnonContent, container: HTMLElement, modal: Record<string, unknown>) => boolean
```

## Browser Support

Modern browsers (Chrome, Firefox, Safari, Edge). Requires ES2015+.

---

## License

MIT
