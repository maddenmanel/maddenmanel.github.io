---
title: "Stability Monitor Loki"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - monitor
  - stability
---


## Install Loki

To install **Loki** version **3.1.2** on a Linux system, follow the steps below. Loki is an open-source log aggregation system developed by Grafana Labs, and you can install it using either pre-built binaries, Docker, or through package managers. We'll use pre-built binaries for this guide.

## Step 1: Download the Loki Binary

Go to the [Loki GitHub releases page](https://github.com/grafana/loki/releases) and find version **3.1.2**. Or use `wget` to directly download the binary:

```bash
 wget https://github.com/grafana/loki/releases/download/v3.1.2/loki-linux-amd64.zip
```

Once downloaded, extract the `.zip` file:

```bash
 unzip loki-linux-amd64.zip
```

This will create a folder containing the Loki binary (`loki-linux-amd64`).

## Step 2: Move Loki to a System Directory

Move the extracted binary to a directory in your PATH (e.g., `/usr/local/bin`):

```bash
mv loki-linux-amd64 /usr/local/bin/loki
```

Ensure the binary is executable:

```bash
chmod +x /usr/local/bin/loki
```

## Step 3: Create a Configuration File (Optional)

```bash
mkdir -p /etc/loki
vim /etc/loki/local-config.yaml
```

local-config.yaml content

```bash
auth_enabled: false

server:
  http_listen_port: 3100
  grpc_listen_port: 9095

common:
  path_prefix: /loki/data  # 设置 Loki 数据的路径前缀，确保所有的路径都在这个目录下

storage_config:
  boltdb_shipper:
    active_index_directory: /loki/index
    cache_location: /loki/cache
    resync_interval: 10m
  filesystem:
    directory: /loki/chunks

limits_config:
  retention_period: 90d  # 日志保留时间
  allow_structured_metadata: false  # 禁用结构化元数据

schema_config:
  configs:
    - from: 2020-10-21
      store: boltdb-shipper
      object_store: filesystem
      schema: v13
      index:
        prefix: index_
        period: 24h  # 设置索引周期为 24 小时

compactor:
  working_directory: /loki/compactor  # compactor 存储路径

table_manager:
  retention_deletes_enabled: true
  retention_period: 90d

ingester:
  lifecycler:
    ring:
      kvstore:
        store: memberlist
      replication_factor: 1
  chunk_target_size: 1048576  # 每个 chunk 的目标大小
  max_chunk_age: 1h  # chunk 最大生命周期

memberlist:
  join_members: []  # 单节点配置为空列表，不需要加入集群
```

Config the category you want loki logfile save

```bash
mkdir -p /var/loki/chunks /var/loki/index /var/loki/cache /var/loki/compactor
chown -R loki:loki /var/loki
vim /etc/systemd/system/loki.service
```

```bash
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

```bash
mkdir -p /loki/index
mkdir -p /loki/cache
mkdir -p /loki/chunks
mkdir -p /loki/compactor
chown -R loki:loki/loki/
chown -R delian:delian /loki/
chown delian:delian /usr/local/bin/loki
vim /etc/systemd/system/loki.service
systemctl daemon-reload
systemctl enable loki
systemctl start loki
systemctl status loki
journalctl -u loki -f
loki --version
journalctl -u loki -f
```
