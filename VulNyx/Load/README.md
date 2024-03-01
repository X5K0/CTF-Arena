
<h1 align="center"> Load </h1> <br>

[![code with hearth by X5K0](https://img.shields.io/badge/Pwned%20with%20%E2%99%A5%20by-X5K0-ff1414.svg)](https://github.com/X5K0)
<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Area/blob/main/VulNyx/Load/Pictures/LoadMachine.png" width="550">
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
[![Wfuzz](https://img.shields.io/badge/wfuzz-blue?style=flat)]()

To start this machine, we will perform an initial scan with nmap to identify the open ports on the target system.
To do this, we will execute the following command:

```bash
sudo nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn "IP"
```

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Area/blob/main/VulNyx/Load/Pictures/targetedLoad.png" width="900">
  </a>
</p>

As we can see, port 80 hosts a robots.txt file which reveals a potential path. Let's navigate to that path to see what we find.

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Area/blob/main/VulNyx/Load/Pictures/RitedevPath.png" width="900">
  </a>
</p>

In this part, we will conduct reconnaissance to identify potential directories within the website. To do so, we will use the tool wfuzz:

```bash
wfuzz -c -w  /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt --hc=404 http://"IP"/ritedev/FUZZ
```
The output shows the following:

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Area/blob/main/VulNyx/Load/Pictures/Wfuzz_ritedev.png" width="900">
  </a>
</p>

Many of those directories are Rabbit Holes. To find any interesting resources, we will search in the root of the CMS to see if there are any PHP files.

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Area/blob/main/VulNyx/Load/Pictures/wfuzz_ritedev_PHP.png" width="900">
  </a>
</p>

As we can see, we have a login page.

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Area/blob/main/VulNyx/Load/Pictures/ritedevLogin.png" width="500">
  </a>
</p>

At this point, we will begin the exploitation phase.


## 👨‍💻 Exploitation

[![Nmap](https://img.shields.io/badge/PHP-brown?style=flat)]()

We will try common usernames and passwords, such as admin - admin, guest - guest... Upon entering 'admin - admin', we may gain access to the CMS administration panel.

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Area/blob/main/VulNyx/Load/Pictures/AdminPanelRitedev.png">
  </a>
</p>

If we navigate to 'Files Manager', we will see a potential way to upload files to the system.
We will attempt to upload a PHP reverse shell, which we can obtain from the [Pentestmonkey repository](https://github.com/pentestmonkey/php-reverse-shell).

Once we have it on the system, we can navigate into the file and change the configuration parameters to set our IP address and the port on which we will be listening, which in our case will be port 443.

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Area/blob/main/VulNyx/Load/Pictures/Pentestmonkey_PHP.png">
  </a>
</p>

Once we have made the changes, we will attempt to upload it to the CMS.

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Area/blob/main/VulNyx/Load/Pictures/exploitUpload.png">
  </a>
</p>

As we see in the top area, it will be located in the 'media' directory, which we found during the reconnaissance phase with nmap.

This will allow us to generate a reverse shell on our machine and gain access to the system.

We will navigate to the 'media' directory and enter the name of our PHP file. Additionally, we will open Netcat to listen on the selected port, as follows:

```bash
nc -nvlp 443
```
Upon hitting Enter on the website, it will send a reverse shell to our machine.

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Area/blob/main/VulNyx/Load/Pictures/ReverseShell.png" width="900">
  </a>
</p>

Once done, we will need to handle the tty. For this, we will need to execute the command:

```bash
script /dev/null -c bash
```
We will press Ctrl + Z, and in our terminal, we will execute:

```bash
stty raw -echo; fg
```

We will execute a reset and export 'xterm' as the terminal and 'bash' as the shell.

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Area/blob/main/VulNyx/Load/Pictures/Exports.png">
  </a>
</p>

We will use sudo -l to see which services we can work with, and as we can see, we can run 'crash' as the 'travis' user.

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Area/blob/main/VulNyx/Load/Pictures/crash.png">
  </a>
</p>

> [!TIP]
> #### What is crash?
> 
> The crash command is an interactive utility for examining an operating system image, or the running kernel.




## 🔥 Privilege Escalation
[![Bash](https://img.shields.io/badge/Bash-3C873A?style=flat)]()

We will navigate to the GTFOBins page and search for 'crash'. As we can see, we can execute the 'crash -h' command with sudo and then use '!sh' to return a shell as the 'travis' user.


<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Area/blob/main/VulNyx/Load/Pictures/GTFOBCrash.png">
  </a>
</p>

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Area/blob/main/VulNyx/Load/Pictures/TravisPivoting.png">
  </a>
</p>

As 'travis', we will be able to view the **flag** in their personal directory. 🏳️

Once done, we will search for which services we can execute using the sudo -l command again.

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Area/blob/main/VulNyx/Load/Pictures/SudoTravis.png">
  </a>
</p>

> [!TIP]
> #### What is xauth?
> 
> Xauthority is generated by the xauth program. It is an authentication system for graphical applications.

To abuse this binary, we can use the 'source' parameter followed by a file we want to read.

We will attempt to view the 'id_rsa' file of root using the command:

```bash
sudo -u root /usr/bin/xauth source /root/.ssh/id_rsa
```
When executed, we will encounter the following:


<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Area/blob/main/VulNyx/Load/Pictures/id_rsa_xauth.png" width="800">
  </a>
</p>

We will manipulate it to obtain the complete 'id_rsa', achieving:


<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Area/blob/main/VulNyx/Load/Pictures/id_rsa_completed.png" width="600">
  </a>
</p>

Once this is done, we will grant permissions to the 'id_rsa' file using:

```bash
sudo chmod 6000 id_rsa
```

Lets access via SSH with the root user and adding its 'id_rsa'.

<p align="center">
  <a href="https://vulnyx.com/">
    <img alt="GitPoint" title="GitPoint" src="https://github.com/X5K0/CTF-Area/blob/main/VulNyx/Load/Pictures/root_connected.png">
  </a>
</p>

Here we will already be inside the machine as the root user.

Try to find the 'hidden' flag! :wink: 🏳️
