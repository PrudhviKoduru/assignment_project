# 🏙️ Regional Computing (RC) IoT Data Simulation

## 📘 Overview

This project simulates how **IoT sensor data in a Smart City** flows through three computing layers — **Edge**, **Regional Computing (RC)**, and **Cloud** — to study performance trade-offs in **latency, energy consumption, and cost**.

The simulation is inspired by modern research on **Regional Computing frameworks** for smart cities, designed to handle massive IoT workloads efficiently while reducing latency and operational cost.

> **Goal:** Understand performance trade-offs between computing tiers and demonstrate scalable data engineering workflows deployable on **AWS**.

---

## 📁 Project Structure

```
assignment_project/
├── data/
│   ├── optinal_generated_data.ipynb   # Notebook to generate synthetic IoT dataset (~100 MB)
│   └── s3_iot_data link               # Link reference for S3-hosted dataset
├── src/
│   └── assignment_project.ipynb       # Core simulation engine (routing + metric calculation)
├── visualization/
│   ├── average_delay_and_cost.ipynb   # Bar charts: avg delay & cost by computing layer
│   ├── comparison.ipynb               # KDE density plots: delay distributions per layer
│   └── cost_energy.ipynb              # Dual-axis chart: cost vs energy trade-offs
├── results/
│   ├── rc_results.csv                 # Output: per-event simulation metrics (10,000 events)
│   └── rc_summary.json                # Summary: averages and distribution statistics
└── README.md
```

---

## ⚙️ How It Works

### 1. Data Generation — `data/optinal_generated_data.ipynb`

Generates a realistic synthetic IoT dataset (~100 MB JSON) containing readings from **5,000 virtual smart city devices** across 4 regions (north / south / east / west).

Each record captures:

| Field | Description |
|-------|-------------|
| `timestamp` | Unix epoch timestamp |
| `device_id` | Unique sensor identifier |
| `region` | Device geographic region |
| `status` | Device status (`active`, `idle`, `error`) |
| `data` | Sensor readings: temperature, humidity, light, CO₂, sound, motion, wind speed, air pressure, GPS lat/lon |

📎 Pre-built dataset (100 MB):
[iot_data_100mb.json on Google Drive](https://drive.google.com/file/d/1pbg8qF_flyxG4xXAqHrPzcHU1gN-hSPj/view?usp=drive_link)

---

### 2. Regional Computing Simulation — `src/assignment_project.ipynb`

Reads the IoT dataset **line by line** (memory-efficient streaming) and routes each event to the optimal computing tier. For every event the notebook calculates:

- **Transmission delay** — network + propagation delay based on tier distance and bandwidth
- **Processing delay** — computation time based on tier processing capacity
- **Total delay** — combined end-to-end latency
- **Processing cost** — economic model per tier
- **Energy consumption** — data movement + computation energy

**Tier configuration:**

| Parameter | Edge | RC | Cloud |
|-----------|------|----|-------|
| Bandwidth | 500 Mbps | 200 Mbps | 50 Mbps |
| Distance | 5 km | 100 km | 2,000 km |
| Processing | 50k MIPS | 100k MIPS | 300k MIPS |
| Cost/MIPS | $0.000001 | $0.000002 | $0.000008 |

Results are written in batches of 10,000 events to:
- `results/rc_results.csv` — detailed per-event metrics
- `results/rc_summary.json` — aggregated averages and path distribution

---

### 3. Visualization — `visualization/`

| Notebook | Chart Type | What It Shows |
|----------|-----------|---------------|
| `average_delay_and_cost.ipynb` | Bar charts + scatter plot | Average delay (ms) and cost (USD) per tier; Energy vs Delay correlation |
| `comparison.ipynb` | KDE density plot | Overlaid delay distributions for Edge / RC / Cloud |
| `cost_energy.ipynb` | Dual-axis bar + line | Average cost (USD) vs average energy (J) per tier |

---

## 🧠 Routing Decision Logic

Each incoming event is routed to a tier using the following algorithm:

1. **Low latency requirement** → send to **Edge** (fastest, most local)
2. **Peak hour + medium/high latency requirement** → offload to **Regional Computing**
3. **Otherwise** → use **Cloud** (most scalable)

```text
latency_need == "low"   →  Edge
peak_hour == True       →  RC
else                    →  Cloud
```

---

## 📊 Output Metrics

| Column | Description |
|--------|-------------|
| `event_id` | Unique event identifier |
| `path` | Routing decision: `edge`, `rc`, or `cloud` |
| `size_kb` | Payload size (KB) |
| `tx_delay_ms` | Transmission + propagation delay (ms) |
| `proc_delay_ms` | Processing delay (ms) |
| `total_delay_ms` | Combined end-to-end delay (ms) |
| `cost_usd` | Total cost (transmission + processing, USD) |
| `energy_j` | Total energy consumed (Joules) |
| `latency_need` | Input QoS requirement: `low`, `medium`, or `high` |
| `peak_hour` | Whether the event occurred during a peak-load window |

---

## 📈 Key Findings

| Tier | Avg Total Delay | Avg Cost | Notes |
|------|----------------|----------|-------|
| **Edge** | ~5 ms | ~$0.00025 | Handles low-latency QoS events; higher processing delay due to lower MIPS |
| **RC** | ~3 ms | ~$0.00050 | Lowest overall delay thanks to 100k MIPS processing capacity |
| **Cloud** | ~12 ms | ~$0.00200 | Highest latency and cost; unlimited scale for non-time-sensitive events |

> **Note:** RC achieves lower total delay than Edge in simulation because its higher processing power (100k MIPS vs 50k MIPS) reduces processing delay more than its longer transmission distance increases it.

---

## 🛠️ Requirements

```
python >= 3.8
pandas
matplotlib
seaborn
jupyter
```

Install dependencies:

```bash
pip install pandas matplotlib seaborn jupyter
```

---

## 🚀 How to Run

1. **Generate the IoT dataset** (optional — you can use the pre-built dataset linked above):
   ```bash
   jupyter nbconvert --to notebook --execute data/optinal_generated_data.ipynb
   ```

2. **Run the simulation:**
   ```bash
   jupyter nbconvert --to notebook --execute src/assignment_project.ipynb
   ```

3. **Explore the results interactively:**
   ```bash
   jupyter notebook visualization/
   ```

---

## ☁️ AWS Deployment Context

This project is designed to mirror a production-ready architecture on AWS:

| Component | AWS Service |
|-----------|-------------|
| IoT data ingest | AWS IoT Core |
| Edge processing | AWS Lambda / Greengrass |
| Regional tier | EC2 (region-local instances) |
| Cloud processing | EC2 / EMR |
| Data storage | Amazon S3 |
| Result analysis | Amazon Athena / S3 Select |
