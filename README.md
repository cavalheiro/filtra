# Filtra

Filtra aims to extract information like lead and cycle times from Github repos for (but not limited to) projects that are doing Kanban.


# The Architecture

In its core Filtra is a Prometheus exporter that fetches all of the issues from a Github repository and provides the gathered and enriched (lead and cycle times) data as metrics. Those metrics are then scraped by Prometheus and visualized with Grafana.

## Prerequisites

To get the most out of Filtra, a baseline needs to be established.

1. The project is using Github projects aka boards with multiple columns.
2. One of the columns is for planned issues. Those are not yet "in progress".
3. There might be a blocked column for issues people can't work on.
4. Lead time starts with the creation of an issue.
5. Cycle time starts when an issue was first moved out of the column for planned issues.
6. Support issues are identified with the `l3` label.
8. Bugs are identified with the `bugs` label.

Currently the planned and blocked column can be set in the [config file](https://github.com/brejoc/filtra/blob/master/config.toml).

The labels for bugs and support issues will also soon be configurable.

## Work In Progress

The prometheus exporter is mostly done. Some metrics tweaking is still needed and additional metrics could also be gathered. Prometheus knows where to fetch the Metrics, but Grafana is not yet automagically showing any graphs. If you know how to make this happen, please ping me or open a pull request.

If you've got ideas or want ot see additional features or metrics, head over to the [issues](https://github.com/brejoc/filtra/issues).

# Hacking on Filtra

Step 1 is only is only needed for >= Go 1.10. With Go Modules dependencies are vendorized.

1. Fetching the dependencies: `go get -d -v .`
2. Running Filtra: `go run *.go`
3. Access the metrics: `http://localhost:8080/metrics`

All of the metrics we are interested in start with `gh_`.


# Deployment

You can use Docker Compose to get Filtra running. But please check the [config file](https://github.com/brejoc/filtra/blob/master/config.toml) first. You might also want to check the [Prometheus config](https://github.com/brejoc/filtra/blob/master/prometheus.yml). For testing puposes the interval is set to 60 seconds, which is probably way too often for this kind of application. You should also have your Github token exported as the environment varible `$GITHUB_TOKEN`. But of course you can also paste it into the `docker-compose.yml`.

```
mkdir prometheus_data
mkdir grafana_data
docker-compose up
```

Prometheus needs some seconds to fetch the metrics. After that you can reach Grafana at `localhost:3000'.

1. Add the Prometheus data source.
2. Add the charts you want to see. Everything should begin with `gh_`.
