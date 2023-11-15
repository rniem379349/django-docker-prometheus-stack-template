# django-docker-prometheus-stack-template
A starter template for a Django webapp with a Prometheus/Grafana stack for metrics gathering and monitoring.
Set up with docker compose. Makes it easy to start a Django project with a basic surrounding devops setup.

## Structure
- Sample Django webapp starter project (result of `django-admin startproject`), with a few tweaks to integrate `django-prometheus`, set Postgres as the database, and set up logging.
- Postgres container to use as the database for the app.
- Prometheus for metric aggregation and querying. The scrape targets are defined in `docker/prometheus/prometheus.yml`. They are:
    - Prometheus itself,
    - node_exporter (for server metrics),
    - The Django app,
    - Grafana.
- node_exporter for collecting server metrics in Prometheus, such as CPU load and memory usage.
- Prometheus' Alertmanager for setting up and dispatching metrics-based alerts. The config file can be found in `docker/prometheus/alertmanager/alertmanager.yml`, and it defines a basic alert routing tree. There are two custom alert types for web app and server alerts, which trigger different messages. For both alert types there is also a severity check, which determines whether an alert is critical or not. Depending on the alert's severity, a different alert title and message is sent through the defined channel configs.
The alerts are defined to be sent through email and Slack. The emails can be intercepted and debugged locally by MailHog, at `localhost:8025`. As for Slack, an account with webhook API URLs is needed to configure the alerts to get sent. Set up Slack webhooks, then add the URLs to the config file under `slack_configs` to route alerts to your server.
- Grafana for monitoring and visualisation of metrics. Prometheus and Loki are defined as datasources in `docker/grafana/datasources`. Additionally, a basic dashboard for monitoring the Django app is available as a JSON file in `docker/grafana/dashboards`. Simply import the dashboard in Grafana to set up the dashboard.
- Loki/Promtail for log collection. You can explore the Loki data in Grafana by navigating to the datasource and clicking on "Explore".
- Mailhog container for local email debugging.

## Deployment
### Running the project
1. Clone the repo
2. Set environment variables defined in `docker-compose.yml`, i.e. postgres connection credentials and Django secret key,
2. Run `docker compose build && docker compose up`,
3. Visit localhost to check the infrastructure. Use the ports defined in `docker-compose.yml` to navigate to specific containers.