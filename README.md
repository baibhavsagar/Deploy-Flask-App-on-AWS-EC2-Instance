# Deploy Flask App on AWS EC2 Instance

## Table of Contents

1. 🤖 [Introduction](#introduction)
2. 🔋 [Features](#features)
3. ⚙️ [Tech Stack](#tech-stack)
4. 📂 [Folder Structure](#folder-structure)
5. 🧰 [Getting Started](#getting-started)
7. ⭐ [Give A Star](#give-a-star)
8. 🚀 [Follow Me](#follow-me)
9. 📚 [Learn More](#learn-more)

## 🤖 Introduction

In this tutorial, we'll guide you through the process of deploying a Flask web application on an AWS EC2 instance. Flask is a lightweight web application framework written in Python, and AWS EC2 (Elastic Compute Cloud) provides scalable computing capacity in the cloud.

## 🔋 Features

- Deploy a Flask web application on AWS EC2.
- Use Gunicorn as the WSGI server.
- Configure Nginx as a reverse proxy.

## ⚙️ Tech Stack

- Python
- Flask
- AWS EC2
- Gunicorn
- Nginx

## 📂 Folder Structure

- **helloworld/**
  - **venv/**
  - **app.py**

## 🧰 Getting Started

### Prerequisites

- An AWS account
- Basic knowledge of Python and Flask
- Familiarity with the Linux command line

## **Steps**

### **1. Install Python Virtual Environment**

First, SSH into your EC2 instance and update the package list:

```bash
sudo apt-get update && sudo apt-get upgrade
```

Then, install the Python virtual environment package:

```bash
sudo apt-get install python3-venv
```

### **2. Set up the Virtual Environment**

Create a new directory for your Flask app and navigate into it:

```bash
mkdir helloworld
cd helloworld
```

Create a virtual environment named 'venv':

```bash
python3 -m venv venv
```

Activate the virtual environment:

```bash
source venv/bin/activate
```

### **3. Install Flask**

Within the virtual environment, install Flask using pip:

```bash
pip install Flask
```

### **4. Create a Simple Flask API**

Create a file named **`app.py`** 

```jsx
sudo vi app.py
```

And add the following code:

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == "__main__":
    app.run()

```

### **5. Verify Flask App**

Run the Flask app to verify it works:

```bash
python app.py
```

### **6. Run Gunicorn WSGI Server**

Run Gunicorn WSGI server to serve the Flask Application When you “run” flask, you are actually running Werkzeug’s development WSGI server, which forward requests from a web server. Since Werkzeug is only for development, we have to use Gunicorn, which is a production-ready WSGI server, to serve our application.

Install Gunicorn using pip:

```bash
pip install gunicorn
```

Run Gunicorn to serve the Flask application:

```bash
gunicorn -b 0.0.0.0:8000 app:app
```

**Gunicorn is running (Ctrl + C to exit gunicorn)!**

Use systemd to manage Gunicorn Systemd is a boot manager for Linux. We are using it to restart gunicorn if the EC2 restarts or reboots for some reason. We create a .service file in the /etc/systemd/system folder, and specify what would happen to gunicorn when the system reboots. We will be adding 3 parts to systemd Unit file — Unit, Service, Install

Unit — This section is for description about the project and some dependencies Service — To specify user/group we want to run this service after. Also some information about the executables and the commands. Install — tells systemd at which moment during boot process this service should start. With that said, create an unit file in the /etc/systemd/system directory

### **7. Manage Gunicorn with systemd**

Create a systemd unit file:

```bash
sudo nano /etc/systemd/system/helloworld.service
```

Add the following configuration:

```
[Unit]
Description=Gunicorn instance for a simple hello world app
After=network.target

[Service]
User=ubuntu
Group=www-data
WorkingDirectory=/home/ubuntu/helloworld
ExecStart=/home/ubuntu/helloworld/venv/bin/gunicorn -b localhost:8000 app:app
Restart=always

[Install]
WantedBy=multi-user.target
```

Enable and start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl start helloworld
sudo systemctl enable helloworld
```

Check if the app is running with

```bash
curl localhost:8000
```

Run Nginx Webserver to accept and route request to Gunicorn Finally, we set up Nginx as a reverse-proxy to accept the requests from the user and route it to gunicorn.

### **8. Set up Nginx as a Reverse Proxy**

Install Nginx:

```bash
sudo apt-get install nginx
```

Start and enable the Nginx service:

```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```

Edit the Nginx default configuration file:

```bash
sudo nano /etc/nginx/sites-available/default
```

Add the following code at the top of the file (below the default comments)

```
upstream flaskhelloworld {
    server 127.0.0.1:8000;
}
```

Add a proxy_pass to flaskhelloworld atlocation /

```
location / {
    proxy_pass http://flaskhelloworld;
}
```

Restart Nginx for changes to take effect:

```bash
sudo systemctl restart nginx
```

Now, your Flask application should be accessible via your EC2 instance's public IP address.

Congratulations! You have successfully deployed a Flask web application on an AWS EC2 instance using Gunicorn and Nginx as a reverse proxy.



## ⭐ Give A Star

If you find this project helpful, please consider giving it a star.

## 🚀 Follow Me

Follow me on [GitHub](https://github.com/yourusername) for more projects like this.

## 📚 Learn More

For more tutorials and guides, visit [my blog](https://yourblog.com).
```

