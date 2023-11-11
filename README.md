
# OpenAI GPT-3.5 Flask Chat Application

This document provides a detailed overview of a Flask application script designed to integrate with OpenAI's GPT-3.5 API for conversational purposes.

## Introduction

The script creates a simple web server using Flask, which serves as an interface between the user and the OpenAI GPT-3.5 API. The server accepts user input, sends it to the GPT-3.5 model, and returns the model's response.

## Requirements

- Python 3
- Flask
- Flask-CORS
- OpenAI Python library
- An OpenAI API key

## Script Breakdown

```python
import openai
from flask import Flask, request, jsonify
from flask_cors import CORS

app = Flask(__name__)
CORS(app)

# Environment variables for API key
import os
from dotenv import load_dotenv
load_dotenv()

openai.api_key = os.getenv('OPEN_AI_KEY')

@app.route('/chat', methods=['POST'])
def chat():
    try:
        input_text = request.json.get('input')
        # API call to OpenAI
        response = openai.ChatCompletion.create(
            model="gpt-3.5-turbo",
            messages=[{"role": "system", "content": "You are a helpful assistant."},
                      {"role": "user", "content": input_text}]
        )
        # Sending response back to user
        return jsonify({'response': response['choices'][0]['message']['content']})
    except Exception as e:
        print(f"Error: {e}")
        # Error handling
        return jsonify({'response': 'Sorry, I am unable to respond right now.'})

if __name__ == '__main__':
    app.run(debug=True)
```

### Script Explanation

- **Flask Setup**: The script sets up a Flask web server and configures CORS for cross-origin requests.
- **Environment Variables**: It securely loads the OpenAI API key from environment variables using `dotenv`.
- **API Integration**: The `/chat` route handles POST requests, sending the user input to OpenAI's GPT-3.5 model and returning the model's response.
- **Error Handling**: The script includes basic error handling for the API call.

## Usage Instructions

1. Install the required Python packages.
2. Set up an `.env` file with your OpenAI API key.
3. Run the script to start the Flask server.
4. Send POST requests with user input to `http://localhost:5000/chat`.

## Error Handling

The script catches exceptions during the API call and returns a default error message. This ensures the server remains operational even if an API call fails.
