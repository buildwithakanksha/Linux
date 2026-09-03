### **5. File Permissions and Security**

File permissions control who can read, write, and execute files and directories in Linux.

---

#### **Understanding File Permissions (read, write, execute)**

Linux permissions are represented as:

```text
r = read
w = write
x = execute
```

Check permissions:
```bash
ls -l file.txt
```

Example:
```text
-rwxr-xr-- 1 devuser devops 100 file.txt
```

Change permissions using `chmod`:
```bash
chmod 755 file.txt
```

Change owner/group using `chown`:
```bash
sudo chown devuser:devops file.txt
```

---

#### **Access Control Lists (ACLs)**

ACLs provide more granular permissions for specific users or groups.

**View ACL:**
```bash
getfacl file.txt
```

**Give a user read/write access:**
```bash
sudo setfacl -m u:devuser:rw file.txt
```

**Remove ACL:**
```bash
sudo setfacl -x u:devuser file.txt
```

---

#### **Special Permissions (`setuid`, `setgid`, `sticky bit`)**

**1. SetUID:** Allows a file to run with the permissions of its owner.
```bash
chmod u+s file
```

**2. SetGID:** Files run with the group owner's permissions; directories can inherit the group.
```bash
chmod g+s directory
```

**3. Sticky Bit:** Only the file owner, directory owner, or root can delete files in the directory.
```bash
chmod +t directory
```

---

### **Examples**

#### **1. Step-by-step File Permissions**

1. Create a file:
```bash
sudo touch /tmp/demo.txt
```

2. Set permissions:
```bash
sudo chmod 640 /tmp/demo.txt
```

3. Check permissions:
```bash
ls -l /tmp/demo.txt
```

---

#### **2. Use ACL for Granular Access**

1. Give `devuser` read/write access:
```bash
sudo setfacl -m u:devuser:rw /tmp/demo.txt
```

2. Verify:
```bash
getfacl /tmp/demo.txt
```

---

#### **3. Configure Special Permissions**

**SetUID:**
```bash
chmod 4755 file
```

**SetGID:**
```bash
chmod 2775 directory
```

**Sticky Bit:**
```bash
chmod 1777 directory
```

Check the special permission:
```bash
ls -ld directory
```
