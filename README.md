# File statistics exporter

> Source: https://github.com/michael-doubez/filestat_exporter

Prometheus exporter gathering metrics about file size, modification time and other statistics.

### Usage

Configure target files on command line, passing glob patterns in parameters

```
./filestat_exporter '*'
```

Optional flags:
* __`-config.file <yaml>`:__ The path to the configuration file (use "none" to disable).
* __`-log.level <level>`:__ Logging level \[debug, info, warn, error\]. (default: `info`)
* __`-version`:__ Print the version of the exporter and exit.
* __`-web.listen-address <port>`:__ Address to listen on for web interface and telemetry. (default: `9943`)
* __`-web.telemetry-path <URL path>`:__ Path under which to expose metrics. (default: `/metrics`)
* __`-web.config <web file>`:__ Path to config yaml file that can enable TLS or authentication.
* __`-web.systemd-socket`:__ Flag to use systemd socket activation listeners instead of port listeners (Linux only).
* __`-path.cwd <path>`:__ Change working directory of path pattern collection.
* __`-metric.crc32`:__ Generate CRC32 hash metric of files.
* __`-metric.nb_lines`:__ Generate line number metric of files.

The exporter can read a config file in yaml format (`filestat.yaml` by default).

```yaml
exporter:
  # Optional network parameters
  listen_address: ':9943'
  #metrics_path: /metrics
  
  # Optional working directory - overridden by parameter '-path.cwd'
  working_directory: "/path/to/my/project"
  # Default enable/disable of metrics - overridden if not set by parameter '-metric.*'
  enable_crc32_metric: true
  # enable_nb_line_metric: false
  # list of patterns to apply - metrics can be enable/disabled for each group
  files:
    - patterns: ["*.html","assets/*.css","scripts/*.js"]
    - patterns: ["data/*.csv"]
      enable_nb_line_metric: true
    - patterns: ["archives/*.tar.gz"]
      enable_crc32_metric: false
      enable_nb_line_metric: false
```

Note: if a file is matched by a pattern more than once, only the first match's config is used


### Exported Metrics

| Metric                       | Description                                  | Labels   |
| ---------------------------- | -------------------------------------------- | -------- |
| file_glob_match_number       | Number of files matching pattern             | pattern  |
| file_stat_size_bytes         | Size of file in bytes                        | path     |
| file_stat_modif_time_seconds | Last modification time of file in epoch time | path     |
| file_content_hash_crc32  (*) | CRC32 hash of file content                   | path     |
| file_content_line_number (*) | Number of lines in file                      | path     |

Note: metrics with `(*)` are only provided if configured
