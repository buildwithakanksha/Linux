### **8. Networking Basics**

Networking basics are used to configure IP addresses, manage hostnames, troubleshoot connectivity, and connect to remote Linux servers.

---

#### **IP Address Configuration (`ifconfig`, `ip`, `nmcli`)**

**1. Show IP address:**
```bash
ip addr
```

**2. Show network interfaces:**
```bash
ifconfig
```

**3. Show connection details using `nmcli`:**
```bash
nmcli connection show
```

**4. Show IP address using `nmcli`:**
```bash
nmcli device show
```

---

#### **Hostname Configuration**

**1. Check hostname:**
```bash
hostname
```

**2. Set hostname:**
```bash
sudo hostnamectl set-hostname webserver
```

**3. Verify:**
```bash
hostnamectl
```

---

#### **DNS and `/etc/hosts`**

`/etc/hosts` is used for local hostname-to-IP address mapping.

**1. Edit hosts file:**
```bash
sudo vi /etc/hosts
```

Add:
```text
192.168.1.10 webserver
```

Test:
```bash
ping webserver
```

**Check DNS configuration:**
```bash
cat /etc/resolv.conf
```

---

#### **Network Tools (`ping`, `netstat`, `traceroute`, `curl`)**

**1. Test connectivity:**
```bash
ping google.com
```

**2. Check network connections and ports:**
```bash
netstat -tulnp
```

**3. Trace network path:**
```bash
traceroute google.com
```

**4. Test URL/API:**
```bash
curl http://example.com
```

---

#### **SSH (Secure Shell)**

SSH is used to securely connect to a remote Linux server.

**1. Connect to a server:**
```bash
ssh user@192.168.1.10
```

**2. Connect using a private key:**
```bash
ssh -i mykey.pem user@192.168.1.10
```

**3. Copy a file to remote server:**
```bash
scp file.txt user@192.168.1.10:/tmp/
```

---

### **Examples**

#### **1. Configure Static IP Using `nmcli` (RedHat)**

```bash
nmcli connection show
sudo nmcli connection modify "System eth0" ipv4.addresses 192.168.1.10/24
sudo nmcli connection modify "System eth0" ipv4.gateway 192.168.1.1
sudo nmcli connection modify "System eth0" ipv4.dns 8.8.8.8
sudo nmcli connection modify "System eth0" ipv4.method manual
sudo nmcli connection up "System eth0"
```

Verify:
```bash
ip addr
```

---

#### **2. Configure Static IP Using Netplan (Ubuntu)**

Edit the Netplan file:

```bash
sudo vi /etc/netplan/01-netcfg.yaml
```

Example:
```yaml
network:
  version: 2
  ethernets:
    ens33:
      addresses:
        - 192.168.1.20/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
```

Apply:
```bash
sudo netplan apply
```

Verify:
```bash
ip addr
```

---

#### **3. Connect to a Remote Server Using SSH**

```bash
ssh -i mykey.pem ubuntu@192.168.1.10
```

For password-based SSH:
```bash
ssh ubuntu@192.168.1.10
```
