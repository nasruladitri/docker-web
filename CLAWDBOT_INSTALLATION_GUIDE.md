# Panduan Instalasi Clawdbot (Moltbot)

Berdasarkan percakapan setup kami, berikut adalah langkah-langkah lengkap untuk menginstal dan menjalankan Clawdbot di dev container.

## Prasyarat

- Node.js v24.11.1 atau lebih baru
- Git
- NPM atau PNPM
- GitHub Codespaces (opsional, untuk akses remote)

## Langkah-Langkah Instalasi

### 1. Install Clawdbot via NPM

```bash
npm install -g moltbot@latest
```

Ini akan menginstal Clawdbot secara global. Versi yang diinstal adalah 2026.1.24-3.

### 2. Jalankan Official Installer

```bash
curl -fsSL https://molt.bot/install.sh | bash
```

Installer ini akan:
- Mendeteksi OS dan environment
- Menginstal Clawdbot 2026.1.24-3
- Menjalankan setup awal (doctor)
- Menyiapkan workspace di `~/.clawdbot`

### 3. Onboarding Interaktif

```bash
clawdbot onboard --install-daemon
```

Selama proses onboarding, Anda akan diminta untuk:
- Mengakui bahwa Clawdbot adalah tool yang powerful dan risky
- Memilih mode onboarding (QuickStart recommended)
- Memilih AI model (default: Google Gemini 2.5 Flash Lite)
- Memasukkan Gemini API key
- Mengatur channel (Telegram recommended untuk testing)
- Mengkonfigurasi skills (opsional)

### 4. Mulai Gateway Secara Manual

Jika gateway tidak start otomatis, jalankan secara manual:

```bash
clawdbot gateway run --bind auto
```

Parameter penting:
- `--bind auto`: Menerima koneksi dari proxy/local
- `--verbose`: (opsional) Untuk debugging
- `--force`: (opsional) Kill proses existing di port 18789

## Akses Dashboard Control UI

### Opsi 1: Akses Lokal (Docker Container)

```bash
# Gateway sudah running di port 18789
http://127.0.0.1:18789/?token=<YOUR_TOKEN>
```

Token bisa didapat dengan:
```bash
clawdbot dashboard
```

### Opsi 2: Akses Remote (GitHub Codespaces)

#### A. Menggunakan GitHub CLI

1. List codespaces untuk mendapat nama yang tepat:
```bash
gh codespace list
```

2. Forward port dengan nama codespace yang benar:
```bash
gh codespace ports forward 18789:18789 --codespace <CODESPACE_NAME>
```

3. Buka di browser:
```
http://localhost:18789/?token=<YOUR_TOKEN>
```

#### B. Menggunakan VS Code Ports Panel

1. Buka PORTS panel di VS Code (bottom left)
2. Klik "Add Port" dan masukkan `18789`
3. Right-click port → "Make Public"
4. Klik globe icon untuk buka di browser

## Struktur Konfigurasi

Semua file konfigurasi tersimpan di `~/.clawdbot/`:

```
~/.clawdbot/
├── clawdbot.json          # Konfigurasi utama
├── clawdbot.json.bak      # Backup konfigurasi
├── agents/                # Workspace agent
│   └── main/
│       └── sessions/      # Session logs
├── credentials/           # API keys & credentials
└── identity/              # Identity files
```

### File Konfigurasi Utama: `clawdbot.json`

```json
{
  "workspace": "~/clawd",
  "gateway": {
    "port": 18789,
    "mode": "local",
    "bind": "loopback",
    "auth": {
      "mode": "token",
      "token": "<YOUR_GATEWAY_TOKEN>"
    }
  },
  "channels": {
    "telegram": {
      "enabled": true,
      "dmPolicy": "pairing",
      "botToken": "<YOUR_TELEGRAM_BOT_TOKEN>"
    }
  }
}
```

## Command Penting

### Gateway Management

```bash
# Start gateway
clawdbot gateway run

# Check status
clawdbot gateway status

# Lihat logs
clawdbot logs

# Health check
clawdbot gateway health
```

### Configuration

```bash
# Interactive config wizard
clawdbot configure

# Get nilai config
clawdbot config get gateway

# Set nilai config
clawdbot config set gateway.port 18789

# Akses dashboard dengan token
clawdbot dashboard
```

### Pairing (DM Security)

```bash
# List pending pairing requests
clawdbot pairing list

# Approve pairing request
clawdbot pairing approve telegram <PAIRING_CODE>
```

### Agent Management

```bash
# Run agent turn
clawdbot agent

# List agents
clawdbot agents list
```

## Troubleshooting

### Gateway tidak start

**Problem**: `clawdbot gateway run` tidak merespons
**Solution**:
```bash
# Gunakan --force untuk kill proses lama
clawdbot gateway run --force

# Atau check apakah port sudah dipakai
lsof -i :18789
```

### Pairing required error

**Problem**: Dashboard disconnect dengan "pairing required"
**Solution**:
```bash
# Approve pairing request dari dashboard
clawdbot pairing list
clawdbot pairing approve telegram <CODE>
```

### Token missing error

**Problem**: WebSocket disconnect dengan "token missing"
**Solution**:
1. Pastikan token di-pass di query parameter: `?token=<TOKEN>`
2. Atau set di gateway config:
```bash
clawdbot config get gateway.auth.token
```

### Port forwarding timeout

**Problem**: SSH atau port forward timeout
**Solution**:
1. Gunakan `gh codespace ports forward` bukan SSH
2. Keep the terminal running (don't close the forwarding process)
3. Check codespace name: `gh codespace list`

## Environment Variables

```bash
# Set Gemini API Key
export GOOGLE_GEMINI_API_KEY=<YOUR_API_KEY>

# Set gateway token
export CLAWDBOT_GATEWAY_TOKEN=<YOUR_TOKEN>

# Dev mode (isolates state)
clawdbot --dev
```

## Useful Docs Links

- Main Docs: https://docs.clawd.bot/
- Security: https://docs.clawd.bot/security
- Gateway: https://docs.clawd.bot/gateway/
- Control UI: https://docs.clawd.bot/web/control-ui
- Pairing: https://docs.clawd.bot/start/pairing

## Next Steps

1. **Setup Channel**: Configure Telegram, Discord, atau channel lain di `clawdbot configure`
2. **Install Skills**: Tambah capabilities dengan `clawdbot skills install`
3. **Configure Models**: Set default AI model di config
4. **Enable Web Search**: Setup Brave Search API untuk web browsing capability
5. **Create Agents**: Buat agent terpisah untuk use cases berbeda

---

**Last Updated**: 28 Januari 2026  
**Clawdbot Version**: 2026.1.24-3
