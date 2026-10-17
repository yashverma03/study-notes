# Intro

Object Data Modeling (ODM) library for MongoDB and Node.js

```javascript
// --------------------------------------------
// ✅ IMPORT & CONNECT TO MONGODB
// --------------------------------------------

import mongoose from 'mongoose';

async function main() {
  try {
    await mongoose.connect('mongodb://localhost:27017/revision_demo');
    console.log('✅ Connected to MongoDB');
  } catch (err) {
    console.error('❌ MongoDB connection failed:', err);
    return;
  }

  // --------------------------------------------
  // ✅ USER SCHEMA WITH VALIDATION
  // --------------------------------------------

  const userSchema = new mongoose.Schema(
    {
      name: {
        type: String,
        required: true,
        minlength: 2,
        maxlength: 100,
      },
      email: {
        type: String,
        required: true,
        unique: true,
        match: /^[^\s@]+@[^\s@]+\.[^\s@]+$/, // basic email regex
      },
      age: {
        type: Number,
        min: 18,
        max: 100,
      },
      role: {
        type: String,
        enum: ['admin', 'user', 'guest'],
        default: 'user',
      },
    },
    {
      timestamps: true,
    }
  );

  // --------------------------------------------
  // ✅ POST SCHEMA (RELATION: Post → User)
// --------------------------------------------

  const postSchema = new mongoose.Schema(
    {
      title: { type: String, required: true },
      content: { type: String },
      author: {
        type: mongoose.Schema.Types.ObjectId,
        ref: 'User', // Reference to User model
        required: true,
      },
    },
    {
      timestamps: true,
    }
  );

  // --------------------------------------------
  // ✅ MODELS
  // --------------------------------------------

  const User = mongoose.model('User', userSchema);
  const Post = mongoose.model('Post', postSchema);

  // --------------------------------------------
  // ✅ CREATE USER & POST (WITH RELATION)
// --------------------------------------------

  const user = await User.create({
    name: 'Yash',
    email: 'yash@example.com',
    age: 24,
    role: 'admin',
  });

  const post = await Post.create({
    title: 'MongoDB with Mongoose',
    content: 'This is a post about relations in Mongoose.',
    author: user._id, // Link to User
  });

  console.log('📥 Created User:', user);
  console.log('📥 Created Post:', post);

  // --------------------------------------------
  // ✅ READ POST WITH POPULATED AUTHOR
  // --------------------------------------------

  const postWithUser = await Post.findOne({ _id: post._id }).populate('author');
  console.log('🔍 Post with Author (populated):', postWithUser);

  // --------------------------------------------
  // ✅ UPDATE USER
  // --------------------------------------------

  const updatedUser = await User.findOneAndUpdate(
    { email: 'yash@example.com' },
    { age: 25 },
    { new: true }
  );
  console.log('✏️ Updated User:', updatedUser);

  // --------------------------------------------
  // ✅ DELETE POST
  // --------------------------------------------

  const deletedPost = await Post.deleteOne({ _id: post._id });
  console.log('🗑️ Deleted Post:', deletedPost);

  // --------------------------------------------
  // ✅ DELETE USER
  // --------------------------------------------

  const deletedUser = await User.deleteOne({ _id: user._id });
  console.log('🗑️ Deleted User:', deletedUser);

  // --------------------------------------------
  // ✅ CLOSE CONNECTION
  // --------------------------------------------

  await mongoose.connection.close();
  console.log('🔌 MongoDB connection closed');
}

// Run the function
main();
```
