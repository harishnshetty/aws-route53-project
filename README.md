# aws-route53-project

sudo apt update
sudo apt install nginx certbot python3-certbot-nginx -y

sudo systemctl enable nginx
sudo systemctl start nginx
sudo nginx -t
sudo systemctl status nginx

sudo certbot certonly --manual --preferred-challenges=dns \
-d "*.harishshetty.xyz"

# Check with Google's DNS tool
nslookup -type=TXT _acme-challenge.harishshetty.xyz



_acme-challenge.harishshetty.xyz → some-long-random-text
/etc/letsencrypt/live/harishshetty.xyz/fullchain.pem
/etc/letsencrypt/live/harishshetty.xyz/privkey.pem


sudo nano /etc/nginx/sites-available/harishshetty.xyz

server {
    listen 80;
    server_name *.harishshetty.xyz harishshetty.xyz;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name *.harishshetty.xyz harishshetty.xyz;

    ssl_certificate /etc/letsencrypt/live/harishshetty.xyz/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/harishshetty.xyz/privkey.pem;

    location / {
        root /var/www/html;
        index index.html index.htm;
    }
}


sudo nano /var/www/html/index.html

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>AWS Route 53 Region Test</title>
</head>
<body>
  <h1>AP-SOUTH-1</h1>
</body>
</html>


sudo apt update
sudo apt install nginx certbot python3-certbot-nginx -y

sudo systemctl enable nginx
sudo systemctl start nginx
sudo nginx -t
sudo systemctl status nginx

sudo ln -s /etc/nginx/sites-available/harishshetty.xyz /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx



scp -i "new-keypair.pem" -r /etc/letsencrypt/live/harishshetty.xyz ubuntu@ec2-13-201-186-36.ap-south-1.compute.amazonaws.com:/tmp/
sudo mkdir -p /etc/letsencrypt/live/harishshetty.xyz
sudo cp -r /tmp/harishshetty.xyz/* /etc/letsencrypt/live/harishshetty.xyz/
systemctl restart nginx

ssh -i "new-keypair.pem" ubuntu@ec2-13-201-186-36.ap-south-1.compute.amazonaws.com



#!/bin/bash
# === Update and install dependencies ===
apt update -y
apt install nginx certbot python3-certbot-nginx -y

# === Enable and start Nginx ===
systemctl enable nginx
systemctl start nginx

# === Set up Nginx configuration for harishshetty.xyz ===
cat <<EOF >/etc/nginx/sites-available/harishshetty.xyz
server {
    listen 80;
    server_name *.harishshetty.xyz harishshetty.xyz;
    return 301 https://\$host\$request_uri;
}

server {
    listen 443 ssl;
    server_name *.harishshetty.xyz harishshetty.xyz;

    ssl_certificate /etc/letsencrypt/live/harishshetty.xyz/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/harishshetty.xyz/privkey.pem;

    location / {
        root /var/www/html;
        index index.html index.htm;
    }
}
EOF

# === Remove default site and enable custom site ===
rm -f /etc/nginx/sites-enabled/default
ln -s /etc/nginx/sites-available/harishshetty.xyz /etc/nginx/sites-enabled/

# === Create website content ===
mkdir -p /var/www/html
cat <<EOF >/var/www/html/index.html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>AWS Route 53 Region Test</title>
</head>
<body>
  <h1>US-EAST-1</h1>
</body>
</html>
EOF

# === Restart Nginx to apply HTTP configuration ===
systemctl restart nginx

ssh -i "nv-keypair.pem" ubuntu@ec2-54-221-22-107.compute-1.amazonaws.com

scp -i "nv-keypair.pem" -r /etc/letsencrypt/live/harishshetty.xyz ubuntu@ec2-54-221-22-107.compute-1.amazonaws.com:/tmp/
sudo mkdir -p /etc/letsencrypt/live/harishshetty.xyz
sudo cp -r /tmp/harishshetty.xyz/* /etc/letsencrypt/live/harishshetty.xyz/
systemctl restart nginx