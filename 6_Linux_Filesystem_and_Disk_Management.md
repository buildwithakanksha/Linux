### **6. Linux Filesystem and Disk Management**

Linux filesystem and disk management is used to understand directory structure, manage disks and partitions, create filesystems, mount storage, and manage LVM.

---

#### **Filesystem Hierarchy**

| Directory | Purpose |
|---|---|
| `/bin` | Essential user commands |
| `/etc` | System configuration files |
| `/home` | User home directories |
| `/var` | Logs and variable data |
| `/tmp` | Temporary files |
| `/root` | Root user's home directory |
| `/dev` | Device files |
| `/proc` | Process and kernel information |
| `/mnt` | Temporary mount point |

Check filesystem usage:

```bash
df -h
```

Check directory size:

```bash
du -sh /var
```

---

#### **Mounting and Unmounting Filesystems**

**1. Mount a filesystem:**
```bash
sudo mount /dev/xvdf1 /mnt/data
```

**2. View mounted filesystems:**
```bash
df -h
```

**3. Unmount a filesystem:**
```bash
sudo umount /mnt/data
```

---

#### **Disk Partitioning (`fdisk`, `parted`)**

**1. List disks:**
```bash
sudo fdisk -l
```

**2. Open a disk using `fdisk`:**
```bash
sudo fdisk /dev/xvdf
```

**3. Check partitions using `parted`:**
```bash
sudo parted -l
```

---

#### **Creating Filesystems (`mkfs`, `mkswap`, `tune2fs`)**

**1. Create an ext4 filesystem:**
```bash
sudo mkfs.ext4 /dev/xvdf1
```

**2. Create swap:**
```bash
sudo mkswap /dev/xvdf2
sudo swapon /dev/xvdf2
```

**3. Check and modify ext filesystem settings:**
```bash
sudo tune2fs -l /dev/xvdf1
```

---

#### **LVM (Logical Volume Manager)**

LVM allows flexible management of disks using **Physical Volumes (PV)**, **Volume Groups (VG)**, and **Logical Volumes (LV)**.

**1. Create Physical Volume:**
```bash
sudo pvcreate /dev/xvdf
```

**2. Create Volume Group:**
```bash
sudo vgcreate devops-vg /dev/xvdf
```

**3. Create Logical Volume:**
```bash
sudo lvcreate -L 5G -n data-lv devops-vg
```

**4. Create filesystem:**
```bash
sudo mkfs.ext4 /dev/devops-vg/data-lv
```

**5. Mount the Logical Volume:**
```bash
sudo mkdir /data
sudo mount /dev/devops-vg/data-lv /data
```

---

### **Examples**

#### **1. Partition and Format a New Disk**

```bash
sudo fdisk /dev/xvdf
sudo mkfs.ext4 /dev/xvdf1
sudo mkdir /data
sudo mount /dev/xvdf1 /data
```

Check:

```bash
df -h
```

---

#### **2. Permanent Mount Using `/etc/fstab`**

Find the UUID:

```bash
sudo blkid /dev/xvdf1
```

Edit `/etc/fstab`:

```bash
sudo vi /etc/fstab
```

Add:

```text
UUID=<UUID>  /data  ext4  defaults  0  2
```

Test:

```bash
sudo mount -a
```

---

#### **3. Set Up LVM Step by Step**

```bash
sudo pvcreate /dev/xvdf
sudo vgcreate devops-vg /dev/xvdf
sudo lvcreate -L 5G -n data-lv devops-vg
sudo mkfs.ext4 /dev/devops-vg/data-lv
sudo mkdir /data
sudo mount /dev/devops-vg/data-lv /data
```

Verify:

```bash
sudo pvs
sudo vgs
sudo lvs
df -h
```
