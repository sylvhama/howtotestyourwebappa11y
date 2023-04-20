---
layout: center
class: text-center
transition: slide-left
theme: default
---

# How to test your web app accessibility?
by [Sylvain Hamann](https://shamann.dev)

---
layout: quote
---

# What's Web Accessibility?

It's the principle of designing and developing websites in a way that ensures they can be used by anyone, regardless of their abilities or disabilities, and regardless of the technology they use to access the web.

---
layout: center
---

# MANUAL AUDIT

---
layout: iframe-right
url: https://en.wikipedia.org/wiki/Screen_reader
---

## Keyboard only navigation

- Via tab or shift+tab;
- Look for visual feedback (e.g. focus ring);
- I should be able to do the exact same actions as a mouse user.

---

## Zoom

- Try different types of zoom and magnifiers;
- WCAG recommends to support 200% zoom.

<v-click>
  <div class="grid grid-cols-2 gap-4">
    <div>
      <p>Default zoom</p>
      <img alt="Screenshot of the default Firefox zoom set to 200% on Wikipedia" src="/zoom-default.png" />
    </div>
    <div>
      <p>Text only zoom</p>
      <img alt="Screenshot of the text only Firefox zoom set to 200% on Wikipedia" src="/zoom-text.png" />
    </div>
  </div>
</v-click>

---

## Screen Readers

A screen reader is a software application that converts on-screen text and visual elements into spoken words or Braille output, allowing visually impaired or blind individuals to use computers and mobile devices.

<p style="display: grid; place-items:center;"><img alt="A pie chart showing the top 3 screen readers: NVDA, JAWS and VoiceOver" style="padding: 1rem; background-color: white; height: 40vh; object-fit: contain;" src="/chart.png" /></p>
<p>Chart source: <a href="https://assistivlabs.com/">assistivlabs.com</a></p>

---
layout: iframe
url: https://en.wikipedia.org/wiki/Screen_reader
---

---
layout: image-right
image: /responsive.png
---

## Responsive Design

- Mobiles and Tablets also have screen readers!
- Resizing or zooming might trigger the "mobile" layout;
- Each major browser has a Responsive Design Mode.

---

## There are many others assistive technologies

<div class="grid grid-cols-2 gap-4">
  <div>
      <p>High Contrast Mode on Windows</p>
      <img alt="A screenshot of an Assistiv Labs session loading Wikipedia on Windows Edge with the high contrast mode on." src="/high.png" />
      <p>Another one: Reduce screen motion on Mac OS.</p>
  </div>
  <div>
      <p>Switch device</p>
      <img alt="A screenshot of YouTube video showcasing switch control mode on iOS." src="/switch.png" />
      <p>Source: <a href="https://www.youtube.com/watch?v=HBo2BZ-Zzwg">Assistive Tech Stuff</a> on YouTube</p>
  </div>
</div>

---
layout: center
---

# AUTOMATIC TESTS

--- 

# Static testing

- ESLint (see `eslint-plugin-jsx-a11y`) can help you to detect a11y issues while coding;

<p><img alt="Screenshot of an ESLint error in VsCode hilighting a missing alt text when using img tag." src="/eslint.png" /></p>

- You can create custom rules for your Design System;
- TypeScript plus JSDoc could be used to achieve similar purpose;
- This does not test the DOM.

---
layout: two-cols
---

## axe by Deque Systems

<v-click>
  <p>Via a browser extension (axe DevTools)</p>
  <img style="padding-right: 2rem;" alt="A screenshot of Axe devtools results on Firefox. The page HTML and CSS were   hacked on purpose in order to trigger issues such as missing alt text and contrast issues." class="hover-scale" src="/axe.png" />

  - Lighthouse has a similar feature;
  - This is still kind of manual.
</v-click>

::right::

<v-click>

  <h2 aria-hidden="true">‎</h2>
  <p>Via React Testing Library</p>

  ```jsx
  import { render } from "@testing-library/react";
  import { axe } from "jest-axe";
  
  import { Button } from "./Button";
  
  test("Button a11y", async () => {
    const { container } = render(<Button>Click me</Button>);
    const results = await axe(container);
    
    expect(results).toHaveNoViolations();
  });
  ```
  
  - ⚠️ jsdom won't catch contrast issues;
  - Also available for E2E testing libraries such as Cypress or Playwright;
  - It feels redundant.

</v-click>

---

# A11y tests with Storybook

- Storybook is a tool for developing, documenting, and testing UI components in isolation;
- Use [`storybook-addon-a11y`](https://storybook.js.org/docs/react/writing-tests/accessibility-testing) to check the a11y of each story (it relies on axe-core).

<img src="/story.png" alt="A screenshot of a Storybook Button story and its accessibility report.">

---
layout: quote
---

# axe can "only" catch up to [57% of a11y issues](https://www.deque.com/blog/automated-testing-study-identifies-57-percent-of-digital-accessibility-issues/).

---
layout: iframe-right
url: https://a11y-rtl-test.netlify.app/
class: iframe-right
---

# Test keyboard interactions

<v-click>

```js
test('Menu keyboard interaction', async () => {
  render(<Menu />);
  const button = screen.getByRole("button", { name: "Dashboard" });

  expect(button).toHaveAttribute("aria-expanded", "false");

  await userEvent.keyboard("{tab}");
  await userEvent.keyboard("{enter}");

  expect(button).toHaveAttribute("aria-expanded", "true");
  expect(screen.getByRole("menuitem", { name: "Profile" })).toHaveFocus();

  await userEvent.keyboard("{arrowdown}");

  expect(screen.getByRole("menuitem", { name: "My account" })).toHaveFocus();

  await userEvent.keyboard("{Escape}");

  expect(button).toHaveFocus();
  expect(button).toHaveAttribute("aria-expanded", "false");
});
```

</v-click>

---
layout: center
---

# Resources

- [Accessibility Myths](https://a11ymyths.com) by Sergei Kriger
- [Learn Accessibility](https://web.dev/learn/accessibility/) by Carie Fisher
- [A11ycasts](https://www.youtube.com/playlist?list=PLNYkxOF6rcICWx0C9LVWWVqvHlYJyqw7g) with Rob Dodson
- [Testing Accessibility](https://testingaccessibility.com) by Marcy Sutton
- [Practical Accessibility](https://practical-accessibility.today/) with Sara Soueidan

---
layout: center
---

# Conclusion

<v-click>

  - Acknowledge your personal biases;
</v-click>
<v-click>

  - Include a11y audits during design phase, code reviews, bug hunts...
</v-click>
<v-click>

  - Consider all possible interactions in your automatic tests;
</v-click>
<v-click>

  - Seek feedback from individuals with diverse abilities via companies such as [Fable](https://makeitfable.com/) or by hiring them!
</v-click>