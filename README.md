# Air Quality Prediction System - Stockholm

**Lab 1 - ID2223 Scalable Machine Learning @ KTH**

**Authors:** Emil Lidbom & Matteus Bäckström

- **Project Repository:** [https://github.com/triggerdude33/ID2223-Lab-1](https://github.com/triggerdude33/ID2223-Lab-1)
- **Live Dashboard:** [https://triggerdude33.github.io/ID2223-Lab-1/air-quality/](https://triggerdude33.github.io/ID2223-Lab-1/air-quality/)

---

### Air Quality Prediction ML System Summary

This project is an automated, serverless MLOps system that predicts daily PM2.5 air quality for a monitoring station in Stockholm. The entire process is orchestrated by GitHub Actions, which connects data sources, a feature store, model training, and a public dashboard.

Before the daily automation could begin, two manual one-time steps were required. First, a **backfill pipeline** was executed. The backfill pipeline served the following purposes:
1. Fetch metadata such as Air quality API keys, street name and geographical location and insert them into **Hopsworks secrets**.
2. Using the metadata, load a large volume of historical air quality and weather data into the **Hopsworks Feature Store**. 

Following this, the **training pipeline** was run to create the first version of the XGBoost model from this historical data, which was then saved to the Hopsworks Model Registry.

With the initial data and model in place, the system now operates in a daily, automated cycle:

1.  **Data Ingestion (GitHub Action):** A scheduled job runs daily to fetch the latest air quality data from the **AQICN API** and weather forecasts from the Open-Meteo API.

2.  **Feature Engineering & Storage (Hopsworks):** The new data is processed into features and appended to the existing feature groups in the **Feature Store**, ensuring a consistent and up-to-date data source.

3.  **Batch Inference (GitHub Action):** Shortly after, another scheduled job loads the latest features and the production-ready XGBoost model from the **Hopsworks Model Registry**.

4.  **Prediction & Monitoring:** The pipeline generates a 7-day air quality forecast and creates a "hindcast" plot by comparing the previous day's prediction with the actual measurement to monitor model performance.

5.  **Dashboard Update (GitHub Pages):** The action automatically commits the new forecast and monitoring plots to the repository, instantly updating the public-facing **dashboard** hosted on **GitHub Pages**.

In essence, after a one-time setup for historical data and initial model training, the project runs as a fully automated system where data flows from an external API, through a feature store to a model, with the final predictions published to a live dashboard—all managed by serverless CI/CD workflows.
