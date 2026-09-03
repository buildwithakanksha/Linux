### **9. Firewalls and SELinux/AppArmor**

Firewalls and security frameworks are used to control network access and enforce security policies on Linux systems.

---

#### **Firewalld (RedHat) and UFW (Ubuntu)**

**RedHat - Check firewalld status:**
```bash
sudo systemctl status firewalld
```

**Start firewalld:**
```bash
sudo systemctl start firewalld
```

**Ubuntu - Check UFW status:**
```bash
sudo ufw status
```

**Enable UFW:**
```bash
sudo ufw enable
```

---

#### **Configuring Firewall Rules**

**Allow HTTP (Port 80):**

RedHat:
```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --reload
```

Ubuntu:
```bash
sudo ufw allow 80/tcp
```

**Allow SSH (Port 22):**

RedHat:
```bash
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --reload
```

Ubuntu:
```bash
sudo ufw allow 22/tcp
```

---

#### **SELinux (RedHat)**

SELinux provides mandatory access control for Linux.

**Check SELinux status:**
```bash
getenforce
```

**SELinux modes:**
- `Enforcing` – Policies are enforced.
- `Permissive` – Policies are logged but not enforced.
- `Disabled` – SELinux is disabled.

**Change to permissive mode temporarily:**
```bash
sudo setenforce 0
```

**Change to enforcing mode:**
```bash
sudo setenforce 1
```

**Check file context:**
```bash
ls -Z /var/www/html
```

---

#### **AppArmor (Ubuntu)**

AppArmor provides application security using profiles.

**Check AppArmor status:**
```bash
sudo aa-status
```

**List loaded profiles:**
```bash
sudo apparmor_status
```

**Restart AppArmor:**
```bash
sudo systemctl restart apparmor
```

---

### **Examples**

#### **1. Configure Firewall for HTTP and SSH**

**RedHat:**
```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

**Ubuntu:**
```bash
sudo ufw allow 80/tcp
sudo ufw allow 22/tcp
sudo ufw enable
sudo ufw status
```

---

#### **2. Enable and Configure SELinux in RedHat**

Check status:
```bash
getenforce
```

Set enforcing mode:
```bash
sudo setenforce 1
```

Check security context:
```bash
ls -Z /var/www/html
```

---

#### **3. Enable and Check AppArmor in Ubuntu**

Check status:
```bash
sudo systemctl status apparmor
```

Check profiles:
```bash
sudo aa-status
```
