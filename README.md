ELK_Stack
=========

Installs ELK_Stack on the target with default values specified in the Elastic formal instructions. Has been tested on CentOS 7 and Ubuntu 18. Should work on all RedHat families and Debian families.

This play will run out of the box with no changes necessary. But if accepting all defaults, it will only install on localhost. Meaning Kibana will not be reachable except from itself. You will need to configure a reverse proxy on the machine (recommend NGINX) as well in order to reach it from localhost, or change the IPs to be a reachable IP by changing the corresponding configs (i.e. /etc/kibana/kibana.yml). Or you can simply change the variable with the customization steps below and everything will be reachable immediatley after the play concludes.

This play installs and configures Elasticsearch, Logstash, Kibana, and then calls the beats role which also installs and configures Auditbeat and Filebeat. This play loads all the templates and dashboards to easily view the information. Additional modules can be added as desired after the play is ran. Changes are required if the user does not want to install with the defaults.

The play also configures indexes to be rotated on a month basis and keeps it as a single shard with no replicas. This play is designed for a small environment. You can edit the shards and replica defaults through the variables. For larger environments this is strongly recommended as your index will be very large and have no backups. If you wish for daily rotation, change the following in ./roles/elk_stack/tasks/configure_elastic.yml located under the "Configure Logstash's Output" task:

- current:   index => "%{[@metadata][beat]}-%{[@metadata][version]}-%{+YYYY.MM}"
- change to: index => "%{[@metadata][beat]}-%{[@metadata][version]}-%{+YYYY.MM.dd}"

This play also installs curator which will automatically clean out indices older than X days (default 365). It adds this to the crontab.

For RHEL distros, I open the ports in firewalld automatically. For Debian I open UFW. Comment these out if unused.

The beats role can be ran independently by running the ./playbooks/Beats.yml on the targets you wish to audit/monitor. It also has a separate readme contained in its role root folder for more information.

**As I am making this, there are known issues with the 6.7.1 auditbeat for RHEL. The os_family it pulls does not line up with the what its looking for: expects "RedHat", gets "rhel". Because of this, the service won't start. This should be fixed in 6.7.2 based on issues reported in github**

Requirements
------------

1) Configure your hosts and ansible user at ./playbooks/ELK_Stack.yml
2) Configure any desired changes from the defaults in ./roles/elk_stack/vars/main.yml (see Role Variables section for what to place). Add the variable and the desired setting under the ---
3) Run the play. I usually run my play with ansible-playbook -kK /path/to/play.

Role Variables
--------------

The following variables are used. All variables have a default value.

If you wish to change the default value, simply add the variable to ./vars/main.yml and set its properties (i.e. "server_ip: 10.10.10.10"). Anything in ./vars/ will overwrite the defaults. We do not recommend changing ./defaults/main.yml.

| Variable  | Location | Required | Default | Description
| ------------- | ------------- | ------------- | ------------- | ------------- |
| auditbeat_conf | ./roles/elk_stack/defaults/main.yml | Yes | /etc/auditbeat/auditbeat.yml | Path to the auditbeat configuration file |
| beat_port | ./roles/elk_stack/defaults/main.yml | Yes | 5044 | Configures the port for logstash to listen on and where beats send to |
| curator_conf | ./roles/elk_stack/defaults/main.yml | Yes | /etc/curator/curator.yml | Path to a curator configuration file |
| curator_delete_conf | ./roles/elk_stack/defaults/main.yml | Yes | /etc/curator/delete_indices.yml | Path to a curator configuration file for delete actions |
| delete_after_days | ./roles/elk_stack/defaults/main.yml | Yes | 365 | Configures the threshold of when indicies are deleted |
| elastic_conf | ./roles/elk_stack/defaults/main.yml | Yes | /etc/elasticsearch/elasticsearch.yml | Path to the elasticsearch configuration file |
| elastic_port | ./roles/elk_stack/defaults/main.yml | Yes | 9200 | Configures the port elasticsearch will listen on |
| filebeat_conf | ./roles/elk_stack/defaults/main.yml | Yes | /etc/filebeat/filebeat.yml | Path to the filebeat configuration file |
| kibana_conf | ./roles/elk_stack/defaults/main.yml | Yes | /etc/kibana/kibana.yml | Path to the kibana configuration file |
| kibana_port | ./roles/elk_stack/defaults/main.yml | Yes | 5601 | Configures the port kibana will listen on |
| logstash_beat_conf | ./roles/elk_stack/defaults/main.yml | Yes | /etc/logstash/conf.d/01-beats-input.conf | Path to the input configuration for logstash |
| logstash_output_conf | ./roles/elk_stack/defaults/main.yml | Yes | /etc/logstash/conf.d/30-elasticsearch-output.conf | Path to the output configuration for logstash |
| logstash_sysfilter_conf | ./roles/elk_stack/defaults/main.yml | Yes | /etc/logstash/conf.d/10-syslog-filter.conf | Path to the system logs filter configuration for logstash |
| replicas | ./roles/elk_stack/defaults/main.yml | Yes | 0 | The amount of replicas that will be kept. If you change this, you need Elasticsearch and configured installed on other servers for the replication to occur. The index status will show yellow as a warning that replication hasn't occurred if you don't |
| server_ip | ./roles/elk_stack/defaults/main.yml | Yes | localhost | Used to configure the host of ELK services |
| shards | ./roles/elk_stack/defaults/main.yml | Yes | 1 | The amount of shards that will be kept (Applied to all indexes) |
| template_path | ./roles/elk_stack/defaults/main.yml | Yes | /etc/logstash/templates/shards.json | Path where the template file will be created/stored at |

This play also depends on the beats role which is contained in the same repo. The following are the variables from that role. These fields are passed automatically from the ELK_Stack role so you only need to change the ELK_Stack vars while running that play; if running the beats role individually (i.e. to install beats on other systems), these fields might need changed as desired:

| Variable  | Location | Required | Default | Description
| ------------- | ------------- | ------------- | ------------- | ------------- |
| auditbeat_conf | ./roles/beats/defaults/main.yml | Yes | /etc/auditbeat/auditbeat.yml | Path to the auditbeat configuration file |
| beat_port | ./roles/beats/defaults/main.yml | Yes | 5044 | Configures the port for logstash is listening on to point beats at |
| elk_ip | ./roles/beats/vars/main.yml | Yes | N/A | Configures beats to point to ELK Stack |
| filebeat_conf | ./roles/beats/defaults/main.yml | Yes | /etc/filebeat/filebeat.yml | Path to the filebeat configuration file |

Dependencies
------------

This play depends on the beats role which is contained in the same repo.

Example Playbook
----------------

    - hosts: 10.10.10.10
      roles:
         - elk_stack

Author Information
------------------

Steven Craig, ISSM, 24Feb19
