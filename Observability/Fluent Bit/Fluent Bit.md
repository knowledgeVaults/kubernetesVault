
Fluent Bit is a lightweight and high-performance telemetry agent for collecting, processing, and forwarding logs, metrics, and traces

* Uses modular plugins to form its data pipeline for collecting, processing, and forwarding telemetry data
## Modular Plugins

Modular plugins are extensible components that form the building blocks of its telemetry processing pipeline

### Service Plugins

```Text
[SERVICE]
    Flush         INTERGER                          # Interval in seconds to flush logs
    Log_Level     {error|warning|info|debug|trace}  # Logging verbosity  
    Daemon        {On|Off}                          # Run in backgroun
    Parsers_File  FILE_PATH                         # Path to the parser file
    HTTP_Server   {On|Off}                          # Enable HTTP server
    HTTP_Listen   IP_ADDRESS                        # HTTP server bind address
    HTTP_Port     PORT_NUMBER                       # HTTP server port
    
    storage.path               DIRECTORY_PATH       # Enable filesystem buffering 
    storage.sync               {normal|full}        # Sync method
    storage.checksum           {On|Off}             # Enable checksum
    storage.backlog.mem_limit  SIZE                 # Memory limit for backlog
```

### Input Plugins

Input plugins collect data from the specified sources

#### INPUT: tail

```Text
[INPUT]
    Name
    Tag
    Path
    Parser
    DB
    Mem_Buf_Limit
    Skip_Long_Lines
    Refresh_Interval
```

#### INPUT: systemd

#### INPUT: cpu

#### INPUT: mem

#### INPUT: http

### Filter Plugins

Filter plugins modify records post-input to help transform and enrich the collected data

#### FILTER: kubernetes

```Text
[FILTER]
```

#### FILTER: grep

#### FILTER: modify

#### FILTER: parser

#### FILTER: record_modifier

#### FILTER: rewrite_tag

### Output Plugins

Output plugins forward processed data to the specified destinations

```Text
[OUTPUT]
```

#### OUTPUT: es

#### OUTPUT: opensearch

#### OUTPUT: loki

#### OUTPUT: prometheus_exporter

#### OUTPUT: stdout