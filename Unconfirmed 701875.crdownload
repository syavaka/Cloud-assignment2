
from flask import Flask, render_template, request, redirect, url_for
import sqlite3
import os

app = Flask(__name__)

# SQLite Database Setup
DATABASE = '/home/ubuntu/flaskapp/users.db'

# Route to display the registration form
@app.route('/')
def index():
    return render_template('register.html')

# Route to handle registration form submission
@app.route('/register', methods=['POST'])
def register():
    try:
        # Get form data
        username = request.form['username']
        password = request.form['password']
        firstname = request.form['firstname']
        lastname = request.form['lastname']
        email = request.form['email']

        # Insert data into SQLite database
        conn = sqlite3.connect(DATABASE)
        c = conn.cursor()
        c.execute("INSERT INTO users (username, password, firstname, lastname, email) VALUES (?, ?, ?, ?, ?)",
                  (username, password, firstname, lastname, email))
        conn.commit()
        conn.close()

        return redirect(url_for('profile', username=username))

    except Exception as e:
        # Return error message in case of failure
        return str(e), 500

# Route to display user profile after registration
@app.route('/profile/<username>')
def profile(username):
    try:
        conn = sqlite3.connect(DATABASE)
        c = conn.cursor()
        c.execute("SELECT * FROM users WHERE username=?", (username,))
        user = c.fetchone()
        conn.close()

        return render_template('profile.html', user=user)

    except Exception as e:
        return str(e), 500

# Route to handle file upload
@app.route('/upload', methods=['POST'])
def upload():
    try:
        # Check if the file is present in the request
        if 'file' not in request.files:
            return "No file part in the request", 400

        file = request.files['file']

        # Check if the filename is empty
        if file.filename == '':
            return "No selected file", 400

        # Save the uploaded file to the new uploads directory
        upload_directory = "/home/ubuntu/flaskapp/uploads"
        file_path = os.path.join(upload_directory, "uploaded_file.txt")
        file.save(file_path)

        # Calculate the word count in the uploaded file
        with open(file_path, "r") as f:
            content = f.read()
            word_count = len(content.split())

        return f"Uploaded file has {word_count} words."

    except Exception as e:
        return str(e), 500

# Route to retrieve user information based on username and password
@app.route('/retrieve', methods=['POST'])
def retrieve():
    try:
        # Get username and password from the login form
        username = request.form['username']
        password = request.form['password']

        # Query the database for the given username and password
        conn = sqlite3.connect(DATABASE)
        c = conn.cursor()
        c.execute("SELECT * FROM users WHERE username=? AND password=?", (username, password))
        user = c.fetchone()
        conn.close()

        if user:
            # If the user exists, display their information
            return render_template('profile.html', user=user)
        else:
            # If no match is found, display a message
            return "Invalid username or password. Please try again."

    except Exception as e:
        return str(e), 500

# Main entry point
if __name__ == '__main__':
    app.run(host='0.0.0.0')
