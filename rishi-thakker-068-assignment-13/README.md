# 💬 Assignment 13: Real-Time Group Chat & Messaging Engine (Socket.io)

**Name:** Rishi Thakker
**Roll No:** 150096725068
**Cohort:** Sam Altman

A multi-room real-time chat app built with **Node.js, Express, and Socket.io**. Users join channels, see message history on join, get live typing indicators, and can send private direct messages.

## Features
- Predefined rooms (`#general`, `#developers`, `#random`) plus joining any custom room name
- New joiners instantly see the last 50 messages for that room via `room:history`
- Live "X is typing..." indicator, debounced (stops automatically ~1s after the user pauses typing)
- Online user roster per room, updated in real time as people join/leave
- Private direct messages between two users, delivered only to the intended recipient

## Setup

```bash
npm install
npm run dev   # or npm start
```

Open `http://localhost:5000` in the browser, enter a name, pick an avatar, and join a room.

## One necessary deviation from the spec
The spec's `room:userlist` payload is `{ room, users: ["Aarav", "Priya"] }` — just usernames. But
`direct:send` needs a `recipientId` (socket ID) to actually deliver a DM to the right person, and
usernames alone can't provide that (two people could share a name, and there's no other event that
maps a name to a socket ID). So `room:userlist` here sends
`users: [{ userId, username }, ...]` instead of a plain string array — same idea, just enough extra
to make the DM feature in the same spec actually work.

## Testing (from assignment spec)
1. Start the server, open three tabs: Aarav, Priya, Rohan.
2. Aarav and Priya join `#developers`; Rohan joins `#random`.
3. Aarav types in `#developers` → confirm Priya sees "Aarav is typing...", Rohan sees nothing.
4. Send messages in `#developers` → confirm Priya receives them live.
5. Open a 4th tab, join `#developers` → confirm full prior history loads immediately.
6. Click Priya's name in Aarav's user list to DM her → confirm Rohan never sees it.

## Real-Time Event Protocol
| Event | Direction | Payload |
|---|---|---|
| `user:login` | Client → Server | `{ username, avatar }` |
| `room:join` | Client → Server | `{ room }` |
| `room:history` | Server → Client | `{ room, messages }` |
| `room:userlist` | Server → Room | `{ room, users: [{ userId, username }] }` |
| `room:leave` | Client → Server | `{ room }` |
| `chat:send` | Client → Server | `{ room, message }` |
| `chat:receive` | Server → Room | `{ id, sender, message, timestamp }` |
| `typing:start` / `typing:stop` | Client → Server | `{ room }` |
| `typing:update` | Server → Room | `{ username, isTyping }` |
| `direct:send` | Client → Server | `{ recipientId, message }` |
| `direct:receive` | Server → Client | `{ from, fromId, message, timestamp }` |

## Folder Structure
```text
rishi-thakker-068-assignment-13/
├── public/
│   ├── index.html
│   ├── app.js
│   └── style.css
├── sockets/
│   ├── chatHandler.js
│   └── userHandler.js
├── util/
│   └── messageStore.js
├── package.json
├── server.js
└── README.md
```
