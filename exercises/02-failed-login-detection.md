# Exercise 2: Failed Login Detection

## Problem
Identify potential brute-force attacks on SSH by monitoring authentication logs.

## Setup
1.  **Source Data**: Logstash will read from `/var/log/auth.log`.
2.  **Filter**: Look for the string "Failed password".

## Solution

### 1. Logstash Configuration
Create `/etc/logstash/conf.d/02-ssh-auth.conf`:

```conf
input {
  file {
    path => "/var/log/auth.log"
    start_position => "beginning"
  }
}

filter {
  if "Failed password" in [message] {
    grok {
      match => { "message" => "Failed password for %{USER:username} from %{IP:src_ip} port %{NUMBER:port} ssh2" }
    }
    add_tag => [ "ssh_brute_force" ]
  } else {
    drop { }
  }
}

output {
  elasticsearch {
    hosts => ["http://localhost:9200"]
    index => "ssh-auth-%{+YYYY.MM.dd}"
  }
  stdout { codec => rubydebug }
}
```

### 2. Implementation Steps
1.  Generate some failed logins: `ssh non-existent-user@localhost`.
2.  Restart Logstash: `sudo systemctl restart logstash`.
3.  In Kibana, create an index pattern for `ssh-auth-*`.

### 3. Expected Result
An alert or visualization in Kibana (e.g., a Data Table) showing the top `src_ip` addresses and `username`s attempting failed logins.
