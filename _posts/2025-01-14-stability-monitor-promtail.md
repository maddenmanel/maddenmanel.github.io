---
title: "Stability Monitor Promtail"
mathjax: true
toc: true
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - monitor
  - stability
---

## Installation of Promtail

### Introduction

Promtail is an agent that collects logs from various sources and sends them to Loki for storage and querying. In this section, we will walk through the installation process of Promtail, which includes downloading the necessary files, setting up configuration, and creating a service to ensure Promtail runs continuously as a background service.

### Step 1: Download Promtail

The first step is to download the Promtail binary from the official GitHub repository. You can find the latest release [here](https://github.com/grafana/loki/releases).

For example, to download version 3.1.2, you can execute the following command:

```bash
wget https://github.com/grafana/loki/releases/download/v2.8.0/promtail-linux-amd64.zip
```

### Step 2: Extract and Move Promtail to a Suitable Location

Once the file is downloaded, extract it and move the binary to a directory included in the system’s `$PATH`, such as `/usr/local/bin/`.

```bash
unzip promtail-linux-amd64.zip
mv promtail-linux-amd64 /usr/local/bin/promtail
```

Next, grant execute permissions to the Promtail binary to allow it to run:

```bash
chmod +x /usr/local/bin/promtail
```

### Step 3: Create Promtail Configuration File

Promtail requires a configuration file that defines how logs will be collected, where they will be sent, and other parameters. Create the configuration directory and the file as follows:

```bash
mkdir -p /etc/promtail
vim /etc/promtail/promtail-config.yaml
```

Below is a sample configuration file you can use, which specifies the Promtail server settings, position tracking, Loki client configuration, and the log scraping setup.

```yaml
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /tmp/positions.yaml

clients:
  - url: http://192.168.18.58:3100/loki/api/v1/push
    batchsize: 1048576
    batchwait: 2s

scrape_configs:
  - job_name: your-application-name
    static_configs:
      - targets:
          - localhost
        labels:
          job: your-application-name
          __path__: /var/log/your-application-name/*log
```

### Step 4: Create Systemd Service File

In order to ensure that Promtail starts automatically during system boot and runs as a background service, we need to create a systemd service file.

```bash
vim /etc/systemd/system/promtail.service
```

Add the following content to the file:

```ini
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

### Step 5: Start and Enable Promtail Service

After creating the systemd service file, reload the systemd manager to register the new service and start it. Additionally, enable it to start automatically at boot time.

```bash
systemctl daemon-reload
systemctl start promtail
systemctl enable promtail
systemctl status promtail
```

The service will now run in the background, collecting and forwarding logs to Loki according to the specified configuration.

---

### Conclusion

In this article, we have outlined the necessary steps to install and configure Promtail. By following these procedures, you will have a fully functional Promtail agent collecting logs and sending them to Loki, ready for real-time log monitoring.