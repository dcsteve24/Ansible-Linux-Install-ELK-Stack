Beats
=========

Installs Filebeat and Auditbeat on the target(s) with values specified to report to your configured ELK stack. Has been tested on CentOS 7, RHEL 7, and Ubuntu 16/18. Should work on all RedHat families and Debian families.

This play will run the basic system auditing for logging Syslog events (SSH, Sudo events, Syslog, and User/Group Creation) as well as the auditbeat logging which is the equivalent of auditd. Additional modules can be added as desired after the play is ran.

For RHEL distros, I open the ports in firewalld automatically. For Debian I open UFW. Comment either out if you do not use them.

**As I am making this, there are known issues with the 6.7.1 auditbeat. The os_family it pulls does not line up with the what its looking for: expects "RedHat", gets "rhel". Because of this, the service won't start. This should be fixed in 6.7.2 based on issues reported in github**

Requirements
------------

1) Configure your hosts and ansible user at ./playbooks/beats.yml
2) Add your ELK IP to the elk_ip variable in ./roles/vars/main.yml.
2) Configure any desired changes from the defaults in ./roles/vars/main.yml (see Role Variables section for what to place).

    A) Edit the vars file with vi ./roles/vars/main.yml

    B) Under "---" add the variables you wish to change and their values.

3) Run the play. i.e. ansible-playbook -kK /path/to/Beats.yml

Role Variables
--------------

The following variables are used. All variables have a default value.

If you wish to change the default value, simply add the variable to ./vars/main.yml and set its properties (i.e. "server_ip: 10.10.10.10"). Anything in ./vars/ will overwrite the defaults. We do not recommend changing ./defaults/main.yml.

| Variable  | Location | Required | Default | Description
| ------------- | ------------- | ------------- | ------------- | ------------- |
| auditbeat_conf | ./roles/beats/defaults/main.yml | Yes | /etc/auditbeat/auditbeat.yml | Path to the auditbeat configuration file |
| beat_port | ./roles/beats/defaults/main.yml | Yes | 5044 | Configures the port for logstash is listening on to point beats at |
| elk_ip | ./roles/beats/vars/main.yml | Yes | N/A | Configures beats to point to ELK Stack |
| filebeat_conf | ./roles/beats/defaults/main.yml | Yes | /etc/filebeat/filebeat.yml | Path to the filebeat configuration file |

Dependencies
------------

Needs a configured ELK stack to point to in order to function. This play configures beats for logstash so at a minimum, Logstash must be working and listening on the configured port (default 5044).

Example Playbook
----------------

    - hosts: 10.10.10.10
      roles:
         - beats

Author Information
------------------

Steven Craig, ISSM, 05Mar19
