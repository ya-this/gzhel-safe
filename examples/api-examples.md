# HTTP API examples (curl)

Base URL: `http://<server-ip>:3000` (the desktop app prints its local port).
All endpoints are same-origin for the web/desktop client; the Android app
sends them to the configured server address. No authentication is required by
default — restrict access with a reverse proxy / firewall for internet exposure.

```bash
BASE=http://192.168.1.10:3000
H='X-Requested-With: XMLHttpRequest'

# Health check
curl -s "$BASE/api/health"

# List vault files
curl -s "$BASE/api/vault/files" -H "$H"

# Upload a file (multipart). encrypted=true stores AES-256-GCM ciphertext
# produced on the client; ivB64/saltB64/sha256 describe it.
curl -s -X POST "$BASE/api/vault/upload" -H "$H" \
  -F "file=@photo.jpg;type=image/jpeg" \
  -F "name=photo.jpg" \
  -F "relativePath=Camera Roll/2026/photo.jpg" \
  -F "folderName=Photos from phone" \
  -F "mimeType=image/jpeg" \
  -F "category=photo" \
  -F "priority=critical" \
  -F "encrypted=true" \
  -F "sha256=<hex>" -F "ivB64=<base64>" -F "saltB64=<base64>" \
  -F "originalSize=10240" -F "localMtime=1747200000000"

# Create a watched (real-time) folder
curl -s -X POST "$BASE/api/vault/folders" -H "$H" -H 'Content-Type: application/json' \
  -d '{"name":"Photos from phone","pathHint":"C:\\Users\\anna\\Pictures\\Camera Roll","platform":"directory-input","intervalSec":5,"autoSync":true}'

# List / update priority rules
curl -s "$BASE/api/settings/priorities" -H "$H"
curl -s -X PUT "$BASE/api/settings/priorities" -H "$H" -H 'Content-Type: application/json' \
  -d '{"priorities":[{"category":"photo","priority":"critical","maxSizeMb":200,"autoEncrypt":true},{"category":"document","priority":"high","maxSizeMb":100,"autoEncrypt":true},{"category":"video","priority":"low","maxSizeMb":2048,"autoEncrypt":true},{"category":"archive","priority":"normal","maxSizeMb":1024,"autoEncrypt":true},{"category":"code","priority":"normal","maxSizeMb":50,"autoEncrypt":false},{"category":"other","priority":"normal","maxSizeMb":50,"autoEncrypt":false}]}'

# Add your own storage server (see server-config.example.json)
curl -s -X POST "$BASE/api/servers" -H "$H" -H 'Content-Type: application/json' \
  -d @server-config.example.json

# Test the connection / browse remote files / sync everything
curl -s -X POST "$BASE/api/servers/<id>/test" -H "$H"
curl -s "$BASE/api/servers/<id>/files?path=/" -H "$H"
curl -s -X POST "$BASE/api/servers/<id>/sync-all" -H "$H"

# Download the receiver script (standalone Node.js receiver) and PC agent
curl -s "$BASE/api/servers/receiver-script" -H "$H" -o gzhel-receiver-server.mjs
curl -s "$BASE/api/agent/download" -H "$H" -o gzhel-pc-agent.mjs
```

Categories: `photo | document | video | archive | code | other`
Priorities: `critical | high | normal | low | exclude`
Protocols: `sftp | ftp | ftps | webdav | s3 | http-post | http-put`
