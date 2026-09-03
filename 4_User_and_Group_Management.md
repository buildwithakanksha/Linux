### **4. User and Group Management**

---

User and group management is used to create, modify, and delete users and groups, manage passwords, and provide administrative access.

---

#### **User Accounts (`useradd`, `usermod`, `userdel`)**

**1. Add a New User:**
```bash
sudo useradd -m devuser
```

**2. Set Password:**
```bash
sudo passwd devuser
```

**3. Modify a User:**
```bash
sudo usermod -s /bin/bash devuser
```

**4. Delete a User:**
```bash
sudo userdel -r devuser
```

---

#### **Group Management (`groupadd`, `groupdel`, `gpasswd`)**

**1. Create a Group:**
```bash
sudo groupadd devops
```

**2. Add User to a Group:**
```bash
sudo usermod -aG devops devuser
```

**3. Remove User from a Group:**
```bash
sudo gpasswd -d devuser devops
```

**4. Delete a Group:**
```bash
sudo groupdel devops
```

---

#### **Switching Users and Understanding `sudo`**

**1. Switch to Another User:**
```bash
su - devuser
```

**2. Run a Command with Administrative Privileges:**
```bash
sudo systemctl restart nginx
```

**3. Check Sudo Permissions:**
```bash
sudo -l
```

---

#### **Managing User Passwords (`passwd`)**

**1. Change User Password:**
```bash
sudo passwd devuser
```

**2. Lock a User:**
```bash
sudo passwd -l devuser
```

**3. Unlock a User:**
```bash
sudo passwd -u devuser
```

---

### **Examples**

#### **1. Add a New User with Specific Permissions**

**Ubuntu:**
```bash
sudo useradd -m devuser
sudo passwd devuser
sudo usermod -aG sudo devuser
```

**RedHat:**
```bash
sudo useradd -m devuser
sudo passwd devuser
sudo usermod -aG wheel devuser
```

---

#### **2. Modify User Group Memberships and Demonstrate `sudo`**

```bash
sudo groupadd devops
sudo usermod -aG devops devuser
```

Verify group membership:
```bash
id devuser
```

Switch to the user:
```bash
su - devuser
```

Test sudo access:
```bash
sudo whoami
```

Output:
```text
root
```
