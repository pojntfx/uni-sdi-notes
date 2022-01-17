# Prometheus

```shell
sudo apt update
sudo apt install -y prometheus

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