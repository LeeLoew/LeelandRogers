---
layout: page
title: ChatGPT Interface
subtitle: Chat with AI
---

<div id="chat-container"></div>
<div id="input-container">
    <input type="text" id="user-input" placeholder="Type your message here...">
    <button id="send-button">Send</button>
</div>
<div id="status"></div>

<style>
    #chat-container {
        border: 1px solid #ccc;
        height: 400px;
        overflow-y: scroll;
        padding: 10px;
        margin-bottom: 10px;
        background-color: #f9f9f9;
    }
    #input-container {
        display: flex;
        margin-bottom: 10px;
    }
    #user-input {
        flex-grow: 1;
        padding: 10px;
        font-size: 16px;
        border: 1px solid #ccc;
        border-radius: 4px 0 0 4px;
    }
    #send-button {
        padding: 10px 20px;
        font-size: 16px;
        background-color: #4CAF50;
        color: white;
        border: none;
        border-radius: 0 4px 4px 0;
        cursor: pointer;
    }
    #send-button:hover {
        background-color: #45a049;
    }
    .message {
        margin-bottom: 10px;
        padding: 10px;
        border-radius: 4px;
    }
    .user-message {
        background-color: #e1f5fe;
        text-align: right;
    }
    .ai-message {
        background-color: #f0f4c3;
    }
    #status {
        color: #888;
        font-style: italic;
    }
</style>

<script src="https://cdnjs.cloudflare.com/ajax/libs/axios/0.21.1/axios.min.js"></script>
<script>
document.addEventListener('DOMContentLoaded', (event) => {
    const chatContainer = document.getElementById('chat-container');
    const userInput = document.getElementById('user-input');
    const sendButton = document.getElementById('send-button');
    const statusDiv = document.getElementById('status');

    const API_KEY = 'your-api-key-here'; // Replace with your actual API key
    const API_URL = 'https://api.openai.com/v1/chat/completions';

    function addMessage(sender, message) {
        const messageElement = document.createElement('div');
        messageElement.className = `message ${sender.toLowerCase()}-message`;
        messageElement.textContent = message;
        chatContainer.appendChild(messageElement);
        chatContainer.scrollTop = chatContainer.scrollHeight;
    }

    async function sendMessage() {
        const message = userInput.value.trim();
        if (message) {
            addMessage('user', message);
            userInput.value = '';
            statusDiv.textContent = 'ChatGPT is thinking...';

            try {
                const response = await axios.post(API_URL, {
                    model: 'gpt-3.5-turbo',
                    messages: [{ role: 'user', content: message }]
                }, {
                    headers: {
                        'Authorization': `Bearer ${API_KEY}`,
                        'Content-Type': 'application/json'
                    }
                });

                const reply = response.data.choices[0].message.content;
                addMessage('ai', reply);
                statusDiv.textContent = '';
            } catch (error) {
                console.error('Error:', error);
                statusDiv.textContent = 'Failed to get a response from ChatGPT.';
            }
        }
    }

    sendButton.addEventListener('click', sendMessage);
    userInput.addEventListener('keypress', (e) => {
        if (e.key === 'Enter') {
            sendMessage();
        }
    });

    console.log('ChatGPT interface script loaded');
});
</script>
