<!SLIDE >
# integration

<!SLIDE bullets incremental>
# Chef ties it all together

* recipes to setup each componenent
* Discovery library integrates parts
* gdash dashboards generated via LWRP
* stdin metrics client available for arbitrary metrics

<!SLIDE code>
# role[monitor]

    @@@ Ruby
    name "monitor"
    description "Setup monitoring components"
    
    run_list [
              "role[sensu_client]",
              "role[collectd_client]"
             ]

# role[sensu_client]

    @@@ Ruby
    name "sensu_client"
    description "sensu monitoring client"
    
    run_list [
             "recipe[sensu::client]"
             ]
    
<!SLIDE code>
# roles[collectd_client]

    @@@ Ruby
    name "collectd_client"
    description "collect client role"

    run_list [ "recipe[collectd::client]" ].
      concat [
          "load",
          "syslog",
          "cpu",
          "df",
          "disk",
          "interface",
          "memory",
          "swap"
         ].map{ |p| "recipe[collectd_plugins::#{p}]" }

.notes client bits are actually pretty simple

<!SLIDE commandline incremental>

    $ knife node show echelon.hw-ops.com
    Node Name:   echelon.hw-ops.com
    Environment: production
    FQDN:        echelon.hw-ops.com
    IP:          10.10.13.37
    Run List:    role[ubuntu_base], role[echelon]
    Roles:       ubuntu_base, users, utilities, firewall, monitor, sensu_client, 
                 sensu, collectd_client, holy_grail, collectd_server,
                 graphite_server, sensu_server, gdash
    Recipes:     sudo, users::sysadmins, chef-client::delete_validation,
                 chef-client::config, chef-client::cron, chef_gem,
                 ntp, logwatch, log_rotations, tmux, sysstat, htop, mg,
                 iptables, openssh, sensu::client, collectd::client, 
                 collectd_plugins::load, collectd_plugins::syslog,
                 collectd_plugins::cpu, collectd_plugins::df, collectd_plugins::disk,
                 collectd_plugins::interface, collectd_plugins::memory, 
                 collectd_plugins::swap, collectd::server,
                 collectd::firewall, runit, collectd_plugins::carbon,
                 graphite, graphite::firewall, sensu::redis, sensu::rabbitmq,
                 sensu::server, sensu::api, sensu::dashboard, sensu::firewall,
                 gdash::basic_dashboard, gdash, gdash::firewall,
                 custom-dashboards
    Platform:    ubuntu 11.10

<!SLIDE center>

![monitor all the things](../img/monitor-allthethings.png)

<!SLIDE code>
# role[echelon]

    @@@ Ruby
    name "echelon"
    description "monitoring master"

    run_list [
        "role[collectd_server]",
        "role[graphite_server]",
        "role[sensu_server]",
        "recipe[gdash::basic_dashboard]"
        "recipe[gse-dashboards]",
        ]                                                            

<!SLIDE code small>
# role[collectd_server]

    @@@ Ruby
    name "collectd_server"
    description "collectd server role"

    run_list [
              "recipe[collectd::server]",
              "recipe[iptables]",
              "recipe[collectd::firewall]",
              "recipe[runit]"
             ].
      concat [
              "load",
              "syslog",
              "carbon", # wait wut? That's right, carbon
              "cpu",
              "df",
              "disk",
              "interface",
              "memory",
              "swap"
             ].map{ |p| "recipe[collectd_plugins::#{p}]" }

<!SLIDE code smaller>
# recipe[collectd_plugins::carbon]

    @@@ Ruby
    case node.platform
    when "ubuntu", "debian"
      package "libpython2.7"
    end

    remote_file File.join(node[:collectd][:plugin_dir], "carbon_writer.py") do
      source "https://raw.github.com/indygreg/collectd-carbon/master/carbon_writer.py"
      owner "root"
      group "root"
      mode "644"
      action :create_if_missing
    end

    collectd_python_plugin "carbon_writer" do
      options(:line_receiver_host => "127.0.0.1",
              :line_receiver_port => 2003,
              :differentiate_counters_over_time => true,
              :lowercase_metric_names => true,
              "TypesDB" => node[:collectd][:types_db],
              :metric_prefix => "collectd" )
    end
