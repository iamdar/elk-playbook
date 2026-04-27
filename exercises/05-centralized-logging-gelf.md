# Exercise 5: Centralized Logging with GELF

## Problem
Ingest logs from applications using the Graylog Extended Log Format (GELF). This is common for Docker container logs.

## Setup
Create a Logstash configuration using the `gelf` input plugin on UDP port 12201.

## Solution

### 1. Logstash Configuration
Create `/etc/logstash/conf.d/05-gelf.conf`:

```conf
input {
  gelf {
    port => 12201
    type => "gelf-logs"
  }
}

output {
  elasticsearch {
    hosts => ["http://localhost:9200"]
    index => "gelf-logs-%{+YYYY.MM.dd}"
  }
  stdout { codec => rubydebug }
}
```

### 2. Testing the Ingestion
You can use `netcat` (nc) to send a test UDP packet to Logstash. Logstash expects a null-terminated JSON string or a GELF compressed packet. For simplicity, here is a JSON payload:

```bash
echo -n -e '{"version": "1.1", "host": "example.org", "short_message": "A short message", "full_message": "Backtrace here\n\nmore stuff", "level": 1, "_user_id": 9001}' | nc -u -w 1 localhost 12201
```

### 3. Implementation Steps
1.  Restart Logstash to apply the new config: `sudo systemctl restart logstash`.
2.  Send the test message using the `nc` command above.
3.  Verify in Kibana by creating an index pattern for `gelf-logs-*`.

### Expected Result
Applications or containers sending GELF packets to port `12201` will have their structured fields automatically parsed and indexed in Elasticsearch.
