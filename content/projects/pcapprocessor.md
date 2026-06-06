---
title: "pcapprocessor"
date: 2026-06-06
description: "A Python toolkit for running ns-3 simulations, extracting TCP metrics from pcap traces, and generating publication-quality figures."
tags: ["python", "networking", "open-source", "pcap", "ns-3"]
---

[pcapprocessor](https://github.com/lordlabakdas/pcapprocessor) is a Python toolkit that automates the full pipeline from ns-3 network simulation to publication-ready figures. Available on [PyPI](https://pypi.org/project/pcapprocessor/).

```bash
pip install pcapprocessor
```

## What it does

Given a config file describing simulation parameters, pcapprocessor:

1. Runs multiple simulation replications via the ns-3 WAF build system
2. Parses each pcap output with pyshark to extract per-flow TCP metrics
3. Aggregates metrics across runs (mean, std dev, 95% confidence intervals)
4. Writes results to tab-separated CSV files — one per protocol/flow
5. Generates matplotlib figures with confidence interval error bars

## Metrics

Extracts 12 TCP-level metrics per flow including throughput, one-way delay, goodput, link utilization, retransmitted packets, queue occupancy, and flow completion time.

## Pipeline

```
Config (.ini) → BfsRunner → ns-3 simulation → pcap traces
                                                    ↓
                                            TraceProcessor (pyshark)
                                                    ↓
                                          MetricStats + CI aggregation
                                                    ↓
                                           MetricsWriter → CSV files
                                                    ↓
                                            Reporter → PNG/SVG plots
```

## Usage

```python
from pcapprocessor import BfsRunner

runner = BfsRunner(config_file="sim.ini", scenario="bottleneckDelay")
runner.run()
# Writes results/metrics_tcp0.csv, results/metrics_udp0.csv, ...
```

Generate figures from existing CSVs:

```python
from pcapprocessor import Reporter

Reporter(
    csvs=["results/metrics_tcp0.csv", "results/metrics_udp0.csv"],
    metrics=["throughput", "delay", "utilization"],
    output_dir="plots",
).plot()
```

## Links

- [GitHub](https://github.com/lordlabakdas/pcapprocessor)
- [PyPI](https://pypi.org/project/pcapprocessor/)
