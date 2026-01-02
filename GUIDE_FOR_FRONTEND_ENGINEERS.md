# 🎓 Auth API Starter - Explained for Frontend Engineers

A comprehensive guide to understanding this backend authentication API from a frontend perspective.

---

## 🤔 What Did We Build?

Think of this as the **"backend brain"** that handles everything related to **users logging in and out** of your app. 

When you build a React/Vue/Angular app with a login page, you need *something* on the server to:
- ✅ Save new users when they sign up
- ✅ Check if their password is correct when they log in
- ✅ Give them a "pass" (token) to prove they're logged in
- ✅ Remember who they are on every page

**That's exactly what this API does!**

---

## 🏠 Real-World Analogy: A Hotel

Imagine your app is a **hotel**:

| Hotel Concept | Our API Equivalent |
|---------------|-------------------|
| 🏨 **Reception desk** | The API server |
| 📝 **Guest registration book** | PostgreSQL database |
| 🔑 **Room key card** | Access Token (JWT) |
| 🎫 **VIP membership card** | Refresh Token |
| 🚪 **Security guard** | Authentication middleware |
| 🚦 **"Only 5 visits per hour"** | Rate limiting |

### The Flow:
1. **Register** = Guest fills out registration form → Gets room key
2. **Login** = Guest shows ID → Gets room key
3. **Access Token** = Room key (expires every 15 min for security)
4. **Refresh Token** = VIP card to get a new room key without showing ID again
5. **Logout** = Return the key, can't access room anymore

---

## 📦 What's Inside (The Building Blocks)

### 1. **Express.js** - The Web Server

```
Frontend analogy: Like React is your framework for UI, 
Express is the framework for handling HTTP requests.

When your frontend does: fetch('/api/auth/login')
Express catches that request and knows what to do.
```

### 2. **PostgreSQL + Prisma** - The Database

```
Frontend analogy: Like localStorage, but:
- Lives on a server (not browser)
- Can store MILLIONS of users
- Survives even if you clear browser data
- Prisma is like an ORM that makes database queries feel like JavaScript
```

### 3. **JWT (JSON Web Token)** - The "Proof of Login"

```
Frontend analogy: Like a signed permission slip.

When user logs in, backend gives them a token:
"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."

Your frontend stores this and sends it with every request:
headers: { "Authorization": "Bearer eyJhbGciOiJIUzI1NiIs..." }

Backend checks this token to know WHO is making the request.
```

### 4. **Helmet** - Security Headers

```
Frontend analogy: Like adding CSP meta tags, but automatic.
Protects against XSS, clickjacking, and other attacks.
```

### 5. **CORS** - Cross-Origin Permissions

```
You know this one! 😅
When your React app on localhost:3000 calls API on localhost:5000,
CORS is what allows (or blocks) that communication.
```

### 6. **Rate Limiting** - Anti-Spam Protection

```
Frontend analogy: Like disabling a button after click.
But on server level - stops hackers from trying 
1 million passwords per second.
```

### 7. **Swagger** - API Documentation

```
Frontend analogy: Like Storybook for components.
Interactive docs where you can TEST the API without writing code.
Visit /api-docs to see all endpoints and try them!
```

---

## 🔄 How Frontend Connects to This

Here's how YOUR frontend code would use this API:

### **1. User Registration**

```javascript
// Your React/Vue component
const register = async (email, password, firstName, lastName) => {
  const response = await fetch('http://localhost:3000/api/auth/register', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ email, password, firstName, lastName })
  });
  
  const data = await response.json();
  // data = { accessToken: "xxx", refreshToken: "yyy", user: {...} }
  
  // Save tokens
  localStorage.setItem('accessToken', data.data.accessToken);
  localStorage.setItem('refreshToken', data.data.refreshToken);
};
```

### **2. User Login**

```javascript
const login = async (email, password) => {
  const response = await fetch('http://localhost:3000/api/auth/login', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ email, password })
  });
  
  const data = await response.json();
  localStorage.setItem('accessToken', data.data.accessToken);
  localStorage.setItem('refreshToken', data.data.refreshToken);
};
```

### **3. Access Protected Data**

```javascript
const getProfile = async () => {
  const token = localStorage.getItem('accessToken');
  
  const response = await fetch('http://localhost:3000/api/auth/me', {
    headers: { 
      'Authorization': `Bearer ${token}`  // 👈 This proves you're logged in!
    }
  });
  
  const data = await response.json();
  // data = { user: { id, email, firstName, lastName } }
};
```

### **4. Refresh Expired Token**

```javascript
const refreshTokens = async () => {
  const refreshToken = localStorage.getItem('refreshToken');
  
  const response = await fetch('http://localhost:3000/api/auth/refresh', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ refreshToken })
  });
  
  const data = await response.json();
  // Save new tokens
  localStorage.setItem('accessToken', data.data.accessToken);
  localStorage.setItem('refreshToken', data.data.refreshToken);
};
```

### **5. Logout**

```javascript
const logout = async () => {
  const refreshToken = localStorage.getItem('refreshToken');
  
  await fetch('http://localhost:3000/api/auth/logout', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ refreshToken })
  });
  
  localStorage.removeItem('accessToken');
  localStorage.removeItem('refreshToken');
  // Redirect to login page
};
```

---

## 🌍 Where Can This Be Used?

This API is the **authentication foundation** for almost ANY web/mobile app:

| App Type | How This API Helps |
|----------|-------------------|
| 📱 **Social Media** | User accounts, login, profiles |
| 🛒 **E-commerce** | Customer accounts, order history |
| 📊 **Dashboard Apps** | Admin logins, team accounts |
| 📚 **Learning Platforms** | Student/teacher accounts |
| 💬 **Chat Apps** | User identity, who sent messages |
| 🎮 **Gaming Apps** | Player profiles, save progress |
| 📝 **Blog/CMS** | Author accounts, content ownership |
| 💼 **SaaS Products** | Company accounts, subscriptions |

**Basically: If your app needs users to "log in", this API handles that!**

---

## 📊 Visual Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                        YOUR FRONTEND                            │
│  (React, Vue, Angular, Next.js, etc.)                          │
└─────────────────────────────────────────────────────────────────┘
                              │
                              │  HTTP Requests
                              │  (fetch / axios)
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                     AUTH API STARTER                            │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐       │
│  │ Helmet   │  │  CORS    │  │Rate Limit│  │ Express  │       │
│  │(Security)│  │(Origins) │  │(Anti-DDoS)│ │ (Server) │       │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘       │
│                              │                                  │
│                              ▼                                  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                    AUTH ROUTES                            │  │
│  │  POST /register  │  POST /login  │  POST /refresh        │  │
│  │  POST /logout    │  GET /me      │  POST /logout-all     │  │
│  └──────────────────────────────────────────────────────────┘  │
│                              │                                  │
│                              ▼                                  │
│  ┌───────────────────┐    ┌───────────────────┐               │
│  │   JWT Tokens      │    │   Prisma ORM      │               │
│  │ (Access/Refresh)  │    │ (Database Query)  │               │
│  └───────────────────┘    └───────────────────┘               │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                     POSTGRESQL DATABASE                         │
│  ┌─────────────────────┐  ┌─────────────────────────────────┐  │
│  │       Users         │  │       Refresh Tokens            │  │
│  │  - id               │  │  - id                           │  │
│  │  - email            │  │  - token                        │  │
│  │  - password (hash)  │  │  - userId (FK)                  │  │
│  │  - firstName        │  │  - expiresAt                    │  │
│  │  - lastName         │  │                                 │  │
│  └─────────────────────┘  └─────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🎯 Summary in One Sentence

> **We built a complete user authentication system that your frontend can call to register users, log them in, keep them logged in with tokens, and log them out - with security protections built-in.**

---

## 💡 Why This is Valuable for Your Portfolio

As a frontend engineer, having this shows employers:

1. **You understand the full picture** - not just UI, but how auth works end-to-end
2. **You can work with backend devs** - you know what to expect from APIs
3. **Security awareness** - you understand tokens, hashing, rate limiting
4. **You're becoming full-stack** - a huge career advantage!

---

## 🔗 Quick Reference: API Endpoints

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | `/api/auth/register` | Register new user | ❌ |
| POST | `/api/auth/login` | Login user | ❌ |
| POST | `/api/auth/refresh` | Refresh access token | ❌ |
| POST | `/api/auth/logout` | Logout (invalidate token) | ❌ |
| POST | `/api/auth/logout-all` | Logout from all devices | ✅ |
| GET | `/api/auth/me` | Get current user profile | ✅ |
| GET | `/api/health` | Health check | ❌ |

---

## 📚 Further Learning

- [JWT.io](https://jwt.io/) - Decode and learn about JWT tokens
- [Express.js Guide](https://expressjs.com/en/guide/routing.html) - Learn Express routing
- [Prisma Docs](https://www.prisma.io/docs) - Database ORM documentation
- [OWASP Auth Guidelines](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html) - Security best practices

---

*Created with ❤️ to help frontend engineers understand backend authentication*

