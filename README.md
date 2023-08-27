<h1 align="center">
  <p>Monitor Linux DNS traffic using eBPF</p>
</h1>
<p align="center"><b> 🔎 Linux host monitoring and threat detection with eBPF</b></p>

<p align="center"><b>Lightweight</b> and high-performance <b>monitoring and security tool</b> that utilizes eBPF and Python to provide real-time <b>DNS monitoring</b>. By executing in kernel space, eBPF tool avoids costly context switches and offers efficient <b>detection</b> and <b>prevention</b> of malicious behavior on your network through monitoring of outbound connections</p>
<div align='center'>
  
</a>

</div>

  ---

## Table of Contents

- [Introduction](#introduction-)
- [Dependencies](#dependencies-)
- [Usage](#usage-)
- [Running script as a systemd service](#running-script-as-a-systemd-service)
- [Logging](#logging-)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

##  Introduction 🌼

Powerful monitoring tool focused on <b>DNS monitoring</b>, utilizes the power of eBPF and python to provide real-time monitoring and actionable insights for identifying and mitigating potential threats.
  
The tool is an alternative monitoring solution for DNS traffic.
Based on open-source eBPF tool for DNS monitoring by [Nurkholish Halim](https://medium.com/@nurkholish.halim/a-deep-dive-into-ebpf-writing-an-efficient-dns-monitoring-2c9dea92abdf).

The source gist may be found [here](https://gist.github.com/oghie/b4e3accf1f87afcb939f884723e2b462).
  
 ##  Dependencies 🧵
 ###### Installation
 
  `apt install python3-bpfcc bpfcc-tools libbpfcc linux-headers-$(uname -r)`
  
 ##  Usage ⚙
  This tool captures outbound DNS requests and responces on a eBPF level.

Run `python3 eBPF_dns_main.py` to get started. Note: based on your Linux kernel version, it may require sudo privileges.

```
$ root@host:~/# python3 eBPF_dns_main.py
```

Sample Output:

```
The program is running. Press Ctrl-C to abort.
COMM=dig PID=140623 TGID=140624 DEV=ens3 PROTO=TCP SRC=10.XX.20.37 DST=1.1.1.1 SPT=60687 DPT=53 UID=0 GID=0 DNS_QR=0 DNS_NAME=google.com. DNS_TYPE=A
COMM=dig PID=140623 TGID=140624 DEV=ens3 PROTO=TCP SRC=1.1.1.1 DST=10.XX.20.37 SPT=53 DPT=60687 UID=0 GID=0 DNS_QR=1 DNS_NAME=google.com. DNS_TYPE=A DNS_DATA=172.217.160.206
```
 ## Running script as a systemd service 
 To set up script automatically run as a service, copy main script to the `/etc/ebpf-dns-monitor`, and the ebpf-dns-monitor.service to the `/etc/systemd/system/`

Create a new folder under `/etc`:
 ```
 sudo mkdir /etc/ebpf-dns-monitor.
 ```
Copy the main .py script to the folder.
 ```
 cp ebpf-dns-main.py /etc/ebpf-dns-monitor/
 ```
Copy .service file to the `/etc/systemd/system/`.
 ```
 cp ebpf-dns-monitor.service /etc/systemd/system/
 ```
Reload the daemon.
 ```
 sudo systemctl daemon-reload
 ```
Now we can enable the new service and start it.
 ```
 sudo systemctl enable ebpf-dns-monitor.service
 sudo systemctl start ebpf-dns-monitor.service
 sudo systemctl status ebpf-dns-monitor.service
 ```


 ##  Logging 📈

The app will log all the request and responses to `/var/log/ebpf-dns-monitor.log` which can be easily monitored with Log management solutions(SIEM). 
The datetime format is RFC 3339.
