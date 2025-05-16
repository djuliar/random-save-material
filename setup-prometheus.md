Berikut ini saya buatkan tiga hal penting untuk setup **Prometheus + Grafana**:

1. ✅ **File konfigurasi `prometheus.yml`**
2. 📊 **Grafana dashboard JSON (CPU, RAM, Disk, Network)**
3. 🚨 **Alert rules Prometheus (contoh: CPU > 80%)**

---

## ✅ 1. File Konfigurasi `prometheus.yml`

File ini untuk digunakan baik di Linux, Docker, atau Kubernetes dengan penyesuaian.

```yaml
global:
  scrape_interval: 15s

alerting:
  alertmanagers:
    - static_configs:
        - targets: ['localhost:9093']  # Jika pakai Alertmanager

rule_files:
  - "alert_rules.yml"

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100']  # Port node_exporter
```

---

## 📊 2. Grafana Dashboard (JSON)

Dashboard ini menampilkan:

* CPU usage
* Memory usage
* Disk usage
* Network traffic

### 🔽 Simpan dengan nama: `basic-node-dashboard.json`

```json
{
  "dashboard": {
    "id": null,
    "uid": "node-monitoring",
    "title": "Node Monitoring",
    "tags": ["node", "infrastructure"],
    "timezone": "browser",
    "panels": [
      {
        "type": "graph",
        "title": "CPU Usage",
        "targets": [
          {
            "expr": "100 - (avg by(instance)(rate(node_cpu_seconds_total{mode=\"idle\"}[5m])) * 100)",
            "legendFormat": "{{instance}}",
            "refId": "A"
          }
        ],
        "datasource": "Prometheus",
        "gridPos": {"x": 0, "y": 0, "w": 12, "h": 8}
      },
      {
        "type": "graph",
        "title": "Memory Usage",
        "targets": [
          {
            "expr": "(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100",
            "legendFormat": "{{instance}}",
            "refId": "A"
          }
        ],
        "datasource": "Prometheus",
        "gridPos": {"x": 12, "y": 0, "w": 12, "h": 8}
      },
      {
        "type": "graph",
        "title": "Disk Usage",
        "targets": [
          {
            "expr": "(node_filesystem_size_bytes{mountpoint=\"/\"} - node_filesystem_free_bytes{mountpoint=\"/\"}) / node_filesystem_size_bytes{mountpoint=\"/\"} * 100",
            "legendFormat": "{{instance}}",
            "refId": "A"
          }
        ],
        "datasource": "Prometheus",
        "gridPos": {"x": 0, "y": 8, "w": 12, "h": 8}
      },
      {
        "type": "graph",
        "title": "Network Traffic",
        "targets": [
          {
            "expr": "rate(node_network_receive_bytes_total[1m])",
            "legendFormat": "{{instance}} receive",
            "refId": "A"
          },
          {
            "expr": "rate(node_network_transmit_bytes_total[1m])",
            "legendFormat": "{{instance}} transmit",
            "refId": "B"
          }
        ],
        "datasource": "Prometheus",
        "gridPos": {"x": 12, "y": 8, "w": 12, "h": 8}
      }
    ],
    "schemaVersion": 37,
    "version": 1
  }
}
```

➡️ Upload ini di Grafana:
**Dashboard → Import → Upload JSON**

---

## 🚨 3. File Alert Rules: `alert_rules.yml`

```yaml
groups:
  - name: node-alerts
    rules:
      - alert: HighCPUUsage
        expr: 100 - (avg by(instance)(rate(node_cpu_seconds_total{mode="idle"}[1m])) * 100) > 80
        for: 2m
        labels:
          severity: warning
        annotations:
          summary: "High CPU usage on {{ $labels.instance }}"
          description: "CPU usage is above 80% for more than 2 minutes."

      - alert: LowDiskSpace
        expr: (node_filesystem_free_bytes{mountpoint="/"} / node_filesystem_size_bytes{mountpoint="/"}) < 0.15
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "Disk space low on {{ $labels.instance }}"
          description: "Free disk space is less than 15%."

      - alert: HighMemoryUsage
        expr: (node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes > 0.85
        for: 2m
        labels:
          severity: warning
        annotations:
          summary: "High Memory usage on {{ $labels.instance }}"
          description: "Memory usage is above 85%."
```

---

## 🚀 Langkah Lanjutan:

1. Jalankan `node_exporter` agar Prometheus bisa memantau resource:

   ```bash
   wget https://github.com/prometheus/node_exporter/releases/download/v1.8.0/node_exporter-1.8.0.linux-amd64.tar.gz
   tar -xvf node_exporter-*.tar.gz
   cd node_exporter-*/
   ./node_exporter
   ```

2. Tambahkan ke `systemd` jika ingin jalan otomatis.
