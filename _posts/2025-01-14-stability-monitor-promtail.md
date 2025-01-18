---
layout: post
title:  "Install Promtail"
date:   2025-01-11 18:00:00

categories: monitoring
tags: monitoring
author: "XueGang Zhang"
image: /images/logo.png
comments: true
published: true
---
## Install Promtail

First you can down load the Promtail zip file from github,https://github.com/grafana/loki/releases.

For example you download the 3.1.2 version 

```bash
wget https://github.com/grafana/loki/releases/download/v2.8.0/promtail-linux-amd64.zip
```

Second you unzip file, and move it the suit place.

```bash
unzip promtail-linux-amd64.zip
mv promtail-linux-amd64 /usr/local/bin/promtail
```

Grant the promtail execute privilage

```bash
chmod +x /usr/local/bin/promtail
```

Create the Promtail config file

```bash
mkdir -p /etc/promtail
vim /etc/promtail/promtail-config.yaml
```

this file config is my use;

```bash
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /tmp/positions.yaml

clients:
  - url: http://192.168.18.58:3100/loki/api/v1/push
    batchsize: 1048576  # 批量大小，单位字节（1MB）
    batchwait: 2s  # 批量发送间隔

scrape_configs:
  - job_name: dl-underwriting-rule-db
    static_configs:
      - targets:
          - localhost
        labels:
          job: dl-underwriting-rule-db
          __path__: /var/log/dl-underwriting-rule-db/*log

  - job_name: dl-underwriting-rule
    static_configs:
      - targets:
          - localhost
        labels:
          job: dl-underwriting-rule
          __path__: /var/log/dl-underwriting-rule/*log

  - job_name: dl-underwriting-rule-hh
    static_configs:
      - targets:
          - localhost
        labels:
          job: dl-underwriting-rule-hh
          __path__: /var/log/dl-underwriting-rule-hh/*log
```

Create Promtail service file 

In order to Promtail can start as the system start, we need create a systemd service file.

```bash
vim /etc/systemd/system/promtail.service

[Unit]
Description=Promtail - A log collection agent for Loki
Documentation=https://grafana.com/docs/loki/latest/clients/promtail/
After=network.target

[Service]
ExecStart=/usr/local/bin/promtail -config.file=/etc/promtail/promtail-config.yaml
Restart=on-failure
User=delian
Group=delian

[Install]
WantedBy=multi-user.target
```

Start the Promtail service

```bash
systemctl daemon-reload
systemctl start promtail
systemctl enable promtail 
systemctl status promtail

```
