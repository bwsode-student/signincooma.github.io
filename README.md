mkdir email-button
cd email-button

npm init -y

npm install express nodemailer body-parser

// server.js

const express = require('express');
const nodemailer = require('nodemailer');
const bodyParser = require('body-parser');

const app = express();
const port = 3000;

// Set up middleware
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));
app.use(express.static('public'));

// Create a Nodemailer transporter
const transporter = nodemailer.createTransport({
  service: 'gmail', // You can use other services like 'hotmail', 'yahoo', etc.
  auth: {
    user: 'your-email@gmail.com', // Your email address
    pass: 'your-email-password'    // Your email password or app password
  }
});

// Define a route to handle the button click
app.post('/send-email', (req, res) => {
  const mailOptions = {
    from: 'lachlan.francisco@education.nsw.gov.au',
    to: 'lachlan.francisco@education.nsw.gov.au',
    subject: 'Button Clicked',
    text: 'The "Sign In to Cooma Downs" button was clicked.'
  };

  transporter.sendMail(mailOptions, (error, info) => {
    if (error) {
      console.log('Error sending email:', error);
      res.status(500).send('Error sending email');
    } else {
      console.log('Email sent:', info.response);
      res.send('Email sent');
    }
  });
});

// Start the server
app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}/`);
});

<!-- public/index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Sign In</title>
  <style>
    body {
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background-color: #f0f0f0;
    }
    #signInButton {
      font-size: 24px;
      padding: 20px 40px;
      background-color: red;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <button id="signInButton">Sign In to Cooma Downs</button>

  <script>
    document.getElementById('signInButton').addEventListener('click', () => {
      fetch('/send-email', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({})
      })
      .then(response => response.text())
      .then(data => {
        alert('Button clicked! Email sent.');
      })
      .catch(error => {
        alert('Error sending email.');
        console.error('Error:', error);
      });
    });
  </script>
</body>
</html>

node server.js
