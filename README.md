const { app, BrowserWindow, ipcMain } = require('electron')
const fs = require('fs')
const path = require('path')
const archiver = require('archiver')

function createWindow() {
  const win = new BrowserWindow({
    width: 1000,
    height: 700,
    webPreferences: {
      nodeIntegration: true,
      contextIsolation: false
    }
  })

  win.loadURL("data:text/html;charset=utf-8," + encodeURIComponent(`
    <html>
    <body style="font-family:sans-serif">
      <h2>RBZ DEV HUB (ONE FILE)</h2>

      <input id="name" placeholder="Plugin name"/><br><br>
      <textarea id="code" rows="20" cols="100"></textarea><br>

      <button onclick="build()">Build RBZ</button>

      <pre id="log"></pre>

      <script>
        const { ipcRenderer } = require('electron')

        async function build(){
          const name = document.getElementById('name').value
          const code = document.getElementById('code').value

          const res = await ipcRenderer.invoke('build', {name, code})
          document.getElementById('log').textContent += res + "\\n"
        }
      </script>
    </body>
    </html>
  `))
}

app.whenReady().then(createWindow)

ipcMain.handle('build', async (_, { name, code }) => {
  const dir = path.join(__dirname, name)
  if (!fs.existsSync(dir)) fs.mkdirSync(dir)

  const rb = path.join(dir, name + ".rb")
  fs.writeFileSync(rb, code)

  const out = path.join(__dirname, name + ".rbz")
  const output = fs.createWriteStream(out)
  const archive = archiver('zip')

  archive.pipe(output)
  archive.directory(dir, false)
  await archive.finalize()

  return "Build OK: " + out
})
