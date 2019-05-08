# Skeleton Screens Concept

[![Skeleton Screens Concept example screenshot](fixtures/example.png)](https://dimitrinicolas.github.io/skeleton-screens-concept)

## [Example](https://dimitrinicolas.github.io/skeleton-screens-concept)

An example is available at the following address:
[dimitrinicolas.github.io/skeleton-screens-concept](https://dimitrinicolas.github.io/skeleton-screens-concept).

I made sure that the style sheet weighs at least 100 kb for the example by
adding random strings inside. You can use your browser developper tools to
simulate a slow internet connection.

The source code is available in this repository: [index.html](index.html).

## Concept

First, we load the application style sheet as a non-blocking resource using the
following pattern in the HTML `head` tag:

```html
<link rel="preload" href="style.css" as="style" onload="this.rel = 'stylesheet';">
<noscript>
  <link rel="stylesheet" href="style.css">
</noscript>
```

Then, we add some CSS inside a `style` tag just before the non-blocking style
sheet loading:

```html
<head>
  <!-- Any head things -->

  <style>
    /** Standard style of the application */
    html {
      background-color: white;
    }
    body {
      margin: 0;
    }

    /** Skeleton style element */
    .header {
      height: 80px;
      background-color: blue;
    }
    .header__content {
      display: none;
    }

    .footer {
      display: none;
    }
  </style>

  <!-- Non-blocking style sheet loading -->
  <link rel="preload" href="style.css" as="style" onload="this.rel = 'stylesheet';">
  <noscript>
    <link rel="stylesheet" href="style.css">
  </noscript>
</head>
```

Then, we need to crush some pre-loading style by adding `display: block` to
`.header__content` and `.footer` in our `style.css` file.

### Animations

We could add skeleton blocks and highlighting animations in our pre-loading
style using `before` and `after` pseudo-elements and CSS animations.

To create a fake logo block inside the header, we can add the following style:

```css
.header__wrap::before {
  content: "";

  position: absolute;
  top: 16px;
  left: 0;
  bottom: 16px;

  width: 100px;

  background-color: #efefef;
}
```

Then we can add an highlight sweeping animation using like that:

```css
.header__wrap::before {
  /* Block style */

  background-image:
    linear-gradient(to right, transparent, rgba(255, 255, 255, 0.6) 50%, transparent 100%);

  background-repeat: no-repeat;
  background-size: 80px 100%;
  background-position: -80px 0;

  animation: sweep 1000ms ease-in-out 0s infinite;
}

@keyframes sweep {
  to {
    background-position: calc(100% + 80px) 0;
  }
}
```

## Known issues

When loading the application style as a non-blocking resource, any element with
a `transition` CSS property will get triggered on style sheet load if its
animated properties values are different from the default or from the
pre-loader style.

To prevent this problem to trigger transition on every properties of our element
we can target properties to animate using the `transition-duration` and
`transition-property` CSS properties. You can learn more about theses on Mozilla
Web documentation:
[developer.mozilla.org/fr/docs/Web/CSS/transition-property](https://developer.mozilla.org/fr/docs/Web/CSS/transition-property).

To totally prevent this issue we'll need to add a specific class name to the
`html` tag like `.js-can-use-transitions` on style loading after a small time
and use the `transition` property in our style only when this class name has
been added:

In `head` tag:

```html
<link
 rel="preload"
 href="style.css"
 as="style"
 onload="this.rel = 'stylesheet'; setTimeout(function () { document.documentElement.className += ' js-can-use-transitions';}, 100);"
>
<noscript>
  <link rel="stylesheet" href="style.css">
</noscript>
```

In `style.css`:

```css
.js-can-use-transitions .header {
  transition: background-color 300ms;
}
```

This solution is not ideal, the user will need to have JavaScript enabled to
have CSS transitions and the time to wait before adding the class name to the
`html` element depends on the user device computation performance. The 100
milliseconds value is the maximum average time the browser will need to compute
the style sheet.

## Use Case

This approach of skeleton screens is usefull for Single Page Applications with a
heavy style sheet.

## Frameworks implementation

I need to find solutions to use this concept with static website frameworks like
[Gatsby](https://github.com/gatsbyjs/gatsby) or [Next.js](https://github.com/zeit/next.js/).

## License

This project is licensed under the [MIT license](LICENSE).