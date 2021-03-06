# Reference
<!-- DO NOT EDIT: This document was generated by Puppet Strings -->

## Classes

### Public Classes

* [`puppet_metrics_dashboard`](#puppet_metrics_dashboard): Installs and configures Grafana with InfluxDB for monitoring Puppet infrastructure.
* [`puppet_metrics_dashboard::profile::postgres`](#puppet_metrics_dashboardprofilepostgres): Apply this class to a PE-managed postgres instance to allow access from telegraf

### Private Classes

* `puppet_metrics_dashboard::config`: Configures dashboard components.
* `puppet_metrics_dashboard::dashboards`: Configures Grafana dashboards.
* `puppet_metrics_dashboard::dashboards::graphite`: Installs graphite example dashboards
* `puppet_metrics_dashboard::dashboards::puppet_metrics`: Installs puppet_metrics example dashboards
* `puppet_metrics_dashboard::dashboards::telegraf`: Installs telegraf example dashboards
* `puppet_metrics_dashboard::grafana`: Install and configure Grafana
* `puppet_metrics_dashboard::install`: Installs InfluxDB components.
* `puppet_metrics_dashboard::params`: Default parameters for the installation
* `puppet_metrics_dashboard::post_start_configs`: InfluxDB post-start configs
* `puppet_metrics_dashboard::repos`: Configures InfluxDB and Grafana repos
* `puppet_metrics_dashboard::service`: Manages services
* `puppet_metrics_dashboard::telegraf`: Install and configure Telegraf
* `puppet_metrics_dashboard::telegraf::config`: Configures Telegraf
* `puppet_metrics_dashboard::telegraf::install`: Installs Telegraf
* `puppet_metrics_dashboard::telegraf::service`: Manages the Telegraf service

## Defined types

* [`puppet_metrics_dashboard::certs`](#puppet_metrics_dashboardcerts): 

## Classes

### puppet_metrics_dashboard

The puppet_metrics_dashboard module installs and configures an InfluxDB stack
monitor the Puppet infrastructure components.

#### Examples

##### Default Configuration

```puppet
include puppet_metrics_dashboard
```

##### Configure Telegraf on a list of masters and PuppetDB servers

```puppet
class { 'puppet_metrics_dashboard':
  configure_telegraf  => true,
  enable_telegraf     => true,
  master_list         => ['master1.com','master2.com'],
  puppetdb_list       => ['puppetdb1','puppetdb2'],
}
```

##### Install example dashboards for all of the collection methods

```puppet
class { 'puppet_metrics_dashboard':
  add_dashboard_examples => true,
  influxdb_database_name => ['puppet_metrics','telegraf','graphite'],
}
```

##### Configure telegraf for one or more masters / puppetdb nodes

```puppet
class { 'puppet_metrics_dashboard':
  configure_telegraf  => true,
  enable_telegraf     => true,
  master_list         => ['master1.com','master2.com'],
  puppetdb_list       => ['puppetdb1','puppetdb2'],
}
```

##### Enable Graphite support

```puppet
class { 'puppet_metrics_dashboard':
  add_dashboard_examples => true,
  consume_graphite       => true,
  influxdb_database_name => ["graphite"],
  master_list            => ["master01.example.com","master02.org"],
}
```

##### Enable Telegraf, Graphite, and Archive

```puppet
class { 'puppet_metrics_dashboard':
  add_dashboard_examples => true,
  influxdb_database_name => ['puppet_metrics','telegraf','graphite'],
  consume_graphite       => true,
  configure_telegraf     => true,
  enable_telegraf        => true,
}
```

##### Enable SSL

```puppet
class { 'puppet_metrics_dashboard':
  use_dashboard_ssl => true,
}
```

#### Parameters

The following parameters are available in the `puppet_metrics_dashboard` class.

##### `add_dashboard_examples`

Data type: `Boolean`

Whether to add the Grafana dashboard example dashboards for the configured InfluxDB databases.
Valid values are `true`, `false`. Defaults to `false`.
_Note_: These dashboards are managed and any changes will be overwritten unless the `overwrite_dashboards` is set to `false`.

Default value: $puppet_metrics_dashboard::params::add_dashboard_examples

##### `manage_repos`

Data type: `Boolean`

Whether or not to setup yum / apt repositories for the dependent packages
Valid values are `true`, `false`. Defaults to `true`

Default value: $puppet_metrics_dashboard::params::manage_repos

##### `dashboard_cert_file`

Data type: `String`

The location of the Grafana certficiate.
Defaults to `"/etc/grafana/${clientcert}_cert.pem"`
Only used when configuring `use_dashboard_ssl` is true.

Default value: $puppet_metrics_dashboard::params::dashboard_cert_file

##### `dashboard_cert_key`

Data type: `String`

The location of the Grafana private key.
Defaults to `"/etc/grafana/${clientcert}_key.pem"`
Only used when configuring `use_dashboard_ssl` is true.

Default value: $puppet_metrics_dashboard::params::dashboard_cert_key

##### `configure_telegraf`

Data type: `Boolean`

Whether to configure the telegraf service.
Valid values are `true`, `false`. Defaults to `true`
This parameter enables configuring telegraf to query the `master_list` and `puppetdb_list` endpoints for metrics. Metrics will be stored
in the `telegraf` database in InfluxDb. Ensure that `influxdb_database_name` contains `telegraf` when using this parameter.
_Note:_ This parameter is only used if `enable_telegraf` is set to true.

Default value: $puppet_metrics_dashboard::params::configure_telegraf

##### `consume_graphite`

Data type: `Boolean`

Whether to enable the InfluxDB Graphite plugin.
Valid values are `true`, `false`. Defaults to `false`
This parameter enables the Graphite plugin for InfluxDB to allow for injesting Graphite metrics. Ensure `influxdb_database_name`
contains `graphite` when using this parameter.
_Note:_ If using Graphite metrics from the Puppet Master, this needs to be set to `true`.

Default value: $puppet_metrics_dashboard::params::consume_graphite

##### `grafana_http_port`

Data type: `Integer`

The port to run Grafana on.
Valid values are Integers from `1024` to `65536`. Defaults to `3000`
The grafana port for the web interface. This should be a nonprivileged port (above 1024).

Default value: $puppet_metrics_dashboard::params::grafana_http_port

##### `grafana_password`

Data type: `String`

The password for the Grafana admin user.
Defaults to `'admin'`

Default value: $puppet_metrics_dashboard::params::grafana_password

##### `grafana_version`

Data type: `String`

The grafana version to install.
Valid values are String versions of Grafana. Defaults to `'4.5.2'`

Default value: $puppet_metrics_dashboard::params::grafana_version

##### `influxdb_database_name`

Data type: `Array[String]`

An array of databases that should be created in InfluxDB.
Valid values are 'puppet_metrics','telegraf', 'graphite', and any other string. Defaults to `['puppet_metrics']`
Each database in the array will be created in InfluxDB. 'puppet_metrics','telegraf', and 'graphite' are specially named and will
be used with their associated metric collection method. Any other database name will be created, but not utilized with
components in this module.

Default value: $puppet_metrics_dashboard::params::influxdb_database_name

##### `influx_db_password`

Data type: `String`

The password for the InfluxDB admin user.
Defaults to `'puppet'`

Default value: $puppet_metrics_dashboard::params::influx_db_password

##### `enable_kapacitor`

Data type: `Boolean`

Whether to install kapacitor.
Valid values are `true`, `false`. Defaults to `false`
Install kapacitor. No configuration of kapacitor is included at this time.

Default value: $puppet_metrics_dashboard::params::enable_kapacitor

##### `enable_chronograf`

Data type: `Boolean`

Whether to install chronograf.
Valid values are `true`, `false`. Defaults to `false`
Installs chronograf. No configuration of chronograf is included at this time.

Default value: $puppet_metrics_dashboard::params::enable_chronograf

##### `enable_telegraf`

Data type: `Boolean`

Whether to install telegraf.
Valid values are `true`, `false`. Defaults to `false`
Installs telegraf. No configuration is done unless the `configure_telegraf` parameter is set to `true`.

Default value: $puppet_metrics_dashboard::params::enable_telegraf

##### `master_list`

Data type: `Array[String]`

An array of Puppet Master servers to collect metrics from. Defaults to `[$trusted['certname']]`
A list of Puppet master servers that will be configured for telegraf to query.

Default value: $puppet_metrics_dashboard::params::master_list

##### `influxdb_urls`

Data type: `String`

The string for telegraf's config defining where influxdb is

Default value: $puppet_metrics_dashboard::params::influxdb_urls

##### `telegraf_db_name`

Data type: `String`

The database in influxdb where telefraf metrics are stored

Default value: $puppet_metrics_dashboard::params::telegraf_db_name

##### `telegraf_agent_interval`

Data type: `Integer[1]`

How often the telefraf agent queries for metrics

Default value: $puppet_metrics_dashboard::params::telegraf_agent_interval

##### `http_response_timeout`

Data type: `Integer[1]`

How long to wait for the queries by telegraf to finish before giving up

Default value: $puppet_metrics_dashboard::params::http_response_timeout

##### `overwrite_dashboards`

Data type: `Boolean`

Whether to overwrite the example Grafana dashboards.
Valid values are `true`, `false`. Defaults to `false`
This paramater disables overwriting the example Grafana dashboards. It takes effect after the second Puppet run and popultes the
`overwrite_dashboards_disabled` fact. This only takes effect when `add_dashboard_examples` is set to true.

Default value: $puppet_metrics_dashboard::params::overwrite_dashboards

##### `puppetdb_list`

Data type: `Array[String]`

An array of PuppetDB servers to collect metrics from. Defaults to `[$trusted['certname']]`
A list of PuppetDB servers that will be configured for telegraf to query.

Default value: $puppet_metrics_dashboard::params::puppetdb_list

##### `postgres_host_list`

Data type: `Array[String]`

An array of Postgres hosts to monitor.  Defaults to `[$trusted['certname']]`

Default value: $puppet_metrics_dashboard::params::postgres_host_list

##### `use_dashboard_ssl`

Data type: `Boolean`

Whether to enable SSL on Grafana.
Valid values are `true`, `false`. Defaults to `false`

Default value: $puppet_metrics_dashboard::params::use_dashboard_ssl

##### `overwrite_dashboards_file`

Data type: `String`

File in use to populate the overwrite_dashboards fact

Default value: $puppet_metrics_dashboard::params::overwrite_dashboards_file

##### `influx_db_service_name`

Data type: `String`

Name of the influxdb service for the operating system

Default value: $puppet_metrics_dashboard::params::influx_db_service_name

### puppet_metrics_dashboard::profile::postgres

Apply this class to a PE-managed postgres instance to allow access from telegraf

#### Parameters

The following parameters are available in the `puppet_metrics_dashboard::profile::postgres` class.

##### `grafana_host`

Data type: `String`

The FQDN of the host where telegraf runs.
Defaults to an empty string.  You can explicitly set this parameter or the class attempts to lookup which host has the puppet_metrics_dashboard class applied in PuppetDB.  If the parameter is not set and the lookup does not return anything we issue a warning.

Default value: ''

## Defined types

### puppet_metrics_dashboard::certs

The puppet_metrics_dashboard::certs class.

#### Parameters

The following parameters are available in the `puppet_metrics_dashboard::certs` defined type.

##### `service`

Data type: `Any`



Default value: $name

