# Electron Notes
## Start a new project
1.  Create a package.json file
```
npm init
```
2.  Install electron
```
npm install --save electron
```
3. Files to create
    * index.js -- all the electron specific code
    * 
4. Call electron CLI to start application
```
    Add a script to package.json:
    "electron": "electron ." 
```
Boilerplate hello world app in ./starter

## Electron Lifecycle
1. Start
2. Create process
3. Ready to work (event: 'ready')
4. ...  
5. Process shuts down

## Make a Window
```javascript
app.on('ready', () => {                        //Set up the event listener
    const mainWindow = new BrowserWindow({
        webPreferences: {
            nodeIntegration: true,  //So the damned electron require works
            contextIsolation: false,
        }
    }); 
    mainWindow.loadURL(`file://${__dirname}/index.html`)  //__dirname is
                                                          // current working directory
});
```

## IPC
1.  Browser Side:  
```javascript
const electron = require("electron"); //NOTE:  Only works inside an electron browser
const { ipcRenderer } = electron;

ipcRenderer.send('channel', data); //send
ipcRenderer.on('channel', (event, data) => {}) //receive
```

2. Electron Side:
```javascript
    ipcMain.on('channel', (event, data) => {}) //receive
    mainWindow.webContents.send('channel', data) //send
```

## Building a menu
* Menu text (header or item) is a _label_
* The main popup of a menu is still a _submenu_
* So, label -> submenu -> label -> submenu/action

1.  grab Menu object from electron variable
```javascript
const {app, BrowserWindow, Menu} = electron;
```
2. Create a template array for the menu
```javascript
const menuTemplate = [
    {
        label: 'File',
        submenu: [
            {
                label: 'Quit',
                accelerator: process.platform === 'darwin' ? 'Command+Q' : 'Ctrl+Q',
                click() {
                    app.quit();
                }
            }
        ]
    }
]

if (process.platform === 'darwin') {
    menuTemplate.unshift({}); //the application menu on macos
}
```
3. Apply menu to application (probably in the on ready event)
```javascript
    const mainMenu = Menu.buildFromTemplate(menuTemplate);
    Menu.setApplicationMenu(mainMenu);
```

## Making a second window
1. Make sure that closing the main window ends the application.
```javascript
mainWindow.on('closed', ()=>{app.quit()});
```
2. Otherwise, same as making the main menu.
3. When closing, be sure to mark for gc:
```javascript
    addWindow.on('closed', () => {
        addWindow = null;
    })
```

## Making a debug mode menu
```javascript
if (process.env.NODE_ENV !== 'production') {
    menuTemplate.push({
        label: 'DEVELOPER',
        submenu: [
            {role: 'reload'},
            {role: 'toggleDevTools '}
        ]
    })
}
```