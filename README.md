# 🚂 Austria Logistics & Transit Performance Analytics (PostgreSQL + Power BI)

An end-to-end data analytics project combining a **cloud-hosted PostgreSQL database (Supabase)**, custom **SQL Views**, and an interactive **Power BI Dashboard** to evaluate railway and freight transit efficiency, punctuality rates (*Pünktlichkeitsquote*), and delay patterns across Austria.

---

## 📸 Dashboard Overview

<!-- IMAGES HERE -->
<img width="2515" height="1281" alt="1" src="https://github.com/user-attachments/assets/66087c39-005a-4767-9987-ee9fa10cb3d9" />
<img width="2553" height="1298" alt="2" src="https://github.com/user-attachments/assets/ab90b558-2c35-456b-bb8b-7d4ceb70a60c" />
<img width="2553" height="1320" alt="3" src="https://github.com/user-attachments/assets/6c0c5451-81d6-435a-ae80-7d621b029f64" />




---

## 🎯 Business Context & Key Insights

Austria serves as a central European logistics hub (*ÖBB & ASFINAG networks*). Monitoring network delay patterns, station efficiency, and freight transport throughput is critical for operational excellence.

### Key Performance Indicators (KPIs):
* **Punctuality Rate (%):** Tracking overall network delays (*Pünktlichkeitsquote*) against a >90% operational benchmark.
* **Average Delay (Minutes):** Identifying bottleneck stations and transit routes with severe delays.
* **Freight Volume (Tons):** Analyzing total cargo throughput (*Güterverkehr*) across Austrian federal states (*Bundesländer*).

---

## 🛠️ Data Architecture & SQL Pipeline

This project demonstrates a full modern data stack architecture using a remote cloud database:

[ PostgreSQL Database (Supabase Cloud) ]
│
├─► Raw Tables: dim_station, dim_route, fact_logistics
├─► Custom SQL View: vw_daily_logistics_summary
│
[ Power BI Desktop (Native Database Connector) ]
│
[ Interactive Analytics Dashboard ]


### Key SQL Implementations:
1. **Database Schema Design:** Normalized Star Schema in PostgreSQL.
2. **SQL View Creation:** Offloaded heavy aggregations and punctuality percentage logic directly to the database via `CREATE VIEW`:

```sql
CREATE VIEW vw_daily_logistics_summary AS
SELECT 
    fl.date,
    ds.station_name,
    ds.bundesland,
    dr.route_name,
    dr.transport_type,
    fl.scheduled_trips,
    fl.completed_trips,
    fl.delayed_trips,
    fl.avg_delay_minutes,
    fl.cargo_tons,
    ROUND(((fl.completed_trips - fl.delayed_trips)::numeric / NULLIF(fl.completed_trips, 0)) * 100, 2) AS punctuality_rate_pct
FROM fact_logistics fl
JOIN dim_station ds ON fl.station_id = ds.station_id
JOIN dim_route dr ON fl.route_id = dr.route_id;
```


## 📊 Core DAX Measures

```dax
Pünktlichkeit % = 
VAR TotalTrips = SUM(fact_logistics[completed_trips])
VAR DelayedTrips = SUM(fact_logistics[delayed_trips])
RETURN DIVIDE(TotalTrips - DelayedTrips, TotalTrips, 0)
```
```dax
Ø Verspätung (Min) = 
AVERAGE(fact_logistics[avg_delay_minutes])
```
```dax
Gesamte Fracht (Tonnen) = 
SUM(fact_logistics[cargo_tons])
```

💻 Tech Stack

PostgreSQL (Supabase Cloud) – Relational Data Storage & SQL Views

Power BI Desktop – Relational Data Modeling & UI/UX Dashboard

DAX – Dynamic Time Intelligence & KPIs

Git & GitHub – Version Control & Portfolio Presentation
