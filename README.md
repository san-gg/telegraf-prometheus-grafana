## Introduction
This repository provides a comprehensive monitoring solution for Host machines and Docker containers using Telegraf, Prometheus, and Grafana. Grafana offers intuitive dashboards to visualize system metrics collected by Prometheus and Telegraf. The setup includes pre-configured files to quickly deploy and visualize metrics for both Host systems and Docker containers. This combination of tools results in a powerful and efficient monitoring solution that provides complete visibility into your system's health.

## Prerequisites
Before you begin, ensure you have the following installed:
- Docker: [Install Docker](https://docs.docker.com/engine/install)
- Docker Compose: [Install Docker Compose](https://docs.docker.com/compose/install)

## Installation
Telegraf is used as a metrics data collector, while Prometheus collects metrics from the data collector and stores them in its database. Grafana is configured to query metrics using([PrompQL](https://prometheus.io/docs/prometheus/latest/querying/basics)), with the results rendered on the dashboard.

To deploy monitoring containers:
```bash
echo DOCKER_GROUP_ID=$(stat -c '%g' /var/run/docker.sock) > .env
export TELEGRAF_HOST=http://telegraf:9273
envsubst < prometheus.template.yml > prometheus.yml
docker compose --profile monitor up -d
```
Alternatively, you can deploy the data collector (i.e., Telegraf) on the target machine that needs to be monitored, and deploy Prometheus and Grafana on a different machine for metric data storage and dashboard visualization.
- To deploy Telegraf(on a target machine)<br>
```bash
echo DOCKER_GROUP_ID=$(stat -c '%g' /var/run/docker.sock) > .env
docker compose --profile telegraf up -d
```
- To deploy Prometheus and Grafana(on a different machine)<br>
Since, Telegraf is deployed to a different machine, you need to set `TELEGRAF_HOST`<br>
```bash
export TELEGRAF_HOST=<ip of the target machine>:9273
envsubst < prometheus.template.yml > prometheus.yml
docker compose --profile grafana-prometheus up -d
```
Check if containers are running and not exited.
```bash
docker compose ps
```
## Configuration
Grafana dashboard can be accessed by navigating to `http://<IP or localhost>:3000`. The default username and password are `admin`.<br><br>
Installing dashboard: -
- Navigate to `connection > Data Sources` from the panel.
- Add Prometheus as a data source.
- Specify `http://prometheus:9090` in the URL.
- Test the connection.
- Navigate to the Dashboard from the panel.
- Click on 'Create New Dashboard' and then 'Import Dashboard'.
- Visit [grafana.com Dashboard](https://grafana.com/grafana/dashboards/21740-host-system-and-docker-container-metrics).
- Click on the `Copy ID to Clipboard` button.
- Navigate to the `Import` page in Grafana and paste the ID into the `Grafana.com dashboard URL or ID` field.
- Select Datasource as prometheus
- Click on the `Load` button, then `Import` button.

<br>
Here is a screenshot of the dashboard:<br>

![overall_usage](https://github.com/user-attachments/assets/0b2be83a-4c43-47ba-8366-97f8b04a3604)

![system_usage](https://github.com/user-attachments/assets/ecd89616-6222-41b1-8cc3-b6144a4f4251)

![docker_usage_1](https://github.com/user-attachments/assets/656a3c29-c44d-40a0-a280-0253325d80b5)

![docker_usage_2](https://github.com/user-attachments/assets/05bc3e81-f5b8-4a82-898c-7b2726e5e5e9)
