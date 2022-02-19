# Prometheus

Prometheus is a free software application used for event monitoring and alerting[^note].

[^note]: From Wikipedia, last checked 2022-02-19 ([https://en.wikipedia.org/wiki/Prometheus\_(software)](<https://en.wikipedia.org/wiki/Prometheus_(software)>))

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

curl http://localhost:9091/metrics # Test Prometheus
```
