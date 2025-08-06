[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-2e0aaae1b6195c2367325f4f02e2d04e9abb55f0b24a779b69b11b9e10269abc.svg)](https://classroom.github.com/online_ide?assignment_repo_id=20040764&assignment_repo_type=AssignmentRepo)
# Real-Time Chat Application with Socket.io

This assignment focuses on building a real-time chat application using Socket.io, implementing bidirectional communication between clients and server.

## Assignment Overview

You will build a chat application with the following features:
1. Real-time messaging using Socket.io
2. User authentication and presence
3. Multiple chat rooms or private messaging
4. Real-time notifications
5. Advanced features like typing indicators and read receipts

## Project Structure

```
socketio-chat/
├── client/                 # React front-end
│   ├── public/             # Static files
│   ├── src/                # React source code
│   │   ├── components/     # UI components
│   │   ├── context/        # React context providers
│   │   ├── hooks/          # Custom React hooks
│   │   ├── pages/          # Page components
│   │   ├── socket/         # Socket.io client setup
│   │   └── App.jsx         # Main application component
│   └── package.json        # Client dependencies
├── server/                 # Node.js back-end
│   ├── config/             # Configuration files
│   ├── controllers/        # Socket event handlers
│   ├── models/             # Data models
│   ├── socket/             # Socket.io server setup
│   ├── utils/              # Utility functions
│   ├── server.js           # Main server file
│   └── package.json        # Server dependencies
└── README.md               # Project documentation
```

## Getting Started

1. Accept the GitHub Classroom assignment invitation
2. Clone your personal repository that was created by GitHub Classroom
3. Follow the setup instructions in the `Week5-Assignment.md` file
4. Complete the tasks outlined in the assignment

## Files Included

- `Week5-Assignment.md`: Detailed assignment instructions
- Starter code for both client and server:
  - Basic project structure
  - Socket.io configuration templates
  - Sample components for the chat interface

## Requirements

- Node.js (v18 or higher)
- npm or yarn
- Modern web browser
- Basic understanding of React and Express

## Submission

Your work will be automatically submitted when you push to your GitHub Classroom repository. Make sure to:

1. Complete both the client and server portions of the application
2. Implement the core chat functionality
3. Add at least 3 advanced features
4. Document your setup process and features in the README.md
5. Include screenshots or GIFs of your working application
6. Optional: Deploy your application and add the URLs to your README.md

## Resources

- [Socket.io Documentation](https://socket.io/docs/v4/)
- [React Documentation](https://react.dev/)
- [Express.js Documentation](https://expressjs.com/)
- [Building a Chat Application with Socket.io](https://socket.io/get-started/chat)
- Final Folder Structure
csharp

**nones/**
├── public/
│   ├── index.html       # Chat UI with nickname modal
│   ├── script.js        # Client-side JS logic
│   └── ping.mp3         # Notification sound
├── server.js            # Express + Socket.IO server
├── package.json         # Dependencies
🛠 1. package.json (dependencies)
**json**

{
  "name": "nones-chat",
  "version": "1.0.0",
  "description": "Real-time chat app with Socket.IO",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.18.2",
    "socket.io": "^4.7.2"
  }
}
Run npm install after creating this file.

🧠 2. server.js
js
Copy
Edit
const express = require('express');
const http = require('http');
const { Server } = require('socket.io');

const app = express();
const server = http.createServer(app);
const io = new Server(server);

app.use(express.static('public'));

const users = {}; // Map socket.id -> nickname

io.on('connection', (socket) => {
  console.log('🟢 User connected:', socket.id);

  // Set nickname
  socket.on('set nickname', (nickname) => {
    users[socket.id] = nickname || 'Anonymous';
    console.log(`Nickname set: ${socket.id} = ${users[socket.id]}`);
  });

  // Broadcast message
  socket.on('chat message', (msg) => {
    const sender = users[socket.id] || 'Anonymous';
    io.emit('chat message', {
      msg,
      senderId: socket.id,
      nickname: sender
    });
  });

  // Typing indicator
  socket.on('typing', () => {
    const sender = users[socket.id] || 'Anonymous';
    socket.broadcast.emit('typing', sender);
  });

  socket.on('stop typing', () => {
    socket.broadcast.emit('stop typing');
  });

  socket.on('disconnect', () => {
    console.log('🔴 User disconnected:', socket.id);
    delete users[socket.id];
  });
});

const PORT = process.env.PORT || 3000;
server.listen(PORT, () => {
  console.log(`✅ Nones server running at http://localhost:${PORT}`);
});
 **public/index.html**
html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Nones Chat</title>
  <style>
    body {
      font-family: sans-serif;
      background: linear-gradient(to right, #f8f9fa, #e3e3e3);
      margin: 0;
      padding: 20px;
    }
    h2 { margin-bottom: 10px; }
    #messages {
      list-style-type: none;
      padding: 0;
      max-height: 300px;
      overflow-y: auto;
      background: #fff;
      border: 1px solid #ccc;
      border-radius: 5px;
      margin-bottom: 10px;
    }
    #messages li {
      padding: 8px 12px;
      margin: 5px;
      border-radius: 4px;
      max-width: 80%;
    }
    #form {
      display: flex;
    }
    #input {
      flex: 1;
      padding: 10px;
      border: 1px solid #ccc;
      border-radius: 4px;
    }
    #send {
      padding: 10px;
      background: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      margin-left: 5px;
      cursor: pointer;
    }
    #send:hover {
      background: #0056b3;
    }
    #typing {
      font-style: italic;
      color: #888;
      margin-bottom: 10px;
    }
    #nicknameModal {
      position: fixed;
      top: 0; left: 0;
      width: 100%; height: 100%;
      background: rgba(0, 0, 0, 0.5);
      display: flex;
      justify-content: center;
      align-items: center;
      z-index: 1000;
    }
    #nicknameModal div {
      background: #fff;
      padding: 20px;
      border-radius: 8px;
      text-align: center;
    }
    #nicknameModal input {
      padding: 8px;
      width: 200px;
      margin-bottom: 10px;
    }
  </style>
</head>
<body>

  <!-- Nickname Modal -->
  <div id="nicknameModal">
    <div>
      <h3>Enter your nickname</h3>
      <input type="text" id="nicknameInput" placeholder="Nickname" />
      <br />
      <button onclick="submitNickname()">Join Chat</button>
    </div>
  </div>

  <h2>Welcome to Nones Chat 🗨️</h2>
  <div id="typing"></div>
  <ul id="messages"></ul>
  <form id="form">
    <input id="input" autocomplete="off" placeholder="Type a message..." />
    <button id="send">Send</button>
  </form>

  <audio id="notificationSound" src="ping.mp3" preload="auto"></audio>

  <script src="/socket.io/socket.io.js"></script>
  <script src="script.js"></script>
</body>
</html>

**public/script.js**
js

const socket = io();
let myId = null;
let myNickname = 'elka';

const form = document.getElementById('form');
const input = document.getElementById('input');
const messages = document.getElementById('messages');
const typingIndicator = document.getElementById('typing');
const sound = document.getElementById('notificationSound');

let isWindowFocused = true;
let unreadCount = 0;
let typingTimeout;

// Desktop notifications
if (Notification.permission !== 'granted') {
  Notification.requestPermission();
}

// Nickname modal
function submitNickname() {
  const nicknameInput = document.getElementById('nicknameInput');
  const name = nicknameInput.value.trim() || 'elka';
  myNickname = name;
  socket.emit('set nickname', myNickname);
  document.getElementById('nicknameModal').style.display = 'none';
}

// Window focus tracking
window.onfocus = () => {
  isWindowFocused = true;
  unreadCount = 0;
  document.title = 'Nones Chat';
};
window.onblur = () => {
  isWindowFocused = false;
};

function showNotification(title, body) {
  if (Notification.permission === 'granted') {
    new Notification(title, { body });
  }
}

function playSound() {
  sound.play().catch(() => {});
}

function updateBadge() {
  if (!isWindowFocused) {
    unreadCount++;
    document.title = `(${unreadCount}) New message - Nones`;
  }
}

socket.on('connect', () => {
  myId = socket.id;
});

form.addEventListener('submit', (e) => {
  e.preventDefault();
  const message = input.value.trim();
  if (message) {
    socket.emit('chat message', message);
    socket.emit('stop typing');
    input.value = '';
  }
});

input.addEventListener('input', () => {
  socket.emit('typing');
  clearTimeout(typingTimeout);
  typingTimeout = setTimeout(() => {
    socket.emit('stop typing');
  }, 1000);
});

socket.on('chat message', (data) => {
  const item = document.createElement('li');
  const isMe = data.senderId === myId;
  const displayName = isMe ? 'You' : data.nickname;

  item.textContent = `${displayName}: ${data.msg} ${isMe ? '✅' : '📩'}`;
  item.style.textAlign = isMe ? 'right' : 'left';
  item.style.background = isMe ? '#dcf8c6' : '#e6e6e6';
  item.style.margin = '5px';
  item.style.padding = '8px';
  item.style.borderRadius = '6px';

  messages.appendChild(item);
  messages.scrollTop = messages.scrollHeight;

  if (!isMe) {
    playSound();
    showNotification(`New message from ${data.nickname}`, data.msg);
    updateBadge();
  }
});

socket.on('typing', (nickname) => {
  typingIndicator.textContent = `${nickname} is typing...`;
});

socket.on('stop typing', () => {
  typingIndicator.textContent = '';
});

**public/ping.mp3**
Download from:
https://www.fesliyanstudios.com/play-mp3/387

Rename the file to: ping.mp3

Place it inside public/
