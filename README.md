Here’s your **cleaned and properly formatted** GitHub `README.md` version of the AWS Route 53 project — with all commands and sections **intact and unchanged**, just fixed for Markdown readability and syntax highlighting 👇

---

# 🧭 AWS Route 53 Project — Multi-Region Setup with Nginx & SSL

## 1️⃣ Install and Configure Nginx with Certbot

```bash
sudo apt update
sudo apt install nginx certbot python3-certbot-nginx -y

sudo systemctl enable nginx
sudo systemctl start nginx
sudo nginx -t
sudo systemctl status nginx
```

---

## 2️⃣ Obtain Wildcard SSL Certificate Using Certbot (Manual DNS Challenge)

```bash
sudo certbot certonly --manual --preferred-challenges=dns \
-d "*.harishshetty.xyz"
```

### ✅ Verify DNS Record

```bash
nslookup -type=TXT _acme-challenge.harishshetty.xyz
```

Expected Output:

```
_acme-challenge.harishshetty.xyz → some-long-random-text
```

Certificates will be stored at:

```
/etc/letsencrypt/live/harishshetty.xyz/fullchain.pem
/etc/letsencrypt/live/harishshetty.xyz/privkey.pem
```

---

## 3️⃣ Configure Nginx for SSL Redirection

```bash
sudo nano /etc/nginx/sites-available/harishshetty.xyz
```

Paste the following:

```nginx
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
```

---

## 4️⃣ Create a Simple HTML Page

```bash
sudo nano /var/www/html/index.html
```

```html
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
```

---

## 5️⃣ Enable Site Configuration

```bash
sudo apt update
sudo apt install nginx certbot python3-certbot-nginx -y

sudo systemctl enable nginx
sudo systemctl start nginx
sudo nginx -t
sudo systemctl status nginx

sudo ln -s /etc/nginx/sites-available/harishshetty.xyz /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

## 6️⃣ Copy SSL Certificates Between Instances

From **Source Instance**:

```bash
scp -i "new-keypair.pem" -r /etc/letsencrypt/live/harishshetty.xyz ubuntu@ec2-13-201-186-36.ap-south-1.compute.amazonaws.com:/tmp/
```

On **Destination Instance**:

```bash
sudo mkdir -p /etc/letsencrypt/live/harishshetty.xyz
sudo cp -r /tmp/harishshetty.xyz/* /etc/letsencrypt/live/harishshetty.xyz/
systemctl restart nginx
```

Connect via SSH:

```bash
ssh -i "new-keypair.pem" ubuntu@ec2-13-201-186-36.ap-south-1.compute.amazonaws.com
```

---

## 7️⃣ EC2 User Data Script for Auto Setup (US-EAST-1 Example)

```bash
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
```

---

## 8️⃣ Copy Certificates to Another Instance (US-EAST-1)

From Source:

```bash
scp -i "nv-keypair.pem" -r /etc/letsencrypt/live/harishshetty.xyz ubuntu@ec2-54-221-22-107.compute-1.amazonaws.com:/tmp/
```

On Target:

```bash
sudo mkdir -p /etc/letsencrypt/live/harishshetty.xyz
sudo cp -r /tmp/harishshetty.xyz/* /etc/letsencrypt/live/harishshetty.xyz/
systemctl restart nginx
```

Connect:

```bash
ssh -i "nv-keypair.pem" ubuntu@ec2-54-221-22-107.compute-1.amazonaws.com
```

---

✅ **End of Setup**

This setup allows you to:

* Use **wildcard SSL** for all subdomains under `*.harishshetty.xyz`.
* Host identical static sites across **multiple AWS regions**.
* Combine with **Route 53 routing policies** (latency, failover, weighted, etc.).

---

Would you like me to add a short **“Project Overview” and “Architecture Diagram”** section at the top (for better GitHub presentation)?
