# Week 12: CommunityHub — Full-Stack Deployment

## Author
- **Name:** ESTHER WANJIRU NJOROGE
- **GitHub:** [@essyken](https://github.com/essyken)
- **Date:** May 12, 2026

## Project Description
The final week brings everything together. The React frontend is connected to the Express + MongoDB backend, CORS is configured for cross-origin communication, authentication flows through React Context, and the complete application is deployed — frontend on Vercel and backend on Render. This marks the completion of the CommunityHub full-stack platform built across the IYF Weekend Academy Season 10.

## Technologies Used

**Frontend**
- React 18
- React Router DOM
- Vite
- Context API (auth state management)
- JavaScript (ES6+)

**Backend**
- Node.js
- Express.js
- MongoDB Atlas
- Mongoose
- bcryptjs
- jsonwebtoken (JWT)
- cors
- dotenv

**Deployment**
- Vercel (frontend)
- Render (backend)

## Features
- React frontend fully connected to Express API via a centralised `api.js` service
- JWT token stored in `localStorage` and attached automatically to every protected request
- Auth Context (`AuthContext`) managing user session across the entire React app
- `ProtectedRoute` component that redirects unauthenticated users to `/login`
- CORS configured on the backend to allow requests from the Vercel frontend
- Health check endpoint (`GET /api/health`) reporting server uptime and database status
- Full CRUD on posts — create, read, update, delete — with auth guards
- Like and comment on posts
- User-friendly error messages and loading states throughout the UI
- Responsive design that works on mobile and desktop
- Environment variables validated on server startup — server exits if required vars are missing

## Project Structure

```
community-hub/
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   └── ProtectedRoute.jsx
│   │   ├── context/
│   │   │   └── AuthContext.jsx
│   │   ├── pages/
│   │   │   ├── Home.jsx
│   │   │   ├── Posts.jsx
│   │   │   ├── PostDetail.jsx
│   │   │   ├── CreatePost.jsx
│   │   │   ├── Login.jsx
│   │   │   └── Register.jsx
│   │   ├── services/
│   │   │   └── api.js
│   │   ├── App.jsx
│   │   └── main.jsx
│   ├── .env.example
│   └── package.json
├── backend/
│   ├── src/
│   │   ├── config/
│   │   │   └── database.js
│   │   ├── models/
│   │   │   ├── Post.js
│   │   │   ├── Comment.js
│   │   │   └── User.js
│   │   ├── routes/
│   │   │   ├── index.js
│   │   │   ├── auth.js
│   │   │   ├── posts.js
│   │   │   └── users.js
│   │   ├── middleware/
│   │   │   ├── auth.js
│   │   │   ├── logger.js
│   │   │   └── errorHandler.js
│   │   ├── controllers/
│   │   │   ├── authController.js
│   │   │   ├── postsController.js
│   │   │   └── commentsController.js
│   │   └── app.js
│   ├── server.js
│   ├── .env.example
│   └── package.json
└── README.md
```

## How to Run Locally

### Prerequisites
- Node.js 18+
- MongoDB Atlas account

### Backend

1. Clone this repository
   ```bash
   git clone https://github.com/essyken/iyf-s10-week-12-essyken.git
   cd iyf-s10-week-12-essyken/backend
   ```
2. Install dependencies
   ```bash
   npm install
   ```
3. Set up environment variables
   ```bash
   cp .env.example .env
   ```
   Edit `.env` with your values:
   ```
   NODE_ENV=development
   PORT=3000
   MONGODB_URI=mongodb+srv://<username>:<password>@cluster.mongodb.net/community-hub
   JWT_SECRET=your-secret-key-min-32-characters
   JWT_EXPIRES_IN=7d
   FRONTEND_URL=http://localhost:5173
   ```
4. Start the backend server
   ```bash
   npm run dev
   ```
   API will be running at `http://localhost:3000`

### Frontend

1. Open a new terminal and navigate to the frontend folder
   ```bash
   cd ../frontend
   ```
2. Install dependencies
   ```bash
   npm install
   ```
3. Set up environment variables
   ```bash
   cp .env.example .env
   ```
   Edit `.env`:
   ```
   VITE_API_URL=http://localhost:3000/api
   ```
4. Start the dev server
   ```bash
   npm run dev
   ```
   App will be running at `http://localhost:5173`

## API Endpoints

### Auth

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | /api/auth/register | Register a new user |
| POST | /api/auth/login | Login and receive JWT |
| GET | /api/auth/me | Get current user (protected) |

### Posts

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /api/posts | Get all posts (supports ?author=, ?sort=, ?search=, ?page=, ?limit=) |
| GET | /api/posts/:id | Get a single post |
| POST | /api/posts | Create a post (protected) |
| PUT | /api/posts/:id | Update a post — author only (protected) |
| DELETE | /api/posts/:id | Delete a post — author only (protected) |
| PATCH | /api/posts/:id/like | Like a post |

### Comments

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /api/posts/:id/comments | Get comments on a post |
| POST | /api/posts/:id/comments | Add a comment (protected) |
| DELETE | /api/posts/:id/comments/:commentId | Delete a comment (protected) |

### Health

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /api/health | Server uptime and database status |

> **Protected routes** require an `Authorization: Bearer <token>` header.

## Lessons Learned
- How to wire a React frontend to an Express API using a centralised service layer (`api.js`) that handles auth headers and 401 redirects automatically
- How React Context and `useEffect` work together to restore a user session on page refresh by re-fetching `/api/auth/me` if a token exists in `localStorage`
- How CORS works and why the backend must explicitly allow the frontend's origin, especially when they run on different domains in production
- How to validate required environment variables at startup so the server fails fast with a clear message rather than crashing silently later
- How Vercel and Render handle environment variables separately from the codebase, keeping secrets out of version control
- How to build React for production with `npm run build` and serve the `dist` folder as static files from Express

## Challenges Faced
- **CORS errors in production:** The frontend's Vercel URL wasn't in the backend's allowed origins list; fixed by setting `FRONTEND_URL` as an environment variable on Render and adding it to `corsOptions`.
- **Token persistence on refresh:** After a page refresh the user was logged out even with a valid token; solved by checking `localStorage` in `AuthContext`'s `useEffect` and calling `/api/auth/me` to restore the session.
- **Environment variable prefix:** Vite requires frontend env vars to start with `VITE_` — using `API_URL` instead of `VITE_API_URL` meant the variable was `undefined` at runtime.
- **Render cold starts:** The free Render tier spins down after inactivity, causing the first request to take 30–60 seconds; documented this in the README so users know to wait on first load.

## Live Demo
