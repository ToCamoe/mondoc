Monitoring in Docker with compose
========

## For start need some things changes

### Containers:

* Grafana (visualize metrics) `http://<host-ip>:3000`
* Loki (providing logs of running docker containers) `http://<host-ip>:3100`
* Prometheus (metrics database) `http://<host-ip>:9090`
* Prometheus-Pushgateway (push acceptor for ephemeral and batch jobs) `http://<host-ip>:9091`
* AlertManager (alerts management) `http://<host-ip>:9093`
* NodeExporter (host metrics collector)
* cAdvisor (containers metrics collector)
* Caddy (reverse proxy and basic auth provider for prometheus and alertmanager)

## Install

### Change .env:
```
USER=admin  
PASSWORD=admin
```

### Loki - Docker Driver Client

By default the parameter is enabled in the docker compose file, but to make it work you need to install the driver and make changes in the docker.

```
docker plugin install grafana/loki-docker-driver:latest --alias loki --grant-all-permissions
```

If you want the Loki logging driver to be the default for all containers, change Docker’s daemon.json file (located in /etc/docker on Linux) and set the value of log-driver to loki:
```yaml
{
  "debug": true,
  "log-driver": "loki"
}
```

## Setup alerting

* Rocket Chat is supposed to be used by default, but additional configuration is needed

The AlertManager service is responsible for handling alerts sent by Prometheus server, by accessing `http://<host-ip>:9093`.

The notification receivers can be configured in alertmanager/config.yml file.

More details on Slack, Pagerduty and Gmail integration [here](https://grafana.com/blog/2020/02/25/step-by-step-guide-to-setting-up-prometheus-alertmanager-with-slack-pagerduty-and-gmail/).

Details on RocketChat integration [here](https://docs.rocket.chat/guides/administration/admin-panel/integrations/prometheus).

A complete list of integrations can be found [here](https://prometheus.io/docs/alerting/configuration).


## Sending metrics to the Pushgateway

The [pushgateway](https://github.com/prometheus/pushgateway) is used to collect data from batch jobs or from services. [Examples](https://github.com/prometheus/pushgateway#command-line)

## Run

```
docker compose up -d
```
* The appearance of the start command depends on the variation of the docker compose installation

## Remove

```
docker compose down -v --rmi all
```