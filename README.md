# VPS DEPLOYMENT WORKSHOP: REVERSE PROXY

## Description
This project is a dual-instance web application using PM2 and Nginx.

## Setup

Create a server on Digital Ocean:
Create an Ubuntu $4 server (check Amsterdam & Regular SSD disk type) at a location of your choosing
Cloud servers are called droplets
Use password authentication
Login to the server using ssh
Login to the server using root : ssh root@your.ip.address
Run this command on your server: apt update to ensure you got all the latest packages.
Clone the repository: git clone https://github.com/codex-academy/vps-deployment-workshop-typescript

-  **install NodeJS on the server**
   - Use this command curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash (log in and out of the server to make sure the node command work from the terminal)
   - then install NodeJS version 18 or higher - nvm install 18
   - install git on the server using: apt install git
   - note git was installed on my server by default.
   - Install dependencies: npm install
- **Install nginx and pm2**
   - sudo apt install nginx
   - sudo systemctl enable nginx
   -  npm install -g pm2
### Create a PM2 ecosystem file: ecosystem.config.js and reverse prox cofing: reverse-proxy.conf**
- **reverse proxy configuration file**
-   sudo nano /etc/nginx/sites-enabled/reverse-proxy.conf
-   Create a symbolic link to /etc/nginx/sites-enabled/:
 `sudo ln -s /etc/nginx/sites-available/reverse-proxy.conf /etc/nginx/sites-enabled/`
- **PM2 ecosystem**
- nano ecosystem.config.js
- Start PM2: pm2 start ecosystem.config.js
- Restart Nginx: sudo systemctl restart nginx

### Configuration
PM2 Ecosystem File
Create a file named ecosystem.config.js with the following content:
JavaScript
module.exports = {
  apps: [
    {
      name: 'app1',
      script: 'dist/index.js',
      env: {
        PORT: 8081,
      },
    },
    {
      name: 'app2',
      script: 'dist/index.js',
      env: {
        PORT: 8082,
        BACKGROUND_COLOR: '#ccc',
      },
    },
  ],
};
Nginx Configuration
Create a file named nginx.conf with the following content:
Nginx
server {
    listen 80;
    server_name site1.londeka.projectcodex.net;
    location / {
        proxy_pass http://127.0.0.1:8081;
        include proxy_params;
    }
}

server {
    listen 443 ssl;
    server_name site1.londeka.projectcodex.net;
    ssl_certificate /path/to/ssl/cert.crt;
    ssl_certificate_key /path/to/ssl/cert.key;
    location / {
        proxy_pass http://127.0.0.1:8081;
        include proxy_params;
    }
}

server {
    listen 80;
    server_name site2.londeka.projectcodex.net;
    location / {
        proxy_pass http://127.0.0.1:8082;
        include proxy_params;
    }
}

server {
    listen 443 ssl;
    server_name site2.londeka.projectcodex.net;
    ssl_certificate /path/to/ssl/cert.crt;
    ssl_certificate_key /path/to/ssl/cert.key;
    location / {
        proxy_pass http://127.0.0.1:8082;
        include proxy_params;
    }
}
Troubleshooting
Check PM2 logs: pm2 logs
Check Nginx logs: sudo nginx -t
Verify port availability: netstat -tlnp | grep 8081 or grep 8082
