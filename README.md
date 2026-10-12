# Cassandra MCP demo

The repo contains docker compose file to launch the following components to demo AI agent driven performance test of Apache Cassandra.

- Apache Cassandra
- [cassandra-easy-stress](https://github.com/apache/cassandra-easy-stress)
- [Grafana LGTM](https://github.com/grafana/docker-otel-lgtm)
- [Grafana Alloy](https://github.com/grafana/alloy/)
  - to send profiles, metrics and logs to Grafana LGTM

## Prerequisite

1. Build cassandra-easy-stress container
2. Download java agents

### 1. Build cassandra-easy-stress container

`cassandra-easy-stress` has built in MCP server, however, it is not in the released binary (v9) yet.
You need to checkout the source and build the container image.

```sh
git clone https://github.com/apache/cassandra-easy-stress
cd cassandra-easy-stress
./gradlew jibDockerBuild -Djib.to.image=localhost/apache/cassandra-easy-stress:latest
# or for podman, ./gradlew jibDockerBuild -Djib.dockerClient.executable=$(which podman) -Djib.to.image=localhost/apache/cassandra-easy-stress:latest
```

### 2. Downlod java agents

The demo uses the following two java agents:
- JMX Exporter
- Pytoscope

Download the java agents for above into `cassandra` directory.

```sh
wget -P cassandra https://github.com/prometheus/jmx_exporter/releases/download/1.6.0/jmx_prometheus_javaagent-1.6.0.jar
wget -P cassandra https://github.com/grafana/pyroscope-java/releases/download/v2.9.2/pyroscope.jar
```

## Launch

```sh
docker compose up -d
```

## Set up MCP server

Set up the following MCP servers:

```json
{
  "mcpServers": {
    "cassandra-easy-stress": {
      "url": "http://localhost:9000/mcp"
    },
    "grafana": {
      "command": "uvx",
      "args": ["mcp-grafana"],
      "env": {
        "GRAFANA_URL": "http://localhost:3000"
      }
    }
  }
}
```
