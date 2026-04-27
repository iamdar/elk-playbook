# Exercise 1: Web Server Log Monitoring

## Problem
Monitor Apache/Nginx logs for `404 Not Found` errors and visualize the distribution of HTTP status codes.

## Setup
1.  **Install Nginx**: `sudo apt install nginx -y`
2.  **Generate Logs**: Visit `http://192.168.56.10` and some non-existent pages like `http://192.168.56.10/missing` to generate 200 and 404 status codes.
3.  **Configure Logstash**: Create a configuration to read `/var/log/nginx/access.log`.

## Solution

### 1. Logstash Configuration
Create `/etc/logstash/conf.d/01-nginx.conf`:

```conf
input {
  file {
    path => "/var/log/nginx/access.log"
    start_position => "beginning"
    sincedb_path => "/dev/null"
  }
}

filter {
  grok {
    match => { "message" => "%{HTTPD_COMBINEDLOG}" }
  }
  mutate {
    convert => { "response" => "integer" }
  }
}

output {
  elasticsearch {
    hosts => ["http://localhost:9200"]
    index => "nginx-access-%{+YYYY.MM.dd}"
  }
  stdout { codec => rubydebug }
}
```

### 2. Implementation Steps
1.  Save the config and restart Logstash: `sudo systemctl restart logstash`.
2.  Check Logstash logs to ensure it's processing: `sudo tail -f /var/log/logstash/logstash-plain.log`.
3.  Go to **Kibana > Stack Management > Index Patterns** and create an index pattern for `nginx-access-*`.
4.  Go to **Kibana > Discover** to see the parsed logs.

### 3. Expected Result
A Kibana dashboard showing a pie chart of HTTP status codes (200s vs 404s). You can use the `response` field for the "Slice by" bucket.
