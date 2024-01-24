const express = require('express');
const bodyParser = require('body-parser');

const app = express();
const port = process.env.PORT || 3000;

app.use(bodyParser.urlencoded({ extended: false }));
app.use(bodyParser.json());

app.listen(port, () => {
  console.log(`Server is running on port ${port}`);
});

const twilio = require('twilio');
const MessagingResponse = twilio.twiml.MessagingResponse;

app.post('/webhook', (req, res) => {
  const twiml = new MessagingResponse();

  // Handle WhatsApp messages here

  res.writeHead(200, { 'Content-Type': 'text/xml' });
  res.end(twiml.toString());
});

const { OpenAIAPI } = require('openai');

const openai = new OpenAIAPI('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');

app.post('/webhook', async (req, res) => {
  const twiml = new MessagingResponse();
  const userMessage = req.body.Body;

  // Use OpenAI to generate a response based on user's message
  const aiResponse = await openai.complete({
    engine: 'text-davinci-002',
    prompt: userMessage,
    max_tokens: 150,
  });

  // Send AI-generated response back to the user
  twiml.message(aiResponse.choices[0].text);

  res.writeHead(200, { 'Content-Type': 'text/xml' });
  res.end(twiml.toString());
});
