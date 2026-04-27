# Exercise 7: Grok Advanced Parsing

## Problem
You have a custom application log that uses a non-standard format:
`[2023-10-27 12:00:00] [PAYMENT] [INFO] User 123 paid $50.00`

You need to extract the timestamp, module name, log level, user ID, and payment amount (as a number).

## Setup
Create a Logstash filter that uses a custom Grok pattern to parse this specific line format.

## Solution

### 1. Logstash Configuration
Create `/etc/logstash/conf.d/07-custom-grok.conf`:

```conf
input {
  stdin { }
}

filter {
  grok {
    match => { "message" => "\[%{TIMESTAMP_ISO8601:timestamp}\] \[%{WORD:module}\] \[%{LOGLEVEL:level}\] User %{INT:user_id:int} paid \$%{NUMBER:amount:float}" }
  }
}

output {
  stdout { codec => rubydebug }
}
```

### 2. Implementation Steps
1.  Run Logstash manually to test the pattern:
    ```bash
    /usr/share/logstash/bin/logstash -f /etc/logstash/conf.d/07-custom-grok.conf
    ```
2.  Once it starts, paste the log line:
    `[2023-10-27 12:00:00] [PAYMENT] [INFO] User 123 paid $50.00`
3.  Observe the structured output in the terminal.

### 3. Key Grok Patterns Used
*   `\[` and `\]`: Escaped brackets to match literal brackets in the log.
*   `%{TIMESTAMP_ISO8601:timestamp}`: Extracts the date/time.
*   `%{INT:user_id:int}`: Extracts the integer and casts it to an actual integer type in Elasticsearch.
*   `%{NUMBER:amount:float}`: Extracts the decimal and casts it to a float.

### Expected Result
The output should contain separate fields:
```json
{
  "timestamp": "2023-10-27 12:00:00",
  "module": "PAYMENT",
  "level": "INFO",
  "user_id": 123,
  "amount": 50.0,
  ...
}
```
