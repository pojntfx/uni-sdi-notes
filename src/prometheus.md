# Prometheus

Prometheus is a free software application used for event monitoring and alerting[^note].

```shell
ssh jean@sdi-1.alphahorizon.io

sudo apt update
sudo apt install -y prometheus

echo 'ARGS="--web.listen-address=localhost:9091"' | sudo tee /etc/default/prometheus

sudo systemctl enable --now prometheus

sudo tee /etc/prometheus/prometheus.yml <<'EOT'
global:
  scrape_interval:     15s
  evaluation_interval: 15s
  external_labels:
      monitor: 'example'

alerting:
  alertmanagers:
  - static_configs:
    - targets: ['localhost:9093']

rule_files:

scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    scrape_timeout: 5s
    static_configs:
      - targets: ['localhost:9090']

  - job_name: node
    static_configs:
      - targets: ['localhost:9100']

  - job_name: traefik
    static_configs:
      - targets: ['localhost:8082']
EOT

sudo systemctl reload prometheus
```

The queries can now be accessed using e.g. `cURL`:

```shell
curl http://localhost:9091/metrics # Test Prometheus
# HELP go_gc_duration_seconds A summary of the pause duration of garbage collection cycles.
# TYPE go_gc_duration_seconds summary
go_gc_duration_seconds{quantile="0"} 5.9355e-05
go_gc_duration_seconds{quantile="0.25"} 8.7103e-05
go_gc_duration_seconds{quantile="0.5"} 0.000102003
go_gc_duration_seconds{quantile="0.75"} 0.000121168
go_gc_duration_seconds{quantile="1"} 0.000503512
go_gc_duration_seconds_sum 0.061645168
go_gc_duration_seconds_count 565
# HELP go_goroutines Number of goroutines that currently exist.
# TYPE go_goroutines gauge
go_goroutines 39
# HELP go_info Information about the Go environment.
# TYPE go_info gauge
go_info{version="go1.15.9"} 1
# HELP go_memstats_alloc_bytes Number of bytes allocated and still in use.
# TYPE go_memstats_alloc_bytes gauge
go_memstats_alloc_bytes 1.9233688e+07
# ...
```

[^note]: From Wikipedia, last checked 2022-02-19 ([https://en.wikipedia.org/wiki/Prometheus\_(software)](<https://en.wikipedia.org/wiki/Prometheus_(software)>))
