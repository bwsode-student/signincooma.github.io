const express = require('express');
const nodemailer = require('nodemailer');
const bodyParser = require('body-parser');

const app = express();
const port = 3000;

// Middleware
app.use(bodyParser.json());
app.use(express.static('public')); // Serve static files from the 'public' directory

// Configure Nodemailer
const transporter = nodemailer.createTransport({
    service: 'gmail', // or any other email service provider
    auth: {
        user: 'lachlan.francisco@education.nsw.gov.au', // Your email
        pass: 'bwsode'    // Your email password
    }
});

// Route to handle email sending
app.post('/send-email', (req, res) => {
    const mailOptions = {
        from: 'lachlan.francisco@education.nsw.gov.au',
        to: 'lachlan.francisco@education.nsw.gov.au',
        subject: 'Bozo at the gate',
        text: 'some goat poacher is at the gate'
    };

    transporter.sendMail(mailOptions, (error, info) => {
        if (error) {
            return res.status(500).json({ message: 'Failed to send email', error: error.toString() });
        }
        res.json({ message: 'Email sent successfully', info });
    });
});

app.listen(port, () => {
    console.log(`Server running at http://localhost:${port}/`);
});
