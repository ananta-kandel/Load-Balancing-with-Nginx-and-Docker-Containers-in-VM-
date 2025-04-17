⚙️ Nginx Load Balancing (Round-Robin) – Inside the VM
Nginx installed and configured inside the VM to distribute traffic between two containers using round-robin method.

🔧 Nginx Config (VM)
nginx
Copy
Edit
upstream backend {
    server localhost:8082;
    server localhost:8081;
}

server {
    listen 80;
    server_name 192.168.32.130;

    location / {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_redirect off;

        proxy_connect_timeout 90;
        proxy_send_timeout 90;
        proxy_read_timeout 90;
    }
}
🖥️ Host Machine Configuration
Nginx is also installed on the host machine.

It acts as a reverse proxy that forwards requests to the VM's Nginx.

🔧 Nginx Config (Host)
nginx
Copy
Edit
server {
    listen       80;
    server_name  localhost;

    location / {
        proxy_pass         http://192.168.32.130/;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection 'upgrade';
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
📈 Results
Tested with curl from host machine to the VM IP. The VM's Nginx distributes the requests between the containers using round-robin.

🔁 Test Command
bash
Copy
Edit
for i in {1..10}; do curl "http://localhost/" && echo; done
✅ Output
You should observe alternating responses from Container 1 and Container 2.

🖼️ Insert screenshot of terminal output here showing round-robin responses

📎 Notes
Ensure the VM's IP (192.168.32.130) is accessible from the host.

Nginx services must be restarted after every configuration change:

bash
Copy
Edit
sudo systemctl restart nginx
If you'd like help adding the images to GitHub or need a diagram created, I can help generate one or guide you step-by-step!
