### **10. Shell Scripting**

Shell scripting is used to automate repetitive Linux administration and DevOps tasks using shell commands.

---

#### **Introduction to Shell Scripting**

A shell script is a file containing a sequence of Linux commands.

Create a script:
```bash
vi script.sh
```

Add:
```bash
#!/bin/bash
echo "Hello DevOps"
```

Make it executable:
```bash
chmod +x script.sh
```

Run:
```bash
./script.sh
```

---

#### **Basic Scripting Concepts (`if`, `while`, `for`, `case`)**

**1. `if` condition:**
```bash
if [ -f file.txt ]; then
    echo "File exists"
fi
```

**2. `for` loop:**
```bash
for i in 1 2 3
do
    echo $i
done
```

**3. `while` loop:**
```bash
count=1
while [ $count -le 3 ]
do
    echo $count
    count=$((count+1))
done
```

**4. `case` statement:**
```bash
case $1 in
    start) echo "Starting" ;;
    stop) echo "Stopping" ;;
    *) echo "Invalid option" ;;
esac
```

---

#### **Creating and Running Scripts**

**Create script:**
```bash
vi backup.sh
```

**Make executable:**
```bash
chmod +x backup.sh
```

**Run script:**
```bash
./backup.sh
```

---

#### **Automation with Cron Jobs**

`cron` is used to schedule scripts or commands automatically.

**Edit cron jobs:**
```bash
crontab -e
```

Cron format:
```text
* * * * * command
```

Example - run a script every day at 2 AM:
```text
0 2 * * * /home/user/backup.sh
```

Check cron jobs:
```bash
crontab -l
```

---

### **Examples**

#### **1. Backup a Directory Using Shell Script**

Create the script:

```bash
vi backup.sh
```

Add:
```bash
#!/bin/bash

SOURCE="/var/www/html"
DEST="/backup"

mkdir -p $DEST
tar -czf $DEST/backup_$(date +%F).tar.gz $SOURCE

echo "Backup completed"
```

Make executable:
```bash
chmod +x backup.sh
```

Run:
```bash
./backup.sh
```

---

#### **2. Schedule Backup Using Cron**

Edit cron:

```bash
crontab -e
```

Add:
```text
0 2 * * * /home/user/backup.sh
```

Verify:
```bash
crontab -l
```

The backup script will run automatically every day at **2:00 AM**.
