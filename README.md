const express = require('express');
const axios = require('axios');
const bodyParser = require('body-parser');

const app = express();
app.use(bodyParser.json());

const LINE_ACCESS_TOKEN = 'S66pIJkFeLLw6XbMxlvdSoJz7CkWMi8GJ9AmlXj+Vun0eluegJUtppybY2eUEAyTQh1DCw3LeKb7sxvM4A5eRmYKowE8Clhz++A/NOo3PTG83Z2UKA95yVeiT8L1XCNXCK0N7Hok4kllNklDE47v6AdB04t89/1O/w1cDnyilFU='; // replace with your actual token
const LINE_API_URL = 'https://api.line.me/v2/bot/message/reply';

// Helper function to send replies to LINE
const replyMessage = (replyToken, messages) => {
    return axios.post(LINE_API_URL, {
        replyToken: replyToken,
        messages: messages
    }, {
        headers: {
            'Content-Type': 'application/json',
            'Authorization': `Bearer ${LINE_ACCESS_TOKEN}`
        }
    });
};

app.post('/webhook', async (req, res) => {
    const event = req.body.events[0];

    // Check if the event is a message and of type 'text'
    if (event.type === 'message' && event.message.type === 'text') {
        const userMessage = event.message.text.toLowerCase();
        const replyToken = event.replyToken;

        // Respond with different content based on keywords
        if (userMessage.includes('menu')) {
            // Respond with a text message and quick reply options
            const messages = [
                {
                    "type": "text",
                    "text": "Choose an option from the menu:",
                    "quickReply": {
                        "items": [
                            {
                                "type": "action",
                                "action": {
                                    "type": "message",
                                    "label": "Option 1",
                                    "text": "You selected Option 1"
                                }
                            },
                            {
                                "type": "action",
                                "action": {
                                    "type": "message",
                                    "label": "Option 2",
                                    "text": "You selected Option 2"
                                }
                            }
                        ]
                    }
                }
            ];
            await replyMessage(replyToken, messages);

        } else if (userMessage.includes('image')) {
            // Respond with an image message
            const messages = [
                {
                    "type": "image",
                    "originalContentUrl": "https://example.com/path/to/your/image.jpg",
                    "previewImageUrl": "https://example.com/path/to/your/preview-image.jpg"
                }
            ];
            await replyMessage(replyToken, messages);

        } else if (userMessage.includes('carousel')) {
            // Respond with a carousel message
            const messages = [
                {
                    "type": "template",
                    "altText": "Carousel example",
                    "template": {
                        "type": "carousel",
                        "columns": [
                            {
                                "thumbnailImageUrl": "https://example.com/path/to/your/image1.jpg",
                                "title": "Option 1",
                                "text": "Description for option 1",
                                "actions": [
                                    {
                                        "type": "message",
                                        "label": "Select 1",
                                        "text": "Option 1 selected"
                                    }
                                ]
                            },
                            {
                                "thumbnailImageUrl": "https://example.com/path/to/your/image2.jpg",
                                "title": "Option 2",
                                "text": "Description for option 2",
                                "actions": [
                                    {
                                        "type": "message",
                                        "label": "Select 2",
                                        "text": "Option 2 selected"
                                    }
                                ]
                            }
                        ]
                    }
                }
            ];
            await replyMessage(replyToken, messages);

        } else {
            // Default response
            const messages = [
                {
                    "type": "text",
                    "text": "I'm sorry, I didn't understand that. Try 'menu', 'image', or 'carousel'."
                }
            ];
            await replyMessage(replyToken, messages);
        }
    }

    res.sendStatus(200);
});

// Start the server
app.listen(3000, () => {
    console.log('Server is running on port 3000');
});
