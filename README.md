# 🔐 VaultChat — End-to-End Encrypted P2P Chat

[![GitHub Pages](https://img.shields.io/badge/hosted-GitHub%20Pages-black?logo=github)](https://your-username.github.io/vaultchat/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![No Backend](https://img.shields.io/badge/backend-none-brightgreen)](/)
[![E2E Encrypted](https://img.shields.io/badge/encryption-AES--GCM%20256-blue)](/)

> **Resume-level** browser-based encrypted chat. No server. No accounts. No data stored anywhere except your own device.

---

## ✨ Features

| Feature | Details |
|---|---|
| 🔐 End-to-End Encryption | AES-GCM 256-bit |
| 🔑 Key Exchange | ECDH P-384 (Elliptic Curve Diffie-Hellman) |
| ⚡ Transport | WebRTC DataChannel (peer-to-peer) |
| 🌐 Signaling | PeerJS (free, open-source) |
| 💾 History | IndexedDB (local device only) |
| 📱 Responsive | WhatsApp-like UI, mobile friendly |
| 🔗 Room sharing | Shareable link + QR code |
| 🌙 Dark/light mode | Toggle in header |
| 🚫 No backend | 100% static — GitHub Pages |
| 🆓 Free | No paid APIs whatsoever |

---

## 🏗️ Architecture

```
Alice's Browser                    Bob's Browser
┌─────────────────┐               ┌─────────────────┐
│  ECDH Key Gen   │               │  ECDH Key Gen   │
│  AES-GCM Enc    │◄─────────────►│  AES-GCM Dec    │
│  RTCDataChannel │  P2P WebRTC   │  RTCDataChannel │
│  IndexedDB      │               │  IndexedDB      │
└────────┬────────┘               └────────┬────────┘
         │  SDP offer/answer (setup only)  │
         └──────────► PeerJS ◄─────────────┘
                   (free signaling)
                   
         Messages NEVER pass through PeerJS.
         Only WebRTC SDP metadata does.
```

### WebRTC Flow
1. **Alice** creates a room → PeerJS assigns a peer ID
2. **Bob** joins → PeerJS relays SDP offer/answer to establish NAT traversal
3. Once WebRTC connects, **PeerJS is bypassed** — all traffic is direct P2P
4. ECDH key exchange happens over the DataChannel
5. All subsequent messages are AES-GCM encrypted before being sent

### Encryption Flow
```
1. Alice  →  crypto.subtle.generateKey(ECDH, P-384)
2. Bob    →  crypto.subtle.generateKey(ECDH, P-384)
3. Exchange public keys over DataChannel
4. Both   →  crypto.subtle.deriveKey(ECDH)  → shared AES-256 key
5. Message encrypt:  AES-GCM(plaintext, randomIV)  → ciphertext
6. Message decrypt:  AES-GCM(ciphertext, IV)       → plaintext
```

**Key properties:**
- Shared key is derived independently on each side — never transmitted
- Each message uses a fresh random IV (12 bytes)
- Web Crypto API (browser-native, no external library)

---

## 🚀 Deploy to GitHub Pages

### Step 1 — Fork / Clone
```bash
git clone https://github.com/your-username/vaultchat.git
cd vaultchat
```

### Step 2 — Push to GitHub
```bash
git add .
git commit -m "Initial VaultChat"
git push origin main
```

### Step 3 — Enable GitHub Pages
1. Go to your repo → **Settings** → **Pages**
2. Under **Source**, select `Deploy from a branch`
3. Choose `main` branch, `/ (root)` folder
4. Click **Save**
5. Your site will be live at `https://your-username.github.io/vaultchat/`

### Step 4 — Done!
Share the link with a friend and start a room. The `?room=<id>` query parameter auto-opens the join dialog.

---

## 📁 Folder Structure

```
vaultchat/
├── index.html        ← entire app (single file)
└── README.md         ← this file
```

The entire app is a **single HTML file** — no build tools, no npm, no dependencies to install.

---

## 🔧 How to Use

1. **Open** the site
2. **Set your username** (top-left)
3. **Create a room** → click "New" or type a room name
4. **Share the link** → click "Share room link" for a QR code + link
5. **Peer joins** via the link or by entering the room ID
6. **Chat** — all messages are encrypted automatically
7. **Reconnect later** → previous messages load from your device's IndexedDB

---

## 🔒 Security Notes

- **Zero knowledge**: The signaling server (PeerJS) only sees your WebRTC metadata (SDP/ICE), never your messages
- **Forward secrecy**: Each session generates new ECDH key pairs
- **No accounts**: No email, no phone number, no tracking
- **Local only**: Chat history stays on your device in IndexedDB
- **Open source**: Audit the code yourself — it's one file

---

## ⚡ Tech Stack

| Layer | Technology |
|---|---|
| P2P transport | WebRTC DataChannel |
| Signaling | [PeerJS](https://peerjs.com/) (free) |
| Encryption | Web Crypto API (built-in) |
| Key exchange | ECDH P-384 |
| Cipher | AES-GCM 256-bit |
| Storage | IndexedDB |
| QR code | qrcodejs |
| NAT traversal | Google STUN servers (free) |
| Hosting | GitHub Pages |

---

## 📜 License

MIT — free to use, modify, and distribute.

---

*Built as a demonstration of browser-native cryptography and WebRTC peer-to-peer communication. No backend required.*
