# Exercise 6: Beats Data Collection

## Problem
Logstash can be resource-intensive. For simple log collection on many edge servers, a lightweight "shipper" like **Filebeat** is preferred.

## Setup
Install Filebeat on the node and configure it to send system logs (`/var/log/syslog`) directly to Elasticsearch.

## Solution

### 1. Installation
On a Debian/Ubuntu system:
```bash
sudo apt-get install filebeat -y
```

### 2. Configuration
Edit `/etc/filebeat/filebeat.yml`:

```yaml
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /var/log/syslog
    - /var/log/auth.log

output.elasticsearch:
  hosts: ["localhost:9200"]
  # Optional: index name pattern
  # index: "filebeat-syslog-%{+yyyy.MM.dd}"

setup.kibana:
  host: "localhost:5601"
```

### 3. Implementation Steps
1.  Enable and start the service:
    ```bash
    sudo systemctl enable filebeat
    sudo systemctl start filebeat
    ```
2.  (Optional) Run the setup command to load default dashboards:
    ```bash
    sudo filebeat setup
    ```
3.  Verify the service status: `sudo systemctl status filebeat`.

### Expected Result
System logs are automatically streamed to Elasticsearch under the `filebeat-*` index pattern. You can view them in Kibana Discover or use the pre-built "System" dashboards if you ran the setup command.
