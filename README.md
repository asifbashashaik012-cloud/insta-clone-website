# insta-clone-website
This is a clear clone of insta website . tutorial

Perfect, Shaik — let’s build a **longer, detailed tutorial** for an Instagram‑style clone website. I’ll walk you through everything from setup to advanced features so you can treat this like a full project guide.

---

# 📖 Insta‑Clone Website Tutorial

## 1. Project Overview
We’re creating a simplified Instagram clone where users can:
- Sign up / log in
- Upload images with captions
- Like and comment on posts
- View profiles
- Follow/unfollow other users

This won’t be 100% Instagram, but it will give you the **core social media mechanics**.

---

## 2. Tech Stack
- **Frontend:** React + CSS (for UI)
- **Backend:** Node.js + Express (API)
- **Database:** MongoDB (data storage)
- **Auth:** JWT (secure login)
- **Image Storage:** Cloudinary (upload images easily)

---

## 3. Project Structure
```
insta-clone/
│── backend/
│   ├── server.js
│   ├── models/
│   ├── routes/
│── frontend/
│   ├── src/
│   │   ├── App.js
│   │   ├── components/
│   │   ├── pages/
│   ├── index.html
│   ├── style.css
```

---

## 4. Backend Setup

### Install Dependencies
```bash
mkdir backend && cd backend
npm init -y
npm install express mongoose cors bcrypt jsonwebtoken cloudinary multer
```

### server.js
```js
const express = require("express");
const mongoose = require("mongoose");
const cors = require("cors");

const app = express();
app.use(express.json());
app.use(cors());

mongoose.connect("mongodb://localhost:27017/instaclone");

app.get("/", (req, res) => res.send("Backend running"));

app.listen(5000, () => console.log("Server started on port 5000"));
```

---

## 5. Models

### User.js
```js
const mongoose = require("mongoose");

const userSchema = new mongoose.Schema({
  username: String,
  email: String,
  password: String,
  followers: [{ type: mongoose.Schema.Types.ObjectId, ref: "User" }],
  following: [{ type: mongoose.Schema.Types.ObjectId, ref: "User" }]
});

module.exports = mongoose.model("User", userSchema);
```

### Post.js
```js
const mongoose = require("mongoose");

const postSchema = new mongoose.Schema({
  caption: String,
  imageUrl: String,
  user: { type: mongoose.Schema.Types.ObjectId, ref: "User" },
  likes: [{ type: mongoose.Schema.Types.ObjectId, ref: "User" }],
  comments: [{ text: String, user: String }]
}, { timestamps: true });

module.exports = mongoose.model("Post", postSchema);
```

---

## 6. Authentication Routes
```js
const jwt = require("jsonwebtoken");
const bcrypt = require("bcrypt");
const User = require("./models/User");

app.post("/signup", async (req, res) => {
  const { username, email, password } = req.body;
  const hashed = await bcrypt.hash(password, 10);
  const user = new User({ username, email, password: hashed });
  await user.save();
  res.json({ message: "User created" });
});

app.post("/login", async (req, res) => {
  const { email, password } = req.body;
  const user = await User.findOne({ email });
  if (!user) return res.status(400).send("User not found");
  const valid = await bcrypt.compare(password, user.password);
  if (!valid) return res.status(400).send("Invalid password");
  const token = jwt.sign({ id: user._id }, "secretKey");
  res.json({ token });
});
```

---

## 7. Image Upload (Cloudinary)
```js
const cloudinary = require("cloudinary").v2;
cloudinary.config({
  cloud_name: "your_cloud_name",
  api_key: "your_api_key",
  api_secret: "your_api_secret"
});

app.post("/upload", async (req, res) => {
  const file = req.body.image; // base64 or file path
  const result = await cloudinary.uploader.upload(file);
  res.json({ url: result.secure_url });
});
```

---

## 8. Frontend Setup
```bash
npx create-react-app frontend
cd frontend
npm install axios react-router-dom
```

### App.js
```jsx
import React, { useState, useEffect } from "react";
import axios from "axios";

function App() {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    axios.get("http://localhost:5000/posts")
      .then(res => setPosts(res.data));
  }, []);

  return (
    <div>
      <h1>Insta Clone</h1>
      {posts.map(post => (
        <div key={post._id}>
          <img src={post.imageUrl} alt="post" width="400" />
          <p>{post.caption}</p>
        </div>
      ))}
    </div>
  );
}

export default App;
```

---

## 9. Styling (style.css)
```css
body {
  font-family: Arial, sans-serif;
  background: #fafafa;
  margin: 0;
  padding: 0;
}
img {
  border-radius: 10px;
  margin: 10px;
}
.post {
  border: 1px solid #ddd;
  padding: 10px;
  margin: 20px auto;
  width: 400px;
  background: #fff;
}
```

---

## 10. Advanced Features
- **Likes & Comments:** Add routes to update likes/comments in Post model.
- **Profiles:** Fetch user posts by ID.
- **Follow/Unfollow:** Update `followers` and `following` arrays in User model.
- **Stories/Reels:** Use short‑lived posts with expiration timestamps.

---

## ✅ Wrap‑Up
This tutorial gives you a **complete roadmap**:
- Backend with Express + MongoDB
- Authentication with JWT
- Image uploads via Cloudinary
- React frontend to display posts
- Extendable features (likes, comments, profiles, follow system)

---

Shaik, do you want me to **expand this into a full course‑style guide** (with separate lessons for backend, frontend, and deployment), or keep it as a **single long tutorial document**?
