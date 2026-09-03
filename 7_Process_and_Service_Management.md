### **7. Process and Service Management**

Process and service management is used to monitor running processes, control services, and manage applications running in the background.

---

#### **Understanding Processes (`ps`, `top`, `htop`, `kill`)**

**1. List running processes:**
```bash
ps aux
```

**2. View processes in real time:**
```bash
top
```

**3. View processes using `htop`:**
```bash
htop
```

**4. Find a process:**
```bash
ps aux | grep nginx
```

**5. Stop a process:**
```bash
kill PID
```

**6. Force kill a process:**
```bash
kill -9 PID
```

---

#### **Systemd and Init Systems (`systemctl`, `service`)**

`systemd` is the service manager used by modern Linux systems. `systemctl` is used to start, stop, restart, enable, and check services.

**1. Check service status:**
```bash
sudo systemctl status nginx
```

**2. Start a service:**
```bash
sudo systemctl start nginx
```

**3. Stop a service:**
```bash
sudo systemctl stop nginx
```

**4. Restart a service:**
```bash
sudo systemctl restart nginx
```

**5. Enable service at boot:**
```bash
sudo systemctl enable nginx
```

**6. Disable service at boot:**
```bash
sudo systemctl disable nginx
```

---

#### **Starting, Stopping, and Managing Services**

**Using `service`:**

Start:
```bash
sudo service nginx start
```

Stop:
```bash
sudo service nginx stop
```

Restart:
```bash
sudo service nginx restart
```

Check status:
```bash
sudo service nginx status
```

---

### **Examples**

#### **1. Check Running Processes**

```bash
ps aux
```

Find a specific process:

```bash
ps aux | grep nginx
```

Monitor processes:

```bash
top
```

---

#### **2. Manage NGINX Service**

**Ubuntu:**
```bash
sudo systemctl start nginx
sudo systemctl status nginx
sudo systemctl enable nginx
sudo systemctl stop nginx
```

**RedHat:**
```bash
sudo systemctl start nginx
sudo systemctl status nginx
sudo systemctl enable nginx
sudo systemctl stop nginx
```

Using `service`:

```bash
sudo service nginx start
sudo service nginx stop
sudo service nginx status
```
