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
 
  `apt install python3-dnslib python3-bpfcc bpfcc-tools libbpfcc linux-headers-$(uname -r)`
  
 ##  Usage ⚙
  This tool captures outbound DNS requests and responces on a eBPF level.

Run `python3 ebpf_dns_main.py` to get started. Note: based on your Linux kernel version, it may require sudo privileges.

```
$ root@host:~/# python3 ebpf_dns_main.py
```

Sample Output:

```
The program is running. Press Ctrl-C to abort.
2023-08-27T17:43:21Z COMM=systemd-resolve PID=645 TGID=645 DEV=ens33 PROTO=UDP SRC=192.168.40.130 DST=192.168.40.2 SPT=47892 DPT=53 UID=101 GID=103 DNS_QR=0 DNS_NAME=visitukraine.today. DNS_TYPE=A
2023-08-27T17:43:21Z COMM=systemd-resolve PID=645 TGID=645 DEV=ens33 PROTO=UDP SRC=192.168.40.2 DST=192.168.40.130 SPT=53 DPT=47892 UID=101 GID=103 DNS_QR=1 DNS_NAME=visitukraine.today. DNS_TYPE=A DNS_DATA=104.21.71.200
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
