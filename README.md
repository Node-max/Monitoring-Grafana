**1. Insal node nolus**
<img width="476" alt="image" src="https://user-images.githubusercontent.com/61777095/224585673-69d8196d-98e6-417b-8ff4-021f727bcc5b.png">
**2. Install monitoring stack**
install the monitoring using the auto installer from kjnode
```python
wget -O install_monitoring.sh https://raw.githubusercontent.com/kj89/cosmos_node_monitoring/master/install_monitoring.sh && chmod +x install_monitoring.sh && ./install_monitoring.sh
```
**3. Setting Config Node**
a. Changing Node Configs
<img width="418" alt="image" src="https://user-images.githubusercontent.com/61777095/224708362-775aef6e-9c45-469e-940a-1fcc8f0162e5.png">

**Follow the instructions below**
- **Step into**
```python
nano .nolus/config/config.toml
```
- **Change prometheus = false to true**
- **Dan simpan baik baik prometheus_listen_addr = ":43660"**
- **Then restart the nolus node using the following command**
```python
sudo systemctl restart nolusd
```
**4. Changing the Prometheus Config**
**a. Follow the instructions below**
**b. Bress on the keyboard cd**
**c. Then enter the following command**
```python
nano cosmos_node_monitoring/prometheus/prometheus.yml
```
<img width="439" alt="image" src="https://user-images.githubusercontent.com/61777095/224709583-2baf92eb-e3e5-4ded-9629-8582a7dab623.png">
**delete all contents by pressing on the keyboard SHIFT+DOWN ARROW for the block code then pressing on the keyboard CTRL+K**
<img width="452" alt="image" src="https://user-images.githubusercontent.com/61777095/224709661-a78af651-7da2-4d3d-b11e-12cf82e88fad.png">
**if it has been deleted, change it with the following config**
```python
global:
  scrape_interval:     15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['<your IP>:9090']
  - job_name: 'node_exporter'
    scrape_interval: 5s
    static_configs:
      - targets: ['<your IP>:9100']
  - job_name: "nolus-testnet"
    metrics_path: '/metrics'
    static_configs:
      - targets: ["<your IP>:43660"] # custom with your own!
```
**if you have pressed on the keyboard CTRL + X then press Y then ENTER**
**5. Enable Firewalls**
**a. If you don't have a firewall installed, install the ufw firewall with the command:**
```python
sudo apt-get install ufw
```
**b. Create firewall rules to allow incoming traffic on ports 9100, 43660, and 9090 with the command:**
```python
sudo ufw allow 9100/tcp
sudo ufw allow 43660/tcp
sudo ufw allow 9090/tcp
```
The above command will create a new firewall rule that allows incoming traffic on the specified port.
**c. Activate ufw with the command:**
```python
sudo ufw enable
```
The above command will enable ufw and start the firewall.

**6. Change the docker-compose.yml Config**
At this stage we will change the docker-compose.yml config to disable the login feature
**a. Make sure you are outside the folder by typing cd then enter the following command**
```python
nano cosmos_node_monitoring/docker-compose.yml
```
**b. then delete using the keyboard SHIFT+DIRECTION ARROW DOWN for the block code then press on the keyboard CTRL+K**
```python
version: "3.5"

volumes:
  prometheus_data: {}
  grafana_data: {}
  alerta_db: {}

networks:
  cosmos-monitoring:

services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    volumes:
       - type: volume
         source: prometheus_data
         target: /prometheus
       - type: bind
         source: ./prometheus
         target: /etc/prometheus
         read_only: true
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.enable-lifecycle'
    ports:
      - 9090:9090
    networks:
      - cosmos-monitoring
    restart: always

  # default login credentials: admin/admin
  grafana:
    image: grafana/grafana:latest
    env_file: ./grafana/grafana.conf
    container_name: grafana
    volumes:
      - type: volume
        source: grafana_data
        target: /var/lib/grafana
      - ./grafana/datasource.yml:/etc/grafana/provisioning/datasources/datasource.yml
      - ./grafana/dashboards:/etc/grafana/provisioning/dashboards
    ports:
      - 9999:3000
    networks:
      - cosmos-monitoring
    restart: always

    environment:
      - GF_AUTH_ANONYMOUS_ENABLED=true
```
**c. if you have pressed on the keyboard CTRL + X then press Y then ENTER**
**7. Run Docker**
<img width="473" alt="image" src="https://user-images.githubusercontent.com/61777095/224712170-30916343-2d79-4196-98a3-8478e594291b.png">

Run docker using the following command
```python
cd $HOME/cosmos_node_monitoring
sudo docker compose up -d
```
**8. Make sure your prometheus is green**
<img width="478" alt="image" src="https://user-images.githubusercontent.com/61777095/224712486-0322b623-b295-4a88-a142-f2bb0e523442.png">

To enter Prometheus you can change the following domains, make sure everything is green!
```python
http://ipmaxnode:9090/targets?search=
```
**8. Create a Monitor Dashboard**
Grafana login
```python
http://ipmaxnode:9999/login
```
<img width="312" alt="image" src="https://user-images.githubusercontent.com/61777095/224713244-05b9bb85-d761-4db9-b861-9b157ebc6c6c.png">

Enter username and password using admin

<img width="403" alt="image" src="https://user-images.githubusercontent.com/61777095/224713355-cca6a48a-44ae-4cda-b629-a235c9270367.png">

**Create data sources**
**Please read this carefully, please**
<img width="478" alt="image" src="https://user-images.githubusercontent.com/61777095/224713586-86ee4b2f-d764-47ef-9dce-685c67a54a57.png">
```
<img width="473" alt="image" src="https://user-images.githubusercontent.com/61777095/224713640-781ab001-9504-4c68-8bce-d38fbeecf964.png">
```
<img width="470" alt="image" src="https://user-images.githubusercontent.com/61777095/224713691-f83e6834-47e2-49ae-a405-14fedaa45ad4.png">









