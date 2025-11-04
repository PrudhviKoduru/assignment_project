# 🏙️ Regional Computing (RC) IoT Data Simulation

## 📘 Overview
This project simulates how **IoT data in a Smart City** flows through three computing layers — **Edge**, **Regional (RC)**, and **Cloud** — to study differences in **delay**, **energy**, and **cost**.

The simulation is inspired by modern research on **Regional Computing frameworks** for smart cities, designed to handle massive IoT workloads efficiently while reducing latency and operational cost.

The goal:  
> Understand performance trade-offs between computing tiers and demonstrate scalable data engineering workflows deployable on **AWS**.

---


---

## ⚙️ How It Works

### 1. Data Generation
`generate_iot_data.py` creates realistic IoT sensor data (~100MB JSON).
[iot Dataset](https://drive.google.com/file/d/1pbg8qF_flyxG4xXAqHrPzcHU1gN-hSPj/view?usp=drive_link)
Each record contains simulated readings for:
- Temperature  
- Humidity  
- Light intensity  
- Air quality  
- Motion  
- GPS coordinates  
- Device region and status  

This dataset mimics data produced by thousands of smart city devices.

### 2. Regional Computing Simulation
`simulate_rc_from_file.py` reads the generated IoT data **line by line** (memory-efficient streaming) and decides where each record should be processed — at the **Edge**, **Regional server**, or **Cloud**.

Each decision computes:
- Transmission Delay  
- Processing Delay  
- Total Delay  
- Energy Consumption  
- Processing Cost  

The results are saved to:
- `results/rc_results.csv` → detailed per-event metrics  
- `results/rc_summary.json` → summary of averages and distribution  

### 3. Visualization
Using `matplotlib` and `seaborn`, results are analyzed to show:
- Delay distributions across Edge/RC/Cloud  
- Cost and energy comparisons  
- Delay vs Cost scatter plots  
- Correlation heatmaps of metrics  
- Hourly delay patterns (if timestamped)  

---

## 📊 Metrics Captured

| Metric | Description |
|---------|--------------|
| `tx_delay_ms` | Transmission and propagation delay (ms) |
| `proc_delay_ms` | Processing time based on compute capacity (ms) |
| `total_delay_ms` | Combined total delay per event (ms) |
| `cost_usd` | Total cost (transmission + processing) |
| `energy_j` | Energy consumed for data movement and computation (Joules) |
| `path` | Processing path: `edge`, `rc`, or `cloud` |

---

## 🧠 Decision Logic (Simplified from Research Algorithm)

1. If latency requirement is **low** → send to **Edge**.  
2. If it’s a **peak hour** → offload to **Regional Computing**.  
3. Otherwise → use **Cloud** for scalability.  
4. Each layer applies bandwidth, distance, and cost models to estimate performance.

---




