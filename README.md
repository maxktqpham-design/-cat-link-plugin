npm install --save-dev electron-builder
{
  "name": "devhub-ultra",
  "version": "1.0.0",
  "main": "main.js",
  "scripts": {
    "start": "electron .",
    "build": "electron-builder"
  },
  "build": {
    "appId": "com.devhub.ultra",
    "productName": "DEV HUB ULTRA MAX++",
    "win": {
      "target": "nsis"
    },
    "directories": {
      "output": "dist"
    }
  }
}
