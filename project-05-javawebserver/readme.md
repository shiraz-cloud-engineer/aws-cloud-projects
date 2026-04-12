# ☁️ Project — Java Web Server on AWS EC2

## 🎯 Goal

Host a multi-page Java web application on AWS EC2 running on port 8080 using Java's built-in HTTP Server — no frameworks needed!

---

## 🌐 Live Pages

| Page | URL | Description |
|------|-----|-------------|
| 🏠 Home | `http://YOUR_EC2_IP:8080/` | Landing page with stats and services |
| 👨‍💻 About | `http://YOUR_EC2_IP:8080/about` | Bio, education and experience |
| 🔧 Skills | `http://YOUR_EC2_IP:8080/skills` | Technical skills overview |
| 📂 Projects | `http://YOUR_EC2_IP:8080/projects` | All AWS projects listed |

---

## 🏗️ Architecture

```
Browser → EC2 Public IP:8080 → Java HTTP Server → HTML Response
```

---

## 📋 Prerequisites

- AWS EC2 instance running Ubuntu
- Port 8080 open in Security Group
- Java JDK installed on EC2

---

## 🔧 Step 1 — Install Java on EC2

```bash
sudo apt update
sudo apt install default-jdk -y
java -version
```

---

## 🔧 Step 2 — Upload Welcome.java to EC2

Use **WinSCP** or **scp** to upload `Welcome.java` to:
```
/home/ubuntu/Welcome.java
```

Using scp from your local machine:
```bash
scp -i your-key.pem Welcome.java ubuntu@YOUR_EC2_IP:/home/ubuntu/
```

---

## 🔧 Step 3 — Compile the Java file

```bash
cd /home/ubuntu
javac Welcome.java
```

> ✅ You should see `Welcome.class` and `Welcome$MyHandler.class` created

---

## 🔧 Step 4 — Run the Server

```bash
java Welcome
```

> ✅ Expected output:
> ```
> ========================================
>    Cloud Hub Server Started!
>    Running on port 8080
>    http://localhost:8080
> ========================================
> ```

---

## 🔧 Step 5 — Open Port 8080 in Security Group

1. Go to **EC2 → Security Groups → Inbound Rules**
2. Click **Edit inbound rules → Add rule**

| Type | Port | Source |
|------|------|--------|
| Custom TCP | 8080 | 0.0.0.0/0 |

3. Click **Save rules** ✅

---

## 🔧 Step 6 — Access in Browser

```
http://YOUR_EC2_PUBLIC_IP:8080
```

---

## 🛠️ How to Stop the Server

```bash
sudo kill -9 $(sudo lsof -t -i :8080)
```

---

## 🔄 How to Update & Redeploy

```bash
# Stop running server
sudo kill -9 $(sudo lsof -t -i :8080)

# Edit the file
nano /home/ubuntu/Welcome.java

# Recompile
javac Welcome.java

# Run again
java Welcome
```

---

## 🐛 Troubleshooting

| Issue | Fix |
|-------|-----|
| `ClassNotFoundException` | Use `java Welcome` not `java welcome` or `java Welcome.java` |
| `Address already in use` | Run `sudo kill -9 $(sudo lsof -t -i :8080)` |
| Can't access in browser | Check port 8080 is open in Security Group |
| `error: '{' expected` | File name must match class name exactly — `Welcome.java` |
| Permission denied (WinSCP) | Upload to `/home/ubuntu/` first |

---

## ✅ Completion Checklist

- [x] Java JDK installed on EC2
- [x] Welcome.java uploaded to EC2
- [x] File compiled successfully
- [x] Server running on port 8080
- [x] Port 8080 opened in Security Group
- [x] All 4 pages accessible in browser
- [x] Website live on AWS EC2 🎉

---

## 🔧 Tech Used

| Technology | Purpose |
|------------|---------|
| AWS EC2 | Hosting the Java server |
| Java JDK | Compiling and running the app |
| com.sun.net.httpserver | Built-in Java HTTP server |
| WinSCP | Transferring files from Windows to Linux |
| Security Groups | Opening port 8080 for public access |
