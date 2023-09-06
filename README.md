# Zimmerman Central Logging
In this project, we aim to introduce centralised logging.

This repository is built with docker in mind: We want to be able export all docker logs,
alongside our NGINX logs, as we use NGINX for virtually every server we host.

### Services
This repository contains a docker compose file which runs:
 - Grafana Loki (read/write): Loki is a log aggregation system designed to store and query logs from all your applications and infrastructure.
 - Promtail: Promtail is an agent which ships the contents of local logs to a private Grafana Loki instance.
 - Minio: A local storage for Loki.
 - Prometheus: Used to monitor the local system.
 - Node Exporter: Used to export the local system for Prometheus.
 - Grafana: The visual interface.
 - NGINX: A local gateway for the different tools in the system.

### Default setup
For Grafana, we include two data sources: Loki and Prometheus, with which we provide these dashboards:
 - System Stats: An overview of how the local server is performing.
 - NGINX Logs - Today: A quick view of today's NGINX `Access` and `Error` logs.
 - NGINX Logs - Old: A quick view of older NGINX `Access` and `Error` logs.

On a project case-by-case basis, we can add more dashboards based on the tools that are available.

### Docker containers
Any docker container logs can be accessed with the label filter `container`, and the value is the name of the container. 
For example: In the case of the monitoring gateway included in this project's services, it is `{container="monitoring-gateway-1"}`

For log files, they can be accessed with either their filename as the labelfilter, or the job that executes retrieves the data.For example `{job="nginx_access_today"}` or `{filename="/var/log/nginx/access.log"}`, or a combination of both with `{filename=~"/var/log/nginx/access\\.log|/var/log/nginx/access\\.log\\.1"}`.

## Supported projects
We create separate project branches which are based off of `main`, for different projects. The DX project has different docker containers compared to the AIDA project. This means we can have our 'standard' configuration available for all of our projects in the `main` branch, and introduce custom additions for projects in their respective branches.

Currently supported dockerised projects are:
 - [Grafana](https://github.com/zimmerman-team/Central-Logging) - at branch: `main`
 - [DataXplorer](https://github.com/zimmerman-team/DX) - at branch: `project/dx`

In these projects, this logging project should be used as a submodule, pointing to the correct branch.

## The Setup
The setup should be simple:

Make sure your .env is set up, there is a `.env.example` file which you can use with:
```
cp .env.example .env
```
Make sure to change the password, and username if desired.

Select the desired branch.
```
sudo docker compose up -d
```

## DX Custom:
We have added dashboards for DX Docker images and DX Internal log files.

The port to the Grafana interface is changed to 3003, as DX client will run on 3000.

Local DX internal log files are also mounted volumes for the promtail service.
