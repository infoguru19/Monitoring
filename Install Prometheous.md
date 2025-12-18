# Install Prometheus as a Service
## 1️⃣ Create User & Directories
```
sudo useradd --no-create-home --shell /bin/false prometheus
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
```
## 2️⃣ Download & Move Files
```
wget https://github.com/prometheus/prometheus/releases/latest/download/prometheus-2.49.0.linux-amd64.tar.gz
tar xvf prometheus-*.tar.gz
```
```
sudo mv prometheus-*/prometheus /usr/local/bin/
sudo mv prometheus-*/promtool /usr/local/bin/
sudo mv prometheus-*/consoles /etc/prometheus/
sudo mv prometheus-*/console_libraries /etc/prometheus/
```
## 3️⃣ Prometheus Config File
```
vi /etc/prometheus/prometheus.yml
```
```
global:
  scrape_interval: 15s


scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']


  - job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100']
```
## 4️⃣ Permissions
```
sudo chown -R prometheus:prometheus /etc/prometheus
sudo chown -R prometheus:prometheus /var/lib/prometheus
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool
```
## 5️⃣ Systemd Service File
```
vi /etc/systemd/system/prometheus.service
```
```
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/var/lib/prometheus \
  --storage.tsdb.retention.time=30d

Restart=always

[Install]
WantedBy=multi-user.target
```
## 6️⃣ Start & Enable
```
sudo systemctl daemon-reload
sudo systemctl start prometheus
sudo systemctl enable prometheus
```
7️⃣ Verify
systemctl status prometheus
curl localhost:9090
