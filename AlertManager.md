# Alertmanager Setup (Systemd Service)

## This section shows how to install Alertmanager, configure it, and run it as a systemd service (production-ready).

### Part 1: Create Alertmanager User
```
sudo useradd --no-create-home --shell /bin/false alertmanager
```
### Part 2: Download & Install Alertmanager
```
wget https://github.com/prometheus/alertmanager/releases/latest/download/alertmanager-0.27.0.linux-amd64.tar.gz
tar xvf alertmanager-*.tar.gz
sudo mv alertmanager-*/alertmanager /usr/local/bin/
sudo mv alertmanager-*/amtool /usr/local/bin/
```
### Part 3: Create Directories
sudo mkdir /etc/alertmanager
sudo mkdir /var/lib/alertmanager
### Part 4: Alertmanager Configuration File
```
vi /etc/alertmanager/alertmanager.yml
```
```
global:
  resolve_timeout: 5m

route:
  receiver: 'default-receiver'

receivers:
- name: 'default-receiver'
  email_configs:
  - to: 'admin@example.com'
    from: 'alertmanager@example.com'
    smarthost: 'smtp.example.com:587'
    auth_username: 'alertmanager@example.com'
    auth_password: 'password'
```
### Part 5: Permissions
```
sudo chown -R alertmanager:alertmanager /etc/alertmanager
sudo chown -R alertmanager:alertmanager /var/lib/alertmanager
sudo chown alertmanager:alertmanager /usr/local/bin/alertmanager
sudo chown alertmanager:alertmanager /usr/local/bin/amtool
```
### Part 6: Systemd Service File
```
vi /etc/systemd/system/alertmanager.service
```
```
[Unit]
Description=Alertmanager
Wants=network-online.target
After=network-online.target

[Service]
User=alertmanager
Group=alertmanager
Type=simple
ExecStart=/usr/local/bin/alertmanager \
  --config.file=/etc/alertmanager/alertmanager.yml \
  --storage.path=/var/lib/alertmanager
Restart=always

[Install]
WantedBy=multi-user.target
```
### Part 7: Start & Enable Alertmanager
```
sudo systemctl daemon-reload
sudo systemctl start alertmanager
sudo systemctl enable alertmanager
```
### Part 8: Verify Alertmanager
```
systemctl status alertmanager
curl http://localhost:9093
```
Alertmanager UI runs on port 9093

### Part 9: Connect Prometheus to Alertmanager
Edit Prometheus service or config:
```
vi /etc/prometheus/prometheus.yml
```
```
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      - localhost:9093
```
Reload Prometheus:
```
sudo systemctl restart prometheus
```
### Part 10: Example Alert Rule
```
vi /etc/prometheus/alert.rules.yml
```
```
groups:
- name: cpu-alerts
  rules:
  - alert: HighCPUUsage
    expr: 100 - avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100 > 80
    for: 2m
    labels:
      severity: warning
    annotations:
      summary: "High CPU usage detected"
      description: "CPU usage is above 80% for more than 2 minutes"
```
### Part 10: Add rule file to Prometheus:
```
vi /etc/Prometheus/Prometheus.yml
```
```
rule_files:
  - "/etc/prometheus/*.yml"
```

Restart Prometheus:
