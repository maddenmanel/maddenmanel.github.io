---
title: "Stability Monitor Jaeger"
mathjax: true
toc: true
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - monitor
  - tracing
---

## Install Jaeger

To install **Jaeger** version **1.42.0** on a Linux system, follow the steps below. Jaeger is an open-source distributed tracing system, and you can install it using pre-built binaries, Docker, or through package managers. This guide will cover the installation process using pre-built binaries.

### Step 1: Download the Jaeger Binary

Go to the [Jaeger GitHub releases page](https://github.com/jaegertracing/jaeger/releases) and find version **1.42.0**. Or use `wget` to directly download the binary:

```bash
wget https://github.com/jaegertracing/jaeger/releases/download/v1.42.0/jaeger-linux-amd64.tar.gz
```

Once downloaded, extract the `.tar.gz` file:

```bash
tar -xvzf jaeger-linux-amd64.tar.gz
```

This will create a folder containing the Jaeger binaries (`jaeger-agent`, `jaeger-collector`, `jaeger-query`, etc.).

### Step 2: Move Jaeger to a System Directory

Move the extracted binaries to a directory in your PATH (e.g., `/usr/local/bin`):

```bash
mv jaeger-linux-amd64 /usr/local/bin/jaeger
```

Ensure the binaries are executable:

```bash
chmod +x /usr/local/bin/jaeger
```

### Step 3: Create a Configuration File (Optional)

Create a directory for Jaeger configuration and add the `jaeger-config.yaml` file:

```bash
mkdir -p /etc/jaeger
vim /etc/jaeger/jaeger-config.yaml
```

The content for `jaeger-config.yaml`:

```yaml
collector:
  http-port: 5775
  grpc-port: 14250

agent:
  http-port: 5778

query:
  http-port: 16686

storage:
  type: cassandra
  cassandra:
    contact-points:
      - "localhost:9042"
    keyspace: jaeger
    timeout: 30s
```

### Step 4: Configure Data Directories

Define storage directories for Jaeger:

```bash
mkdir -p /var/jaeger/cassandra /var/jaeger/storage
chown -R jaeger:jaeger /var/jaeger
```

Create the Jaeger systemd service file:

```bash
vim /etc/systemd/system/jaeger.service
```

Add the following content:

```ini
[Unit]
Description=Jaeger
Documentation=https://www.jaegertracing.io/docs/latest/
After=network.target

[Service]
ExecStart=/usr/local/bin/jaeger --config.file=/etc/jaeger/jaeger-config.yaml
Restart=on-failure
User=delian
Group=delian

[Install]
WantedBy=multi-user.target
```

### Step 5: Start Jaeger Service

Reload systemd, enable and start the Jaeger service:

```bash
systemctl daemon-reload
systemctl enable jaeger
systemctl start jaeger
systemctl status jaeger
```

To monitor logs:

```bash
journalctl -u jaeger -f
```

Verify the Jaeger version:

```bash
jaeger --version
```

## Conclusion

By following the above steps, you should have Jaeger installed and running with the necessary configurations for distributed tracing. You can now proceed to integrate Jaeger with other components like Prometheus, Grafana, etc., for a complete monitoring and tracing solution.
