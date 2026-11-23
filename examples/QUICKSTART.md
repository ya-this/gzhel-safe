# Quick start — Gzhel Safe

## Option A: Windows desktop app as your server (easiest)

1. Install and run `Gzhel-Safe-Setup-1.0.0-x64.exe` on your PC.
2. The vault UI opens automatically. Upload files via **Upload files** or watch
   a folder under **Real-time folders**.
3. On your Android phone, install `gzhel-safe-1.0.0.apk`. On first launch enter
   the PC address, e.g.:

   ```
   http://192.168.1.10:3000
   ```

   (same Wi-Fi; use the IP shown by your router for the PC, port 3000 is the
   default when you host the server yourself — the desktop app picks a free
   port automatically and prints it to its console window).

4. The phone now sees the same vault: files, folders, priorities, servers.

## Option B: self-host the server on any machine (Node.js 20+)

Any host with Node.js can run the vault server; the `.exe` is just a packaged
version of it.

```bash
# on the server machine
PORT=3000 NODE_ENV=production node server.js     # or run the .exe on Windows
```

Then point the Android app at `http://<server-ip>:3000` (or HTTPS behind a
reverse proxy).

## Option C: forward the vault to your own storage server

In the app: **Own storage server → Add server** — choose SFTP / FTP / FTPS /
WebDAV / S3 / HTTP POST and enter the address. Files from the vault can be
pushed there with **Send all files** or automatically (`Auto: ON`).

To receive files on another machine without installing anything, download the
**receiver script** from the app (*Own storage server → receiver script*) and
run:

```bash
node gzhel-receiver-server.mjs --port 8080 --dir ./gzhel-incoming --token YOUR_SECRET_TOKEN
```

Then add it in the app as an `HTTP POST` server pointing at
`http://<that-machine>:8080/upload` with a Bearer token.

## PC agent (instant push from a local folder)

Download `gzhel-pc-agent.mjs` from the app (*Real-time folders → PC agent*)
and run it on any PC:

```bash
node gzhel-pc-agent.mjs --server http://192.168.1.10:3000 --dir "D:\Backups" --interval 5
```

New/changed files in the folder are encrypted and uploaded immediately.
