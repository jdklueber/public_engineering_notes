# Tailwind CSS
Documentation: https://tailwindcss.com/docs/

## Install 

See https://tailwindcss.com/docs/installation/framework-guides for other frameworks.

#### Install tailwind

From the console:

```console
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

#### Configure tailwind

Update `tailwind.config.js`

```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./src/**/*.{js,jsx,ts,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

#### Update `index.css`

Add this to `index.css`:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

#### Restart the server and verify that tailwind styling is in place

Update `App.js` to look like this:

```javascript
function App() {
  return (
    <div className="text-4xl">
      Hello World
    </div>
  );
}

export default App;

```

Your hello world should now be quite large and sans-serif.

## Optional Setup

### Forms plugin

If you want to use Tailwind's base styling for your forms, this is the package to install. 

1. Install the package

`npm install -D @tailwindcss/forms`

2. Configure the plugin

Add the plugin to your `tailwind.config.js` file:

```javascript
// tailwind.config.js
module.exports = {
  theme: {
    // ...
  },
  plugins: [
    require('@tailwindcss/forms'),
    // ...
  ],
}
```

No further work need be done to get reasonable styles onto your form elements.  However, Tailwind is always there for the additional customization, so here's a good resource for all the other things that the forms plugin can do:

https://github.com/tailwindlabs/tailwindcss-forms

### Heroicons

Made by the creators of React, this is an excellent resource for tailwind friendly react friendly icons.

```
npm install @heroicons/react
```

Documentation:  https://github.com/tailwindlabs/heroicons

Main page and icon catalog:  https://heroicons.com/

### Styling the `<body>` tag (or other base styles)

In `index.css` you can add the following to change up how certain tags are styled across the board:

```css
//These lines should already be there for Tailwind to work
@tailwind base;
@tailwind components;
@tailwind utilities;

//This is how you add styling to the base elements
@layer base {
    body {
        @apply bg-green-50;
    }
}
```

While this works with any element, I heartily recommend that you only use this for the `body` tag, as that **should** be the only tag you can't access directly inside of React.  (Tailwind's preprocessor doesn't look at `index.html` and so won't catch if you put styles on the `body` tag there.)
