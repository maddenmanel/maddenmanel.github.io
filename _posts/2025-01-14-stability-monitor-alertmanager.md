---
title: "Stability Monitor Alertmanager"
mathjax: true
toc: true
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - monitor
  - alertmanager
---

## Introduction

Alertmanager is a tool designed to handle alerts sent by Prometheus, providing features such as deduplication, grouping, and routing of alerts to various receivers like email, Slack, or custom webhooks. This article demonstrates the process of installing and configuring Alertmanager, ensuring it operates as a service, and integrating it with Prometheus to handle alert notifications effectively.

### Step 1: Download Alertmanager

The first step is to download the Alertmanager binary from the official GitHub repository. You can download the latest release from [Prometheus's Alertmanager GitHub page](https://github.com/prometheus/alertmanager/releases).

For example, to download version 0.24.0, run the following command:

```bash
wget https://github.com/prometheus/alertmanager/releases/download/v0.24.0/alertmanager-0.24.0-linux-amd64.tar.gz
```

### Step 2: Extract and Move Alertmanager to a Suitable Location

Once the file is downloaded, extract it and move the binary to a directory included in the system’s `$PATH` (e.g., `/usr/local/bin/`):

```bash
tar -xvzf alertmanager-0.24.0-linux-amd64.tar.gz
mv alertmanager-0.24.0-linux-amd64/alertmanager /usr/local/bin/
```

Next, grant execute permissions to the Alertmanager binary:

```bash
chmod +x /usr/local/bin/alertmanager
```

### Step 3: Create Alertmanager Configuration File

Alertmanager requires a configuration file (`alertmanager.yml`) to define how it handles incoming alerts and how they should be routed to notification receivers.

Create the configuration directory and the file:

```bash
mkdir -p /etc/alertmanager
vim /etc/alertmanager/alertmanager.yml
```

Here is a sample configuration file to get started:

```yaml
global:
  resolve_timeout: 5m

route:
  group_by: ['alertname']
  receiver: 'email-config'

receivers:
  - name: 'email-config'
    email_configs:
      - to: 'your-email@example.com'
        from: 'alertmanager@example.com'
        smarthost: 'smtp.example.com:587'
        auth_username: 'your-username'
        auth_password: 'your-password'
        require_tls: true

group_interval: 5m
```

This configuration sets up a route that groups alerts by their `alertname` and sends notifications via email. You can customize this for other notification types (e.g., Slack, PagerDuty, etc.).

### Step 4: Create Systemd Service File

To ensure that Alertmanager starts automatically at system boot and runs as a background service, create a `systemd` service file:

```bash
vim /etc/systemd/system/alertmanager.service
```

Add the following content to the file:

```ini
[Unit]
Description=Alertmanager - A tool to handle alerts sent by Prometheus
Documentation=https://prometheus.io/docs/alerting/latest/alertmanager/
After=network.target

[Service]
ExecStart=/usr/local/bin/alertmanager --config.file=/etc/alertmanager/alertmanager.yml
Restart=on-failure
User=delian
Group=delian

[Install]
WantedBy=multi-user.target
```

### Step 5: Start and Enable Alertmanager Service

Once the service file is created, reload the `systemd` manager to register the new service and start it. Additionally, enable it to start automatically at boot time:

```bash
systemctl daemon-reload
systemctl start alertmanager
systemctl enable alertmanager
systemctl status alertmanager
```

Alertmanager will now run in the background and handle alerts according to the configuration file.

### Step 6: Configuring Alerting Rules in Prometheus

To trigger alerts, Prometheus requires alerting rules. These rules define the conditions under which an alert should be fired. Alerting rules can be defined in the `prometheus.yml` configuration file or in a separate rules file.

#### Step 6.1: Define Alerting Rules in Prometheus

Create a separate file for alerting rules (e.g., `alert.rules.yml`), or define the rules directly in `prometheus.yml`. Below is an example of an alert rules file:

Create a rules file:

```bash
vim /etc/prometheus/alert.rules.yml
```

Add the following alerting rules:

```yaml
groups:
- name: example-alerts
  rules:
  - alert: HighCPUUsage
    expr: avg(rate(cpu_usage_seconds_total{mode="user"}[5m])) by (instance) > 0.9
    for: 5m
    labels:
      severity: critical
    annotations:
      summary: "High CPU usage on {{ labels.instance }}"
      description: "CPU usage is above 90% for the last 5 minutes on {{ labels.instance }}."

  - alert: DiskSpaceLow
    expr: (node_filesystem_free_bytes / node_filesystem_size_bytes) * 100 < 10
    for: 10m
    labels:
      severity: warning
    annotations:
      summary: "Low disk space on {{ labels.instance }}"
      description: "Disk space on {{ labels.instance }} is below 10%."
```

Update the `prometheus.yml` file to include the alert rules:

```yaml
rule_files:
  - /etc/prometheus/alert.rules.yml
```

#### Step 6.2: Ensure Prometheus Sends Alerts to Alertmanager

Make sure Prometheus is configured to send alerts to Alertmanager. Modify the `alerting` section in the `prometheus.yml` file:

```yaml
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          - 'localhost:9093'
```

Reload Prometheus to apply the changes:

```bash
systemctl reload prometheus
```

### Step 7: Testing and Verifying Alerts

After setting up the alerting rules, it’s important to test that the alerts are correctly triggered and routed.

1. **Verify Alertmanager Receives Alerts**: Visit the Alertmanager web interface (`http://<alertmanager-ip>:9093`) to check if alerts are being received and processed.

2. **Test Alert Triggers**: You can artificially trigger alert conditions (e.g., simulate high CPU usage) to test the rule.

3. **Check Notification Receivers**: Ensure that the notifications are sent to the configured receivers (e.g., email, Slack).

## Conclusion

In this article, we have walked through the installation and configuration of Alertmanager, as well as the setup of alerting rules in Prometheus. By following the outlined steps, you can ensure that your monitoring system is capable of detecting and notifying you about critical infrastructure issues, helping you to maintain operational reliability.

By configuring both Prometheus and Alertmanager, you set up a robust alerting mechanism, which will notify you in a timely manner about critical problems within your infrastructure.
