# MarketFlow

Automated stock‑market data pipeline: ingest, validate, transform, and serve. Pulls OHLCV data at daily and intraday intervals, lands raw files to object storage, upserts into a relational warehouse across bronze, silver, and gold layers, and exposes a lightweight API and dashboards.

> Owner: Jaime Manzueta
> Status: MVP in progress
> Stack: Python, Airflow, Postgres, S3, FastAPI, Metabase

---

## Features

* Ingestion of daily and one‑minute bars with historical backfill and incremental refresh.
* Storage layers: Raw in object storage; Bronze, Silver, and Gold in the warehouse with idempotent upserts.
* Transformations: Deduplication, trading‑calendar alignment, adjusted close handling, and common technical indicators such as moving averages, RSI, and VWAP.
* Serving layer: Read‑only API for programmatic access and prebuilt dashboard charts for exploration.
* Operations: Scheduled runs, retries, basic alerting, and hooks for continuous integration.

---

## Architecture (Overview)

* Source: Public market data provider for OHLCV (initially a free provider; can be swapped for a paid source later).
* Orchestration: Tasks scheduled and monitored by a workflow manager.
* Raw Landing: Files written to object storage in a partitioned layout by source, ticker, and date.
* Warehouse: Postgres hosts bronze (type‑stable), silver (cleaned and aligned), and gold (analytics‑ready) tables.
* Serving: A small web service provides endpoints for latest prices and features; a BI tool connects to the warehouse for dashboards.

---

## Tech Stack

* Python for all extract, transform, and service components.
* Airflow for scheduling and orchestration.
* PostgreSQL for the analytics warehouse.
* S3 (or an S3‑compatible store) for raw data.
* FastAPI for the read‑only service.
* Metabase or Grafana for dashboards.

---

## Quickstart (Conceptual)

1. Clone the repository and open it in your editor.
2. Create a local environment and install project dependencies.
3. Set environment variables for the database, object storage, region, and the list of tickers.
4. Start the workflow manager, the API service, and the BI tool (locally via containers or native services).
5. Open the workflow UI to trigger an initial backfill and then enable the scheduled runs.
6. Connect the BI tool to the warehouse and explore the starter charts.

---

## Configuration

Provide values for:

* Database host, port, user, password, and database name.
* Object storage endpoint or cloud region, bucket name, and credentials (or instance role).
* Application settings such as the tracked tickers list and the deployment timezone.

In production, manage secrets via the workflow manager’s connection store or a cloud parameter service.

---

## Data Model

* **Raw (Object Storage)**: Partitioned files by source, ticker, and date.
* **Bronze (Warehouse)**: Type‑stable ingestion table keyed by ticker, timestamp, and interval.
* **Silver (Warehouse)**: Deduplicated, trading‑calendar aligned data with adjusted close when available.
* **Gold (Warehouse)**: Analytics‑oriented tables and views, including features such as moving averages, RSI, and VWAP.

---

## Orchestration

Planned workflows include:

* Daily close ingestion on weekdays.
* Intraday ingestion during market hours.
* Transformation jobs from bronze to silver and feature builds to gold.
* Parameterized historical backfills.
  Each workflow includes retry logic and basic alerting.

---

## API

Read‑only endpoints expose the latest prices and derived features for specified tickers and intervals. The service is intended for dashboards and light programmatic queries rather than heavy analytics.

---

## Dashboards

A BI tool connects directly to the warehouse. Starter visuals include daily OHLC, daily returns, moving‑average crossovers, and RSI. Additional charts can be added as the dataset grows.

---

## Make Targets and CI (Overview)

Common developer tasks include environment setup, formatting and linting, unit tests, and bringing up or tearing down local services. A continuous‑integration pipeline runs lint and tests on each change.

---

## Roadmap

* MVP: Daily ingestion to the warehouse, basic features, API, and starter dashboards.
* Next: Limited one‑minute intraday coverage with freshness checks.
* Data Quality: Add formal test suites on silver and gold.
* Modeling: Introduce a transformation framework with documentation and tests.
* Coverage: Expand to a broader universe such as the S&P 500 with sector tags.
* CI/CD: Build and publish container images and run automated checks on every commit.

---

## License

MIT (see the license file in the repository).
