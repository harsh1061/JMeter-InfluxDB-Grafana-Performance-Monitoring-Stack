
---

# 🚀 JMeter-InfluxDB-Grafana Performance Stack

This repository contains a fully automated, containerized performance monitoring environment. It orchestrates a load generator (JMeter), a time-series database (InfluxDB), and a visualization layer (Grafana) using **Docker Compose**.

---

## 📂 Project Structure

Ensure your project folder is organized exactly as follows. If these folders do not exist, create them manually:

```text
.
├── docker-compose.yml         # Container orchestration
├── dashboards/                # JSON dashboard storage
│   └── jmeter-dashboard.json  # Your pre-configured dashboard file
├── scripts/                   # Place your JMeter .jmx files here
├── results/                   # JMeter results (.jtl) and logs will save here
└── provisioning/              # Auto-configuration folders
    ├── datasources/
    │   └── datasource.yml     # Pre-connects InfluxDB to Grafana
    └── dashboards/
        └── dashboard-provider.yml # Tells Grafana where to find JSON files

```

---

## ⚡ Quick Start

### 1. Start the Stack

Open your terminal in this folder and run:

```bash
docker-compose up -d

```

### 2. Access the Tools

* **Grafana:** [http://localhost:3000](https://www.google.com/search?q=http://localhost:3000) (User: `admin` | Pass: `admin`)
* **InfluxDB:** [http://localhost:8086](https://www.google.com/search?q=http://localhost:8086) (User: `admin` | Pass: `password12345`)

---

## 🧪 Running a Load Test

### 1. Configure JMeter (.jmx)

Open your `.jmx` file and locate the **Backend Listener**. Use these exact settings:

* **influxdbUrl:** `http://influxdb:8086/api/v2/write?org=my-org&bucket=jmeter_results`
* **influxdbToken:** `my-super-secret-token-123`
* **application:** `my_app` (Must match the filter at the top of your Grafana dashboard)

> **Note:** Use the hostname `influxdb` inside the JMX because JMeter is running inside the same Docker network.

### 2. Execute the Test

Run this command from your terminal to start the test:

```bash
docker exec -it jmeter jmeter -n -t /scripts/your_test.jmx -l /results/run1.jtl

```

---

## 🛑 Stopping the Test

If your test is running and you need to stop it, try these methods in order:

### Method 1: Graceful Shutdown

Tells JMeter to stop starting new threads and finish active samples.

```bash
docker exec -it jmeter /opt/apache-jmeter/bin/shutdown.sh

```

### Method 2: Immediate Stop

If Method 1 doesn't work, this forces an immediate halt of the engine.

```bash
docker exec -it jmeter /opt/apache-jmeter/bin/stoptest.sh

```

### Method 3: Docker Stop (The "Service" Stop)

If the internal scripts fail, you can stop the JMeter container service entirely.

```bash
docker-compose stop jmeter

```

*To use the container again after this, run `docker-compose start jmeter`.*

### Method 4: Force Kill

If the container is completely frozen, kill the Java process directly:

```bash
docker exec -it jmeter pkill -9 java

```

---

## 📊 Dashboard Management

### Automation

The `jmeter-dashboard.json` file in your `./dashboards` folder is automatically imported into the **"Performance"** folder in Grafana on startup.

### Saving Changes Permanently

If you edit the dashboard in the Grafana UI:

1. **Export:** Go to Share > Export > Save to file.
2. **Cleanup:** Ensure all `"datasource": { "uid": ... }` entries in the JSON are set to `"InfluxDB_JMeter"`.
3. **Overwrite:** Replace the local `jmeter-dashboard.json` file.
4. **Reload:** Run `docker-compose restart grafana`.

---

## 🛠️ Essential Commands

| Task | Command |
| --- | --- |
| **Start everything** | `docker-compose up -d` |
| **Stop everything** | `docker-compose down` |
| **Reset (Wipe all data)** | `docker-compose down -v` |
| **View Grafana Logs** | `docker-compose logs -f grafana` |
| **Check Influx Status** | `docker exec -it influxdb influx ping` |

---

## ⚠️ Troubleshooting

* **No Data in Grafana:** Ensure the **Time Range** (top right) is set correctly (e.g., "Last 5 minutes").
* **Bucket Dropdown:** Ensure the `bucket` variable at the top of the dashboard is set to `jmeter_results`.
* **Unauthorized (401):** Verify the `influxdbToken` in your JMX matches the one in `docker-compose.yml`.