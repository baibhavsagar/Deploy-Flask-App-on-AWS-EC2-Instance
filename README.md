# Deploy Flask App on AWS EC2 Instance

## Table of Contents

1. 🤖 [Introduction](#introduction)
2. 🔋 [Features](#features)
3. ⚙️ [Tech Stack](#tech-stack)
4. 📂 [Folder Structure](#folder-structure)
5. 🧰 [Prerequisites](#prerequisites)
6. ⭐ [Give A Star](#give-a-star)
7. 🚀 [Follow Me](#follow-me)
8. 📚 [Learn More](#learn-more)


## 🤖 <a name="introduction">Introduction</a>


In this tutorial, we'll guide you through the process of deploying a Flask web application on an AWS EC2 instance. Flask is a lightweight web application framework written in Python, and AWS EC2 (Elastic Compute Cloud) provides scalable computing capacity in the cloud.

## 🔋  <a name="features">Features</a>

- Deploy a Flask web application on AWS EC2.
- Use Gunicorn as the WSGI server.
- Configure Nginx as a reverse proxy.


## ⚙️ <a name="tech-stack">Tech Stacks</a>

- Python
- Flask
- AWS EC2
- Gunicorn
- Nginx


## 📂 <a name="table">Folder Structure</a>

- **helloworld/**
  - **venv/**
  - **app.py**
 

## 🧰 <a name="prerequisites">Prerequisites</a>

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


## ⭐ <a name="give-a-star">Give A Star</a>

You can also give this repository a star to show more people and they can use this repository.

## 🚀 <a name="follow-me">Follow Me</a>

[![Instagram](https://img.shields.io/badge/Instagram-%23E4405F.svg?style=for-the-badge&logo=Instagram&logoColor=white)](https://www.instagram.com/baibhav_sagar/ "Instagram")

[![GitHub](https://img.shields.io/badge/github-%23121011.svg?style=for-the-badge&logo=github&logoColor=white)](https://github.com/baibhavsagar "Instagram")

[![Follow Me](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/baibhavsagar "Linked In")

## 📚 <a name="learn-more">Learn More</a>
Of course! Here are the resources with descriptions and direct links:

**Python:**
1. **Official Python Documentation**: The Python Software Foundation maintains comprehensive documentation covering all aspects of Python, from beginner to advanced topics.
   - [Documentation Link](https://www.python.org/doc/)

2. **Python Crash Course by Eric Matthes**: This book is great for beginners who want to get started with Python programming. It covers the basics of Python syntax and also delves into more advanced topics.
   - [Book Link](https://nostarch.com/pythoncrashcourse2e)

3. **Automate the Boring Stuff with Python by Al Sweigart**: Perfect for those interested in automating tasks using Python, this book covers practical Python programming for total beginners.
   - [Book Link](https://automatetheboringstuff.com/)

**Flask:**
1. **Flask Documentation**: The official Flask documentation is an excellent resource for learning Flask. It covers everything from installation to advanced topics like Blueprints and Extensions.
   - [Documentation Link](https://flask.palletsprojects.com/en/2.1.x/)

2. **Flask Web Development by Miguel Grinberg**: This book provides a comprehensive guide to building web applications with Flask. It covers everything from basic concepts to more advanced topics like deployment and testing.
   - [Book Link](https://www.amazon.com/Flask-Web-Development-Developing-Applications/dp/1491991739)

3. **Flask Mega-Tutorial by Miguel Grinberg**: This online tutorial series covers building a full-featured web application with Flask, step by step. It's a great resource for hands-on learning.
   - [Tutorial Link](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world)

**AWS EC2:**
1. **AWS Documentation - EC2**: The official AWS documentation provides comprehensive guides and tutorials for using EC2 instances, covering everything from launching instances to managing security and scalability.
   - [Documentation Link](https://docs.aws.amazon.com/ec2/index.html)

2. **AWS Certified Solutions Architect - Associate Certification Guide**: This guide covers all the topics you need to know to become certified in AWS, including EC2 instances.
   - [Book Link](https://www.amazon.com/Certified-Solutions-Architect-Associate-All/dp/1119138558)

3. **AWS in Action by Andreas Wittig and Michael Wittig**: This book provides practical examples and insights into using various AWS services, including EC2.
   - [Book Link](https://www.amazon.com/AWS-Action-Andreas-Wittig/dp/1617295116)

**Gunicorn:**
1. **Gunicorn Documentation**: The official Gunicorn documentation provides information on installing, configuring, and using Gunicorn as a WSGI server for Python web applications.
   - [Documentation Link](https://docs.gunicorn.org/en/stable/)

2. **Deploying Django by Kenneth Love**: While focusing on Django, this book includes a chapter on deploying Django applications with Gunicorn, useful for understanding its usage in real-world scenarios.
   - [Book Link](https://www.amazon.com/Deploying-Django-Kenneth-Love/dp/1680505316)

3. **Real Python Tutorials**: Real Python offers tutorials on various Python-related topics, including deploying web applications with Gunicorn.
   - [Tutorial Link](https://realpython.com/)

**Nginx:**
1. **Nginx Documentation**: The official Nginx documentation provides detailed information on installing, configuring, and using Nginx as a web server and reverse proxy.
   - [Documentation Link](https://nginx.org/en/docs/)

2. **Nginx Essentials by Valery Kholodkov**: This book covers the essentials of Nginx configuration and usage, making it a good resource for beginners.
   - [Book Link](https://www.amazon.com/Nginx-Essentials-Valery-Kholodkov/dp/0985193208)

3. **DigitalOcean Tutorials**: DigitalOcean offers numerous tutorials on web development and server administration topics, including configuring Nginx.
   - [Tutorial Link](https://www.digitalocean.com/community/tutorials)

