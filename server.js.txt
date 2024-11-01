// server.js

const express = require('express');
const fetch = require('node-fetch');
const dotenv = require('dotenv');

dotenv.config(); // Load environment variables from .env file

const app = express();
app.use(express.json()); // Parse JSON request bodies

// Endpoint to solve math questions
app.post('/solve-math', async (req, res) => {
    const question = req.body.question; // Get the question from the request body

    try {
        // Make a request to the OpenAI API
        const response = await fetch("https://api.openai.com/v1/completions", {
            method: "POST",
            headers: {
                "Content-Type": "application/json",
                "Authorization": `Bearer ${process.env.OPENAI_API_KEY}` // Use the API key from environment variables
            },
            body: JSON.stringify({
                model: "gpt-4-turbo", // Specify the model
                prompt: `Solve this math question: ${question}`, // Create the prompt
                max_tokens: 100 // Limit the response length
            })
        });

        // Check if the API request was successful
        if (!response.ok) {
            throw new Error(`Error ${response.status}: ${response.statusText}`);
        }

        const data = await response.json(); // Parse the JSON response
        res.json({ solution: data.choices[0].text.trim() }); // Send the solution back
    } catch (error) {
        console.error("Error:", error);
        res.status(500).json({ error: "An error occurred while solving the math question." }); // Handle errors
    }
});

// Start the server
const PORT = process.env.PORT || 3000; // Use PORT from environment or default to 3000
app.listen(PORT, () => {
    console.log(`Server is running on http://localhost:${PORT}`);
});
