# Perplexity

Perplexity is a full-stack AI chat application inspired by Perplexity-style search assistance. It combines a React + Vite frontend with an Express + MongoDB backend to provide user authentication, persistent chat history, and AI-generated responses powered by LangChain and LLM providers.

## Overview

The project is structured as a monorepo with two main folders:

- `Backend/` - Express API, MongoDB models, JWT auth, AI orchestration, and socket server
- `Frontend/` - React app for login, registration, chat dashboard, and Redux-managed state

The app allows a user to:

- register and log in securely
- verify email before access is granted
- create and manage chat threads
- send messages to an AI assistant
- persist chats and messages in MongoDB
- use web search results to answer time-sensitive or current-event questions

## Tech Stack

### Frontend

- React 19
- Vite
- Redux Toolkit
- React Router
- Tailwind CSS
- Axios
- Socket.IO client
- React Markdown + GFM for rendered AI responses

### Backend

- Node.js + Express
- MongoDB + Mongoose
- JWT for authentication
- Cookie-based auth session
- Socket.IO server
- LangChain
- Google Gemini
- Mistral AI
- Tavily for internet search
- Nodemailer for email verification

## Folder Structure

```text
perplexity/
├── Backend/
│   ├── package.json
│   ├── server.js
│   └── src/
│       ├── app.js
│       ├── config/
│       │   └── database.js
│       ├── controllers/
│       │   ├── auth.controller.js
│       │   └── chat.controller.js
│       ├── middleware/
│       │   └── auth.middleware.js
│       ├── models/
│       │   ├── chat.model.js
│       │   ├── message.model.js
│       │   └── user.model.js
│       ├── routes/
│       │   ├── auth.routes.js
│       │   └── chat.routes.js
│       ├── services/
│       │   ├── ai.service.js
│       │   ├── internet.service.js
│       │   └── mail.service.js
│       ├── sockets/
│       │   └── server.socket.js
│       └── validators/
│           └── auth.validator.js
├── Frontend/
│   ├── package.json
│   ├── index.html
│   ├── vite.config.js
│   ├── eslint.config.js
│   └── src/
│       ├── app/
│       │   ├── App.jsx
│       │   ├── app.routes.jsx
│       │   ├── app.store.js
│       │   └── index.css
│       ├── features/
│       │   ├── auth/
│       │   │   ├── auth.slice.js
│       │   │   ├── components/
│       │   │   │   └── Protected.jsx
│       │   │   ├── hook/
│       │   │   │   └── useAuth.js
│       │   │   ├── pages/
│       │   │   │   ├── Login.jsx
│       │   │   │   └── Register.jsx
│       │   │   └── service/
│       │   │       └── auth.api.js
│       │   └── chat/
│       │       ├── chat.slice.js
│       │       ├── hooks/
│       │       │   └── useChat.js
│       │       ├── pages/
│       │       │   └── Dashboard.jsx
│       │       └── service/
│       │           ├── chat.api.js
│       │           └── chat.socket.js
│       ├── main.jsx
│       └── assets/
└── README.md
```

## How It Works

### Authentication Flow

- The frontend provides a login / registration UI.
- Requests are sent to the Express backend via Axios.
- The backend validates input using `express-validator`.
- User credentials are checked against MongoDB.
- Passwords are compared using `bcryptjs`.
- A JWT is generated and stored in a cookie for protected routes.
- `authUser` middleware verifies the token before chat endpoints can be used.
- Email verification is handled through a JWT-based verification link sent to the user's inbox.

### Chat Flow

- A user sends a message from the dashboard.
- The backend creates or reuses a chat record and stores the user message.
- The message history is passed to the AI service.
- The AI service uses LangChain and a configured model to generate a response.
- For queries requiring up-to-date information, the system calls the Tavily search tool through the `searchInternet` function.
- The generated AI reply is stored as a message and returned to the frontend.

### Real-Time Layer

- Socket.IO is initialized in the backend at server startup.
- The frontend connects to the socket server using `socket.io-client`.
- This architecture is prepared for real-time events such as notifications or live chat updates.

## Environment Variables

Create a `.env` file inside the `Backend` folder with the following values:

```env
PORT=3000
JWT_SECRET=your_jwt_secret
MONGODB_URI=mongodb://localhost:27017/perplexity
GEMINI_API_KEY=your_google_gemini_key
MISTRAL_API_KEY=your_mistral_key
TAVILY_API_KEY=your_tavily_key

GOOGLE_USER=your@gmail.com
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
GOOGLE_REFRESH_TOKEN=your_google_refresh_token
```

These environment variables power:

- database connection
- JWT signing
- LLM access
- internet search
- email verification delivery

## Setup Instructions

### 1. Install backend dependencies

```bash
cd Backend
npm install
```

### 2. Install frontend dependencies

```bash
cd Frontend
npm install
```

### 3. Start MongoDB

Make sure MongoDB is running locally, or update `MONGODB_URI` to point to your hosted database.

### 4. Start the backend

```bash
cd Backend
npm run dev
```

The backend will run on:

```text
http://localhost:3000
```

### 5. Start the frontend

```bash
cd Frontend
npm run dev
```

The frontend will run on:

```text
http://localhost:5173
```

## Core API Routes

### Auth

- `POST /api/auth/register` - register a new user
- `POST /api/auth/login` - log in and receive a JWT cookie
- `GET /api/auth/get-me` - fetch current logged-in user
- `GET /api/auth/verify-email` - verify email using token

### Chats

- `POST /api/chats/message` - send a message and receive AI response
- `GET /api/chats` - get all chats for the current user
- `GET /api/chats/:chatId/messages` - fetch messages for a specific chat
- `DELETE /api/chats/delete/:chatId` - delete a chat and its messages

## Notes

- The app is designed around a Perplexity-like AI assistant experience, where the LLM can make source-backed or web-informed answers.
- The search tool is integrated through Tavily, which makes the AI more useful for current information queries.
- The frontend uses Redux slices to track user data and chat state, while the backend stores persistent data in MongoDB.

## License

This project is a study/workshop-style app and does not include a production-grade license file by default.

## Future Improvements

Possible enhancements include:

- proper UI wiring for full registration flow
- chat deletion from the frontend
- streaming AI responses
- message editing and retry
- improved search result citations
- better error handling and toast notifications
- production deployment configuration
