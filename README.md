# Deploy-Flask-App-on-AWS-EC2-Instance
In this tutorial, we'll guide you through the process of deploying a Flask web application on an AWS EC2 instance. Flask is a lightweight web application framework written in Python, and AWS EC2 (Elastic Compute Cloud) provides scalable computing capacity in the cloud.

## **Prerequisites**

- An AWS account
- Basic knowledge of Python and Flask
- Familiarity with the Linux command line

## **Steps**

### **1. Install Python Virtual Environment**

First, SSH into your EC2 instance and update the package list:

```bash
sudo apt-get update
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
bashCopy code
source venv/bin/activate

```

### **3. Install Flask**

Within the virtual environment, install Flask using pip:

```bash
bashCopy code
pip install Flask

```

### **4. Create a Simple Flask API**

Create a file named **`app.py`** and add the following code:

```python
pythonCopy code
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
bashCopy code
python app.py

```

### **6. Run Gunicorn WSGI Server**

Install Gunicorn using pip:

```bash
bashCopy code
pip install gunicorn

```

Run Gunicorn to serve the Flask application:

```bash
bashCopy code
gunicorn -b 0.0.0.0:8000 app:app

```

### **7. Manage Gunicorn with systemd**

Create a systemd unit file:

```bash
bashCopy code
sudo nano /etc/systemd/system/helloworld.service

```

Add the following configuration:

```
iniCopy code
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
bashCopy code
sudo systemctl daemon-reload
sudo systemctl start helloworld
sudo systemctl enable helloworld

```

### **8. Set up Nginx as a Reverse Proxy**

Install Nginx:

```bash
bashCopy code
sudo apt-get install nginx

```

Start and enable the Nginx service:

```bash
bashCopy code
sudo systemctl start nginx
sudo systemctl enable nginx

```

Edit the Nginx default configuration file:

```bash
bashCopy code
sudo nano /etc/nginx/sites-available/default

```

Update the file with the following configuration:

```
nginxCopy code
upstream flaskhelloworld {
    server 127.0.0.1:8000;
}

server {
    listen 80;

    location / {
        proxy_pass http://flaskhelloworld;
    }
}

```

Restart Nginx for changes to take effect:

```bash
bashCopy code
sudo systemctl restart nginx

```

Now, your Flask application should be accessible via your EC2 instance's public IP address.

Congratulations! You have successfully deployed a Flask web application on an AWS EC2 instance using Gunicorn and Nginx as a reverse proxy.
