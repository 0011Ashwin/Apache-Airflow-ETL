# 🌤️ Weather ETL Pipeline with Apache Airflow

[![Apache Airflow](https://img.shields.io/badge/Apache%20Airflow-2.x-017CEE.svg)](https://airflow.apache.org/)
[![Docker](https://img.shields.io/badge/Docker-Ready-2496ED.svg)](https://docker.com)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15+-336791.svg)](https://postgresql.org)
[![Astronomer](https://img.shields.io/badge/Astronomer-Runtime-purple.svg)](https://astronomer.io)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

> A production-ready **ETL (Extract, Transform, Load)** pipeline built with Apache Airflow that fetches real-time weather data from the Open-Meteo API and stores it in PostgreSQL for analytics and monitoring.

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Architecture](#-architecture)
- [Features](#-features)
- [Prerequisites](#-prerequisites)
- [Installation](#-installation)
- [Usage](#-usage)
- [Project Structure](#-project-structure)
- [DAG Details](#-dag-details)
- [Configuration](#-configuration)
- [Monitoring](#-monitoring)
- [Contributing](#-contributing)
- [License](#-license)

---

## 🔍 Overview

This project demonstrates a **real-world ETL pipeline** using Apache Airflow, showcasing:

- **Data Engineering Best Practices** - Modular, testable, and maintainable code
- **Modern Orchestration** - Apache Airflow for workflow management
- **Cloud-Ready Architecture** - Containerized with Docker, deployable to Astronomer
- **API Integration** - RESTful API consumption using Airflow hooks
- **Database Operations** - PostgreSQL integration with proper schema management

### Use Cases

- 🌡️ Weather monitoring and historical analysis
- 📊 Building datasets for ML weather prediction
- 🔔 Alert systems based on weather conditions
- 📈 Business intelligence dashboards

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         Apache Airflow                                   │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │                    Weather ETL Pipeline DAG                      │    │
│  │                                                                  │    │
│  │  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐      │    │
│  │  │   EXTRACT    │───▶│  TRANSFORM   │───▶│     LOAD     │      │    │
│  │  │              │    │              │    │              │      │    │
│  │  │ Open-Meteo   │    │ Data Clean   │    │ PostgreSQL   │      │    │
│  │  │ API Call     │    │ & Structure  │    │ Insert       │      │    │
│  │  └──────────────┘    └──────────────┘    └──────────────┘      │    │
│  │                                                                  │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                          │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐  ┌────────────┐        │
│  │  Postgres  │  │ Webserver  │  │ Scheduler  │  │  Triggerer │        │
│  │ (Metadata) │  │   :8080    │  │            │  │            │        │
│  └────────────┘  └────────────┘  └────────────┘  └────────────┘        │
└─────────────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                        External Services                                 │
│                                                                          │
│  ┌─────────────────────┐              ┌─────────────────────┐           │
│  │   Open-Meteo API    │              │  PostgreSQL (Data)  │           │
│  │                     │              │                     │           │
│  │ • Weather forecast  │              │ • weather_data      │           │
│  │ • Current weather   │              │ • Historical store  │           │
│  │ • Historical data   │              │ • Analytics ready   │           │
│  └─────────────────────┘              └─────────────────────┘           │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## ✨ Features

### ETL Pipeline Features

| Feature | Description |
|---------|-------------|
| **Daily Scheduling** | Automated daily weather data collection |
| **Idempotent Operations** | Safe to re-run without data duplication |
| **Error Handling** | Robust error handling with retries |
| **Modular Tasks** | Separate Extract, Transform, Load tasks |
| **TaskFlow API** | Modern Airflow 2.x TaskFlow decorators |

### Infrastructure Features

| Feature | Description |
|---------|-------------|
| **Docker Compose** | One-command local development setup |
| **Astronomer Runtime** | Production-ready Airflow image |
| **PostgreSQL** | Reliable data storage |
| **Web UI** | Full Airflow web interface for monitoring |

### Data Captured

| Field | Type | Description |
|-------|------|-------------|
| `latitude` | FLOAT | Location latitude |
| `longitude` | FLOAT | Location longitude |
| `temperature` | FLOAT | Current temperature (°C) |
| `windspeed` | FLOAT | Wind speed (km/h) |
| `winddirection` | FLOAT | Wind direction (degrees) |
| `weathercode` | INT | WMO weather code |
| `timestamp` | TIMESTAMP | Data collection time |

---

## 📋 Prerequisites

- **Docker** & **Docker Compose** (v2.0+)
- **Astronomer CLI** (optional, for deployment)
- **Python 3.8+** (for local development)

---

## 🚀 Installation

### Method 1: Using Astronomer CLI (Recommended)

```bash
# Install Astronomer CLI
curl -sSL install.astronomer.io | sudo bash -s

# Clone the repository
git clone https://github.com/0011Ashwin/Apache-Airflow-ETL.git
cd Apache-Airflow-ETL

# Start Airflow locally
astro dev start
```

### Method 2: Using Docker Compose

```bash
# Clone the repository
git clone https://github.com/0011Ashwin/Apache-Airflow-ETL.git
cd Apache-Airflow-ETL

# Start services
docker-compose up -d

# Verify containers are running
docker ps
```

### Access the Airflow UI

- **URL**: http://localhost:8080
- **Username**: `admin`
- **Password**: `admin`

---

## 💻 Usage

### 1. Configure Connections

In Airflow UI, go to **Admin → Connections** and add:

#### PostgreSQL Connection
| Field | Value |
|-------|-------|
| Connection Id | `postgres_default` |
| Connection Type | `Postgres` |
| Host | `postgres` |
| Schema | `postgres` |
| Login | `postgres` |
| Password | `postgres` |
| Port | `5432` |

#### HTTP Connection (Open-Meteo API)
| Field | Value |
|-------|-------|
| Connection Id | `open_meteo_api` |
| Connection Type | `HTTP` |
| Host | `https://api.open-meteo.com` |

### 2. Enable and Trigger DAG

1. Go to **DAGs** in the Airflow UI
2. Find `weather_etl_pipeline`
3. Toggle the DAG to **ON**
4. Click **Trigger DAG** to run manually

### 3. Monitor Execution

- View task logs in **Graph View**
- Check **Task Instance Details** for debugging
- Monitor **Gantt Chart** for performance

### 4. Query the Data

```sql
-- Connect to PostgreSQL and run:
SELECT * FROM weather_data 
ORDER BY timestamp DESC 
LIMIT 10;

-- Get average temperature over time
SELECT 
    DATE(timestamp) as date,
    AVG(temperature) as avg_temp,
    AVG(windspeed) as avg_wind
FROM weather_data
GROUP BY DATE(timestamp)
ORDER BY date DESC;
```

---

## 📁 Project Structure

```
Apache-Airflow-ETL/
├── dags/                           # Airflow DAG definitions
│   ├── etlweather.py               # Main Weather ETL pipeline
│   ├── exampledag.py               # Example DAG template
│   └── .airflowignore              # Files to ignore
│
├── tests/                          # Unit tests for DAGs
│
├── Dockerfile                      # Astronomer Runtime image
├── docker-compose.yml              # Local development setup
├── packages.txt                    # OS-level dependencies
├── requirements.txt                # Python dependencies
└── README.md                       # Documentation
```

---

## 📊 DAG Details

### weather_etl_pipeline

```python
DAG Configuration:
├── dag_id: weather_etl_pipeline
├── schedule_interval: @daily
├── catchup: False
└── default_args:
    ├── owner: airflow
    └── start_date: days_ago(1)

Tasks:
├── extract_weather_data()    # Fetch from Open-Meteo API
├── transform_weather_data()  # Clean and structure data
└── load_weather_data()       # Insert into PostgreSQL
```

### Task Dependencies

```
extract_weather_data → transform_weather_data → load_weather_data
```

### Customization

Modify these variables in `etlweather.py` to change location:

```python
LATITUDE = '51.5074'    # London
LONGITUDE = '-0.1278'
```

---

## ⚙️ Configuration

### Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `AIRFLOW__CORE__EXECUTOR` | `LocalExecutor` | Airflow executor type |
| `AIRFLOW__DATABASE__SQL_ALCHEMY_CONN` | - | Metadata DB connection |
| `POSTGRES_USER` | `postgres` | PostgreSQL username |
| `POSTGRES_PASSWORD` | `postgres` | PostgreSQL password |

### Scaling for Production

```yaml
# docker-compose.yml modifications for production
services:
  scheduler:
    deploy:
      replicas: 2
  worker:
    deploy:
      replicas: 4
```

---

## 📈 Monitoring

### Airflow UI Dashboards

- **DAG Runs**: Track execution history
- **Task Duration**: Monitor performance trends
- **Gantt Chart**: Visualize task parallelism
- **Logs**: Debug task failures

### Metrics to Watch

| Metric | Alert Threshold | Description |
|--------|-----------------|-------------|
| DAG Duration | > 5 minutes | Pipeline taking too long |
| Task Failures | > 0 | Any task failure |
| API Response Time | > 10 seconds | Slow API calls |

---

## 🤝 Contributing

Contributions are welcome! Here's how to get started:

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/NewDAG`)
3. **Add tests** for your changes
4. **Commit** your changes (`git commit -m 'Add multi-city weather DAG'`)
5. **Push** to the branch (`git push origin feature/NewDAG`)
6. **Open** a Pull Request

### Ideas for Contributions

- [ ] Add multi-location support
- [ ] Implement data quality checks
- [ ] Add weather alerts DAG
- [ ] Create visualization dashboard
- [ ] Add historical backfill DAG

---

## 📚 Resources

- [Apache Airflow Documentation](https://airflow.apache.org/docs/)
- [Astronomer Learn](https://www.astronomer.io/docs/learn/)
- [Open-Meteo API Docs](https://open-meteo.com/en/docs)
- [TaskFlow API Tutorial](https://airflow.apache.org/docs/apache-airflow/stable/tutorial/taskflow.html)

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

<p align="center">
  Built with ❤️ for data engineering
  <br>
  <a href="https://github.com/0011Ashwin">@0011Ashwin</a>
</p>
