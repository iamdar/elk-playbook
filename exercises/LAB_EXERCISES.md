# ELK Stack Lab Exercises

This document contains practical, hands-on scenarios to help you master the ELK Stack.

## Exercise 1: Web Server Log Monitoring
*   **Problem**: Monitor Apache/Nginx logs for `404 Not Found` errors.
*   **Setup**: 
    1. Install Nginx: `sudo apt install nginx`
    2. Generate some logs by visiting `http://192.168.56.10` and some non-existent pages like `http://192.168.56.10/missing`.
    3. Configure Logstash to read `/var/log/nginx/access.log`.
*   **Expected Result**: A Kibana dashboard showing a pie chart of HTTP status codes (200s vs 404s).

## Exercise 2: Failed Login Detection
*   **Problem**: Identify brute-force attacks on SSH.
*   **Setup**: 
    1. Point Logstash to `/var/log/auth.log`.
    2. Use a filter to look for the string "Failed password".
*   **Expected Result**: An alert or visualization showing the top IP addresses attempting failed logins.

## Exercise 3: Application Error Tracking
*   **Problem**: Developers need to see stack traces from a Java application.
*   **Setup**: 
    1. Use the `multiline` codec in the Logstash input plugin.
    2. Configure it to merge lines that do not start with a timestamp into the previous event.
*   **Expected Result**: Searchable error logs in Kibana where a single "event" contains the entire multi-line stack trace.

## Exercise 4: Direct Data Ingestion (Elasticsearch API)
*   **Problem**: Quickly push a single JSON document to Elasticsearch without using Logstash or Beats.
*   **Setup**: Use `curl` to send a POST request to the Elasticsearch `_doc` endpoint.
    ```bash
    curl -X POST "localhost:9200/my-manual-index/_doc/" -H 'Content-Type: application/json' -d'
    {
      "timestamp": "2023-10-27T12:00:00",
      "user": "jdoe",
      "action": "manual_upload",
      "status": "success"
    }'
    ```
*   **Expected Result**: The document is immediately searchable in Kibana or via `curl -X GET "localhost:9200/my-manual-index/_search?pretty"`.

## Exercise 5: Centralized Logging with GELF
*   **Problem**: Ingest logs from Docker containers or applications using the Graylog Extended Log Format (GELF).
*   **Setup**: Create a Logstash configuration using the `gelf` input plugin.
    ```conf
    # /etc/logstash/conf.d/02-gelf.conf
    input {
      gelf {
        port => 12201
        type => "gelf-logs"
      }
    }
    output {
      elasticsearch {
        hosts => ["http://localhost:9200"]
        index => "gelf-%{+YYYY.MM.dd}"
      }
    }
    ```
*   **Expected Result**: Applications sending UDP packets to port `12201` will have their structured logs automatically indexed.
