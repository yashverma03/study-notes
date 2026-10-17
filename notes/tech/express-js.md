# Index

- [Intro](#intro)


# Intro

Express.js is a minimal and flexible Node.js web framework used to build web applications and APIs

```javascript
const express = require('express');
const cors = require('cors');

const app = express();
const PORT = 3000;

const users = [
  { id: 1, name: 'Alice', email: 'alice@mail.com' },
  { id: 2, name: 'Bob', email: 'bob@mail.com' },
];

// --- Middleware ---

app.use(cors());
app.use(express.json());

// Logger middleware
app.use((req, res, next) => {
  console.log(`[${req.method}] ${req.url}`);
  next();
});

// Mock authentication middleware
app.use((req, res, next) => {
  // Fake logged-in user
  req.user = { id: 99, name: 'Admin' };
  next();
});

// --- Routes ---

// Home
app.get('/', (req, res) => {
  res.send(`Welcome, ${req.user.name}`);
});

// GET /users - optionally filter by ?name=
app.get('/users', (req, res) => {
  const { name } = req.query;
  if (name) {
    const filtered = users.filter(u => u.name.toLowerCase().includes(name.toLowerCase()));
    return res.json(filtered);
  }
  res.json(users);
});

// GET /users/:id - get user by ID
app.get('/users/:id', (req, res) => {
  const userId = parseInt(req.params.id);
  const user = users.find(u => u.id === userId);
  if (!user) {
    return res.status(404).json({ error: 'User not found' });
  }
  res.json(user);
});

// POST /users - create new user
app.post('/users', (req, res) => {
  const { id, name, email } = req.body;
  if (!id || !name || !email) {
    return res.status(400).json({ error: 'id, name, and email are required' });
  }
  users.push({ id, name, email });
  res.status(201).json({ message: 'User created', createdBy: req.user.name });
});

// 404 handler
app.use((req, res) => {
  res.status(404).json({ error: 'Route not found' });
});

// Global error handler
app.use((err, req, res, next) => {
  console.error('Error:', err.message);
  res.status(500).json({ error: 'Internal Server Error' });
});

// --- Start Server ---
app.listen(PORT, () => {
  console.log(`🚀 Server running at http://localhost:${PORT}`);
});
```
