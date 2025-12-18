# Install Grafana 
## 1. update ubuntu and install Grafana
```
sudo apt update
sudo apt install -y grafana
sudo systemctl start grafana-server
```

## 2. Grafana runs on port 3000

### Open:
```
http://localhost:3000
```
### Login:

Username: admin
Password: admin

### 3. Connect Grafana to Prometheus

- Go to Settings → Data Sources
- Click Add data source
- Choose Prometheus

URL: http://localhost:9090

Click Save & Test

Step 5: Create a Simple Dashboard
Example: CPU Usage

Query in Grafana panel:

100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

👉 Shows CPU usage percentage

Example: Memory Usage
(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100

👉 Shows memory usage percentage
