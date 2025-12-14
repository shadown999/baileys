# 🚀 SHADOW-N PRO

> **A Powerful & Enhanced WhatsApp Web API Library**

[![NPM Version](https://img.shields.io/npm/v/@shadow999/baileys?style=flat-square&color=6C63FF)](https://www.npmjs.com/package/@shadow999/baileys)
[![Downloads](https://img.shields.io/npm/dt/@shadow999/baileys?style=flat-square&color=FF6584)](https://www.npmjs.com/package/@shadow999/baileys)
[![License](https://img.shields.io/badge/license-MIT-blue?style=flat-square)](LICENSE)
[![WhatsApp](https://img.shields.io/badge/WhatsApp-Multi--Device-25D366?style=flat-square&logo=whatsapp)](https://www.whatsapp.com)

A modified and enhanced version of Baileys - the lightweight, WebSocket-based TypeScript/JavaScript library for interacting with WhatsApp Web API.

**✨ Built with ❤️ by Nethupa Methwan**

---

## 📑 Table of Contents

- [✨ Features](#-features)
- [📦 Installation](#-installation)
- [🚀 Quick Start](#-quick-start)
- [💡 Usage Examples](#-usage-examples)
- [🎯 Why SHADOW-N PRO](#-why-shadow-n-pro)
- [📚 API Documentation](#-api-documentation)
- [⚠️ Disclaimer](#️-disclaimer)
- [🤝 Contributing](#-contributing)
- [📄 License](#-license)
- [📧 Contact](#-contact)

---

## ✨ Features

### 🔐 **Authentication & Security**
- ✅ Multi-device support with QR code authentication
- ✅ Pairing code login support
- ✅ Secure session management
- ✅ Automatic credential saving

### 💬 **Messaging Capabilities**
- ✅ Send/receive text messages
- ✅ Images, videos, and audio files
- ✅ Documents with custom filenames
- ✅ Stickers and GIFs support
- ✅ Message reactions and replies
- ✅ Read receipts and delivery status

### 👥 **Group Management**
- ✅ Create and delete groups
- ✅ Add/remove participants
- ✅ Update group settings
- ✅ Admin privilege management
- ✅ Group invite links

### ⚡ **Performance & Reliability**
- ✅ Lightweight and efficient
- ✅ Automatic reconnection handling
- ✅ Event-driven architecture
- ✅ Full TypeScript support
- ✅ Real-time updates
- ✅ Presence management (online, typing, recording)

---

## 📦 Installation

Install using npm:

```bash
npm install @shadow999/baileys
```

Or using yarn:

```bash
yarn add @shadow999/baileys
```

Or using pnpm:

```bash
pnpm add @shadow999/baileys
```

---

## 🚀 Quick Start

Get up and running in minutes with this simple example:

```javascript
const { 
    default: makeWASocket, 
    DisconnectReason, 
    useMultiFileAuthState 
} = require('@shadow999/baileys')

async function connectToWhatsApp() {
    // Load authentication credentials
    const { state, saveCreds } = await useMultiFileAuthState('auth_info_baileys')
    
    // Create WhatsApp socket connection
    const sock = makeWASocket({
        auth: state,
        printQRInTerminal: true
    })
    
    // Save credentials whenever they update
    sock.ev.on('creds.update', saveCreds)
    
    // Handle connection status updates
    sock.ev.on('connection.update', (update) => {
        const { connection, lastDisconnect } = update
        
        if(connection === 'close') {
            const shouldReconnect = 
                lastDisconnect.error?.output?.statusCode !== DisconnectReason.loggedOut
            
            console.log('Connection closed:', lastDisconnect.error)
            console.log('Reconnecting:', shouldReconnect)
            
            // Automatically reconnect if not logged out
            if(shouldReconnect) {
                connectToWhatsApp()
            }
        } else if(connection === 'open') {
            console.log('✅ Connected successfully!')
        }
    })
    
    // Handle incoming messages
    sock.ev.on('messages.upsert', async m => {
        const msg = m.messages[0]
        
        if (!msg.key.fromMe && m.type === 'notify') {
            console.log('📨 New message received:', JSON.stringify(msg, null, 2))
            
            // Send an automatic reply
            await sock.sendMessage(msg.key.remoteJid, { 
                text: '👋 Hello! Thanks for your message. This is SHADOW-N PRO!' 
            })
        }
    })
}

// Start the connection
connectToWhatsApp()
```

---

## 💡 Usage Examples

### 📤 Send a Text Message

```javascript
await sock.sendMessage('1234567890@s.whatsapp.net', { 
    text: 'Hello from SHADOW-N PRO! 👋' 
})
```

### 📷 Send an Image with Caption

```javascript
await sock.sendMessage('1234567890@s.whatsapp.net', {
    image: { url: './path/to/image.jpg' },
    caption: 'Check out this amazing image! 📸'
})
```

### 🎥 Send a Video

```javascript
await sock.sendMessage('1234567890@s.whatsapp.net', {
    video: { url: './path/to/video.mp4' },
    caption: 'Awesome video! 🎬',
    gifPlayback: false // Set to true for GIF-style playback
})
```

### 📄 Send a Document

```javascript
await sock.sendMessage('1234567890@s.whatsapp.net', {
    document: { url: './document.pdf' },
    mimetype: 'application/pdf',
    fileName: 'ImportantDocument.pdf'
})
```

### 🎤 Send an Audio Message

```javascript
await sock.sendMessage('1234567890@s.whatsapp.net', {
    audio: { url: './audio.mp3' },
    mimetype: 'audio/mp4',
    ptt: true // Set to true for voice message
})
```

### 💬 Reply to a Message

```javascript
await sock.sendMessage('1234567890@s.whatsapp.net', {
    text: 'This is a reply!',
    quoted: msg // The message object you're replying to
})
```

### ❤️ React to a Message

```javascript
await sock.sendMessage('1234567890@s.whatsapp.net', {
    react: {
        text: '❤️', // Emoji to react with
        key: msg.key // Key of the message to react to
    }
})
```

### 👥 Create a Group

```javascript
const group = await sock.groupCreate('My Awesome Group', [
    '1234567890@s.whatsapp.net',
    '0987654321@s.whatsapp.net'
])
console.log('✅ Group created with ID:', group.id)
```

### ➕ Add Participant to Group

```javascript
await sock.groupParticipantsUpdate(
    'groupid@g.us', 
    ['1234567890@s.whatsapp.net'],
    'add'
)
```

### 🚫 Remove Participant from Group

```javascript
await sock.groupParticipantsUpdate(
    'groupid@g.us', 
    ['1234567890@s.whatsapp.net'],
    'remove'
)
```

### 👑 Promote to Admin

```javascript
await sock.groupParticipantsUpdate(
    'groupid@g.us', 
    ['1234567890@s.whatsapp.net'],
    'promote'
)
```

### 📝 Update Group Subject

```javascript
await sock.groupUpdateSubject('groupid@g.us', 'New Group Name')
```

### 📋 Update Group Description

```javascript
await sock.groupUpdateDescription('groupid@g.us', 'New group description here')
```

### 🔗 Get Group Invite Link

```javascript
const code = await sock.groupInviteCode('groupid@g.us')
console.log('Invite link: https://chat.whatsapp.com/' + code)
```

### 👁️ Update Presence (Typing/Recording)

```javascript
// Show typing indicator
await sock.sendPresenceUpdate('composing', '1234567890@s.whatsapp.net')

// Show recording indicator
await sock.sendPresenceUpdate('recording', '1234567890@s.whatsapp.net')

// Mark as available/online
await sock.sendPresenceUpdate('available')
```

### ✅ Mark Messages as Read

```javascript
await sock.readMessages([msg.key])
```

---

## 🎯 Why SHADOW-N PRO?

SHADOW-N PRO is built on top of the community-maintained Baileys library with significant enhancements and optimizations:

| Feature | SHADOW-N PRO | Standard Baileys |
|---------|--------------|------------------|
| **Performance** | ⚡ Optimized & Fast | 🐢 Standard |
| **Updates** | ✅ Regular Updates | ⚠️ Community-dependent |
| **Stability** | ✅ Enhanced Error Handling | ⚠️ Basic |
| **TypeScript** | ✅ Full Support | ✅ Full Support |
| **Documentation** | ✅ Comprehensive | ⚠️ Limited |
| **Maintenance** | ✅ Actively Maintained | ✅ Community Maintained |
| **Additional Features** | ✅ Enhanced Methods | ❌ Standard Only |

### 🌟 Key Improvements:
- 🔧 Better error handling and logging
- ⚡ Performance optimizations
- 🛡️ Enhanced security features
- 📖 Improved documentation
- 🔄 More reliable reconnection logic
- 🎨 Additional helper methods

---

## 📚 API Documentation

### Core Methods

#### `makeWASocket(config)`
Creates a new WhatsApp socket connection.

**Parameters:**
- `config.auth` - Authentication state
- `config.printQRInTerminal` - Print QR code in terminal (default: false)
- `config.logger` - Custom logger instance
- `config.browser` - Browser information

**Returns:** Socket instance

#### `useMultiFileAuthState(folder)`
Manages authentication state using file system.

**Parameters:**
- `folder` - Directory to store auth files

**Returns:** Object with `state` and `saveCreds` function

### Events

#### `connection.update`
Fired when connection status changes.

#### `creds.update`
Fired when credentials need to be saved.

#### `messages.upsert`
Fired when new messages arrive.

#### `messages.update`
Fired when messages are updated (read, delivered, etc.)

#### `presence.update`
Fired when contact presence changes.

#### `groups.update`
Fired when group information updates.

For complete API documentation, visit the [Baileys Wiki](https://baileys.wiki).

---

## ⚠️ Disclaimer

### Important Notice

This project is **NOT** affiliated with, endorsed by, or officially connected to:
- WhatsApp Inc.
- Meta Platforms, Inc.
- Any of their subsidiaries or affiliates

The official WhatsApp website: [whatsapp.com](https://www.whatsapp.com)

### Responsible Use

**This library should ONLY be used for:**
- ✅ Personal automation and productivity
- ✅ Educational and research purposes
- ✅ Building legitimate chatbots and tools
- ✅ Non-commercial personal projects

**DO NOT use this library for:**
- ❌ Spamming or bulk unsolicited messaging
- ❌ Automated marketing without explicit consent
- ❌ Stalkerware, surveillance, or privacy violations
- ❌ Scraping user data without permission
- ❌ Any activities that violate WhatsApp's Terms of Service
- ❌ Commercial use without proper authorization

### Legal Responsibility

⚖️ **You are solely responsible for how you use this library.** The maintainers and contributors are not liable for any misuse or damages resulting from the use of this software. Always ensure your use case complies with:
- WhatsApp Terms of Service
- Local laws and regulations
- Privacy and data protection laws (GDPR, CCPA, etc.)
- Applicable telecommunications regulations

### Account Safety

⚠️ **Warning:** Using unofficial WhatsApp clients can result in:
- Temporary or permanent account suspension
- Ban from WhatsApp services
- Loss of access to your WhatsApp account

Use at your own risk and always maintain backups of important data.

---

## 🤝 Contributing

Contributions are always welcome! We appreciate your help in making SHADOW-N PRO better.

### How to Contribute:

1. **Fork the Repository**
   ```bash
   git clone https://github.com/shadown999/baileys.git
   ```

2. **Create a Feature Branch**
   ```bash
   git checkout -b feature/AmazingFeature
   ```

3. **Make Your Changes**
   - Write clean, documented code
   - Follow existing code style
   - Add tests if applicable

4. **Commit Your Changes**
   ```bash
   git commit -m 'Add some AmazingFeature'
   ```

5. **Push to Your Branch**
   ```bash
   git push origin feature/AmazingFeature
   ```

6. **Open a Pull Request**

### Contribution Guidelines:
- 📝 Clear commit messages
- 🧪 Test your changes thoroughly
- 📖 Update documentation if needed
- 🎨 Follow code style conventions
- 🐛 Include bug fixes with test cases

---

## 📄 License

This project is licensed under the **MIT License**.

```
MIT License

Copyright (c) 2024 Nethupa Methwan

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

## 💖 Support & Star

If you find this project helpful, please consider:

⭐ **Starring the repository** on GitHub  
🐛 **Reporting bugs** you encounter  
💡 **Suggesting new features**  
📢 **Sharing with others** who might find it useful  

Your support helps keep this project alive and growing!

---

## 📧 Contact

**Developer:** Nethupa Methwan

- 💼 GitHub: [@shadown999](https://github.com/shadown999)
- 📧 Email: methwan2000@gmail.com
- 💬 Telegram: https://t.me/shadown999

For bug reports and feature requests, please use the GitHub Issues page.

---

## 🙏 Acknowledgments

Special thanks to:
- The original Baileys library creators and maintainers
- The WhatsApp Web reverse engineering community
- All contributors and users of SHADOW-N PRO
- Open source community for continuous support

---

## 📊 Project Stats

```
⭐ Stars: [Your Stars]
🍴 Forks: [Your Forks]
🐛 Issues: [Open Issues]
📦 Downloads: [NPM Downloads]
```

---

<div align="center">

**Made with 💻 and ☕ by Nethupa Methwan**

**© 2024 SHADOW-N PRO • All Rights Reserved**

---

### Show your support by giving a ⭐ if this project helped you!

[![Star on GitHub](https://img.shields.io/github/stars/shadown999/baileys?style=social)](https://github.com/yourusername/shadow-baileys)

</div>
