# 🌟 Ultimate Monitoring Guide with Prometheus + Grafana

In the ever-evolving world of DevOps, monitoring your infrastructure, applications, and services is crucial for ensuring uptime and performance. Among the most popular and powerful monitoring tools are **Prometheus** and **Grafana**. These two tools work seamlessly together to provide a complete solution for collecting, storing, and visualizing metrics.

Whether you're new to monitoring or looking to enhance your current setup, this guide will walk you through the steps to implement **Prometheus** and **Grafana**, ensuring your systems run smoothly.

## 📖 Table of Contents

1. [🔍 Why Prometheus + Grafana?](#-why-prometheus--grafana)
2. [⚙️ Prerequisites](#️-prerequisites)
3. [🚀 Step 1: Installing Prometheus](#-step-1-installing-prometheus)
4. [📊 Step 2: Installing Node Exporter](#-step-2-installing-node-exporter)
5. [🖥️ Step 3: Installing Grafana](#️-step-3-installing-grafana)
6. [🔗 Step 4: Connecting Prometheus to Grafana](#-step-4-connecting-prometheus-to-grafana)
7. [📈 Step 5: Creating Dashboards](#-step-5-creating-dashboards)
8. [🚨 Step 6: Setting Up Alerts](#-step-6-setting-up-alerts)
9. [🏁 Conclusion](#-conclusion)

## 🔍 Why Prometheus + Grafana?

**Prometheus** is a powerful time-series database designed for monitoring and alerting. It collects metrics from targets by scraping defined endpoints. **Grafana** complements Prometheus by providing a rich platform for data visualization and analysis.

### Key Features:

-   **Prometheus**:

    -   📊 Multi-dimensional data model
    -   🛠️ Flexible query language (PromQL)
    -   📬 Integrated alerting

-   **Grafana**:
    -   🎨 Interactive and customizable dashboards
    -   🔌 Support for multiple data sources
    -   🚨 Built-in alerting and annotations

Together, they form a comprehensive monitoring stack for modern infrastructure.

## ⚙️ Prerequisites

Before starting, make sure you have:

1. A Linux-based server (Ubuntu/Debian/CentOS recommended)
2. Docker installed (for a containerized setup) or root access for direct installation
3. Basic knowledge of Linux commands

## 🚀 Step 1: Installing Prometheus

### Using Docker

```bash
docker run -d --name=prometheus \
  -p 9090:9090 \
  -v /path/to/prometheus.yml:/etc/prometheus/prometheus.yml \
  prom/prometheus
```

### Direct Installation

1.  **Download Prometheus**:

    ```bash
    wget https://github.com/prometheus/prometheus/releases/download/v2.47.0/prometheus-2.47.0.linux-amd64.tar.gz
    ```

2.  **Extract and move binaries**:

    ```bash
    tar xvfz prometheus-2.47.0.linux-amd64.tar.gz
    sudo mv prometheus /usr/local/bin/
    ```

3.  **Create a configuration file**: Save the following content in `/etc/prometheus/prometheus.yml`:

    ```yaml
    global:
        scrape_interval: 15s

    scrape_configs:
        - job_name: 'node_exporter'
          static_configs:
              - targets: ['localhost:9100']
    ```

4.  **Start Prometheus**:

    ```bash
    prometheus --config.file=/etc/prometheus/prometheus.yml
    ```

### Verify Installation

Visit `http://<your-server-ip>:9090` to access the Prometheus web interface.

## 📊 Step 2: Installing Node Exporter

Prometheus relies on exporters to collect metrics. Node Exporter is ideal for system-level metrics.

1.  **Download Node Exporter**:

    ```bash
    wget https://github.com/prometheus/node_exporter/releases/download/v1.6.0/node_exporter-1.6.0.linux-amd64.tar.gz
    ```

2.  **Extract and run**:

    ```bash
    tar xvfz node_exporter-1.6.0.linux-amd64.tar.gz
    ./node_exporter
    ```

3.  **Update Prometheus Configuration**: Add `localhost:9100` as a target under the `scrape_configs` section of `prometheus.yml`.
4.  **Restart Prometheus**:

    ```bash
    systemctl restart prometheus
    ```

### Verify Node Exporter Metrics

Visit `http://<your-server-ip>:9100/metrics`.

## 🖥️ Step 3: Installing Grafana

### Using Docker

```bash
docker run -d --name=grafana \
  -p 3000:3000 \
  grafana/grafana
```

### Direct Installation

1.  **Add the Grafana repository**:

    ```bash
    sudo apt-get install -y software-properties-common
    sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
    wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
    ```

2.  **Install Grafana**:

    ```bash
    sudo apt-get update
    sudo apt-get install grafana
    ```

3.  **Start Grafana**:

    ```bash
    sudo systemctl start grafana-server
    ```

4.  **Enable Grafana on boot**:

    ```bash
    sudo systemctl enable grafana-server
    ```

### Access Grafana

Visit `http://<your-server-ip>:3000`. The default credentials are `admin`/`admin`.

## 🔗 Step 4: Connecting Prometheus to Grafana

1.  **Log in to Grafana**.
2.  Navigate to **Configuration → Data Sources → Add data source**.
3.  Select **Prometheus**.
4.  Enter `http://<your-server-ip>:9090` as the URL and save.

## 📈 Step 5: Creating Dashboards

1.  **Import a Pre-made Dashboard**:

    -   Navigate to **Dashboards → Import**.
    -   Use a public dashboard ID from [Grafana's Dashboard Library](https://grafana.com/grafana/dashboards/).
    -   Example: Use ID **1860** for Node Exporter Full.

2.  **Build Custom Dashboards**:

    -   Click **Create → Dashboard**.
    -   Add panels with PromQL queries like:
        -   CPU Usage: `100 - (avg by (instance) (irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)`
        -   Memory Usage: `node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes * 100`

## 🚨 Step 6: Setting Up Alerts

### Prometheus Alerts

1.  Add rules to your Prometheus config:

    ```yaml
    rule_files:
        - 'alerts.yml'
    ```

2.  Create `alerts.yml` with an example alert:

    ```yaml
    groups:
        - name: example-alert
          rules:
              - alert: HighCPUUsage
                expr: avg(node_cpu_seconds_total{mode!="idle"}) > 0.8
                for: 2m
                labels:
                    severity: warning
                annotations:
                    summary: 'High CPU usage detected'
    ```

3.  Reload Prometheus:

    ```bash
    kill -HUP $(pgrep prometheus)
    ```

### Grafana Alerts

1.  In Grafana, navigate to **Alerting → Alert Rules**.
2.  Create a new rule, select the Prometheus data source, and define conditions.

## 🏁 Conclusion

Congratulations! You've set up a comprehensive monitoring solution using Prometheus and Grafana. This powerful combo ensures you can track, visualize, and act on key metrics, keeping your systems reliable and performant.

**Happy Monitoring! 🚀**
