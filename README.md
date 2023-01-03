# Beholder

The aim of this repository is to be a drop in replacement of [Datadog's DogStatsD agent](https://docs.datadoghq.com/agent/) on the local development environment in order to be able to check the metrics we are sending before actually pushing them.

## Why I need a drop in replacement for Datadog's agent?

To be able to:

* Check the number of metrics we are sending to Datadog. As every application metric with a unique tags combination is a custom one, and we are charged for them, this is a good practice.
* Inspect the name, type, and values, in order to be sure we are sending valid samples.
* Validate the application behaviour and improve its observability by checking its metrics.

## How it works?

Just execute `docker-compose up -d` and then run your application making the datadog integration point to `localhost` (the port is the same, 8125).

Metrics scrapping happens every 5s. After that, you can check them in [http://localhost:3000/explore](http://localhost:3000/explore), selecting the `Prometheus` datasource, or using the [Metrics overview dashboard](http://localhost:3000/d/metrics-overview) provided.

### Metric name adjustments

Given `prometheus`, the time series database used, doesn't allow metric names with dots (`.`) as DataDog does, `telegraf` automatically translate those dots to `_` when exporting metrics to prometheus format.

### How can I debug it?

From `bash`, you can execute the following command and send a custom metric to the `statsd` daemon:

```
echo -n "is_alive:1|g|#hostname:${HOSTNAME}" >/dev/udp/localhost/8125
```

After 5 seconds, `prometheus` will scrape `telegraf` metrics and it should be shown in the grafana explore endpoint (see [above](#how-it-works)).

If you need a fine grained debug, check [`telegraf` exposed metrics](http://localhost:9089/metrics), or directly [query `prometheus`](http://localhost:9090/graph).

Check [documentation about PromQL to query prometheus](https://prometheus.io/docs/prometheus/latest/querying/basics/).
