That’s an awesome initiative 👏 — helping students with end-to-end observability setups using **Prometheus + Node Exporter + Grafana** will be super useful!

# 🚀 End-to-End Setup Guide for Node Exporter, Prometheus & Grafana on Linux

This guide helps you **install and configure Node Exporter, Prometheus, and Grafana** on any Linux system — perfect for learning, projects, or monitoring servers.

---

## 🧱 1. Prerequisites

✅ Linux VM or EC2 instance  
✅ SSH access with `sudo` privileges  
✅ Internet access to download packages  
✅ Open required ports in firewall / security group:

| Service        | Port | Description          |
|----------------|------|----------------------|
| Node Exporter  | 9100 | System metrics       |
| Prometheus     | 9090 | Metrics dashboard    |
| Grafana        | 3000 | Visualization UI     |

---

## 📊 2. Install Node Exporter

Node Exporter collects **system-level metrics** like CPU, memory, disk, and network.

### Step 2.1: Download & Extract

sudo wget https://github.com/prometheus/node_exporter/releases/download/v1.8.1/node_exporter-1.8.1.linux-amd64.tar.gz
sudo tar -xvf node_exporter-1.8.1.linux-amd64.tar.gz
sudo mv node_exporter-1.8.1.linux-amd64 /usr/local/bin/node_exporter
sudo rm -f node_exporter-1.8.1.linux-amd64.tar.gz


### Step 2.2: Create a dedicated user
sudo useradd --no-create-home --shell /bin/false nodeusr


### Step 2.3: Create systemd service file
sudo vim /etc/systemd/system/node_exporter.service

**Add the following:**
[Unit]
Description=Prometheus Node Exporter
After=network.target

[Service]
User=nodeusr
ExecStart=/usr/local/bin/node_exporter/node_exporter
Restart=always

[Install]
WantedBy=multi-user.target


### Step 2.4: Start & Enable
sudo systemctl daemon-reload
sudo systemctl enable node_exporter
sudo systemctl start node_exporter
sudo systemctl status node_exporter

### Step 2.5: Verify
curl http://<server-ip>:9100/metrics

Or open in browser:
👉 `http://<server-ip>:9100/metrics`

## 📈 3. Install Prometheus

Prometheus scrapes and stores metrics from Node Exporter.

### Step 3.1: Download & Extract


sudo wget https://github.com/prometheus/prometheus/releases/download/v2.55.1/prometheus-2.55.1.linux-amd64.tar.gz
sudo tar -xvf prometheus-2.55.1.linux-amd64.tar.gz
sudo mv prometheus-2.55.1.linux-amd64 /usr/local/bin/prometheus
sudo rm -f prometheus-2.55.1.linux-amd64.tar.gz


### Step 3.2: Create user & directories
sudo useradd --no-create-home --shell /bin/false promuser
sudo mkdir /etc/prometheus /var/lib/prometheus
sudo chown promuser:promuser /etc/prometheus /var/lib/prometheus

### Step 3.3: Create Prometheus config
sudo vim /etc/prometheus/prometheus.yml

**Add the following:**

```yaml
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

### Step 3.4: Create systemd service
sudo vim /etc/systemd/system/prometheus.service


**Add:**
[Unit]
Description=Prometheus Monitoring
After=network.target

[Service]
User=promuser
ExecStart=/usr/local/bin/prometheus/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/var/lib/prometheus \
  --web.console.templates=/usr/local/bin/prometheus/consoles \
  --web.console.libraries=/usr/local/bin/prometheus/console_libraries
Restart=always

[Install]
WantedBy=multi-user.target

### Step 3.5: Start & Enable
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
sudo systemctl status prometheus

### Step 3.6: Verify
👉 `http://<server-ip>:9090`



## 📊 4. Install Grafana
Grafana visualizes metrics from Prometheus.

### Step 4.1: Install Grafana Enterprise 12.2.0
sudo yum install -y https://dl.grafana.com/grafana-enterprise/release/12.2.0/grafana-enterprise_12.2.0_17949786146_linux_amd64.rpm

### Step 4.2: Enable & Start
sudo systemctl daemon-reload
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
sudo systemctl status grafana-server

### Step 4.3: Verify

👉 `http://<server-ip>:3000`

**Default credentials:**

```
Username: admin
Password: admin
```

(Change password on first login)

## ⚙️ 5. Configure Grafana to Use Prometheus
1. Open Grafana → **Connections → Data sources → Add data source**
2. Select **Prometheus**
3. Set URL → `http://localhost:9090`
4. Click **Save & Test**
   ✅ Should show “Data source is working”

## 🧩 Summary

| Component     | Port | Purpose                  |
| ------------- | ---- | ------------------------ |
| Node Exporter | 9100 | Collects host metrics    |
| Prometheus    | 9090 | Stores & scrapes metrics |
| Grafana       | 3000 | Visualization dashboards |

---

## 📸 Screenshots (Add yours!)


## 📹 YouTube Vlog Idea

🎥 **Video Title:** “Setup Node Exporter, Prometheus & Grafana from Scratch on Linux | Full Observability Stack Tutorial”

💡 Include:

* Step-by-step terminal commands
* Explanation of each tool
* End dashboard demo

---

### 👨‍💻 Author

**Srikrishna H S**
Platform Engineer & Certified Kubernetes Administrator
🔗 [LinkedIn](http://linkedin.com/in/srikrishna-hs-20a4021b4)
📧 [hssrikrishna39@gmail.com](mailto:hssrikrishna39@gmail.com)

