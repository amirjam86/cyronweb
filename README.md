# TLTGramChat

A Telegram-like web messenger focused on security, speed, and real-time UX.

## Stack
- Backend: Node.js + Express + Socket.IO
- DB: PostgreSQL
- Presence/Cache: Redis
- Reverse Proxy/TLS: Nginx + Certbot
- Push: Web Push + Service Worker + VAPID
- Calls: WebRTC with signaling over Socket.IO

## Core Features
- Separate register/login flows
- Register: `name + username + email + password`
- Login with `email` or `username`
- `bcrypt` password hashing
- JWT auth
- Login audit logs (`IP`, `User-Agent`, `success/fail`, timestamp)
- Direct chats + groups + channels
- Public/private groups and channels
- Public join links: `/gap/{slug}`
- Text + media/file messages
- Timed self-destruct images (`ttlSeconds`)
- At-rest message text encryption with AES-256-GCM
- Add contact by username
- Global search: users + groups + channels
- Chat ordering by last activity
- Online visibility toggle
- WebRTC signaling events (`offer`, `answer`, `ice_candidate`, `end` aliases supported)
- Web Push for offline users
- Admin panel (login logs + password reset by username)

## Security Controls
- HTTPS-first deployment flow in `install.sh`
- Helmet with CSP
- Restricted CORS
- Rate limiting on auth/write/socket events
- Strong secrets generated during install (`JWT`, encryption key, DB password, VAPID)
- Strict input validation with `zod`
- Unsafe upload MIME rejection
- Secret redaction in logs

## Local Development

### 1) Prerequisites
- Node.js 20+
- PostgreSQL 14+
- Redis 7+

### 2) Configure env
```bash
cp .env.example .env
# edit .env
```

### 3) Install and run
```bash
npm install
npm run migrate
npm run seed:admin
npm run dev
```

App: `http://localhost:3000`

## Production (Ubuntu/Debian)
Use the one-step installer:

```bash
chmod +x install.sh
sudo ./install.sh
```

It will:
- install Node, PostgreSQL, Redis, Nginx, Certbot
- prompt for domain + SSL email + admin credentials
- generate secure secrets automatically
- create DB/user, run migrations, seed admin
- create and enable `systemd` service
- configure Nginx reverse proxy + TLS redirect

## Selected API Endpoints
- `POST /api/auth/register`
- `POST /api/auth/login`
- `GET /api/auth/me`
- `PATCH /api/auth/visibility`
- `GET /api/chats`
- `POST /api/chats/direct`
- `POST /api/chats/group`
- `POST /api/chats/channel`
- `GET /api/chats/:chatId/messages`
- `POST /api/chats/:chatId/messages`
- `GET /api/gap/:slug`
- `POST /api/gap/:slug/join`
- `POST /api/uploads`
- `POST /api/push/subscribe`
- `GET /api/admin/login-logs` (admin)
- `POST /api/admin/users/:username/reset-password` (admin)

## Socket Events
- `message:send` -> `message:new`
- `call:offer` / `offer`
- `call:answer` / `answer`
- `call:ice_candidate` / `ice_candidate`
- `call:end` / `end`

## Notes
- Uploaded files are stored in `storage/uploads`.
- Expired self-destruct messages are cleaned periodically by a background job.
- CSP allows JSDelivr for Apple-style emoji assets used in the emoji button/picker.
