## Ollama Structured Output
This project demonstrates how to use Ollama to convert unstructured data into structured outputs (like JSON) using Llama 3.2 models. The pipeline includes parsing text and images, creating APIs, and building a user interface for managing data seamlessly.

Table of Contents
Overview
Installation
Extract JSON from Unstructured Text
Extract JSON from Images
Create an API Endpoint
Build a User Interface
Running the Project
Resources
Overview
Ollama enables turning chaos into clean data by converting large language model (LLM) responses into structured JSON. This project covers:

Parsing unstructured text and images into structured JSON.
Building a Flask API backend.
Creating a simple HTML/CSS/JavaScript user interface.
Installation
Install the required packages:

bash
Copy code
pip install ol pantic flask flask-cors openai
Pull the latest Llama 3.2 models:

bash
Copy code
ollama pull llama3.2
ollama pull llama3.2-vision
Extract JSON from Unstructured Text
Create a file app.py with the following content:

python
Copy code
from olama import chat
from pantic import BaseModel

# Define the structure for the pet data
class Pet(BaseModel):
    type: str
    name: str
    age: int
    color: str
    favorite_toy: str

# Sample unstructured input
unstructured_data = """
I have two pets: a cat named Luna who is 5 years old and loves playing with yarn.
I also have a 2-year-old cat named Loki who loves tennis balls.
"""

# Parse the data
response = chat(unstructured_data, schema=Pet)
print(response)
Run the script:

bash
Copy code
python app.py
Output:

json
Copy code
[
  {"type": "cat", "name": "Luna", "age": 5, "color": "gray", "favorite_toy": "yarn"},
  {"type": "cat", "name": "Loki", "age": 2, "color": "black", "favorite_toy": "tennis balls"}
]
Extract JSON from Images
Update app.py to analyze images with Llama 3.2 Vision:

python
Copy code
from olama import chat

# Analyze an image and extract pet details
image_path = "pets.jpeg"
prompt = "Analyze this image and identify all pets present."

response = chat(prompt, image=image_path, model="llama3.2-vision")
print(response)
Run the script:

bash
Copy code
python app.py
Create an API Endpoint
Create api.py to set up a Flask API:

python
Copy code
from flask import Flask, request, jsonify
from flask_cors import CORS
from olama import chat

app = Flask(__name__)
CORS(app)

@app.route("/pets", methods=["POST"])
def extract_pet_data():
    unstructured_data = request.json.get("data")
    response = chat(unstructured_data)
    return jsonify(response)

if __name__ == "__main__":
    app.run(debug=True)
Run the API:

bash
Copy code
python api.py
Build a User Interface
Create an index.html file with the following code:

html
Copy code
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Pet Management Dashboard</title>
  <style>
    body { font-family: Arial, sans-serif; }
    #output { margin-top: 20px; }
  </style>
</head>
<body>
  <h1>Pet Management Dashboard</h1>
  <textarea id="input" rows="4" cols="50" placeholder="Enter unstructured pet data..."></textarea><br>
  <button onclick="addPet()">Add Pet</button>

  <div id="output"></div>

  <script>
    function addPet() {
      const input = document.getElementById('input').value;
      fetch('http://localhost:5000/pets', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ data: input })
      })
      .then(response => response.json())
      .then(data => {
        document.getElementById('output').innerText = JSON.stringify(data, null, 2);
      });
    }
  </script>
</body>
</html>
Running the Project
Run the Flask API:

bash
Copy code
python api.py
Open index.html in your browser and test the interface.

Enter unstructured data in the text area (e.g., A dog named Tiger who is 4 years old) and click "Add Pet".

Resources
Ollama Documentation
Flask Documentation
Llama 3.2 Model