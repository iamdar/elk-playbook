# Exercise 3: Application Error Tracking

## Problem
Developers need to see full stack traces from a Java application. By default, Logstash treats every line as a separate event, which breaks multi-line stack traces.

## Setup
1.  **Requirement**: Use the `multiline` codec in the Logstash input plugin.
2.  **Logic**: Merge lines that do not start with a timestamp into the previous event.

## Solution

### 1. Logstash Configuration
Create `/etc/logstash/conf.d/03-app-errors.conf`:

```conf
input {
  file {
    path => "/var/log/app/*.log"
    codec => multiline {
      pattern => "^%{TIMESTAMP_ISO8601}"
      negate => true
      what => "previous"
    }
  }
}

output {
  elasticsearch {
    hosts => ["http://localhost:9200"]
    index => "app-errors-%{+YYYY.MM.dd}"
  }
}
```

### 2. Explanation
*   `pattern => "^%{TIMESTAMP_ISO8601}"`: Look for lines starting with a timestamp.
*   `negate => true`: Apply the logic to lines that **do not** match the pattern.
*   `what => "previous"`: Merge these non-matching lines into the **previous** line that did match.

### 3. Expected Result
Searchable error logs in Kibana where a single "event" contains the entire multi-line stack trace, allowing developers to see the full context of an error.
