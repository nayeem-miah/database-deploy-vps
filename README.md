# 🚀 MongoDB VPS Deployment Guide (Production Ready)

A complete production-ready guide to deploy MongoDB on a VPS with authentication, replica set, Prisma support, and security best practices.

---

## 📦 Features

* ✅ MongoDB Installation (Ubuntu)
* ✅ Secure Authentication
* ✅ Remote Access Enabled
* ✅ Replica Set (Prisma Ready)
* ✅ KeyFile Security (Production)
* ✅ Firewall & VPS Network Fix
* ✅ Backup Guide

---

# 🧱 1. VPS Login

```bash
ssh root@your_server_ip
```

---

# 🔄 2. System Update

```bash
sudo apt update && sudo apt upgrade -y
```

---

# 📦 3. Install MongoDB (Updated Way)

```bash
curl -fsSL https://pgp.mongodb.com/server-7.0.asc | \
sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg --dearmor

echo "deb [ arch=amd64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] \
https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | \
sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list

sudo apt update
sudo apt install -y mongodb-org
```

---

# ▶️ 4. Start MongoDB

```bash
sudo systemctl start mongod
sudo systemctl enable mongod
```

Check:

```bash
sudo systemctl status mongod
```

---

# 🌐 5. Enable Remote Access

```bash
sudo nano /etc/mongod.conf
```

```yaml
net:
  port: 27017
  bindIp: 0.0.0.0
```

Restart:

```bash
sudo systemctl restart mongod
```

---

# 🔥 6. Firewall Setup

```bash
# For testing
sudo ufw allow 27017

# Enable firewall
sudo ufw enable
```

---

# 🔐 7. Create Admin User

```bash
mongosh
```

```js
use admin

db.createUser({
  user: "admin",
  pwd: "Admin12345",
  roles: [{ role: "root", db: "admin" }]
})
```

---

# 🔒 8. Enable Authentication

```bash
sudo nano /etc/mongod.conf
```

```yaml
security:
  authorization: enabled
```

Restart:

```bash
sudo systemctl restart mongod
```

---

# 🔑 9. Test Login

```bash
mongosh -u admin -p --authenticationDatabase admin
```

---

# 🔁 10. Replica Set Setup (Prisma Required)

```bash
sudo nano /etc/mongod.conf
```

```yaml
replication:
  replSetName: rs0
```

---

## ⚠️ Disable auth temporarily

```yaml
# security:
#   authorization: enabled
```

Restart:

```bash
sudo systemctl restart mongod
```

---

## Initialize

```bash
mongosh
```

```js
rs.initiate()
```

---

## Fix Host (IMPORTANT)

```js
rs.reconfig({
  _id: "rs0",
  members: [
    { _id: 0, host: "YOUR_SERVER_IP:27017" }
  ]
}, { force: true })
```

---

## Check

```js
rs.status()
```

Expected:

```
stateStr: PRIMARY
```

---

# 🔐 11. Enable KeyFile (Production Required)

```bash
openssl rand -base64 756 > /etc/mongo-keyfile
sudo chmod 400 /etc/mongo-keyfile
sudo chown mongodb:mongodb /etc/mongo-keyfile
```

---

## Update config

```yaml
security:
  authorization: enabled
  keyFile: /etc/mongo-keyfile
```

Restart:

```bash
sudo systemctl restart mongod
```

---

# 🔍 12. Verify MongoDB Running

```bash
ss -tulnp | grep 27017
```

Expected:

```
0.0.0.0:27017
```

---

# 🌐 13. VPS Firewall (VERY IMPORTANT)

👉 If connection fails:

* Open VPS provider panel
* Allow:

```
Port: 27017
Protocol: TCP
Source: 0.0.0.0/0 (testing)
```

---

# 🔗 14. Connection String

```bash
mongodb://admin:Admin12345@YOUR_SERVER_IP:27017/mydb?authSource=admin&replicaSet=rs0
```

---

# ⚙️ 15. Prisma Setup

### `.env`

```env
DATABASE_URL="mongodb://admin:Admin12345@YOUR_SERVER_IP:27017/mydb?authSource=admin&replicaSet=rs0"
```

---

### Run

```bash
npx prisma generate
npx prisma db push
npm run dev
```

---

# 🧪 16. Test Connection

```bash
mongosh "mongodb://admin:Admin12345@YOUR_SERVER_IP:27017/?authSource=admin&replicaSet=rs0"
```

---

# ❌ Common Errors & Fix

---

### 🔴 Error: `ENOTFOUND`

👉 Fix:

```js
rs.reconfig({
  host: "IP:27017"
})
```

---

### 🔴 Error: `ETIMEDOUT`

👉 Fix:

* Check firewall
* Check VPS provider firewall

---

### 🔴 Error: `mongod failed`

👉 Fix:

* Check config indentation
* Check keyFile permission

---

# 🔐 Security Best Practices

```bash
# Restrict access
sudo ufw allow from YOUR_IP to any port 27017
```

---

# 💾 Backup (IMPORTANT)

```bash
mongodump --uri="mongodb://admin:password@IP:27017"
```

Restore:

```bash
mongorestore --uri="mongodb://admin:password@IP:27017"
```

---

# 🎯 Final Checklist

* [x] MongoDB Installed
* [x] Running
* [x] Remote Access Enabled
* [x] Authentication Enabled
* [x] Replica Set Ready
* [x] KeyFile Enabled
* [x] Prisma Connected

---

# 🎉 Done

Your MongoDB is now:

* 🚀 Production Ready
* 🔐 Secure
* ⚡ Prisma Compatible
