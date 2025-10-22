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


sudo ln -s /etc/nginx/sites-available/harishshetty.xyz /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx



scp -i mykey.pem -r /etc/letsencrypt/live/harishshetty.xyz ubuntu@<EC2-IP>:/tmp/
sudo mv /tmp/harishshetty.xyz /etc/letsencrypt/live/
sudo systemctl reload nginx

