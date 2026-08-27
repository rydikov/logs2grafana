# Logs and metrics to Grafana Cloud

Grafana Alloy collects Linux host metrics and the persistent systemd journal,
then sends them to Grafana Cloud Prometheus and Loki.

## Configure

Create the local environment file and fill in the Grafana Cloud credentials:

```sh
cp .env.example .env
```

Set `ALLOY_HOSTNAME` to the host name that should appear as the Prometheus
`instance` label. The default value in the example is `example-host-7f3c9a`.

Use the Prometheus and Loki usernames shown in the Grafana Cloud connection
details. `GRAFANA_CLOUD_API_KEY` must be an access policy token with permission
to write metrics and logs.

Keep `.env` untracked. Revoke any credential that has ever been committed before
publishing or sharing the repository.

## Run

```sh
docker compose pull
docker compose up -d
docker compose logs -f alloy
```

The first start imports the old journal cursor from `tmp/positions.yaml` when it
exists. Alloy stores subsequent journal positions and the Prometheus remote-write
WAL under `data/`.

Validate the running pipeline from the host. The Alloy UI and API are published
on the loopback interface only:

```sh
curl --fail http://127.0.0.1:12345/-/ready
```

Open <http://127.0.0.1:12345/graph> to inspect the component graph.

The official Alloy image supports Linux `amd64` and `arm64`. Before deploying
to a Wiren Board, check `uname -m`: `x86_64` and `aarch64` are supported, while
32-bit `armv7l` is not supported by Grafana Alloy.
