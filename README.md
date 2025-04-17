# 🔄 Load Balancing with Nginx and Docker Containers in a Virtual Machine

## 📌 Workflow Overview

**Architecture:**

> \`![Workflow](https://github.com/ananta-kandel/Load-Balancing-with-Nginx-and-Docker-Containers-in-VM-/blob/main/WorkFlow1.png)\`

---

## 🖥️ Inside the VM
> \`![Workflow](https://github.com/ananta-kandel/Load-Balancing-with-Nginx-and-Docker-Containers-in-VM-/blob/main/InsideVMWorkFlow.png)\`

### ✅ VM Setup
- VM provisioned using **Vagrant**.
- VM runs **two Docker containers**, each serving a simple web application.
- Container port mappings:
  - \`8081\` → Container 1
  - \`8082\` → Container 2

### 🔍 Docker Container Verification

Command used to verify containers:
\`\`\`bash
docker ps
\`\`\`

> \`![Workflow](https://github.com/ananta-kandel/Load-Balancing-with-Nginx-and-Docker-Containers-in-VM-/blob/main/DockerContainerRunning.png)\`
---

## ⚙️ Nginx Load Balancing (Round-Robin) – Inside the VM

Nginx installed and configured inside the VM to distribute traffic between two containers using **round-robin** method.

### 🔧 Nginx Config (VM)
\`\`\`nginx
upstream backend {
    server localhost:8082;
    server localhost:8081;
}

server {
    listen 80;
    server_name 192.168.32.130;

    location / {
        proxy_pass http://backend;
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
        proxy_redirect off;

        proxy_connect_timeout 90;
        proxy_send_timeout 90;
        proxy_read_timeout 90;
    }
}
\`\`\`

---

## 🖥️ Host Machine Configuration

- Nginx is also installed on the **host machine**.
- It acts as a reverse proxy that forwards requests to the VM's Nginx.

### 🔧 Nginx Config (Host)
\`\`\`nginx
server {
    listen       80;
    server_name  localhost;

    location / {
        proxy_pass         http://192.168.32.130/;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade \$http_upgrade;
        proxy_set_header   Connection 'upgrade';
        proxy_set_header   Host \$host;
        proxy_cache_bypass \$http_upgrade;
    }
}
\`\`\`

---

## 📈 Results

Tested with curl from host machine to the VM IP. The VM's Nginx distributes the requests between the containers using round-robin.

### 🔁 Test Command
\`\`\`bash
for i in {1..10}; do curl "http://localhost/" && echo; done
\`\`\`

### ✅ Output
You should observe alternating responses from Container 1 and Container 2.


> \`![Workflow](https://github.com/ananta-kandel/Load-Balancing-with-Nginx-and-Docker-Containers-in-VM-/blob/main/Screenshot%202025-04-17%20at%2011.30.18%E2%80%AFpm.png)\`
---


---

## 📎 Notes

- Ensure the VM's IP (\`192.168.32.130\`) is accessible from the host.
- Nginx services must be restarted after every configuration change:
\`\`\`bash
sudo systemctl restart nginx
\`\`\`
