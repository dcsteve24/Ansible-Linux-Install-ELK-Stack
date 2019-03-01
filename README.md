Role Name
=========

Installs ELK_Stack on the target with default values specified in the Elastic formal instructions.

Requirements
------------

N/A

Role Variables
--------------

The following variables are used. All variables have a default value. If you wish to change the default value, simply add the variable to ./vars/main.yml and set its properties (i.e. "server_ip: 10.10.10.10"). Anything in ./vars/ will overwrite the defaults. We do not recommend changing ./defaults/main.yml.



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
