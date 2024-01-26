# Delivery Demo

> IMPORTANT: At this time, this fork only supports the Docker Setup.

This bonus section showcases delivery focused metrics by leveraging the Astronomy
shop. There are two sets of delivery metrics available through this fork:

1. Leading indicators (GitHub Metrics)
2. Lagging indicators (DORA from GitHub)

Both sets of metrics are meant to be leveraged together, yet on their own, still
provide engineers with valuable data. These metrics capture point-in-time data and
are meant to be looked at overtime to determine trends of improvemenent.

There is some setup required in order to get these metrics working. You will need:

- A personal access token with access to GitHub 
- A repository setup to emit even logs from GitHub through a webhook based GitHub
app
- Some mechanism to forward webhook calls to your local machine, like ngrok

We'll detail how to setup these things up exactly in a later section.

**Table of Contents**
- [Delivery Demo](#delivery-demo)
    - [Architecture](#architecture)
    - [Pre-requisites](#pre-requisites)
        - [PAT for GitHub Metrics](#personal-access-token-for-github-metrics)
        - [GitHub App Setup](#github-app-setup-for-webhook-events)
    - [Local Docker Setup](#local-docker-setup)

## Architecture

To delivery metrics side of the house relies on the existing architecture of the
demo but adds in the [Liatrio distribution of the OpenTelemetry](https://github.com/liatrio/liatrio-otel-collector)
collector to gather metrics from GitHub through the [Git Provider Receiver](https://github.com/liatrio/liatrio-otel-collector/tree/main/receiver/gitproviderreceiver)
and the [WebHook Event Receiver](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/receiver/webhookeventreceiver)
which gathers event logs to populate DORA metrics.

```mermaid
stateDiagram-v2 
    Collector --> GitHub: Scrapes
    Collector <-- GitHub: WebHook Events
    Collector <-- Prometheus: Pulls
    Grafana --> Prometheus: Pulls
```

## Pre-requisites

### Personal Access Token for GitHub Metrics

- The Liatrio OpenTelemetry Collector images are hosted on ghcr.io see [here](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry#authenticating-with-a-personal-access-token-classic) for instructions on how to login
- The runtime Collector requires a GitHub Personal Access Token with the following configurations (Can be fine-grained or classic):
  - repo:All
  - read:packages
  - read:org
  - read:user
  - read:enterprise
  - read:project


### GitHub App Setup for WebHook Events


## Local Docker Setup

1. Clone this repository and navigate to the root
2. Create a `.liatrio-otelcol.env` with the following content:
```
GH_PAT=<github token>
GH_ORG=<github org name>
GH_SEARCH_QUERY="org:${GH_ORG} <any search parameters https://docs.github.com/en/search-github/searching-on-github>"
```
3. Run `make start-delivery`
