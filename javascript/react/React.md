# React

Documentation: https://reactjs.org/docs/getting-started.html

## Install

### Build a new React application

From your usual project directory:

```console
npx create-react-app <application name>
```

## Remove unnecessary files

* From `public/`:  Remove all but index.html
* Inside of `index.html`: Remove all references to `favicon.ico`, `logo.png`, `manifest.json`.  Also consider changing the `title` tag and the `meta` `description` tag.
* From `src`: Remove all but `App.js`, `index.css`, and `index.js`. 
* `App.js`:  Replace with the following snippet

```javascript
function App() {
  return (
    <div>
      Hello World
    </div>
  );
}

export default App;

```

* `index.css`:  Delete all content
* `index.js`:  Delete references to `reportWebVitals`
* `package.json`:  Change the name of the project if needed, remove reference to `web-vitals` in the dependencies section.

Run the project to verify you get a Hello World message.