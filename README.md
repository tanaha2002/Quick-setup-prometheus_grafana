# Quick-setup-prometheus-grafana
This is quick guide to setup the prometheus + grafana docker container


### Step 1
First, we need to create a prometheus.yml for config it

`mkdir prometheus`

then `nano prometheus/prometheus.yml`:

```
global:
  scrape_interval: 1m

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
  - job_name: 'node'
    static_configs:
      - targets: ['192.168.101.30:9100']
```

Now save and exit.

### Step 2
Create a `docker-compose.yml`:
```
version: '3.8'

volumes:
  grafana-data: {}
  prometheus-data: {}
  

services:
  node_exporter:
    image: quay.io/prometheus/node-exporter:latest
    container_name: node_exporter
    command:
      - '--path.rootfs=/host'
    network_mode: host
    pid: host
    restart: unless-stopped
    volumes:
      - '/:/host:ro,rslave'
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    ports:
      - '9090:9090'
    restart: unless-stopped
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus-data:/prometheus
    command:
      - '--web.enable-lifecycle'
      - '--config.file=/etc/prometheus/prometheus.yml'
  

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    ports:
      - 3001:3000

    restart: unless-stopped
    volumes:
      - grafana-data:/var/lib/grafana
    depends_on:
      - prometheus

```

You can change port `3001` to whatever port you want to forward, for me my current port `3000` using for another application then i need to forward it to `3001`.

Let save and exit it.

### Step 3
Ok now you can run the command:

`docker compose up -d` to create the container. That all, just need a little bit of time to run all of it.
