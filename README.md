# ELK Stack Hands-On Study Project

Welcome to the ELK Stack Lab! This project is designed to help you learn how to deploy, configure, and use the Elastic Stack (Elasticsearch, Logstash, and Kibana) for log management and data visualization.

## 1. Overview of the ELK Stack

The **ELK Stack** is a powerful trio of open-source tools used for searching, analyzing, and visualizing data in real-time.

*   **Elasticsearch**: A distributed, RESTful search and analytics engine. It stores your data and provides near real-time search capabilities.
*   **Logstash**: A server-side data processing pipeline that ingests data from multiple sources, transforms it, and then sends it to your "stash" (like Elasticsearch).
*   **Kibana**: A browser-based user interface that lets you visualize your Elasticsearch data through charts, graphs, and dashboards.

### Real-World Use Cases
*   **Server Monitoring**: Tracking CPU, memory, and disk usage across a fleet of servers.
*   **Log Analysis**: Centralizing logs from web servers, applications, and databases to identify errors or performance bottlenecks.
*   **Security Auditing**: Detecting failed login attempts or unusual network traffic patterns.

---

## 2. Prerequisites

Before starting, ensure you have the following installed on your host machine:
*   [Vagrant](https://www.vagrantup.com/downloads)
*   [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
*   At least 8GB of physical RAM (the VM will use 4GB).

---

## 3. Getting Started with Vagrant

### Step 1: Initialize the Project
The project files are already provided. Navigate to the project directory in your terminal:
```bash
cd elk-playbook
```

### Step 2: Understand the Vagrantfile
The `Vagrantfile` defines two virtual machines:
1.  **`elk-node` (192.168.56.10)**:
    *   **Resources**: 4GB RAM, 2 CPUs.
    *   **Provisioning**: Installs Elasticsearch, Logstash, and Kibana.
2.  **`php-app` (192.168.56.20)**:
    *   **Resources**: 1GB RAM, 1 CPU.
    *   **Provisioning**: Installs Nginx and PHP-FPM with a sample JSON API.

### Step 3: Start the VMs
Run the following command to create and provision both nodes:
```bash
vagrant up
```
*Note: This may take several minutes as it downloads the OS image and installs all components.*

### Step 4: Access the VMs
You can SSH into either node by specifying its name:
```bash
vagrant ssh elk-node
# OR
vagrant ssh php-app
```

---

## 4. Manual Installation & Configuration (Inside VM)

Although the Vagrantfile automates the installation, here is how you would do it manually or verify the setup.

### Elasticsearch
Elasticsearch is the heart of the stack.
*   **Config File**: `/etc/elasticsearch/elasticsearch.yml`
*   **Verify Service**:
    ```bash
    curl -X GET "localhost:9200"
    ```

### Kibana
Kibana provides the UI.
*   **Config File**: `/etc/kibana/kibana.yml`
*   **Access**: Open `http://192.168.56.10:5601` in your host's browser.

### Logstash
Logstash handles the data pipeline.
*   **Config Directory**: `/etc/logstash/conf.d/`
*   **Pipeline Structure**:
    1.  **Input**: Where data comes from (e.g., a file, syslog).
    2.  **Filter**: How to parse and clean data (e.g., Grok, Mutate).
    3.  **Output**: Where to send data (e.g., Elasticsearch).

---

## 5. Practical Lab: Data Ingestion

Let's create a simple Logstash pipeline to ingest custom logs.

### Step 1: Create a Sample Log File
```bash
echo "2023-10-27 10:00:01 ERROR User authentication failed" >> /tmp/app.log
echo "2023-10-27 10:05:20 INFO User logged in successfully" >> /tmp/app.log
```

### Step 2: Configure Logstash
Create a new configuration file:
```bash
sudo nano /etc/logstash/conf.d/01-app-logs.conf
```
Add the following content:
```conf
input {
  file {
    path => "/tmp/app.log"
    start_position => "beginning"
  }
}

filter {
  if [message] =~ /ERROR/ {
    mutate { add_tag => ["error_log"] }
  }
}

output {
  elasticsearch {
    hosts => ["http://localhost:9200"]
    index => "app-logs-%{+YYYY.MM.dd}"
  }
  stdout { codec => rubydebug }
}
```

### Step 3: Start Logstash
```bash
sudo systemctl restart logstash
```

---

## 6. Real-Life Scenario Exercises

Ready to put your knowledge into practice? We have prepared several real-world scenarios for you to work through, including web server monitoring, security auditing, and GELF log ingestion.

👉 **[View the Lab Exercises here](./exercises/LAB_EXERCISES.md)**

---

## 7. Troubleshooting

*   **Services not starting?** Check logs: `sudo journalctl -u elasticsearch`.
*   **Cannot access Kibana?** Ensure the VM IP `192.168.56.10` is reachable and `server.host` is set to `0.0.0.0` in `kibana.yml`.
*   **Elasticsearch out of memory?** Increase VM RAM in `Vagrantfile` or adjust JVM heap in `/etc/elasticsearch/jvm.options.d/heap.options`.

## 8. Next Steps
*   Explore **Beats** (Filebeat, Metricbeat) for lightweight data shipping.
*   Learn **Grok Patterns** for advanced log parsing.
*   Implement **Index Lifecycle Management (ILM)** to handle data retention.
