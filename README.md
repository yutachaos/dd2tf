# Dd2tf

This gem export datadog configuration as terraform format.
Inspired by [kurochan/datadog_monitor2terraform](https://github.com/kurochan/datadog_monitor2terraform)

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'dd2tf'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install dd2tf

## Usage

```
$ dd2tf help
Commands:
  dd2tf help [COMMAND]    # Describe available commands or one specific command
  dd2tf print [resource]  # Print resources importing command for outputting to tfstate. [resource] is one of the [monitor, timeboard, user, downtime]
  dd2tf tf [resource]     # Print resources config terrafom formatted. [resource] is one of the [monitor, timeboard, user]

Options:
  --dd-api-key=DD_API_KEY
  --dd-app-key=DD_APP_KEY
```


### Print resources config terraform formatted

```
$ dd2tf tf user --dd_api_key=xxx --dd-app_key=xxx
resource "datadog_user" "reiji_kainuma" {
  disabled = "false"
  email = "reizist@example.com"
  handle = "reizist@example.com"
  is_admin = "true"
  name = "Reiji Kainuma"
  role = ""
}

```

```
$ dd2tf tf monitor --dd_api_key=xxx --dd-app_key=xxx
resource "datadog_monitor" "auto_clock_in_sync_with_ntp" {
  name               = "[Auto] Clock in sync with NTP"
  type               = "service check"
  message            = <<EOF
  Triggers if any host's clock goes out of sync with the time given by NTP. The offset threshold is configured in the Agent's `ntp.yaml` file.

Please read the [KB article](http://help.datadoghq.com/hc/en-us/articles/204282095-Network-Time-Protocol-NTP-Offset-Issues) on NTP Offset issues for more details on cause and resolution.
  EOF
  query = "\"ntp.in_sync\".over(\"*\").last(2).count_by_status()"
  thresholds {
	ok = 1
	warning = 1
	critical = 1
  }
  tags = []
}
```

```
$ dd2tf tf timeboard --dd_api_key=xxx --dd-app_key=xxx
resource "datadog_timeboard" "presto_staging" {
  title       = "Presto(staging)"
  description = "created by xxx"
  read_only   = false


  graph {
    title = "cluster_memory_manager_metrics"
    viz   = "timeseries"
    autoscale = "true"

    request {
      q  = "avg:presto_staging.cluster_memory_manager_metrics.cluster_memory_usage_bytes{*}"
      aggregator = "avg"
      type = "line"
    }
    request {
      q  = "avg:presto_staging.cluster_memory_manager_metrics.cluster_memory_bytes{*}"
      type = "line"
    }
  }
}
```

### Print import command for importing resources to tfstate

```
$ dd2tf print monitor --dd_api_key=xxx --dd-app_key=xxx
terraform import datadog_monitor.presto_insufficient_resource_error xxxxxxx 
terraform import datadog_monitor.presto_queueing_is_very_high xxxxxxx
```

# Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/reizist/dd2tf. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the Contributor Covenant code of conduct.
