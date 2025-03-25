# Setting Up an FRP Server on an EC2 Instance

## Introduction
FRP (Fast Reverse Proxy) allows you to expose local servers behind a NAT or firewall to the internet. This guide walks you through setting up an FRP server on an AWS EC2 instance.

---

## 1. Installing FRP on Your EC2 Instance
First, download the latest FRP release from GitHub:
```sh
wget https://github.com/fatedier/frp/releases/download/v0.49.0/frp_0.49.0_linux_amd64.tar.gz
```
![image alt](https://github.com/user-attachments/assets/5b76cc0b-e6b8-4b8e-9824-1a51b27f5e95)

### 2. Extract the Downloaded FRP Archive
```sh
tar -xvzf frp_0.49.0_linux_amd64.tar.gz
cd frp_0.49.0_linux_amd64
```
![Screenshot (5)](https://github.com/user-attachments/assets/bd17ff30-f5be-4417-8d74-47ee63e245b3)

### 3. Move FRP Binaries to a System Path
```sh
sudo mv frps /usr/local/bin/
sudo mv frpc /usr/local/bin/
```
This ensures FRP can be accessed globally on your system.
![Screenshot (6)](https://github.com/user-attachments/assets/243801c9-47b0-4f07-b292-2105e5e3867c)

## 4. Configuring FRP

### **Server Configuration (frps)**
Create a directory for FRP configuration:
```sh
sudo mkdir -p /etc/frp
```
![Screenshot (6)](https://github.com/user-attachments/assets/243801c9-47b0-4f07-b292-2105e5e3867c)

Create and open the server configuration file:
```sh
sudo nano /etc/frp/frps.ini
```
Add the following configuration:
```ini
[common]
bind_port = 7000
vhost_http_port = 8080
vhost_https_port = 443
```
Save and exit the file.

---

### **Client Configuration (frpc)**
Create and open the client configuration file:
```sh
nano frpc.ini
```
Add the following configuration:
```ini
[common]
server_addr = your-ec2-public-ip
server_port = 7000

[web]
type = tcp
local_ip = 127.0.0.1
local_port = 80
remote_port = 8080
```
Replace `your-ec2-public-ip` with your actual EC2 instance IP.

---
![Screenshot (7)](https://github.com/user-attachments/assets/d07bbb3f-6b5e-4318-987e-5f607b6fcad0)

## 5. Running the FRP Server
To start the FRP server in the background, run:
```sh
sudo frps -c /etc/frp/frps.ini &
```
![image alt](https://drive.google.com/file/d/1e496otzw_Jbat62XleSjQkxf8VeenSKi/view?usp=sharing)
To ensure FRP starts automatically on reboot, create a systemd service:
```sh
sudo nano /etc/systemd/system/frps.service
```

Add the following:
```ini
[Unit]
Description=FRP Server
After=network.target

[Service]
ExecStart=/usr/local/bin/frps -c /etc/frp/frps.ini
Restart=always

[Install]
WantedBy=multi-user.target
```

Save and exit, then enable the service:
```sh
sudo systemctl enable frps
sudo systemctl start frps
```

Your FRP server is now up and running!

