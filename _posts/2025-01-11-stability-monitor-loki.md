---
title: "Stability Monitor Loki"
mathjax: true
toc: true
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - monitor
  - stability
---

## Install Loki

To install **Loki** version **3.1.2** on a Linux system, follow the steps below. Loki is an open-source log aggregation system developed by Grafana Labs, and you can install it using either pre-built binaries, Docker, or through package managers. We'll use pre-built binaries for this guide.

### Step 1: Download the Loki Binary

Go to the [Loki GitHub releases page](https://github.com/grafana/loki/releases) and find version **3.1.2**. Or use `wget` to directly download the binary:

```bash
wget https://github.com/grafana/loki/releases/download/v3.1.2/loki-linux-amd64.zip
```

Once downloaded, extract the `.zip` file:

```bash
unzip loki-linux-amd64.zip
```

This will create a folder containing the Loki binary (`loki-linux-amd64`).

### Step 2: Move Loki to a System Directory

Move the extracted binary to a directory in your PATH (e.g., `/usr/local/bin`):

```bash
mv loki-linux-amd64 /usr/local/bin/loki
```

Ensure the binary is executable:

```bash
chmod +x /usr/local/bin/loki
```

### Step 3: Create a Configuration File (Optional)

Create a directory for Loki configuration and add the `local-config.yaml` file:

```bash
mkdir -p /etc/loki
vim /etc/loki/local-config.yaml
```

The content for `local-config.yaml`:

```yaml
auth_enabled: false

server:
  http_listen_port: 3100
  grpc_listen_port: 9095

common:
  path_prefix: /loki/data  

storage_config:
  boltdb_shipper:
    active_index_directory: /loki/index
    cache_location: /loki/cache
    resync_interval: 10m
  filesystem:
    directory: /loki/chunks

limits_config:
  retention_period: 90d
  allow_structured_metadata: false 

schema_config:
  configs:
    - from: 2020-10-21
      store: boltdb-shipper
      object_store: filesystem
      schema: v13
      index:
        prefix: index_
        period: 24h  

compactor:
  working_directory: /loki/compactor 

table_manager:
  retention_deletes_enabled: true
  retention_period: 90d

ingester:
  lifecycler:
    ring:
      kvstore:
        store: memberlist
      replication_factor: 1
  chunk_target_size: 1048576  
  max_chunk_age: 1h 

memberlist:
  join_members: [] 
```

### Step 4: Configure Log File Storage

Define storage directories for Loki logs:

```bash
mkdir -p /var/loki/chunks /var/loki/index /var/loki/cache /var/loki/compactor
chown -R loki:loki /var/loki
```

Create the Loki systemd service file:

```bash
vim /etc/systemd/system/loki.service
```

Add the following content:

```ini
[Unit]
Description=Loki
Documentation=https://grafana.com/docs/loki/latest/
After=network.target

[Service]
ExecStart=/usr/local/bin/loki -config.file=/etc/loki/local-config.yaml
Restart=on-failure
User=delian
Group=delian

[Install]
WantedBy=multi-user.target
```

### Step 5: Start Loki Service

Reload systemd, enable and start the Loki service:

```bash
systemctl daemon-reload
systemctl enable loki
systemctl start loki
systemctl status loki
```

To monitor logs:

```bash
journalctl -u loki -f
```

Verify the Loki version:

```bash
loki --version
```

## Conclusion

By following the above steps, you should have Loki installed and running with the necessary configurations for log aggregation. You can now proceed to integrate Loki with other components like Promtail, Grafana, etc., for a complete logging solution.
