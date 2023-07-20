# Electron Forge + React -> Distribution

# Sources
https://dev.to/mandiwise/electron-apps-made-easy-with-create-react-app-and-electron-forge-560e

# Short Version
1.  CRA new application
2.  Modify package.json as per [this](./electronforge/package.json)
3.  Copy [electron.js](./electronforge/electron.js) to public 
4.  npm install
6. pray
7.  ** may have to run npm install --ignore-scripts
8.  ** may have to force make script to run specific architecture x64 as ia32 is currently busted
9.  ** may need to add author and description to package.json
10. ** ffs, the path name is too long??? start builds from c:/projects/whatever, I guess...
11. Fuck it.  Use zip files for now.


# Workflow
1. CRA -> New Application
2. Install electron as a dev dependency
```
npm i -D electron
```
3. Install electron-is-dev
```
npm i electron-is-dev
```
4. Create public/electron.js
```javascript
const path = require("path");

const { app, BrowserWindow } = require("electron");
const isDev = require("electron-is-dev");

function createWindow() {
    // Create the browser window.
    const win = new BrowserWindow({
        width: 800,
        height: 600,
        webPreferences: {
            nodeIntegration: true
        }
    });

    // and load the index.html of the app.
    // win.loadFile("index.html");
    win.loadURL(
        isDev
            ? "http://localhost:3000"
            : `file://${path.join(__dirname, "../build/index.html")}`
    );

    // Open the DevTools.
    if (isDev) {
        win.webContents.openDevTools({ mode: "detach" });
    }
}

// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
// Some APIs can only be used after this event occurs.
app.whenReady().then(createWindow);

// Quit when all windows are closed, except on macOS. There, it's common
// for applications and their menu bar to stay active until the user quits
// explicitly with Cmd + Q.
app.on("window-all-closed", () => {
    if (process.platform !== "darwin") {
        app.quit();
    }
});

app.on("activate", () => {
    // On macOS it's common to re-create a window in the app when the
    // dock icon is clicked and there are no other windows open.
    if (BrowserWindow.getAllWindows().length === 0) {
        createWindow();
    }
});

// In this file you can include the rest of your app's specific main process
// code. You can also put them in separate files and require them here.
```
5. Set electron.js as main entry point in package.json
```json
"main": "public/electron.js",
```
6. Install concurrently and wait-on
```
npm i -D concurrently wait-on
```
7. Set up dev electron script in package.json
```json
    "dev": "concurrently -k \"set BROWSER=none && npm start\" \"npm:electron\"",
    "electron": "wait-on tcp:3000 && electron .",
```
Note that set BROWSER=none is windows specific; mac/linux might need export BROWSER=none or something similar.

8. Install @electron-forge/cli as dev dependency
```
npm install --save-dev @electron-forge/cli
```

9.  run electron-forge import
```
npx electron-forge import
```

10. revise package.json - change start script back to react-scripts start, correct package make, and electron scripts as below:
```json
"start": "react-scripts start",
    "package": "react-scripts build && electron-forge package",
    "make": "react-scripts build && electron-forge make",
    "electron": "wait-on tcp:3000 && electron-forge start",
```

11. Add following to electron.js under the require electron-is-dev line:
```javascript
if (require("electron-squirrel-startup")) {
  app.quit();
}
```

12. Install electron-devtools-installer as dev dependency
```
npm i -D electron-devtools-installer
```

13. Add install script for react devtools to electron.js just above the squirrel startup added above:
```javascript
let installExtension, REACT_DEVELOPER_TOOLS;

if (isDev) {
  const devTools = require("electron-devtools-installer");
  installExtension = devTools.default;
  REACT_DEVELOPER_TOOLS = devTools.REACT_DEVELOPER_TOOLS;
}
```

14.  Add the installation command on app.whenReady in electron.js:
```javascript
if (isDev) {
    installExtension(REACT_DEVELOPER_TOOLS)
      .then(name => console.log(`Added Extension:  ${name}`))
      .catch(error => console.log(`An error occurred: , ${error}`));
  }
```
15. Set homepage for CRA to infer root path in package.json:
```json
"homepage": "./",
```

16. npm run package to build for host env