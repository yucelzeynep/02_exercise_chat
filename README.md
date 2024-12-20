# 03 Exercise Chat
### Programming Exercise: Build a Simple Chat Service Using Flask

In this exercise, you will create a simple chat service using Python with Flask as the backend and basic HTML/CSS/JavaScript for the frontend. The chat service will allow users to send messages, which will be stored in memory for the session.

### Objectives:
- Create a Flask application to handle chat messages.
- Use HTML/CSS for the frontend interface.
- Use JavaScript to send and retrieve messages via AJAX.
- Deploy the application on Render.

### Step-by-Step Instructions:

#### Step 1: Set up the Flask Environment

Create a new directory for your project. If necessary, set up a virtual environment and install Flask.

#### Step 2: Create the Flask Application

1. **Create a file named `app.py`:**
   ```python
   from flask import Flask, render_template, request, jsonify

   app = Flask(__name__)

   # This will hold our messages
   messages = []

   @app.route('/')
   def index():
       return render_template('index.html')

   @app.route('/send', methods=['POST'])
   def send_message():
       # Get the message from the request
       message = request.json['message']
       # Store the message in the list
       messages.append(message)
       return jsonify(success=True)

   @app.route('/messages', methods=['GET'])
   def get_messages():
       # Return the list of messages as JSON
       return jsonify(messages)

   if __name__ == '__main__':
       app.run(debug=True)
   ```

2. **Create a folder named `templates` and add a file named `index.html`:**
   ```html
   <!DOCTYPE html>
    <html lang="en">
    <head>
        <!-- Character encoding and viewport setting -->
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">

        <title>Simple Chat Service</title>
        <style>
            /* Basic styling for the body element, 
            chat display area and message inout field */
            body {
                font-family: Arial, sans-serif; 
            }
            #chat {
                border: 1px solid #ccc; 
                padding: 10px; 
                height: 300px; 
                overflow-y: scroll; /* Enable vertical scrolling for overflow text */
                margin-bottom: 10px; 
            }
            #message {
                width: 80%; /* Input field takes 80% of the width */
            }
        </style>
    </head>
    <body>
        <h1>Chat Service</h1>
        <!-- Div element to display chat messages -->
        <div id="chat"></div>
        <!-- Input field for users to type messages -->
        <input type="text" id="message" placeholder="Type a message..." />
        <button id="send">Send</button>

        <script>
            // Get references to the send button and chat display area
            const sendButton = document.getElementById('send');
            const chatDiv = document.getElementById('chat');

            // Function to fetch messages from the server
            function fetchMessages() {
                // Make a GET request to the '/messages' endpoint
                fetch('/messages')
                    .then(response => response.json()) // Parse the JSON response
                    .then(data => {
                        chatDiv.innerHTML = ''; // Clear the chat display area
                        // Loop through each message and display it in the chat area
                        data.forEach(msg => {
                            chatDiv.innerHTML += `<div>${msg}</div>`; // Create a new div for each message
                        });
                    });
            }

            // Set an interval to fetch messages every second (1000 milliseconds)
            setInterval(fetchMessages, 1000);

            // Event listener for the send button click event
            sendButton.addEventListener('click', () => {
                // Get the message input field
                const messageInput = document.getElementById('message');
                const message = messageInput.value; // Get the message text

                // Make a POST request to send the message
                fetch('/send', {
                    method: 'POST', 
                    headers: {
                        'Content-Type': 'application/json' 
                    },
                    // Send the message as a JSON object
                    body: JSON.stringify({ message })
                }).then(() => {
                    messageInput.value = ''; // Clear the input field after sending
                });
            });
        </script>
    </body>
    </html>
   ```

#### Step 3: Run the Flask Application Locally

1. **Run the application:**
   ```bash
   python app.py
   ```

2. **Open your browser and go to:**
   ```
   http://127.0.0.1:5000/
   ```

3. **You should see the chat interface. Try sending messages!**

#### Step 4: Deploy to Render

1. **Create a `requirements.txt` file for your dependencies:**
   ```bash
   pip freeze > requirements.txt
   ```
   If all these dependencies create problems while building, you can try to manually simplfy the file.

2. **Create a `render.yaml` file to configure your deployment:**
   ```yaml
   version: 1
   services:
     - type: web
       name: chat-service
       env: python
       plan: free
       buildCommand: pip install -r requirements.txt
       startCommand: python app.py
       envVars:
         - key: FLASK_ENV
           value: development
   ```

3. **Push your code to GitHub**

4. **Create a new web service on Render:**
   - Connect it to your repository (similar to the previous exercise.
   - Make sure it uses the `render.yaml` configuration file.

5. **After deployment, access your chat service via the URL provided by Render.**

Try sharing your link with others and see that you can send and receive.
