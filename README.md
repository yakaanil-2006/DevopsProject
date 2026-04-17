# DevopsProject
---

## 🚀 Implementation

This project demonstrates the complete deployment of a **Node.js (Express) application inside a Docker container on an AWS EC2 instance**, where all development and configuration are performed remotely on the cloud machine.

---

### 🔹 1. EC2 Setup

* Launched an **AWS EC2 instance (Amazon Linux 2023)**
* Connected securely using SSH:

```bash
ssh -i <your-key>.pem ec2-user@<EC2-Public-IP>
```

---

### 🔹 2. Install Node.js & npm

Updated system packages and installed Node.js:

```bash
sudo dnf update -y
sudo dnf install nodejs -y

node -v
npm -v
```

---

### 🔹 3. Install and Configure Docker

```bash
sudo dnf install docker -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ec2-user
```

Reconnect SSH to apply Docker permissions.

---

### 🔹 4. Create Project

```bash
mkdir node-docker-app
cd node-docker-app

npm init -y
npm install express
npm install --save-dev nodemon
```

---

### 🔹 5. Application Code

Create `app.js`:

```javascript
const express = require("express");
const app = express();

const PORT = 3000;

app.get("/", (req, res) => {
  res.send("Hello from EC2 Docker App");
});

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

---

### 🔹 6. Update package.json

```json
"scripts": {
  "start": "node app.js",
  "dev": "nodemon app.js"
}
```

---

### 🔹 7. Dockerfile

Create a `Dockerfile`:

```dockerfile
FROM node:18

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "run", "dev"]
```

---

### 🔹 8. .dockerignore

```txt
node_modules
npm-debug.log
```

---

### 🔹 9. Build Docker Image

```bash
docker build -t node-app .
```

---

### 🔹 10. Run Container with Live Reload

```bash
docker run -d \
-p 3000:3000 \
-v $(pwd):/app \
-v /app/node_modules \
node-app
```

* `-p 3000:3000` → maps container port to EC2
* `-v $(pwd):/app` → enables live reload
* Nodemon automatically restarts on file changes

---

### 🔹 11. Configure Security Group

* Allow **Inbound Rule:**

  * Type: Custom TCP
  * Port: 3000
  * Source: Anywhere (0.0.0.0/0)

---

### 🔹 12. Access Application

Open in browser:

```
http://<EC2-Public-IP>:3000
```

---

### 🔹 13. Live Reload Verification

* Edit `app.js` inside EC2
* Save changes
* Refresh browser
* Changes reflect instantly without restarting container

---

## ✅ Result

* Docker container runs successfully
* Application accessible via EC2 public IP
* Live reload works using Nodemon
* Entire workflow executed inside EC2 (no local setup required) 

---
