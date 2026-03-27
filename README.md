### Programming Exercise: Build a simple chat service 

#### Exercise Definition
In this exercise, you will create a simple chat service using Python with Flask as the backend and basic HTML/CSS/JavaScript for the frontend. The chat service will allow users to send messages, which will be stored in memory for the session.

#### Step-by-Step Instructions

1. **Set Up Your Environment**:
   - You should have everything ready if you have successfully completed the previous exercise.

2. **Create a Virtual Environment**:
   - On the terminal, create a new directory (e.g. chat_app) for your project and navigate into it:
     ```bash
     mkdir chat_app
     cd chat_app
     ```
   - Set up a virtual environment to manage your project dependencies:
     ```bash
     python -m venv venv_chat
     source venv_chat/bin/activate  

3. **Install Flask**:
   - In the same directory, install Flask using pip:
     ```bash
     pip install Flask
     ```

4. **Create the HTML Template**:
   - Create a folder named `templates`.
     ```bash
     mkdir templates
     ```

   - In your development environment, navigate into the directory '/chat_app/templates' and open new file.
   - Add the following code and save it as `index.html`.

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
            // This allows the chat application to continuously check for new messages without requiring the user to refresh the page.
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

5. **Create the Flask Application**:
   - In your development environment, navigate into the directory '/chat_app' and open new file.
   - Add the following code and save it as `app.py`. 

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

6. **Run the Application Locally**:
   - In the terminal, navigate into the directory '/chat_app'.
   - Start the Flask application by running the following command:
     ```bash
     python app.py
     ```
   - You will see some debug messages on the terminal (e.g. Running on http://127.0.0.1:5000)
   - Open your browser and go to `http://127.0.0.1:5000/`. You should see the chat app. Try it.


7. **Prepare for deployment on Render**:
   - In the terminal, make sure you are under the directory '/greeting_app'.
   - Create a `requirements.txt` file to specify your project dependencies:
     ```bash
     pip freeze > requirements.txt
     ```
   If all these dependencies create problems while building, you can try to manually simplfy the file.

   - In your IDE, create a `render.yaml` file and add the following content (Render configurations):
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

8. **Create a Repository on GitHub and Commit**:
   - Go to your project folder, initialize a repository and commit
     ```bash
     git init
     git add .
     git commit -m "Initial commit"
     ```
   - Push your files
     ```bash
     git remote add origin https://github.com/yourusername/your-repo.git
     git push -u origin master
     ```
You can also get the URL to your repository by clicking on "<> Code" at the top of your repository page.

9. **Deploy on Render**:
   - Go to [Render.com](https://render.com/) and create an account, if you do not have one.
   - Once logged in, click on "Projects" on the left pane and then "Deploy a Web Service"  under "Overview". 
   - Connect it to your GitHub account.
   - Click on "Public Git Repository" tab and enter the URL to your repository. 
   - On the next page, enter "python app.py" as start command and choose free plan.
   - Click on "Deploy Web Application" at the bottom (it will take some time...) 
   - Render will automatically detect the `render.yaml` file and set up your application.
   - After deployment, you will be provided with a live URL for your application around the upper part of the browser window. Try sharing your link with others and see that you can send and receive.

10. **Delete your project**:
   - Click on render icon on the upper left, find your deployment under the list, click on "..." on the right, choose "Settings" and click on "Delete Web Service" at the bottom. 

