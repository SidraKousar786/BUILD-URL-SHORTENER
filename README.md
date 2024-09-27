# BUILD-URL-SHORTENER
To create a URL shortener service as per your requirements, we'll break down the project into three main components: the Frontend (React.js), Backend (Node.js/Express.js), and Database (MongoDB). Here's how we can approach building it:

1. Frontend (React.js)

Steps:

Set up a basic React project.

Create a form to accept the long URL.

Display the shortened URL after submission.

Redirect users when the shortened URL is clicked.


Code Example:

Install necessary packages:

npx create-react-app url-shortener
cd url-shortener
npm install axios

App.js:

import React, { useState } from 'react';
import axios from 'axios';

function App() {
  const [longUrl, setLongUrl] = useState('');
  const [shortUrl, setShortUrl] = useState('');

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      const response = await axios.post('http://localhost:5000/api/shorten', { longUrl });
      setShortUrl(response.data.shortUrl);
    } catch (err) {
      console.error(err);
    }
  };

  return (
    <div>
      <h1>URL Shortener</h1>
      <form onSubmit={handleSubmit}>
        <input
          type="text"
          placeholder="Enter long URL"
          value={longUrl}
          onChange={(e) => setLongUrl(e.target.value)}
        />
        <button type="submit">Shorten</button>
      </form>
      {shortUrl && (
        <div>
          <p>Shortened URL: <a href={shortUrl} target="_blank" rel="noopener noreferrer">{shortUrl}</a></p>
        </div>
      )}
    </div>
  );
}

export default App;

Explanation:

A form allows users to input a long URL.

Upon submitting the form, an API request is sent to the backend to generate a shortened URL.

The shortened URL is displayed and clickable.



---

2. Backend (Node.js & Express.js)

Steps:

Set up an Express.js server.

Implement an API endpoint to accept the long URL, generate a short code (using nanoid), and store the mapping in MongoDB.

Create an endpoint to redirect the short URL to the original long URL.


#Code Example:

Install dependencies:

npm init -y
npm install express mongoose nanoid cors

server.js:

const express = require('express');
const mongoose = require('mongoose');
const { nanoid } = require('nanoid');
const cors = require('cors');

const app = express();
app.use(express.json());
app.use(cors());

// MongoDB connection
mongoose.connect('mongodb://localhost/urlShortener', { useNewUrlParser: true, useUnifiedTopology: true });

// Schema for URL
const urlSchema = new mongoose.Schema({
  longUrl: String,
  shortUrl: String,
  urlCode: String,
});

const Url = mongoose.model('Url', urlSchema);

// API endpoint to shorten URL
app.post('/api/shorten', async (req, res) => {
  const { longUrl } = req.body;
  const urlCode = nanoid(6);
  const shortUrl = http://localhost:5000/${urlCode};

  const url = new Url({ longUrl, shortUrl, urlCode });
  await url.save();

  res.json({ shortUrl });
});

// Redirect endpoint
app.get('/:code', async (req, res) => {
  const url = await Url.findOne({ urlCode: req.params.code });
  if (url) {
    return res.redirect(url.longUrl);
  } else {
    return res.status(404).json('URL not found');
  }
});

// Start server
app.listen(5000, () => {
  console.log('Server running on http://localhost:5000');
});

Explanation:

MongoDB connection: It connects to MongoDB using mongoose.

URL schema: Defines the structure for storing the long URL, short URL, and a generated code (nanoid).

Shorten URL API: Takes the long URL, generates a short code, saves it in MongoDB, and responds with the short URL.

Redirect endpoint: Searches for the short code and redirects users to the original long URL.



---

3. Database (MongoDB)

Steps:

Install MongoDB and ensure it's running locally or use a cloud-based MongoDB service (e.g., MongoDB Atlas).

Store long URLs, short URLs, and the associated unique codes.



---

How Everything Works Together:

1. Frontend: The user inputs a long URL and submits the form.


2. Backend: The server generates a unique short code, stores the URL mapping in MongoDB, and returns the shortened URL.


3. Database: MongoDB holds the mapping of the original URL to the short code.


4. Redirect: When the user visits the shortened URL, they are redirected to the original long URL.






Enhancements:

Expiration Dates: You can add an expiration field for each URL if you want to automatically delete old URLs.

Analytics: Track the number of times a short URL has been visited.

Custom Short URLs: Allow users to specify their own short codes.
