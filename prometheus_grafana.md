## 🔍 Apa Itu Prometheus?

**Prometheus** adalah **sistem monitoring dan time-series database** (TSDB) open-source yang dikembangkan pertama kali oleh SoundCloud dan kini dikelola oleh Cloud Native Computing Foundation (CNCF), sama seperti Kubernetes.

### 🔧 Fungsi Utama Prometheus:

1. **Mengumpulkan data metrik** dari target yang dimonitor melalui HTTP (biasanya endpoint `/metrics`).
2. **Menyimpan data sebagai time-series**, dengan label (key-value) yang memungkinkan filter yang fleksibel.
3. Memiliki **query language khusus** bernama **PromQL (Prometheus Query Language)** untuk mengekstrak dan menganalisis data.
4. Dapat memicu **alert (peringatan)** melalui Alertmanager jika terjadi anomali atau kondisi kritis.

---

### 🛠️ Arsitektur Prometheus:

Komponen utama:

1. **Prometheus Server**: Mengambil data metrik dari target dan menyimpannya.
2. **Exporters**: Komponen yang mengekspos data metrik dari sistem eksternal, contoh:

   * `node_exporter` untuk sistem Linux/Windows.
   * `mysqld_exporter` untuk database MySQL.
   * `blackbox_exporter` untuk ping/HTTP endpoint.
3. **Push Gateway**: Untuk job yang bersifat short-lived (seperti batch job), karena Prometheus menganut model pull (bukan push).
4. **Alertmanager**: Menangani alert dan mengirimkannya ke email, Slack, dll.
5. **PromQL**: Digunakan untuk query dan visualisasi data.

---

### 📈 Cara Kerja Prometheus:

1. Prometheus **men-scrape data** dari endpoint HTTP yang disediakan oleh exporters.
2. Data disimpan dalam format time-series (timestamp + value).
3. Data bisa ditampilkan di UI Prometheus atau dikirim ke Grafana.
4. Jika kondisi tertentu terjadi (misal: CPU > 90% selama 5 menit), **alert** dikirim ke Alertmanager.

---

## 🎨 Apa Itu Grafana?

**Grafana** adalah **platform visualisasi dan analisis data** yang bisa terhubung ke berbagai sumber data seperti Prometheus, InfluxDB, Elasticsearch, MySQL, PostgreSQL, dll.

### 🔧 Fungsi Utama Grafana:

1. Membuat **dashboard interaktif** dan real-time berdasarkan data metrik.
2. Menyediakan **panel-panel visual** (grafik, tabel, heatmap, gauge, dll).
3. Bisa digunakan untuk **monitoring sistem, aplikasi, database, jaringan**, dll.
4. Dapat digunakan untuk membuat alert berbasis visualisasi metrik.
5. Mendukung **authentication & role-based access control (RBAC)**.

---

### 🔗 Integrasi Prometheus dan Grafana:

1. **Prometheus sebagai data source** di Grafana.
2. Di Grafana, kita buat dashboard dengan panel-panel yang menampilkan data dari Prometheus.
3. Bisa memanfaatkan PromQL langsung dalam query di Grafana.
4. Visualisasi akan diperbarui **real-time** sesuai interval yang ditentukan.

---

### 📦 Contoh Use Case:

| Komponen        | Tujuan                                               |
| --------------- | ---------------------------------------------------- |
| `node_exporter` | Mengekspor metrik sistem (CPU, RAM, disk)            |
| Prometheus      | Mengambil data dari node\_exporter dan menyimpannya  |
| Grafana         | Menampilkan data dari Prometheus dalam bentuk visual |
| Alertmanager    | Mengirim peringatan jika resource overload           |

---

## 🖥️ Contoh Visualisasi:

Misalnya kita ingin menampilkan **penggunaan CPU** selama 1 jam terakhir:

```promql
100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
```

Query ini menunjukkan persentase penggunaan CPU per instance, lalu Grafana menampilkannya dalam bentuk **grafik line chart**.

---

## 🚀 Kelebihan Prometheus dan Grafana:

### Prometheus:

* Time-series database internal.
* Pull-based model = mudah kontrol.
* Query language kuat (PromQL).
* Integrasi baik dengan Kubernetes.

### Grafana:

* UI sangat fleksibel dan powerful.
* Bisa menggabungkan data dari banyak sumber.
* Support alerting dan sharing dashboard.
* Komunitas besar dan banyak plugin.

---

## ⚙️ Contoh Arsitektur Deployment:

```
| Application / Server |
|----------------------|
|    node_exporter     | <- menyajikan metrik di /metrics
         ↓
|     Prometheus       | <- scrape data metrik dari node_exporter
         ↓
|     Alertmanager     | <- kirim notifikasi jika kondisi kritis
         ↓
|       Grafana        | <- visualisasi data dari Prometheus
```

---

## 📚 Kesimpulan

| Prometheus                      | Grafana                                |
| ------------------------------- | -------------------------------------- |
| Mengumpulkan & menyimpan metrik | Menampilkan data dalam bentuk visual   |
| Menggunakan PromQL              | Menggunakan query dari data source     |
| Bisa trigger alert              | Bisa menampilkan alert visual          |
| Terintegrasi dengan exporters   | Terintegrasi dengan banyak sumber data |

