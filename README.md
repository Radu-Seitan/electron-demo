# Desktop app with Electron JS

This repository contains the code for setting up an Electron JS application. The application is designed to provide a template for creating desktop applications using Electron.

## Getting Started

To get started with this project and run it, follow the steps below:

1. **Clone the repository:**

   ```bash
   git clone https://github.com/Radu-Seitan/electron-demo.git
   ```

2. **Navigate to the project directory:**

   ```bash
   cd electron-demo
   ```

3. **Install dependencies:**

   ```bash
   npm install
   ```

4. **Run the project:**
   ```bash
   npm run start
   ```

## Step by step approach

This section would detail how the project was generated, in order to serve as a tutorial on how to create a setup for any Electron JS desktop app. In order to run Electron, you need to have [Node.js](https://nodejs.org/en/download/) installed. You can check if Node.js was installed correctly by typing the following commands in your terminal client:

```bash
node -v
```

and

```bash
npm -v
```

Create a new folder with the name of your project. For Linux users:

```bash
mkdir electron-demo
```

### Add a gitignore

You should add a [gitignore](https://git-scm.com/docs/gitignore) to your project. In the project folder, create a file named `.gitignore`. Then, go to this [link](https://www.toptal.com/developers/gitignore) and search for the technologies used in your project. For this demo, only `Node` and `VisualStudioCode` are required. If you use another IDE/text editor, please add it in the list.

After generating the content, copy it and paste it in the `.gitignore` file.

### Electron app init

A more detailed approach can be found [here](https://www.electronjs.org/docs/latest/tutorial/quick-start)

1. **Create a new folder and initialize an npm package (go through the necessary fields and keep in mind that the entry point should be `main.js`)**

   ```bash
   mkdir electron-demo
   cd electron-demo
   npm init
   ```

2. **Install the electron package**

   ```bash
   npm install --save-dev electron
   ```

3. **Create a new file in the root folder named `main.js`**

4. **Add a new script in your `package.json` config**

```
{
    "scripts": {
        "start": "electron ."
    }
}
```

5. **Create a new file in the root folder named `index.html`**

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <!-- https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP -->
    <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self'">
    <title>Hello World!</title>
  </head>
  <body>
    <h1>Hello World!</h1>
    We are using Node.js <span id="node-version"></span>,
    Chromium <span id="chrome-version"></span>,
    and Electron <span id="electron-version"></span>.
  </body>
</html>
```

6. **Add content in your `main.js` file**

```
const { app, BrowserWindow } = require("electron");

const createWindow = () => {
  const win = new BrowserWindow({
    width: 800,
    height: 600,
  });

  win.loadFile("index.html");
};

app.whenReady().then(() => {
  createWindow();

  app.on("activate", () => {
    if (BrowserWindow.getAllWindows().length === 0) createWindow();
  });
});

app.on("window-all-closed", () => {
  if (process.platform !== "darwin") app.quit();
});

```

Now you can run the project using the following command:

```bash
npm run start
```

### Access Node.js from the renderer with a preload script

If you run the project, you see a new window opening, listing the main components of our aplication.Now, the last thing to do is print out the version numbers for Electron and its dependencies onto your page.

Create a new file named `preload.js` as such:

```
window.addEventListener('DOMContentLoaded', () => {
  const replaceText = (selector, text) => {
    const element = document.getElementById(selector)
    if (element) element.innerText = text
  }

  for (const dependency of ['chrome', 'node', 'electron']) {
    replaceText(`${dependency}-version`, process.versions[dependency])
  }
})
```

Refer to the [documentation](https://www.electronjs.org/docs/latest/tutorial/quick-start#access-nodejs-from-the-renderer-with-a-preload-script)
for udnerstanding the preload process.

Lastly, attach this script to your renderer process, pass in the path to your preload script to the `webPrefenrences.preload` option in your existing `BrowserWindow` constructor.

```
const { app, BrowserWindow } = require('electron')
// include the Node.js 'path' module at the top of your file
const path = require('node:path')

// modify your existing createWindow() function
const createWindow = () => {
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, 'preload.js')
    }
  })

  win.loadFile('index.html')
}
// ...
```

### Adding functionality

At this point, you might be wondering how to add more functionality to your application.

For any interactions with your web contents, you want to add scripts to your renderer process. Because the renderer runs in a normal web environment, you can add a `<script>` tag right before your index.html file's closing <`/body>` tag to include any arbitrary scripts you want:

```
<script src="./renderer.js"></script>
```

The file `renderer.js` can be used then to provide extra functionality

### Packaging and distributing the application

This section is dedicated to packaging your desktop app.

1. **Check your `package.json` file and a description and the author if haven't previously**

2. **Add Electron Forge as a development dependency of your app, and use its import command to set up Forge's scaffolding:**

```bash
npm install --save-dev @electron-forge/cli
npx electron-forge import
```

A succesful run should output something like this:

```
✔ Checking your system
✔ Initializing Git Repository
✔ Writing modified package.json file
✔ Installing dependencies
✔ Writing modified package.json file
✔ Fixing .gitignore

We have ATTEMPTED to convert your app to be in a format that electron-forge understands.

Thanks for using "electron-forge"!!!
```

Also notice that a file named `forge.config.js` was generated.

3. **Add `@electron-forge/plugin-fuses` as a dependency**

```bash
npm add @electron-forge/plugin-fuses
```

4. **Create a distributable using Forge's `make` command**

```bash
npm run make
```

Electron Forge creates the `out` folder where your package will be located. The app can be run by executing `electron-demo.exe`
