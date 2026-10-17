# Index

- [Intro](#intro)


# Intro

HTML (HyperText Markup Language)- markup language that tells the web browser how to display text, images, videos, and other content on a webpage.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>HTML Revision Page</title>

  <!-- Link to external CSS -->
  <link rel="stylesheet" href="styles.css" />

  <!-- Internal CSS for quick styling -->
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 20px;
      background-color: #f9f9f9;
    }
    header, footer {
      background-color: #eee;
      padding: 10px;
      text-align: center;
    }
    section {
      margin-top: 20px;
    }
  </style>
</head>
<body>
  <!-- Header Section -->
  <header>
    <h1>Welcome to HTML Revision</h1>
    <nav>
      <!-- Navigation Links -->
      <a href="#form">Form</a> |
      <a href="#table">Table</a> |
      <a href="#media">Media</a>
    </nav>
  </header>

  <!-- Section with basic text formatting -->
  <section>
    <h2>Text Formatting</h2>
    <p>This is a <strong>bold</strong> and <em>italic</em> text.</p>
    <p>Use <code>&lt;code&gt;</code> tag to show inline code.</p>
    <p>Here is a <a href="https://example.com" target="_blank">link</a> to example.com</p>
    <ul>
      <li>Unordered List Item</li>
      <li>Another Item</li>
    </ul>
    <ol>
      <li>Ordered List Item</li>
      <li>Another Item</li>
    </ol>
  </section>

  <!-- Image and Video -->
  <section id="media">
    <h2>Media</h2>
    <img src="https://via.placeholder.com/150" alt="Placeholder Image" />
    <br /><br />
    <video controls width="320">
      <source src="sample.mp4" type="video/mp4" />
      Your browser does not support the video tag.
    </video>
  </section>

  <!-- Form -->
  <section id="form">
    <h2>Form Elements</h2>
    <form>
      <label for="name">Name:</label>
      <input type="text" id="name" name="name" /><br /><br />

      <label for="email">Email:</label>
      <input type="email" id="email" name="email" /><br /><br />

      <label for="gender">Gender:</label>
      <input type="radio" name="gender" value="male" /> Male
      <input type="radio" name="gender" value="female" /> Female<br /><br />

      <label for="hobby">Hobbies:</label>
      <input type="checkbox" name="hobby" value="coding" /> Coding
      <input type="checkbox" name="hobby" value="music" /> Music<br /><br />

      <label for="country">Country:</label>
      <select id="country" name="country">
        <option value="in">India</option>
        <option value="us">USA</option>
      </select><br /><br />

      <textarea placeholder="Write something..."></textarea><br /><br />

      <input type="submit" value="Submit" />
    </form>
  </section>

  <!-- Table -->
  <section id="table">
    <h2>Table Example</h2>
    <table border="1" cellpadding="5">
      <thead>
        <tr>
          <th>Name</th>
          <th>Age</th>
          <th>Profession</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>Alice</td>
          <td>24</td>
          <td>Engineer</td>
        </tr>
        <tr>
          <td>Bob</td>
          <td>30</td>
          <td>Designer</td>
        </tr>
      </tbody>
    </table>
  </section>

  <!-- Footer -->
  <footer>
    <p>&copy; 2025 HTML Revision Page</p>
  </footer>

  <!-- Script section -->
  <script>
    // Simple JavaScript alert
    console.log("Page loaded!");
  </script>
</body>
</html>
```

XHTML (eXtensible HyperText Markup Language)- stricter and XML-based version of HTML

IndexedDB- low-level API for client-side storage of significant amounts of structured data, including files/blobs

CSS- Cascading Style Sheets
