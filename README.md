# electron-keytar-windows10-howto

This is a how-to for making the [Keytar](https://github.com/atom/node-keytar) native password module work with [Electron](https://github.com/electron/electron) on Windows 10 in spite of [this problem](https://github.com/atom/node-keytar/issues/51).

## Solution 1

This solution is based on [electron-quick-start](https://github.com/electron/electron-quick-start) and will currently (26/SEPT/2018) provide an instance of Electron 2.0.0 with Node.js 8.9.3 and Chromium 61.0.3163.100. I'll be using a Windows PowerShell terminal. I have Visual Studio 2017 with support for C++ installed.

```
git clone https://github.com/electron/electron-quick-start
cd .\electron-quick-start\
npm install
npm install keytar
npm install electron-rebuild
.\node_modules\.bin\electron-rebuild -w keytar -p -f
npm start
```

After opening the Console in Electron's Developer Tools you can check keytar is working:

```
const keytar = require('keytar')
keytar.setPassword('my-service', 'my-user', 'my-password')
keytar.getPassword('my-service', 'my-user').then(function(v) {console.log(v)})
```

On my machine (Windows 10 Home 64 bit, version 1803, build 17134.285, x64 processor) electron-rebuild didn't actually need to do much and only took second or two.

## Solution 2

This solution will provide an up-to-date instance of Electron (v.3.0.0 with Node.js 10.2.0 and Chromium 66.0.3359.181 as of 26/SEPT/2018). You'll need Visual Studio with support for C++ installed (I used Visual Studio Community 2017). It also [requires python 2.7](https://stackoverflow.com/a/39648550):

#### A) from a Windows PowerShell with admin provileges

```
npm install --global --production windows-build-tools
npm install --global node-gyp
setx PYTHON $env:USERPROFILE\.windows-build-tools\python27\python.exe
```

Note: you can check the installed python executable before setting the environment variable with `C:\Users\<user-dir>\.windows-build-tools\python27\python.exe`.

#### B) from an unprivileged shell

```
mkdir electron-keytar
cd electron-keytar
npm init
npm install electron
npm install keytar
npm install electron-rebuild --save-dev
.\node_modules\.bin\electron-rebuild -w keytar -p -f
```

Notes:
* npm init will ask for a few inputs and generate a package.json file for you: confirm "index.js" as entry point script or create the file accordingly in step C
* electron-rebuild may take a few minutes.

#### C) create a minimal index.js

```
const {app, BrowserWindow} = require('electron')
let mainWindow
app.on('ready', function() {
  mainWindow = new BrowserWindow({width: 800, height: 600})
  mainWindow.loadFile('index.html')
  mainWindow.on('closed', function () {
    mainWindow = null
  });
})
app.on('window-all-closed', function () {
  app.quit()
})
```

#### D) create a minimal index.html

```
<!DOCTYPE html>
<html>
  <body>
    We are using Node.js <script>document.write(process.versions.node)</script>,
    Chromium <script>document.write(process.versions.chrome)</script>,
    and Electron <script>document.write(process.versions.electron)</script>.
  </body>
</html>
```

#### E) add this to scripts in package.json

```
"start": "electron ."
```

#### F) start electron

```
npm start
```

#### G) check keytar

After opening the Console in Electron's Developer Tools you can check keytar is working:

```
const keytar = require('keytar')
keytar.setPassword('my-service', 'my-user', 'my-password')
keytar.getPassword('my-service', 'my-user').then(function(v) {console.log(v)})
```
