To create a blog website using the MERN stack, follow these steps. This project will help you practice with React Router, Express, MongoDB, and Node.js while building a complete blog platform. Let's break down the process into the following main sections:

### Step 1: Setting up the Backend (Express and MongoDB)

1. **Initialize the Project**
   - Create a new directory for the backend.
   - Run `npm init` to initialize a new Node.js project.
   - Install dependencies: `npm install express mongoose dotenv cors`.
   - Install `nodemon` for development: `npm install -D nodemon`.

2. **Create the Server**
   - Create a file called `server.js`.
   - Set up the Express server:
     ```js
     const express = require('express');
     const mongoose = require('mongoose');
     const cors = require('cors');
     const dotenv = require('dotenv');

     dotenv.config();
     const app = express();

     app.use(cors());
     app.use(express.json());

     // Connect to MongoDB
     mongoose.connect(process.env.MONGO_URI, { useNewUrlParser: true, useUnifiedTopology: true })
       .then(() => console.log('MongoDB connected'))
       .catch(err => console.error(err));

     const PORT = process.env.PORT || 5000;
     app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
     ```

3. **Create Models**
   - Create a `models` directory and add a `Post.js` file:
     ```js
     const mongoose = require('mongoose');

     const PostSchema = new mongoose.Schema({
       title: { type: String, required: true },
       content: { type: String, required: true },
       category: { type: String, required: true },
       createdAt: { type: Date, default: Date.now },
     });

     module.exports = mongoose.model('Post', PostSchema);
     ```

4. **Create API Endpoints**
   - Create a `routes` directory and add a `posts.js` file:
     ```js
     const express = require('express');
     const Post = require('../models/Post');

     const router = express.Router();

     // Create a new post
     router.post('/', async (req, res) => {
       try {
         const newPost = new Post(req.body);
         const savedPost = await newPost.save();
         res.status(201).json(savedPost);
       } catch (err) {
         res.status(500).json(err);
       }
     });

     // Get all posts
     router.get('/', async (req, res) => {
       try {
         const posts = await Post.find();
         res.status(200).json(posts);
       } catch (err) {
         res.status(500).json(err);
       }
     });

     // Update a post
     router.put('/:id', async (req, res) => {
       try {
         const updatedPost = await Post.findByIdAndUpdate(req.params.id, req.body, { new: true });
         res.status(200).json(updatedPost);
       } catch (err) {
         res.status(500).json(err);
       }
     });

     // Delete a post
     router.delete('/:id', async (req, res) => {
       try {
         await Post.findByIdAndDelete(req.params.id);
         res.status(200).json('Post deleted');
       } catch (err) {
         res.status(500).json(err);
       }
     });

     module.exports = router;
     ```
   - Add the route to `server.js`:
     ```js
     const postRoutes = require('./routes/posts');
     app.use('/api/posts', postRoutes);
     ```

### Step 2: Setting up the Frontend (React)

1. **Initialize React App**
   - Create a new directory for the frontend.
   - Run `npx create-react-app blog-frontend` to create a React app.
   - Navigate into the directory: `cd blog-frontend`.
   - Install React Router: `npm install react-router-dom`.
   - Install Axios for API requests: `npm install axios`.

2. **Set Up Routing**
   - Create pages for `Home`, `CreatePost`, `EditPost`, and `PostDetails`.
   - In `App.js`, set up routing:
     ```jsx
     import { BrowserRouter as Router, Route, Routes } from 'react-router-dom';
     import Home from './pages/Home';
     import CreatePost from './pages/CreatePost';
     import EditPost from './pages/EditPost';
     import PostDetails from './pages/PostDetails';

     function App() {
       return (
         <Router>
           <Routes>
             <Route path='/' element={<Home />} />
             <Route path='/create' element={<CreatePost />} />
             <Route path='/edit/:id' element={<EditPost />} />
             <Route path='/post/:id' element={<PostDetails />} />
           </Routes>
         </Router>
       );
     }

     export default App;
     ```

3. **Create Components**
   - Use Axios to interact with the backend APIs for creating, editing, and deleting blog posts.
   - For example, in `Home.js`:
     ```jsx
     import React, { useEffect, useState } from 'react';
     import axios from 'axios';
     import { Link } from 'react-router-dom';

     const Home = () => {
       const [posts, setPosts] = useState([]);

       useEffect(() => {
         const fetchPosts = async () => {
           try {
             const res = await axios.get('http://localhost:5000/api/posts');
             setPosts(res.data);
           } catch (err) {
             console.error(err);
           }
         };
         fetchPosts();
       }, []);

       return (
         <div>
           <h1>Blog Posts</h1>
           <Link to='/create'>Create New Post</Link>
           {posts.map(post => (
             <div key={post._id}>
               <h2>{post.title}</h2>
               <p>{post.content}</p>
               <Link to={`/edit/${post._id}`}>Edit</Link>
               <Link to={`/post/${post._id}`}>View Details</Link>
             </div>
           ))}
         </div>
       );
     };

     export default Home;
     ```

### Step 3: Additional Features

- **User Authentication**: Use libraries like `jsonwebtoken` and `bcrypt` to add authentication (optional).
- **Markdown Support**: Use a library like `react-markdown` to allow users to write their posts in markdown format.
- **Categorization**: Update the `Post` model to include categories, and filter posts based on categories in the frontend.

### Step 4: Running the Application

1. **Backend**: Run the backend server using `nodemon`:
   ```bash
   nodemon server.js
   ```

2. **Frontend**: Run the React app:
   ```bash
   npm start
   ```

### Step 5: Connecting Frontend and Backend
- Make sure to configure CORS correctly in your backend.
- Use Axios in your React components to interact with the Express API.

Now you have a complete blogging platform with the MERN stack! You can extend it by adding more features, improving the UI, or adding authentication to enhance your skills.
