# Grafana + Prometheus + Node Exporter Setup for Linux Monitoring

## Architecture

```text
Linux Server
 └── Node Exporter (:9100)

Prometheus Server
 └── Scrapes Node Exporter

Grafana
 └── Visualizes Prometheus metrics
```

---

# 1. Install Node Exporter on Linux

## Create user

```bash
sudo useradd --no-create-home --shell /bin/false node_exporter
```

---

## Download Node Exporter

Latest releases:
https://github.com/prometheus/node_exporter/releases

Example:

```bash
cd /tmp

curl -LO https://github.com/prometheus/node_exporter/releases/download/v1.9.1/node_exporter-1.9.1.linux-amd64.tar.gz

tar xvf node_exporter-1.9.1.linux-amd64.tar.gz

sudo cp node_exporter-1.9.1.linux-amd64/node_exporter /usr/local/bin/
```

---

## Set permissions

```bash
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
```

---

## Create systemd service

```bash
sudo nano /etc/systemd/system/node_exporter.service
```

Paste:

```ini
[Unit]
Description=Node Exporter
After=network.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```

---

## Start service

```bash
sudo systemctl daemon-reload
sudo systemctl enable node_exporter
sudo systemctl start node_exporter
```

---

## Verify

```bash
curl http://localhost:9100/metrics
```

---

# 2. Install Prometheus

## Create user

```bash
sudo useradd --no-create-home --shell /bin/false prometheus
```

---

## Download Prometheus

Downloads:
https://prometheus.io/download/

Example:

```bash
cd /tmp

curl -LO https://github.com/prometheus/prometheus/releases/download/v3.3.1/prometheus-3.3.1.linux-amd64.tar.gz

tar xvf prometheus-3.3.1.linux-amd64.tar.gz
```

---

## Create directories

```bash
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
```

---

## Copy binaries

```bash
sudo cp prometheus-3.3.1.linux-amd64/prometheus /usr/local/bin/
sudo cp prometheus-3.3.1.linux-amd64/promtool /usr/local/bin/
```

---

## Copy config

```bash
sudo cp prometheus-3.3.1.linux-amd64/prometheus.yml /etc/prometheus/
```

---

## Permissions

```bash
sudo chown -R prometheus:prometheus /etc/prometheus
sudo chown -R prometheus:prometheus /var/lib/prometheus
```

---

## Configure scrape target

Edit:

```bash
sudo nano /etc/prometheus/prometheus.yml
```

Example config:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'node'

    static_configs:
      - targets: ['localhost:9100']
```

---

## Create systemd service

```bash
sudo nano /etc/systemd/system/prometheus.service
```

Paste:

```ini
[Unit]
Description=Prometheus
After=network.target

[Service]
User=prometheus
Group=prometheus
Type=simple

ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/var/lib/prometheus

[Install]
WantedBy=multi-user.target
```

---

## Start Prometheus

```bash
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
```

---

## Verify

Open:

```text
http://SERVER_IP:9090
```

Test query:

```promql
up
```

Expected result:

```text
node 1
```

---

# 3. Install Grafana

## Official Docs

https://grafana.com/docs/grafana/latest/setup-grafana/installation/

---

## Ubuntu/Debian example

```bash
sudo apt-get install -y apt-transport-https software-properties-common wget

wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -

echo "deb https://packages.grafana.com/oss/deb stable main" | \
sudo tee /etc/apt/sources.list.d/grafana.list

sudo apt update
sudo apt install grafana
```

---

## Start Grafana

```bash
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
```

---

## Open Grafana

```text
http://SERVER_IP:3000
```

Default login:

```text
admin / admin
```

---

# 4. Add Prometheus Data Source

In Grafana:

```text
Connections → Data Sources → Add data source
```

Choose:
- Prometheus

URL:

```text
http://localhost:9090
```

Click:
- Save & Test

---

# 5. Import Linux Dashboard

## Node Exporter Full Dashboard

Dashboard ID:

```text
1860
```

Dashboard:
https://grafana.com/grafana/dashboards/1860-node-exporter-full/

---

## Import dashboard

In Grafana:

```text
Dashboards → Import
```

Enter:

```text
1860
```

Select your Prometheus datasource.

Done.

---

# 6. Useful PromQL Queries

## CPU usage

```promql
100 - (avg by(instance)(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
```

---

## Memory usage

```promql
(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes)
/
node_memory_MemTotal_bytes * 100
```

---

## Disk usage

```promql
100 - (
  node_filesystem_avail_bytes * 100
  / node_filesystem_size_bytes
)
```

---

## Network receive

```promql
rate(node_network_receive_bytes_total[5m])
```

---

## Load average

```promql
node_load1
```

---

# 7. Firewall Ports

| Service | Port |
|---|---|
| Node Exporter | 9100 |
| Prometheus | 9090 |
| Grafana | 3000 |

Example:

```bash
sudo ufw allow 3000
sudo ufw allow 9090
sudo ufw allow 9100
```

---

# 8. Production Recommendations

## Reverse proxy Grafana with NGINX

```bash
sudo apt install nginx
```

Use HTTPS with:
- Let's Encrypt
- Certbot

---

## Restrict Node Exporter

Recommended:
- bind to private network
- firewall allow only Prometheus

Example:

```bash
--web.listen-address="10.0.0.5:9100"
```

---

## Add alerts

Use Alertmanager for:
- High CPU
- Low disk
- Memory exhaustion
- Host down

---

# Optional Next Steps

You can extend this stack with:
- Docker Compose deployment
- Loki log aggregation
- Tempo tracing
- Kubernetes monitoring
- Slack/email alerts
- Blackbox exporter
- cAdvisor
