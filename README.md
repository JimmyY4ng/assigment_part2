# README.md

## Part 2: Setup Instructions

Follow these instructions to set up your system user, configure Nginx, and serve files using a file server.
Vist http://137.184.90.141

---

### Steps to Complete Setup

1. **Create the `webgen` Directory and User**
   ```bash
   sudo mkdir /var/lib/webgen
   sudo useradd -r -m -d /var/lib/webgen webgen
   sudo chown -R webgen:webgen /var/lib/webgen
   ```

2. **Clone and Organize Required Files**
   ```bash
   sudo git clone https://git.sr.ht/~nathan_climbs/2420-as3-p2-start /var/lib/webgen/
   sudo mkdir -p /var/lib/webgen/bin /var/lib/webgen/documents /var/lib/webgen/HTML
   sudo touch /var/lib/webgen/documents/file-one
   sudo touch /var/lib/webgen/documents/file-two
   sudo mv /var/lib/webgen/2420-as3-p2-start/generate_index /var/lib/webgen/bin/
   sudo chmod +x /var/lib/webgen/bin/generate_index
   sudo /var/lib/webgen/bin/generate_index
   ```
   
3. **Configure Nginx**
Create directories for server block configuration:
  ```bash
  sudo mkdir -p /etc/nginx/sites-available /etc/nginx/sites-enabled
  ```

Create the server block file:
```bash
sudo nvim /etc/nginx/sites-available/webgen.conf
```

Add the following configuration:
```nginx
server {
    listen 80;
    listen [::]:80;
    server_name 137.184.90.141;

    root /var/lib/webgen/HTML;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location /documents {
        root /var/lib/webgen;
        autoindex on;
    }
}
```

Enable the server block by creating a symbolic link:
```bash
sudo ln -s /etc/nginx/sites-available/webgen.conf /etc/nginx/sites-enabled/webgen.conf
```

Update the main nginx.conf file to include the sites-enabled directory:
```bash
sudo nvim /etc/nginx/nginx.conf
```

Add the following line inside the http block:
```nginx
include /etc/nginx/sites-enabled/*;
```

5. **Enable and Start Nginx**
```bash
sudo systemctl enable nginx
sudo systemctl start nginx
```

6. **Verify Configuration**
Test the Nginx configuration:
```bash
sudo nginx -t
```

Restart Nginx to apply changes:
```bash
sudo systemctl restart nginx
```
