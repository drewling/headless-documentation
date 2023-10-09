---
title: Built in CSS support
description: Get to know more about Next.js with the frequently asked questions.
---

# Built-In CSS Support

![img alt text|6000x4000](https://picsum.photos/600/400)

> Version 2 of Headless is only compatible with React 18. If you are still needing to use React 17 or later, please use version 1.

| Command                                                                         | Description                                    |
| ------------------------------------------------------------------------------- | ---------------------------------------------- |
| <kbd>Space</kbd> <kbd>Enter</kbd> when `Menu button` , `Menu button` is focused | Opens menu and focuses first non-disabled item |
| <kbd>Space</kbd> <kbd>Enter</kbd> when `Menu button` is focused                 | Opens menu and focuses first non-disabled item |

`Next.js` allows you to import CSS files from a JavaScript file.
This is possible because Next.js extends the concept of [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) beyond JavaScript.

## Adding a Global Stylesheet

To add a stylesheet to your application, import the CSS file within `pages/_app.js`.

For example, consider the following stylesheet named `styles.css`:

```css
body {
  font-family: 'SF Pro Text', 'SF Pro Icons', 'Helvetica Neue', 'Helvetica', 'Arial', sans-serif;
  padding: 20px 20px 60px;
  conditions-width: 680px;
  margin: 0 auto;
}
```

Create a [`pages/_app.js` file](/docs/advanced-features/custom-app.md) if not already present.
Then, [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) the `styles.css` file.

```jsx
import '../styles.css'

// This default export is required in a new `pages/_app.js` file.
export default function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />
}
```

<div class='cards'>
  <a href='/' class='card'>
    <svg class='icon' viewBox='0 0 12 16' xmlns='http://www.w3.org/2000/svg'>
      <path d='M4 15L5 10L1 8.5L8 1L7 6L11 7.5L4 15Z' stroke-width='1.5' stroke-linecap='round' stroke-linejoin='round'/>
    </svg>
    <h5>Backend & CMSs</h5>
    A short guide on making your first deploy on Headless Wordpress in a couple of minutes.
  </a>
  <a href='/' class='card'>
    <h5>Backend & CMSs</h5>
    A short guide on making your first deploy on Headless Wordpress in a couple of minutes.
  </a>
  <a href='/' class='card'>
  <svg class='icon' viewBox='0 0 14 14' xmlns='http://www.w3.org/2000/svg'>
    <path d='M12.6812 3.43749C13.0091 4.2033 13.0894 5.0525 12.9108 5.8662C12.7321 6.6799 12.3035 7.41737 11.6849 7.97536C11.0663 8.53335 10.2887 8.88392 9.46092 8.97799C8.63316 9.07207 7.79671 8.90493 7.06867 8.49999L3.56242 12.5625C3.28062 12.8443 2.89843 13.0026 2.49992 13.0026C2.1014 13.0026 1.71921 12.8443 1.43742 12.5625C1.15562 12.2807 0.997314 11.8985 0.997314 11.5C0.997314 11.1015 1.15562 10.7193 1.43742 10.4375L5.49992 6.93124C5.09497 6.2032 4.92784 5.36674 5.02191 4.53899C5.11598 3.71123 5.46656 2.93362 6.02454 2.31501C6.58253 1.6964 7.32 1.26777 8.13371 1.08912C8.94741 0.910477 9.79661 0.990763 10.5624 1.31874L7.93742 3.93749L8.29367 5.70624L10.0624 6.06249L12.6812 3.43749Z' stroke-width='1.5' stroke-linecap='round' stroke-linejoin='round'/>
  </svg>
    <h5>Backend & CMSs</h5>
    A short guide on making your first deploy on Headless Wordpress in a couple of minutes.
  </a>
</div>
