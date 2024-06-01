## Setting up the Twoge Application

We are now at the point where we can install and configure the Twoge application on the EC2 instance. This will be the first "proof of life" to ensure that all of our components are connecting correctly and the Twoge application can successfully launch.

1. Navigate to the EC2 service again in the AWS Management Console and select your Twoge application EC2.
2. Click the "Connect" box among the options on the top of the page.
3. Click on the "SSH client" tab and follow the steps to connect to the instance. An example is:

```shell
cd <LOCATION-OF-MY-KEY>
chmod 400 <MY-KEY.pem>
ssh -i <MY-KEY.pem> ec2-user@<INSTANCE-PUBLIC-IP>
```

> [!TIP]
> The console may display a warning regarding the fact that the IP address is unknown and that it does not know the authenticity of it. This is due to the fact that the IP address is new to your machine's known hosts. If the IP address is the correct one from your EC2's public IP address, type "yes" and the IP address will be added to your machine's known hosts. It will then connect to the instance.

4. Once a connection is established to the instance, run the following commands to configure the Twoge application environment.

```shell
sudo yum update
sudo yum install git
git clone https://github.com/chandradeoarya/twoge
cd twoge
vim .env # Add SQLALCHEMY_DATABASE_URI=postgresql://postgres:password@rds-endpoint
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

5. Install Nginx on the EC2 instance with the following commands:

```shell
sudo amazon-linux-extras install nginx1 -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

6. Create a wsgi.py file in the twoge directory and add the following lines to it:

```shell
sudo vim wsgi.py
```

```python
from app import app

if __name__ == '__main__':
    app.run()
```

6. Create a custom **Twoge daemon** using the following command:

```shell
sudo vim /etc/systemd/system/twoge.service
# Add the following lines within
[Unit]
Description=Gunicorn instance to serve the Twoge application
After=network.target

[Service]
User=ec2-user
Group=nginx
WorkingDirectory=/home/ec2-user/twoge
Environment="PATH=/home/ec2-user/twoge/.venv/bin"
ExecStart=/home/ec2-user/twoge/.venv/bin/gunicorn -w 3 -b 0.0.0.0:8000 wsgi:app

[Install]
WantedBy=multi-user.target
```

7. Start and enable the twoge daemon:

```shell
sudo systemctl start twoge
sudo systemctl enable twoge
```

8. Create an Nginx configuration for the Flask application:

```shell
sudo vim /etc/nginx/conf.d/twoge.conf
```

9. Add the following content to the **Nginx configuration**:

```nginx
server {
    listen 80;
    server_name <your_domain_or_IP>;

    location / {
        proxy_pass http://0.0.0.0:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

10. Test the Nginx configuration and, if successful, restart Nginx:

```shell
sudo nginx -t
sudo systemctl restart nginx
```

Now, your Twoge application is successfully hosted at your EC2 instance's public IP address. Visit this address and ensure that the application loads successfully.
