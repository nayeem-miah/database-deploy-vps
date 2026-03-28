# 🚀 MongoDB VPS Deployment Guide (Production Ready)

A complete step-by-step guide to deploy MongoDB on a VPS with authentication, replica set, and Prisma support.

---

## 📦 Features

* ✅ MongoDB Installation (Ubuntu)
* ✅ Secure Authentication
* ✅ Remote Access Enabled
* ✅ Replica Set (Prisma Ready)
* ✅ Production Security Tips

---

## 🧱 1. VPS Login

```bash
ssh root@your_server_ip
```

---

## 🔄 2. System Update

```bash
sudo apt update && sudo apt upgrade -y
```

---

## 📦 3. Install MongoDB

```bash
wget -qO - https://pgp.mongodb.com/server-7.0.asc | sudo apt-key add -

echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list

sudo apt update
sudo apt install -y mongodb-org
```

---

## ▶️ 4. Start MongoDB

```bash
sudo systemctl start mongod
sudo systemctl enable mongod
```

### Check Status

```bash
sudo systemctl status mongod
```

---

## 🌐 5. Enable Remote Access

```bash
sudo nano /etc/mongod.conf
```

```yaml
net:
  port: 27017
  bindIp: 0.0.0.0
```

---

## 🔥 6. Firewall Setup

```bash
sudo ufw allow 27017
sudo ufw enable
```

---

## 🔐 7. Authentication Setup

### Open Mongo Shell

```bash
mongosh
```

### Create Admin User

```js
use admin

db.createUser({
  user: "admin",
  pwd: "Admin12345",
  roles: [{ role: "root", db: "admin" }]
})
```

---

## 🔒 Enable Authentication

```bash
sudo nano /etc/mongod.conf
```

```yaml
security:
  authorization: enabled
```

```bash
sudo systemctl restart mongod
```

---

## 🔑 Test Login

```bash
mongosh -u admin -p --authenticationDatabase admin
```

---

## 🔁 8. Replica Set Setup (For Prisma)

### Update Config

```bash
sudo nano /etc/mongod.conf
```

```yaml
replication:
  replSetName: rs0
```

---

### ⚠️ Important

Temporarily disable auth before initializing replica set:

```yaml
# security:
#   authorization: enabled
```

---

### Restart MongoDB

```bash
sudo systemctl restart mongod
```

---

### Initialize Replica Set

```bash
mongosh
```

```js
rs.initiate()
```

---

### Fix Host

```js
rs.reconfig({
  _id: "rs0",
  members: [
    { _id: 0, host: "YOUR_SERVER_IP:27017" }
  ]
}, { force: true })
```

---

### Check Status

```js
rs.status()
```

Expected:

```
stateStr: PRIMARY
```

---

## 🔗 Connection String

```bash
mongodb://admin:Admin12345@YOUR_SERVER_IP:27017/mydb?authSource=admin&replicaSet=rs0
```

---

## ⚙️ Prisma Setup

### .env

```env
DATABASE_URL="mongodb://admin:Admin12345@YOUR_SERVER_IP:27017/mydb?authSource=admin&replicaSet=rs0"
```

### Run Commands

```bash
npx prisma generate
npx prisma db push
npm run dev
```

---

## 🔐 Production Security

### Basic

```bash
sudo ufw allow from YOUR_IP to any port 27017
```

---

### Advanced (Recommended)

```bash
openssl rand -base64 756 > /etc/mongo-keyfile
sudo chmod 400 /etc/mongo-keyfile
sudo chown mongodb:mongodb /etc/mongo-keyfile
```

```yaml
security:
  authorization: enabled
  keyFile: /etc/mongo-keyfile
```

---

## ✅ Final Checklist

* [x] MongoDB Installed
* [x] Running
* [x] Remote Access Enabled
* [x] Authentication Enabled
* [x] Replica Set Ready
* [x] Prisma Connected

---

## 💡 Tips

* Small/Medium Project → VPS MongoDB ✅
* Large Scale → MongoDB Atlas 🚀
* Always keep backups ⚠️

---

## 🎉 Done

Now your MongoDB is:

* Production Ready 🚀
* Secure 🔐
* Prisma Compatible ⚡
