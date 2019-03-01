ELK_Stack
=========

Installs ELK_Stack on the target with default values specified in the Elastic formal instructions. Is tailored to install on CentOS, RHEL, Ubuntu, or Debian.

This play will run out of the box for logging Syslog events (SSH, Sudo events, Syslog, and User/Group Creation), with no changes required. Additional modules can be added as desired after the play is ran. Changes are required if the user does not want to install with the defaults.

For RHEL distros, I open the ports in firewalld automatically. For any other distro, you will need to open the ports in your firewall as needed.

Requirements
------------

1) Configure your hosts and ansible user at ./playbooks/ELK_Stack.yml
2) Configure any desired changes from the defaults in ./vars/main.yml (see Role Variables section for what to place).
3) Run the play. I usually run my play with ansible-playbook -kK /path/to/play.

Role Variables
--------------

The following variables are used. All variables have a default value. 

If you wish to change the default value, simply add the variable to ./vars/main.yml and set its properties (i.e. "server_ip: 10.10.10.10"). Anything in ./vars/ will overwrite the defaults. We do not recommend changing ./defaults/main.yml.

| Variable  | Location | Required | Default | Description
| ------------- | ------------- | ------------- | ------------- | ------------- |
| server_ip | ./roles/elk_stack/defaults/main.yml | Yes | localhost | Used to configure the host of ELK services |
| elastic_port | ./roles/elk_stack/defaults/main.yml | Yes | 9200 | Configures the port elasticsearch will listen on |
| kibana_port | ./roles/elk_stack/defaults/main.yml | Yes | 5601 | Configures the port kibana will listen on |
| beat_port | ./roles/elk_stack/defaults/main.yml | Yes | 5044 | Configures the port for logstash to listen on and where beats send to |
| filebeat_conf | ./roles/elk_stack/defaults/main.yml | Yes | /etc/filebeat/filebeat.yml | Path to the filebeat configuration file |
| elastic_conf | ./roles/elk_stack/defaults/main.yml | Yes | /etc/elasticsearch/elasticsearch.yml | Path to the elasticsearch configuration file |
| kibana_conf | ./roles/elk_stack/defaults/main.yml | Yes | /etc/kibana/kibana.yml | Path to the kibana configuration file |
| logstash_beat_conf | ./roles/elk_stack/defaults/main.yml | Yes | /etc/logstash/conf.d/01-beats-input.conf | Path to the input configuration for logstash |
| logstash_sysfilter_conf | ./roles/elk_stack/defaults/main.yml | Yes | /etc/logstash/conf.d/10-syslog-filter.conf | Path to the filter configuration for logstash |
| logstash_output_conf | ./roles/elk_stack/defaults/main.yml | Yes | /etc/logstash/conf.d/30-elasticsearch-output.conf | Path to the output configuration for logstash |

Dependencies
------------

N/A

Example Playbook
----------------

    - hosts: 10.10.10.10
      roles:
         - elk_stack

Author Information
------------------

Steven Craig, ISSM, 24Feb19
