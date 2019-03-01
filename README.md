Role Name
=========

Installs ELK_Stack on the target

Requirements
------------

N/A

Role Variables
--------------

Multiple role variables are used; hoever, all variables are listed with default values. If any of these values need changed, simply add the variable to /vars/main.yml and set your wanted value.
Anything in vars will override anything in defaults.

Dependencies
------------

N/A

Example Playbook
----------------

    - hosts: auditor.besl.org
      roles:
         - elk_stack


Author Information
------------------

Steven Craig, ISSM, 24Feb19
