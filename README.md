icinga2 Cookbook
==================

[![Build Status](https://travis-ci.org/vkhatri/chef-icinga2.svg?branch=master)](https://travis-ci.org/vkhatri/chef-icinga2)

This is a [Chef] cookbook to manage [Icinga2] using Chef LWRP.


More features and attributes will be added over time, **feel free to contribute**
what you find missing!


## Repository

https://github.com/vkhatri/chef-icinga2


## Recipes

- `icinga2::default`     	- does not do anything, used for LWRP usage

- `icinga2::server`  		- install & configure icinga2 server with default incinga Objects

- `icinga2::server_apache`		- manages apache and icinga2 classic ui / web / web2 vhost using `apache2` cookbook

- `icinga2::server_install`   		- install icinga2 core/ido server packages

- `icinga2::server_core`   			- configures icinga2 core configuration files & directories

- `icinga2::server_features`   		- enable/disable icinga2 features

- `icinga2::server_classic_ui`   		- configures icinga2 classic ui

- `icinga2::server_pnp`   		- configures pnp4nagios for icinga2

- `icinga2::server_objects`   		- manages icinga2 default objects/templates objects if `node['icinga2']['disable_conf_d']` is set in which case `conf.d` objects config is not included in `icinga2.conf` and objects are created using LWRP

- `icinga2::server_object_host`   	- creates icinga2 `generic-host` `Host` template

- `icinga2::server_object_notificationcommand`   - creates icinga2 default `NotificationCommand` objects

- `icinga2::server_object_service`   		- creates icinga2 default`generic-service` `Service`  templates

- `icinga2::server_object_timeperiod`   	- creates icinga2 default `TimePeriod` objects

- `icinga2::server_object_user`   			- creates icinga2 `generic-user` `User` template and `icingaadmin` `User` object

- `icinga2::server_object_usergroup`   	- creates icinga2 `icingaadmins` `UserGroup` object


## Attributes

- `icinga2::default` 			- icinga2 server default attributes file

- `icinga2::repo`				- icinga2 yum/apt repositories file for packages install

- `icinga2::server_classic_ui` - icinga2 server classic ui default attributes file

- `icinga2::server_constants`	- icinga2 server constants parameters file

- `icinga2::server_objects`		- icinga2 objects default attributes file

- `icinga2::server_ido`		- icinga2 ido db default attributes file

- `icinga2::server_web2`		- icinga2 web2 ui default attributes file



## Supported Icinga Version

This cookbook is being developed for Icinga2 - v2.2.x primarily on Amazon Platform (EPEL Release 6 Package).


## Supported Icinga2 Install Types

Currently Icinga2 installation is supported **ONLY** via Repository Packages.


## Icinga2 Server Setup

To setup icinga2 server on a node, add recipe `icinga2::server` which will install necessary packages and configuration files.



## Icinga2 Classic UI

Icinga2 Classic UI is enabled by default and set as default UI.

However, Classic UI can be disabled by attribute `node['icinga2']['classic_ui']['enable']`.


**How to add users for icinga2 Classic UI**

Icinga2 Classic UI users is controlled by node Hash attribute `node['icinga2']['classic_ui']['users']`. This attribute accepts a `Hash` of `username => htpasswd(passwd)`, so that password is not available in plain text.

By default `icingaadmin` user is added with password `icingaadmin`:

	default['icinga2']['classic_ui']['users'] = {
	  'icingaadmin' => '$apr1$MZtKRLAy$AV9OiJ.V/mI9g30bHn9ol1'
	}

This can be overrided in wrapper cookbook or role or environment.

To add more users for icigna2 Classic UI auth, add new users to Hash attribute in the same wasy `icingaadmin` user is added

	default['icinga2']['classic_ui']['users']['user_name'] = '$apr1$MZtKRLAy$AV9OiJ.V/mI9g30bHn9ol1'

**Icinga2 Classic UI Authorization**


Icigna2 Classic UI User authorization is managed by below node Arrary attributes:

	node['icinga2']['classic_ui']['authorized_for_system_information']
	node['icinga2']['classic_ui']['authorized_for_configuration_information']
	node['icinga2']['classic_ui']['authorized_for_full_command_resolution']
	node['icinga2']['classic_ui']['authorized_for_system_commands']
	node['icinga2']['classic_ui']['authorized_for_all_services']
	node['icinga2']['classic_ui']['authorized_for_all_hosts']
	node['icinga2']['classic_ui']['authorized_for_all_service_commands']
	node['icinga2']['classic_ui']['authorized_for_all_host_commands']

By simply adding users to above attributes will provide necessary access to the UI.



**How to add a guest user**

To add a guest user without any admin privileges, first add a `guest` user (with password `guest`)

	default['icinga2']['classic_ui']['users']['guest'] = '$apr1$cA/eVUgT$aIoWUPwV5uONJoYslb7lg0'

Then authorize `guest` user to view `Host/Service` status

	node['icinga2']['classic_ui']['authorized_for_all_services'] = %w(icingaadmin guest)
	node['icinga2']['classic_ui']['authorized_for_all_hosts'] = %w(icingaadmin guest)



**How to add an admin user**

To make a user `admin`, add the user to below node attributes:

	node['icinga2']['classic_ui']['authorized_for_system_information']
	node['icinga2']['classic_ui']['authorized_for_configuration_information']
	node['icinga2']['classic_ui']['authorized_for_full_command_resolution']
	node['icinga2']['classic_ui']['authorized_for_system_commands']
	node['icinga2']['classic_ui']['authorized_for_all_services']
	node['icinga2']['classic_ui']['authorized_for_all_hosts']
	node['icinga2']['classic_ui']['authorized_for_all_service_commands']
	node['icinga2']['classic_ui']['authorized_for_all_host_commands']



## Icinga Web2

Icingaweb2 recipe setup is work in progress.


## Icinga2 Cluster Deployment

Icinga2 Distributes / HA cluster setup examples will be added soon.


## Icinga2 Monitor a Chef Environment Nodes

This cookbook does not only provide management of Icinga2 server & objects, it also provides
automation around Chef environment.

Using LWRP `environment` a whole environment nodes can be added to `Host` objects with environment wide `Host` object parameters.

There are certain functionalities added to LWRP `environment`, like:

- define icinga2 `Host` parameters for an entire environment

- auto create `HostGroup` object for an entire chef environment

- auto create `HostGroup` object for node's `application` attribute to group nodes for an entire chef environment `application` type

- auto create `HostGroup` object for node's `cluster` attribute to group nodes for a chef environment `cluster`

- auto add chef node Cloud attributes as `Host` custom `vars`, currently only AWS EC2 attributes are supported, but is easy to extend the suport to other cloud providers

- auto create `HostGroup` list for a chef environment node

- limit a chef environment spreaded across multiple regions to icinga2 server region, e.g. in multi region ec2 production environment, one would want to setup an icinga2 server in region `us-east-1` just to monitor `us-east-1` nodes, but not the production nodes of other regions, like `ap-southeast-1`

- allow chef node to determine `host.address` from `node['fqdn']` DNS resolution instead of `node['ipaddress']` and either ignore chef node if failed to resolve DNS or fallback to use `node['ipaddress']` as `host.address`

- exclude a node by `run_list` role, not yet tested

- exclude a node by `run_list` role, not yet tested

- filter chef node if match certain node attributes

- override an environment and use an entire different `search_pattern`, this feature extends LWRP `environment` functionality to select nodes by a user given search pattern

- `Host` object attribute `disply_name` is set to chef node hostname


Simply create a LWRP resource for a chef environment, to start monitoring all nodes in that environment. More details can be found in examples.


## Icinga2 Host Custom Vars

**environment LWRP Host Vars**

LWRP `environment` resources sets Host custom vars for each node via node `Hash` attribute -
`node['icinga2']['client']['custom_vars']`. All defined `vars` will be added to `Host` object.


**host LWRP Host Vars**

When using `icinga2_host` LWRP, node custom vars will not be added automatically. There will be no search performed as the `Host` object could be different than a chef `Node`.

To add `Host` custom vars, use Hash attribute `custom_vars`.

A resource attribute will be added to `icinga2_host` LWRP to perform a search to fetch custom vars defined for a node, so that manual addition is not required.



## Icing2 with PNP4Nagios

Recipe `icinga2::server_pnp` setup and configures `PNP4Nagios` with `rrdcached` daemon.

Simply add recipe `icinga2::server_pnp` to icinga2 server `run_list` to setup `PNP4Nagios` along with `rrdcached`.



## Client

NRPE Client recipe has been removed from this cookbook.

Icinga2 Agent setup will be added soon.



## LWRP Examples

Different LWRP usage examples are added to `examples` directory.

To configure icinga2 server, check `examples/icinga2_server` directory. More exaamples will be added here upon further testing.




## icinga2 LWRP Resources

Currently icinga2 cookbook supports below Objects LWRP Resources:

- icinga2_apilistener
- icinga2_applynotification
- icinga2_applyservice
- icinga2_checkcommand
- icinga2_endpoint
- icinga2_envhostgroup
- icinga2_environment
- icinga2_eventcommand
- icinga2_externalcommandlistener
- icinga2_feature
- icinga2_gelfwriter
- icinga2_graphitewriter
- icinga2_host
- icinga2_hostgroup
- icinga2_idomysqlconnection
- icinga2_idopgsqlconnection
- icinga2_notification
- icinga2_notificationcommand
- icinga2_scheduleddowntime
- icinga2_service
- icinga2_servicegroup
- icinga2_sysloglogger
- icinga2_timeperiod
- icinga2_user
- icinga2_usergroup
- icinga2_zone

Few of LWRP attributes which are required to create an icinga2 Object are not declared `:required => true` in LWRP in favour of creating icinga2 Object template.

Same LWRP resource used to create icinga2 `Object`, can also be used to create icinga2 `Template` as well.



## LWRP icinga2_environment

As mentioned in a section above, instead of creating `Host` objects for each chef node, using LWRP `environment` `Host` objects can easily be created for all chef nodes for a chef environment.

LWRP resource attributes are common for all the `Host`. It may be required to define or override attribute for few specific `Host` objects, but it is not yet incorporated or forseen any usage at this point which might change over time.


**LWRP example**

	icinga2_environment 'SingaporeDevelopment' do
	  import node['icinga2']['server']['object']['host']['import']
	  environment 'development'
	  limit_region true
	  ignore_resolv_error true
	  enable_cluster_hostgroup true
	  enable_application_hostgroup true
	  cluster_attribute 'flock'
	  application_attribute 'application'
	  check_interval '1m'
	  retry_interval '10s'
	  max_check_attempts 3
	  action_url '/pnp4nagios/graph?host=$HOSTNAME$&srv=_HOST_'
	end


Above LWRP resource will create `Host` objects for a chef environment nodes for a given `search_pattern` and other filter.


**LWRP Options**


- *action* (optional)			- default :create, options: :create, :delete, :reload
- *environment* (required)		- chef environment name
- *search_pattern* (optional)	- chef search pattern for given environment
- *cluster_attribute* (optional)	-  chef node cluster attribute to create hostgroup and `Host` vars
- *application_attribute* (optional) - chef noder application attribute to create hostgroup and `Host` vars
- *enable_cluster_hostgroup* (optional)	- whether to create `HostGroup` objects for chef node cluster's
- *enable_application_hostgroup* (optional)	- whether to create `HostGroup` objects for chef node application's
- *enable_role_hostgroup* (optional)	- whether to create `HostGroup` objects for chef node run_list role
- *use_fqdn_resolv* (optional)	- whether to use DNS FQDN resolved address for `Host` object attribute `address`
- *failover_fqdn_address* (optional)	- whether to use chef node attribute `node['ipaddress']` if failed to resolve node FQDN
- *ignore_resolv_error* (optional)	- whether to ignore node FQDN resolve error
- *ignore_node_error* (optional)	- whether to ignore node if failed to determine `node[]'chef_environment']` or `node['fqdn']` or `node['hostname']`
<del>- *exclude_recipes* (optional)	- exclude chef node if `run_list` matches recipe, not yet tested </del>
<del>- *exclude_roles* (optional)	- exclude chef node if `run_list` matches role, not yet tested </del>
- *env_custom_vars* (optional)	- add `Host` obect custom vars to all chef node
- *limit_region* (optional)	- whether to limit chef node to chef server region, currently tested for Amazon EC2, e.g. a icinga2 server located in region `us-east-1` will only collect nodes located in `us-east-`` region
- *server_region* (optional)	- icinga2 server region can be overriden if cloud provider is not supported by the cookbook using this attribute
- *add_cloud_custom_vars* (optional)	- whether to add cloud attributes, currently supports amazon ec2, e.g. instance id, vpc subnet etc.
- *env_filter_node_vars* (optional)	- filter or match chef nodes for a given `Hash` attribute values
- *import* (optional)	- icinga `Host` object import template attribute
- *check_command* (optional)	- icinga `Host` object attribute `check_command`
- *max_check_attempts* (optional)	- icinga `Host` object attribute `max_check_attempts`
- *check_period* (optional)	- icinga `Host` object attribute `check_period`
- *check_interval* (optional)	- icinga `Host` object attribute `check_interval`
- *retry_interval* (optional)	- icinga `Host` object attribute `retry_interval`
- *enable_notifications* (optional)	- icinga `Host` object attribute `enable_notifications`
- *enable_active_checks* (optional)	- icinga `Host` object attribute `enable_active_checks`
- *enable_passive_checks* (optional)	- icinga `Host` object attribute `enable_passive_checks`
- *enable_passive_checks* (optional)	- icinga `Host` object attribute `enable_passive_checks`
- *enable_event_handler* (optional)	- icinga `Host` object attribute `enable_event_handler`
- *enable_flapping* (optional)	- icinga `Host` object attribute `enable_flapping`
- *enable_perfdata* (optional)	- icinga `Host` object attribute `enable_perfdata`
- *event_command* (optional)	- icinga `Host` object attribute `event_command`
- *flapping_threshold* (optional)	- icinga `Host` object attribute `flapping_threshold`
- *volatile* (optional)	- icinga `Host` object attribute `volatile`
- *zone* (optional)	- icinga `Host` object attribute `zone`
- *command_endpoint* (optional)	- icinga `Host` object attribute `command_endpoint`
- *notes* (optional)	- icinga `Host` object attribute `notes`
- *notes_url* (optional)	- icinga `Host` object attribute `notes_url`
- *action_url* (optional)	- icinga `Host` object attribute `action_url`
- *icon_image* (optional)	- icinga `Host` object attribute `icon_image`
- *icon_image_alt* (optional)	- icinga `Host` object attribute `icon_image_alt`


Note: Few of LWRP resource attributes has default value, please check LWRP resource until information is added here.


## LWRP icinga2_envhostgroup

LWRP `envhostgroup` creates an icinga `Hostgroup` object for LWRP `environment` host groups.

An `environment` host groups are evaluated at compile time, hence it conflicts with LWRP `hostgroup` resources. To avoid the conflict, LWRP `envhostgroup` resources are created in a separate object file for an environemnt.


**LWRP Environment HostGroup example**

	icinga2_envhostgroup 'envhostgroup' do
	  environment environment_name
	  groups %w(group)
	end

Above LWRP resource will create `HostGroup` for a chef environment.


**LWRP Options**

- *action* (optional)	- default :enable, options: :enable, :disable
- *environment* (name_attribute)	- chef environment name
- *groups* (optional)	- host group names

Note: This LWRP resoruce is only meant for LWRP `environment` and not to be used for custom resources.

## LWRP icinga2_host


Unlike LWRP `environment`, LWRP `host` creates an icinga `Host` object or template.

LWRP `host` can be useful for servers not managed by Chef or to monitor Cloud Services like Amazon Elastic Cache or Amazon RDS etc.

**LWRP Host Object example**

	icinga2_host 'redis-ec.abcdef.0001.apse1.cache.amazonaws.com' do
	  display_name 'Production Redis Server'
	  address 'redis-ec.abcdef.0001.apse1.cache.amazonaws.com'
	  custom_vars :check_tcp_ports => %w(6379), :application => 'redis', :environment => 'production', :cluster_name => 'prodredis0001'
	end


Above LWRP resource will create an icinga `Host` object.


**LWRP Host Template example**

	icinga2_host 'generic-host' do
	  template true
	  max_check_attempts 5
	  check_interval '1m'
	  retry_interval '30s'
	  check_command 'hostalive'
	end

Above LWRP resource will create an icinga `Host` template - `generic-host`.


**LWRP Options**


- *action* (optional)			- default :create, options: :create, :delete, :reload
- *display_name* (optional)	- icinga `Host` object import template attribute
- *import* (optional)	- icinga `Host` object import template attribute
- *address* (optional)	- icinga `Host` object import template attribute
- *address6* (optional)	- icinga `Host` object import template attribute
- *groups* (optional)	- icinga `Host` object import template attribute
- *check_command* (optional)	- icinga `Host` object attribute `check_command`
- *max_check_attempts* (optional)	- icinga `Host` object attribute `max_check_attempts`
- *check_period* (optional)	- icinga `Host` object attribute `check_period`
- *check_interval* (optional)	- icinga `Host` object attribute `check_interval`
- *retry_interval* (optional)	- icinga `Host` object attribute `retry_interval`
- *enable_notifications* (optional)	- icinga `Host` object attribute `enable_notifications`
- *enable_active_checks* (optional)	- icinga `Host` object attribute `enable_active_checks`
- *enable_passive_checks* (optional)	- icinga `Host` object attribute `enable_passive_checks`
- *enable_passive_checks* (optional)	- icinga `Host` object attribute `enable_passive_checks`
- *enable_event_handler* (optional)	- icinga `Host` object attribute `enable_event_handler`
- *enable_flapping* (optional)	- icinga `Host` object attribute `enable_flapping`
- *enable_perfdata* (optional)	- icinga `Host` object attribute `enable_perfdata`
- *event_command* (optional)	- icinga `Host` object attribute `event_command`
- *flapping_threshold* (optional)	- icinga `Host` object attribute `flapping_threshold`
- *volatile* (optional)	- icinga `Host` object attribute `volatile`
- *zone* (optional)	- icinga `Host` object attribute `zone`
- *command_endpoint* (optional)	- icinga `Host` object attribute `command_endpoint`
- *notes* (optional)	- icinga `Host` object attribute `notes`
- *notes_url* (optional)	- icinga `Host` object attribute `notes_url`
- *action_url* (optional)	- icinga `Host` object attribute `action_url`
- *icon_image* (optional)	- icinga `Host` object attribute `icon_image`
- *icon_image_alt* (optional)	- icinga `Host` object attribute `icon_image_alt`
- *custom_vars* (optional)	- icinga `Host` object attribute `vars`
- *tempalte* (optional)	- whether to create a `Host` template


## LWRP icinga2_hostgroup

LWRP `hostgroup` creates an icinga `HostGroup` object.


**LWRP HostGroup example**

	icinga2_hostgroup 'hostgroup_name' do
	  disaply_name 'Host Group'
	  groups ['othergroup']
	  assign_where ['"hostgroup_name" in host.vars.hostgroups']
	  ignore_where ['"hostgroup_name" in host.vars.hostgroups']
	end

Above LWRP resource will create an icinga `HostGroup` object.


**LWRP Options**

- *action* (optional)	- default :enable, options: :enable, :disable
- *display_name* (optional)	- icinga `HostGroup` attribute `display_name`
- *groups* (optional)	- icinga `HostGroup` attribute `groups`
- *assign_where* (optional)	 - an array of `assign where` statements
- *ignore_where* (optional)	 - an array of `ignore where` statements


## LWRP icinga2_service


LWRP `service` creates an icinga `Service` object or template.


**LWRP Service Object example**

	icinga2_service 'redis-ec.abcdef.0001.apse1.cache.amazonaws.com_service_check' do
	  display_name 'Production Redis Service Check'
	  host_name 'redis-ec.abcdef.0001.apse1.cache.amazonaws.com'
	  check_command 'tcp'
	  custom_vars :tcp_port => 6379
	end


Above LWRP resource will create an icinga `Service` object for a host tcp port check.


**LWRP Service Template example**

	icinga2_service 'generic-service' do
	  template true
	  max_check_attempts 5
	  check_interval '1m'
	  retry_interval '30s'
	end

Above LWRP resource will create an icinga `Service` template object for a `generic-service` `Service`.


**LWRP Options**


- *action* (optional)	- default :create, options: :create, :delete, :reload
- *display_name* (optional)	- icinga `Service` object attribute `display_name`
- *import* (optional)	- icinga `Service` object attribute `import`
- *host_name* (optional)	- icinga `Service` object attribute `host_name`
- *groups* (optional)	- icinga `Service` object attribute `groups`
- *check_command* (optional)	- icinga `Service` object attribute `check_command`
- *max_check_attempts* (optional)	- icinga `Service` object attribute `max_check_attempts`
- *check_period* (optional)	- icinga `Service` object attribute `check_period`
- *check_interval* (optional)	- icinga `Service` object attribute `check_interval`
- *retry_interval* (optional)	- icinga `Service` object attribute `retry_interval`
- *enable_notifications* (optional)	- icinga `Service` object attribute `enable_notifications`
- *enable_active_checks* (optional)	- icinga `Service` object attribute `enable_active_checks`
- *enable_passive_checks* (optional)	- icinga `Service` object attribute `enable_passive_checks`
- *enable_passive_checks* (optional)	- icinga `Service` object attribute `enable_passive_checks`
- *enable_event_handler* (optional)	- icinga `Service` object attribute `enable_event_handler`
- *enable_flapping* (optional)	- icinga `Service` object attribute `enable_flapping`
- *enable_perfdata* (optional)	- icinga `Service` object attribute `enable_perfdata`
- *event_command* (optional)	- icinga `Service` object attribute `event_command`
- *flapping_threshold* (optional)	- icinga `Service` object attribute `flapping_threshold`
- *volatile* (optional)	- icinga `Service` object attribute `volatile`
- *zone* (optional)	- icinga `Service` object attribute `zone`
- *command_endpoint* (optional)	- icinga `Service` object attribute `command_endpoint`
- *notes* (optional)	- icinga `Service` object attribute `notes`
- *notes_url* (optional)	- icinga `Service` object attribute `notes_url`
- *action_url* (optional)	- icinga `Service` object attribute `action_url`
- *icon_image* (optional)	- icinga `Service` object attribute `icon_image`
- *icon_image_alt* (optional)	- icinga `Service` object attribute `icon_image_alt`
- *custom_vars* (optional)	- icinga `Service` object attribute `vars`
- *tempalte* (optional)	- whether to create a `Service` template


## LWRP icinga2_applyservice

LWRP `service` creates an icinga `Service` object or template.


**LWRP Apply Service Object example**

	icinga2_applyservice 'nrpe_check_load' do
	  display_name 'CPU Average Load'
	  object_name 'nrpe_check_load'
	  import 'generic-service'
	  check_command 'nrpe'
	  custom_vars :nrpe_command => 'check_load'
	  assign_where ['host.vars.nrpe']
	  check_interval '1m'
	  retry_interval '15s'
	  max_check_attempts 3
	  action_url '/pnp4nagios/graph?host=$HOSTNAME$&srv=$SERVICEDESC$'
	end

Above LWRP resource will apply an icinga `Service` object to all `Hosts` with custom vars `host.vars.nrpe`.


**LWRP Options**


- *action* (optional)	- default :create, options: :create, :delete, :reload
- *display_name* (optional)	- icinga `Service` object attribute `display_name`
- *import* (optional)	- icinga `Service` object attribute `import`
- *host_name* (optional)	- icinga `Service` object attribute `host_name`
- *groups* (optional)	- icinga `Service` object attribute `groups`
- *check_command* (optional)	- icinga `Service` object attribute `check_command`
- *max_check_attempts* (optional)	- icinga `Service` object attribute `max_check_attempts`
- *check_period* (optional)	- icinga `Service` object attribute `check_period`
- *check_interval* (optional)	- icinga `Service` object attribute `check_interval`
- *retry_interval* (optional)	- icinga `Service` object attribute `retry_interval`
- *enable_notifications* (optional)	- icinga `Service` object attribute `enable_notifications`
- *enable_active_checks* (optional)	- icinga `Service` object attribute `enable_active_checks`
- *enable_passive_checks* (optional)	- icinga `Service` object attribute `enable_passive_checks`
- *enable_passive_checks* (optional)	- icinga `Service` object attribute `enable_passive_checks`
- *enable_event_handler* (optional)	- icinga `Service` object attribute `enable_event_handler`
- *enable_flapping* (optional)	- icinga `Service` object attribute `enable_flapping`
- *enable_perfdata* (optional)	- icinga `Service` object attribute `enable_perfdata`
- *event_command* (optional)	- icinga `Service` object attribute `event_command`
- *flapping_threshold* (optional)	- icinga `Service` object attribute `flapping_threshold`
- *volatile* (optional)	- icinga `Service` object attribute `volatile`
- *zone* (optional)	- icinga `Service` object attribute `zone`
- *command_endpoint* (optional)	- icinga `Service` object attribute `command_endpoint`
- *notes* (optional)	- icinga `Service` object attribute `notes`
- *notes_url* (optional)	- icinga `Service` object attribute `notes_url`
- *action_url* (optional)	- icinga `Service` object attribute `action_url`
- *icon_image* (optional)	- icinga `Service` object attribute `icon_image`
- *icon_image_alt* (optional)	- icinga `Service` object attribute `icon_image_alt`
- *custom_vars* (optional)	- icinga `Service` object attribute `vars`
- *assign_where* (optional)	 - an array of `assign where` statements
- *ignore_where* (optional)	 - an array of `ignore where` statements


## LWRP icinga2_servicegroup

LWRP `servicegroup` creates an icinga `ServiceGroup` object.


**LWRP ServiceGroup example**

	icinga2_servicegroup 'servicegroup_name' do
	  disaply_name 'Service Group'
	  groups ['othergroup']
	  assign_where ['"servicegroup_name" in host.vars.servicegroups']
	  ignore_where ['"servicegroup_name" in host.vars.servicegroups']
	end

Above LWRP resource will create an icinga `ServiceGroup` object.


**LWRP Options**

- *action* (optional)	- default :enable, options: :enable, :disable
- *display_name* (optional)	- icinga `ServiceGroup` attribute `display_name`
- *groups* (optional)	- icinga `ServiceGroup` attribute `groups`
- *assign_where* (optional)	 - an array of `assign where` statements
- *ignore_where* (optional)	 - an array of `ignore where` statements


## LWRP icinga2_feature

LWRP `feature` enable or disable an icinga `Feature`.


**LWRP Feature enable example**

	icinga2_feature 'feature'

Above LWRP resource will enable an icinga `Feature`.


**LWRP Feature disable example**

	icinga2_feature 'feature' do
	  action :disable
	end

Above LWRP resource will disable an icinga `Feature`.


**LWRP Options**

- *name*(name_attribute)	- icinga2 feature name
- *action* (optional)	- default :enable, options: :enable, :disable


## LWRP icinga2_user

LWRP `user` creates an icinga `User` object.


**LWRP User Object example**

	icinga2_user 'user_name' do
	  import 'generic-user'
	  enable_notifications true
	  states %w(OK Warning Critical Unknown Up Down)
	  types %w(DowntimeStart DowntimeEnd DowntimeRemoved Custom Acknowledgement Problem Recovery FlappingStart FlappingEnd)
	  display_name 'User Info'
	  groups %w(usergroup)
	  email 'user@domain'
	end


Above LWRP resource will create an icinga `User` object.

**LWRP User Template example**

	icinga2_user 'generic-user' do
	  template true
	end

Above LWRP resource will create an icinga `User` template.


**LWRP Options**

- *action* (optional)	- default :enable, options: :enable, :disable
- *display_name* (optional)	- icinga `User` attribute `display_name`
- *import* (optional)	- icinga `User` attribute `import`
- *groups* (optional)	- icinga `User` attribute `groups`
- *email* (optional)	- icinga `User` attribute `email`
- *pager* (optional)	- icinga `User` attribute `pager`
- *period* (optional)	- icinga `User` attribute `period`
- *states* (optional)	- icinga `User` attribute `states`
- *types* (optional)	- icinga `User` attribute `types`
- *zone* (optional)	- icinga `User` attribute `zone`
- *custom_vars* (optional)	- icinga `User` attribute `vars`
- *enable_notifications* (optional)	- icinga `User` attribute `enable_notifications`
- *template* (optional)	- whether to create a `User` template


## LWRP icinga2_usergroup

LWRP `usergroup` creates an icinga `UserGroup` object.


**LWRP UserGroup example**

	icinga2_usergroup 'usergroup_name' do
	  disaply_name 'User Group'
	  groups ['usergroup']
	  zone 'zone_name'
	end

Above LWRP resource will create an icinga `UserGroup` object.


**LWRP Options**

- *action* (optional)	- default :enable, options: :enable, :disable
- *display_name* (optional)	- icinga `UserGroup` attribute `display_name`
- *groups* (optional)	- icinga `UserGroup` attribute `groups`
- *zone* (optional)	- icinga `UserGroup` attribute `zone`


## LWRP icinga2_zone

LWRP `zone` creates an icinga `Zone` object.


**LWRP Zone example**

	icinga2_zone 'zone' do
	  endpoints %w(endpoint)
	  parent 'parent endpoint'
	end

Above LWRP resource will create an icinga `Zone` object.


**LWRP Options**

- *action* (optional)	- default :enable, options: :enable, :disable
- *endpoints* (optional)	- icinga `Zone` attribute `endpoints`
- *parent* (optional)	- icinga `Zone` attribute `parent`


## LWRP icinga2_endpoint

LWRP `endpoint` creates an icinga `Endpoint` object.


**LWRP Endpoint example**

	icinga2_endpoint 'endpoint' do
	  host 'host address'
	  port 'port'
	  log_duration 'log duration'
	end

Above LWRP resource will create an icinga `Endpoint` object.


**LWRP Options**

- *action* (optional)	- default :enable, options: :enable, :disable
- *host* (optional)	- icinga `Endpoint` attribute `host`
- *port* (optional)	- icinga `Endpoint` attribute `port`
- *log_duration* (optional)	- icinga `Endpoint` attribute `log_duration`


## LWRP icinga2_timeperiod

LWRP `timeperiod` creates an icinga `TimePeriod` object.


**LWRP TimePeriod example**

	icinga2_timeperiod '24x7' do
	  import 'legacy-timeperiod'
	  display_name 'Icinga 2 24x7 TimePeriod'
	  ranges 'monday' => '00:00-24:00',
		'tuesday' => '00:00-24:00',
		'wednesday' => '00:00-24:00',
		'thursday' => '00:00-24:00',
		'friday' => '00:00-24:00',
		'saturday' => '00:00-24:00',
		'sunday' => '00:00-24:00'
	end

Above LWRP resource will create an icinga `TimePeriod` object.

**LWRP TimePeriod template**

	icinga2_timeperiod 'legacy-timeperiod' do
	  template true
	  display_name 'legacy-timeperiod'
	end

Above LWRP resource will create an icinga `TimePeriod` template.


**LWRP Options**

- *action* (optional)	- default :enable, options: :enable, :disable
- *display_name* (optional)	- icinga `TimePeriod` attribute `display_name`
- *import* (optional)	- icinga `TimePeriod` attribute `import`
- *zone* (optional)	- icinga `TimePeriod` attribute `zone`
- *ranges* (optional)	- icinga `TimePeriod` attribute `ranges`
- *template* (optional)	- whether to create an icinga `TimePeriod` template


## LWRP icinga2_eventcommand

LWRP `eventcommand` creates an icinga `EventCommand` object.


**LWRP EventCommand example**

	icinga2_eventcommand 'eventcommand' do
	  command 'command name'
	  custom_vars :attribute => 'value'
	end

Above LWRP resource will create an icinga `EventCommand` object.


**LWRP Options**

- *action* (optional)	- default :enable, options: :enable, :disable
- *import* (optional)	- icinga `EventCommand` attribute `import`
- *command* (optional)	- icinga `EventCommand` attribute `command`
- *env* (optional)	- icinga `EventCommand` attribute `env`
- *timeout* (optional)	- icinga `EventCommand` attribute `timeout`
- *arguments* (optional)	- icinga `EventCommand` attribute `arguments`
- *custom_vars* (optional)	- icinga `EventCommand` attribute `custom_vars`


## LWRP icinga2_checkcommand


LWRP `checkcommand` creates an icinga `CheckCommand` object.


**LWRP CheckCommand example**

	icinga2_checkcommand 'eventcommand' do
	  command 'command name'
	  env :attribute => 'value'
	  arguments :attribute => 'value'
	  custom_vars :attribute => 'value'
	  zone 'zone name'
	end

Above LWRP resource will create an icinga `CheckCommand` object.


**LWRP Options**

- *action* (optional)	- default :enable, options: :enable, :disable
- *import* (optional)	- icinga `CheckCommand` attribute `import`
- *command* (optional)	- icinga `CheckCommand` attribute `command`
- *env* (optional)	- icinga `CheckCommand` attribute `env`
- *timeout* (optional)	- icinga `CheckCommand` attribute `timeout`
- *zone* (optional)	- icinga `CheckCommand` attribute `zone`
- *arguments* (optional)	- icinga `CheckCommand` attribute `arguments`
- *custom_vars* (optional)	- icinga `CheckCommand` attribute `custom_vars`


## LWRP icinga2_scheduleddowntime


LWRP `scheduleddowntime` creates an icinga `ScheduledDowntime` object.


**LWRP ScheduledDowntime example**

	icinga2_scheduleddowntime 'downtime' do
	  host_name 'host name'
	  service_name 'service name'
	  author 'author'
	  comment 'comment'
	  duration 'duration'
	end

Above LWRP resource will create an icinga `ScheduledDowntime` object.


**LWRP Options**

- *action* (optional)	- default :enable, options: :enable, :disable
- *host_name* (optional)	- icinga `ScheduledDowntime` attribute `host_name`
- *service_name* (optional)	- icinga `ScheduledDowntime` attribute `service_name`
- *author* (optional)	- icinga `ScheduledDowntime` attribute `author`
- *comment* (optional)	- icinga `ScheduledDowntime` attribute `comment`
- *fixed* (optional)	- icinga `ScheduledDowntime` attribute `fixed`
- *duration* (optional)	- icinga `ScheduledDowntime` attribute `duration`
- *zone* (optional)	- icinga `ScheduledDowntime` attribute `zone`
- *ranges* (optional)	- icinga `ScheduledDowntime` attribute `ranges`
- *template* (optional)	- whether to create an icinga `ScheduledDowntime` template


## LWRP icinga2_externalcommandlistener

LWRP `externalcommandlistener` creates an icinga `ExternalCommandListener` object.


**LWRP ExternalCommandListener example**

	icinga2_externalcommandlistener 'externalcommandlistener' do
	  library 'library'
	  command_path 'command path'
	end

Above LWRP resource will create an icinga `ExternalCommandListener` config object.


**LWRP Options**

- *action* (optional)	- default :enable, options: :enable, :disable
- *library* (optional)	- default 'compat', icinga `ExternalCommandListener` attribute `library`
- *command_path* (optional)	- icinga `ExternalCommandListener` attribute `command_path`


## LWRP icinga2_gelfwriter

LWRP `gelfwriter` creates an icinga `GelfWriter` object.


**LWRP GelfWrite example**

	icinga2_gelfwriter 'gelfwriter' do
	  library 'library'
	  host 'host address'
	  port 'port'
	  source 'source'
	end

Above LWRP resource will create an icinga `GelfWriter` config object.


**LWRP Options**

- *action* (optional)	- default :enable, options: :enable, :disable
- *library* (optional)	- default 'perfdata', icinga `GelfWriter` attribute `library`
- *host* (optional)	- icinga `GelfWriter` attribute `host`
- *port* (optional)	- icinga `GelfWriter` attribute `port`
- *source* (optional)	- icinga `GelfWriter` attribute `source`


## LWRP icinga2_graphitewriter

LWRP `graphitewriter` creates an icinga `GraphiteWriter` object.


**LWRP GraphiteWriter example**

	icinga2_graphitewriter 'graphitewriter' do
	  library 'library'
	  host 'host address'
	  port 'port'
	end

Above LWRP resource will create an icinga `GraphiteWriter` config object.


**LWRP Options**

- *action* (optional)	- default :enable, options: :enable, :disable
- *library* (optional)	- default 'perfdata', icinga `GraphiteWriter` attribute `library`
- *host* (optional)	- icinga `GraphiteWriter` attribute `host`
- *port* (optional)	- icinga `GraphiteWriter` attribute `port`
- *host_name_template* (optional)	- icinga `GraphiteWriter` attribute `host_name_template`
- *service_name_template* (optional)	- icinga `GraphiteWriter` attribute `service_name_template`


## LWRP icinga2_idomysqlconnection

LWRP `idomysqlconnection` creates an icinga `IdoMySqlConnection` object.


**LWRP IdoMySqlConnection example**

	icinga2_idomysqlconnection 'idomysqlconnection' do
	  library 'library'
	  host 'host address'
	  port 'port'
	  user 'user name'
	  password 'password'
	  database 'database name'
	end

Above LWRP resource will create an icinga `IdoMySqlConnection` config object.


**LWRP Options**

- *action* (optional)	- default :enable, options: :enable, :disable
- *library* (optional)	- default 'db_ido_mysql', icinga `IdoMySqlConnection` attribute `library`
- *host* (optional)	- icinga `IdoMySqlConnection` attribute `host`
- *port* (optional)	- icinga `IdoMySqlConnection` attribute `port`
- *user* (optional)	- icinga `IdoMySqlConnection` attribute `user`
- *password* (optional)	- icinga `IdoMySqlConnection` attribute `password`
- *database* (optional)	- icinga `IdoMySqlConnection` attribute `database`
- *table_prefix* (optional)	- icinga `IdoMySqlConnection` attribute `table_prefix`
- *instance_name* (optional)	- icinga `IdoMySqlConnection` attribute `instance_name`
- *instance_description* (optional)	- icinga `IdoMySqlConnection` attribute `instance_description`
- *enable_ha* (optional)	- icinga `IdoMySqlConnection` attribute `enable_ha`
- *failover_timeout* (optional)	- icinga `IdoMySqlConnection` attribute `failover_timeout`
- *enable_ha* (optional)	- icinga `IdoMySqlConnection` attribute `enable_ha`
- *cleanup* (optional)	- icinga `IdoMySqlConnection` attribute `cleanup`
- *categories* (optional)	- icinga `IdoMySqlConnection` attribute `categories`


## LWRP icinga2_idopgsqlconnection

LWRP `idopgsqlconnection` creates an icinga `IdoPgSqlConnection` object.


**LWRP IdoPgSqlConnection example**

	icinga2_idopgsqlconnection 'idopgsqlconnection' do
	  library 'library'
	  host 'host address'
	  port 'port'
	  user 'user name'
	  password 'password'
	  database 'database name'
	end

Above LWRP resource will create an icinga `IdoPgSqlConnection` config object.


**LWRP Options**

- *action* (optional)	- default :enable, options: :enable, :disable
- *library* (optional)	- default 'db_ido_mysql', icinga `IdoPgSqlConnection` attribute `library`
- *host* (optional)	- icinga `IdoPgSqlConnection` attribute `host`
- *port* (optional)	- icinga `IdoPgSqlConnection` attribute `port`
- *user* (optional)	- icinga `IdoPgSqlConnection` attribute `user`
- *password* (optional)	- icinga `IdoPgSqlConnection` attribute `password`
- *database* (optional)	- icinga `IdoPgSqlConnection` attribute `database`
- *table_prefix* (optional)	- icinga `IdoPgSqlConnection` attribute `table_prefix`
- *instance_name* (optional)	- icinga `IdoPgSqlConnection` attribute `instance_name`
- *instance_description* (optional)	- icinga `IdoPgSqlConnection` attribute `instance_description`
- *enable_ha* (optional)	- icinga `IdoPgSqlConnection` attribute `enable_ha`
- *failover_timeout* (optional)	- icinga `IdoPgSqlConnection` attribute `failover_timeout`
- *enable_ha* (optional)	- icinga `IdoPgSqlConnection` attribute `enable_ha`
- *cleanup* (optional)	- icinga `IdoPgSqlConnection` attribute `cleanup`
- *categories* (optional)	- icinga `IdoPgSqlConnection` attribute `categories`


## LWRP icinga2_sysloglogger

LWRP `sysloglogger` creates an icinga `SyslogLogger` object.


**LWRP SyslogLogger example**

	icinga2_sysloglogger 'sysloglogger' do
	  severity 'critical'
	end

Above LWRP resource will create an icinga `SyslogLogger` object.


**LWRP Options**

- *name*(name_attribute)	- icinga `SyslogLogger` name
- *severity* (optional)	- icinga `SyslogLogger` attribute `port`


## LWRP icinga2_applynotification

 To be added.


## LWRP icinga2_notification

 To be added.


## LWRP icinga2_notificationcommand

 To be added.


## LWRP icinga2_applydependency

 To be added.



## Cookbook Advanced Attributes

* `default['icinga2']['disable_conf_d']` (default: `false`): disable icinga2 `conf.d` default configuration and use LWRP to manage icinga2 objects / templates

* `default['icinga2']['add_cloud_custom_vars']` (default: `true`): add cloud node attributes, limited cloud provider support is available

* `default['icinga2']['enable_features_recipe']` (default: `false`): manage icinga2 features via node attribute `node['icinga2']['enable_features']`

* `default['icinga2']['features_enabled_dir']` (default: `/etc/icinga2/features-enabled`): icinga2 enabled features location

* `default['icinga2']['features_available_dir']` (default: `/etc/icinga2/features-available`): icinga2 available features location

* `default['icinga2']['cluster_attribute']` (default: `nil`): icinga2 node cluster attribute name to add node cluster attribute and value to node vars

* `default['icinga2']['application_attribute']` (default: `nil`): icinga2 node application attribute name to add node application attribute and value to node vars

* `default['icinga2']['enable_cluster_hostgroup']` (default: `true`): creates icinga2 HostGroup Objects for environment clusters

* `default['icinga2']['enable_application_hostgroup']` (default: `true`): creates icinga2 HostGroup Objects for environment application types

* `default['icinga2']['enable_role_hostgroup']` (default: `false`): creates icinga2 HostGroup Objects for environment roles

* `default['icinga2']['limit_region']` (default: `true`): whether to limit monitoring to icinga2 server region, e.g. for ec2 collect nodes belongs to same region

* `default['icinga2']['use_fqdn_resolv']` (default: `false`): whether to determine node `address` from fqdn

* `default['icinga2']['failover_fqdn_address']` (default: `true`): whether to use ohai attribute `node['ipaddress']` if node fqdn does not exists

* `default['icinga2']['ignore_node_error']` (default: `false`): whether to ignore node errors if node fqdn, hostname and chef_environment is missing while collecting for a chef environment

* `default['icinga2']['ignore_resolv_error']` (default: `true`): whether to ignore node fqdn resolve errors while collecting for a chef environment

* `default['icinga2']['web_engine']` (default: `'apache'): icinga2 web server, currently supports only apache

* `default['icinga2']['install_nagios_plugins']` (default: `true`): install nagios plugins to icinga2 server and clients

* `default['icinga2']['features']` (default: `[available features]`): list of icinga2 available features

* `default['icinga2']['enable_features']` (default: `[checker command compatlog mainlog notification statusdata perfdata]`): icinga2 enabled features


## Cookbook Core Attributes

* `default['icinga2']['version']` (default: `2.2.2-1`): icinga2 version

* `default['icinga2']['conf_dir']` (default: `/etc/icinga2`): icinga2 configuration location

* `default['icinga2']['conf_d_dir']` (default: `/etc/icinga2/conf.d`): icinga2 conf.d directory location

* `default['icinga2']['pki_dir']` (default: `/etc/icinga2/pki`): icinga2 pki directory location

* `default['icinga2']['scripts_dir']` (default: `/etc/icinga2/scripts`): icinga2 script directory location

* `default['icinga2']['zones_dir']` (default: `/etc/icinga2/zones.d`): icinga2 zones.d directory location

* `default['icinga2']['databag']` (default: `'icinga2'): icinga2 databag name, currently not used

* `default['icinga2']['objects_d']` (default: `'objects.d`): cookbook created icinga2 Object/Templates resources directory name

* `default['icinga2']['objects_dir']` (default: `/etc/icinga2/objects.d`): cookbook created icinga2 Object/Templates resources directory location

* `default['icinga2']['run_dir']` (default: `/var/run/icinga2`): icinga2 run directory

* `default['icinga2']['run_cmd_dir']` (default: `/var/run/icinga2/cmd`): icinga2 location for process `icinga2.cmd`

* `default['icinga2']['cache_dir']` (default: `/var/cache/icinga2`): icinga2 cache directory location

* `default['icinga2']['spool_dir']` (default: `/var/spool/icinga2`): icinga2 spool directory location

* `default['icinga2']['lib_dir']` (default: `/var/lib/icinga2`): icinga2 lib directory location

* `default['icinga2']['log_dir']` (default: `/var/log/icinga2`): icinga2 core process log directory location

* `default['icinga2']['cache_dir']` (default: `/var/cache/icinga2`): icinga2 cache directory location

* `default['icinga2']['service_name']` (default: `icinga2'`): icinga2 process name*

* `default['icinga2']['service_config_file']` (default: `/etc/default/icinga2`): icinga2 * process configuration file

* `default['icinga2']['plugins_dir']` (default: `/usr/lib/nagios/plugins`): icinga2 plugins directory location

* `default['icinga2']['custom_plugins_dir']` (default: `/opt/icinga2_custom_plugins`): icinga2 custom plugins directory

* `default['icinga2']['admin_user']` (default: `icingaadmin`): icinga2 admin user

* `default['icinga2']['user']` (default: `icinga`): icinga2 user

* `default['icinga2']['group']` (default: `icinga`): icinga2 user group

* `default['icinga2']['cmdgroup']` (default: `icingacmd`): icinga2 cmd user group


## Cookbook Icinga2 Constants Attributes


* `default['icinga2']['server']['constants']['PluginDir']` (default: `node['icinga2']['plugins_dir']`): icinga2 plugins directory location

* `default['icinga2']['server']['constants']['ManubulonPluginDir']` (default: `node['icinga2']['plugins_dir']`): icinga2 plugins directory location

* `default['icinga2']['server']['constants']['TicketSalt']` (default: `ch2ng3m3`): icinga2 default TicketSalt key


## Cookbook Icinga2 Host Object default Attributes

* `default['icinga2']['server']['object']['host']['import']` (default: `'generic-host`)

* `default['icinga2']['server']['object']['host']['max_check_attempts']` (default: `3`)

* `default['icinga2']['server']['object']['host']['check_period']` (default: `nil`)

* `default['icinga2']['server']['object']['host']['check_interval']` (default: `1800`)

* `default['icinga2']['server']['object']['host']['retry_interval']` (default: `60`)

* `default['icinga2']['server']['object']['host']['enable_notifications']` (default: `true`)

* `default['icinga2']['server']['object']['host']['enable_active_checks']` (default: `true`)

* `default['icinga2']['server']['object']['host']['enable_passive_checks']` (default: `false`)

* `default['icinga2']['server']['object']['host']['enable_event_handler']` (default: `true`)

* `default['icinga2']['server']['object']['host']['enable_flapping']` (default: `true`)

* `default['icinga2']['server']['object']['host']['enable_perfdata']` (default: `true`)

* `default['icinga2']['server']['object']['host']['event_command']` (default: `nil`)

* `default['icinga2']['server']['object']['host']['flapping_threshold']` (default: `nil`)

* `default['icinga2']['server']['object']['host']['volatile']` (default: `nil`)

* `default['icinga2']['server']['object']['host']['check_command']` (default: `hostalive`)

* `default['icinga2']['server']['object']['host']['zone']` (default: `nil`)

* `default['icinga2']['server']['object']['host']['command_endpoint']` (default: `nil`)

* `default['icinga2']['server']['object']['host']['zone_attribute']` (default: `icinga2_zone`)




## Cookbook Ulimit Attributes

 * `default[:icinga2][:limits][:memlock]` (default: `unlimited`): Icinga2 service user memory limit

 * `default[:icinga2][:limits][:nofile]` (default: `48000`): Icinga2 service user file limit

 * `default[:icinga2][:limits][:nproc]` (default: `unlimited`): Icinga2 service user process limit


## Cookbook Repository Attributes

* `default['icinga2']['yum']['description']` (default: `ICINGA Stable Release'):

* `default['icinga2']['yum']['mirrorlist']` (default: `nil`):

* `default['icinga2']['yum']['gpgcheck']` (default: `true`):

* `default['icinga2']['yum']['enabled']` (default: `true`):

* `default['icinga2']['yum']['gpgkey']` (default: `http://packages.icinga.org/icinga.key`):

* `default['icinga2']['yum']['action']` (default: `:create`):

* `default['icinga2']['yum']['baseurl']` (default: ``):

* `default['icinga2']['apt']['repo']` (default: `ICINGA Stable Release`):

* `default['icinga2']['apt']['uri']` (default: `http://ppa.launchpad.net/formorer/icinga/ubuntu`):

* `default['icinga2']['apt']['distribution']` (default: `node['lsb']['codename']`):

* `default['icinga2']['apt']['keyserver']` (default: `keyserver.ubuntu.com`):

* `default['icinga2']['apt']['components']` (default: `[main]`):

* `default['icinga2']['apt']['deb_src']` (default: `true`):

* `default['icinga2']['apt']['repo_key']` (default: `http://packages.icinga.org/icinga.key`):

* `default['icinga2']['apt']['action']` (default: `:add`):


## Cookbook Classic UI CGI Core Attributes

* `default['icinga2']['classic_ui']['version']` (default: `2.2.2-1`): icinga2 classic-ui package version

* `default['icinga2']['classic_ui']['gui_version']` (default: `1.12.0-0`): icinga2 gui package version

* `default['icinga2']['classic_ui']['web_root']` (default: `/usr/share/icinga`): icinga2 web doc root directory location

* `default['icinga2']['classic_ui']['home_dir']` (default: `/etc/icinga`): icinga2 classic ui configuration home directory location

* `default['icinga2']['classic_ui']['conf_dir']` (default: `/etc/icinga`): icinga2 classic ui configuration directory location

* `default['icinga2']['classic_ui']['log_dir']` (default: `/var/log/icinga`): icinga2 classic ui log directory location

* `default['icinga2']['classic_ui']['cgi_log_dir']` (default: `/var/log/icinga/gui`): icinga2 gui log directory location


## Cookbook Classic UI CGI User Access Attributes

* `default['icinga2']['classic_ui']['users']` (default: `{ 'icingaadmin' => '$apr1$MZtKRLAy$AV9OiJ.V/mI9g30bHn9ol1' }`): Hash List of user => password (md5) for class ui access

* `default['icinga2']['classic_ui']['authorized_for_system_information']` (default: `[node['icinga2']['admin_user']]`): Array list of users authorized for system information

* `default['icinga2']['classic_ui']['authorized_for_configuration_information']` (default: `[node['icinga2']['admin_user']]`): Array list of users authorized for configuration information

* `default['icinga2']['classic_ui']['authorized_for_full_command_resolution']` (default: `[node['icinga2']['admin_user']]`): Array list of users authorized for full command resolution

* `default['icinga2']['classic_ui']['authorized_for_system_commands']` (default: `[node['icinga2']['admin_user']]`): Array list of users authorized for system commands

* `default['icinga2']['classic_ui']['authorized_for_all_services']` (default: `[node['icinga2']['admin_user']]`): Array list of users authorized for all services

* `default['icinga2']['classic_ui']['authorized_for_all_hosts']` (default: `[node['icinga2']['admin_user']]`): Array list of users authorized for all hosts

* `default['icinga2']['classic_ui']['authorized_for_all_service_commands']` (default: `[node['icinga2']['admin_user']]`): Array list of users authorized for all service commands

* `default['icinga2']['classic_ui']['authorized_for_all_host_commands']` (default: `[node['icinga2']['admin_user']]`): Array list of users authorized for all host commands


## Cookbook Classic UI CGI Configuration Attributes

* `default['icinga2']['classic_ui']['cgi']['standalone_installation']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['physical_html_path']` (default: `/usr/share/icinga`)

* `default['icinga2']['classic_ui']['cgi']['url_html_path']` (default: `/icinga`)

* `default['icinga2']['classic_ui']['cgi']['url_stylesheets_path']` (default: `/icinga/stylesheets`)

* `default['icinga2']['classic_ui']['cgi']['http_charset']` (default: `utf-8`)

* `default['icinga2']['classic_ui']['cgi']['refresh_rate']` (default: `60`)

* `default['icinga2']['classic_ui']['cgi']['refresh_type']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['escape_html_tags']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['result_limit']` (default: `50`)

* `default['icinga2']['classic_ui']['cgi']['show_tac_header']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['use_pending_states']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['first_day_of_week']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['suppress_maintenance_downtime']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['action_url_target']` (default: `main`)

* `default['icinga2']['classic_ui']['cgi']['notes_url_target']` (default: `main`)

* `default['icinga2']['classic_ui']['cgi']['use_authentication']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['use_ssl_authentication']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['lowercase_user_name']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['authorized_for_system_information']` (default: `node['icinga2']['classic_ui']['authorized_for_system_information'].join(',')`)

* `default['icinga2']['classic_ui']['cgi']['authorized_for_configuration_information']` (default: `node['icinga2']['classic_ui']* ['authorized_for_configuration_information'].join(',')`)

* `default['icinga2']['classic_ui']['cgi']['authorized_for_full_command_resolution']` (default: `node['icinga2']['classic_ui']['authorized_for_full_command_resolution'].join(',')`)

* `default['icinga2']['classic_ui']['cgi']['authorized_for_system_commands']` (default: `node['icinga2']['classic_ui']['authorized_for_system_commands'].join(',')`)

* `default['icinga2']['classic_ui']['cgi']['authorized_for_all_services']` (default: `node['icinga2']['classic_ui']['authorized_for_all_services'].join(',')`)

* `default['icinga2']['classic_ui']['cgi']['authorized_for_all_hosts']` (default: `node['icinga2']['classic_ui']['authorized_for_all_hosts'].join(',')`)

* `default['icinga2']['classic_ui']['cgi']['authorized_for_all_service_commands']` (default: `node['icinga2']['classic_ui']['authorized_for_all_service_commands'].join(',')`)

* `default['icinga2']['classic_ui']['cgi']['authorized_for_all_host_commands']` (default: `node['icinga2']['classic_ui']['authorized_for_all_host_commands'].join(',')`)

* `default['icinga2']['classic_ui']['cgi']['show_all_services_host_is_authorized_for']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['show_partial_hostgroups']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['show_partial_servicegroups']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['default_statusmap_layout']` (default: `5`)

* `default['icinga2']['classic_ui']['cgi']['status_show_long_plugin_output']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['display_status_totals']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['highlight_table_rows']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['add_notif_num_hard']` (default: `28`)

* `default['icinga2']['classic_ui']['cgi']['add_notif_num_soft']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['use_logging']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['cgi_log_file']` (default: `node['icinga2']['classic_ui']['cgi_log_dir']/icinga-cgi.log`)

* `default['icinga2']['classic_ui']['cgi']['cgi_log_rotation_method']` (default: `d`)

* `default['icinga2']['classic_ui']['cgi']['cgi_log_archive_path']` (default: `node['icinga2']['classic_ui']['cgi_log_dir']`)

* `default['icinga2']['classic_ui']['cgi']['enforce_comments_on_actions']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['send_ack_notifications']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['persistent_ack_comments']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['lock_author_names']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['default_downtime_duration']` (default: `7200`)

* `default['icinga2']['classic_ui']['cgi']['set_expire_ack_by_default']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['default_expiring_acknowledgement_duration']` (default: `86_400`)

* `default['icinga2']['classic_ui']['cgi']['default_expiring_disabled_notifications_duration']` (default: `86_400`)

* `default['icinga2']['classic_ui']['cgi']['tac_show_only_hard_state']` (default: `0`)

* `default['icinga2']['classic_ui']['cgi']['show_tac_header_pending']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['exclude_customvar_name']` (default: `PASSWORD,COMMUNITY`)

* `default['icinga2']['classic_ui']['cgi']['exclude_customvar_value']` (default: `secret`)

* `default['icinga2']['classic_ui']['cgi']['extinfo_show_child_hosts']` (default: `)

* `default['icinga2']['classic_ui']['cgi']['tab_friendly_titles']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['object_cache_file']` (default: `/var/cache/icinga2/objects.cache`)

* `default['icinga2']['classic_ui']['cgi']['status_file']` (default: `node['icinga2']['cache_dir']/status.dat`)

* `default['icinga2']['classic_ui']['cgi']['resource_file']` (default: `node['icinga2']['classic_ui']['conf_dir']/resource.cfg`)

* `default['icinga2']['classic_ui']['cgi']['command_file']` (default: `node['icinga2']['run_dir']/cmd/icinga2.cmd`)

* `default['icinga2']['classic_ui']['cgi']['check_external_commands']` (default: `1`)

* `default['icinga2']['classic_ui']['cgi']['interval_length']` (default: `60`)

* `default['icinga2']['classic_ui']['cgi']['status_update_interval']` (default: `10`)

* `default['icinga2']['classic_ui']['cgi']['log_file']` (default: `node['icinga2']['log_dir']/compat/icinga.log`)

* `default['icinga2']['classic_ui']['cgi']['log_rotation_method']` (default: `h`)

* `default['icinga2']['classic_ui']['cgi']['log_archive_path']` (default: `node['icinga2']['log_dir']/compat/archives`)

* `default['icinga2']['classic_ui']['cgi']['date_format']` (default: `us`)


## Cookbook Dependencies

* `ulimit` cookbook
* `apache2` cookbook
* `yum` cookbook
* `apt` cookbook
* `pnp4nagios` cookbook


## Contributing

1. Fork the repository on Github
2. Create a named feature branch (like `add_component_x`)
3. Write your change
4. Write tests for your change (if applicable)
5. Run the tests (`rake`), ensuring they all pass
6. Write new resource/attribute description to `README.md`
7. Write description about changes to PR
8. Submit a Pull Request using Github


## Copyright & License

Authors:: Virender Khatri and [Contributors]

<pre>
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
</pre>


[Icinga2]: https://www.icinga.org/
[Chef]: https://www.chef.io/
[Contributors]: https://github.com/vkhatri/chef-icinga2/graphs/contributors
[Install]: https://github.com/icinga/icinga2/
