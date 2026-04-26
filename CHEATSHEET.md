# ELK Stack Quick Reference Cheatsheet

## 🚀 Vagrant Commands
| Command | Description |
| :--- | :--- |
| `vagrant up` | Start and provision the VM |
| `vagrant ssh` | Log into the VM |
| `vagrant halt` | Gracefully shut down the VM |
| `vagrant destroy` | Delete the VM and all its data |
| `vagrant status` | Check if the VM is running |

## 🐧 Linux Service Management
| Command | Description |
| :--- | :--- |
| `sudo systemctl start <service>` | Start a service (elasticsearch, kibana, logstash) |
| `sudo systemctl stop <service>` | Stop a service |
| `sudo systemctl restart <service>` | Restart a service |
| `sudo systemctl status <service>` | Check service health |
| `sudo journalctl -u <service> -f` | Tail service logs in real-time |

## 🔍 Elasticsearch API (Curl)
*All commands assumed to be run inside the VM.*

| Action | Command |
| :--- | :--- |
| **Cluster Health** | `curl -X GET "localhost:9200/_cluster/health?pretty"` |
| **List Indices** | `curl -X GET "localhost:9200/_cat/indices?v"` |
| **Create Index** | `curl -X PUT "localhost:9200/my-new-index?pretty"` |
| **Search Data** | `curl -X GET "localhost:9200/<index_name>/_search?pretty"` |
| **Delete Index** | `curl -X DELETE "localhost:9200/<index_name>"` |

## 🪵 Logstash Basics
*   **Test Config**: `sudo /usr/share/logstash/bin/logstash -f /etc/logstash/conf.d/01-app-logs.conf --config.test_and_exit`
*   **Run Logstash manually**: `sudo /usr/share/logstash/bin/logstash -f /etc/logstash/conf.d/01-app-logs.conf`

## 🎨 Kibana Tips
*   **URL**: `http://192.168.56.10:5601`
*   **Dev Tools**: Use the Console in Kibana (Management -> Dev Tools) to run Elasticsearch queries without `curl`.
*   **Index Patterns**: You must create an **Index Pattern** in Stack Management before you can see data in the **Discover** tab.

## 📂 Common File Paths
| Component | Configuration File | Log Directory |
| :--- | :--- | :--- |
| **Elasticsearch** | `/etc/elasticsearch/elasticsearch.yml` | `/var/log/elasticsearch/` |
| **Kibana** | `/etc/kibana/kibana.yml` | `/var/log/kibana/` |
| **Logstash** | `/etc/logstash/logstash.yml` | `/var/log/logstash/` |
| **JVM Options** | `/etc/elasticsearch/jvm.options` | N/A |

## 🔌 Default Ports
*   **Elasticsearch**: `9200` (API), `9300` (Nodes communication)
*   **Kibana**: `5601` (Web UI)
*   **Logstash**: `5044` (Beats input), `12201` (GELF UDP), `9600` (Monitoring API)

