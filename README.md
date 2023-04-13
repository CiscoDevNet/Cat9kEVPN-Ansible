# ANSIBLE COLLECTION FOR CISCO BGP EVPN

## How to use the roles & playbooks in this repository

###
  * This role was designed to configure a CISCO BGP EVPN network.

###
  * **Requirements**: this role needs Ansible pre-installed and the CISCO IOS ANSIBLE COLLECTION, to install use:
    ```SHELL
    ansible-galaxy collection install cisco.ios
    ```

###
  * To install this role execute this command on the Ansible server:
    ```SHELL
    ansible-galaxy install git+https://github.com/CiscoDevNet/Cat9kEVPN-Ansible.git
    ```

###
  * **The role gets installed on the roles default paths**:
    * **/etc/ansible/roles/**: for system wide roles (requires root privileges)
    * **/home/user/.ansible/roles/**: user roles (user privileges required)
    * **/usr/share/ansible/roles/**: for system wide roles (requires root privileges)

###
  * Inside the role folder a copy of the documentation and this "README.md" file is located.

###
  * If a local copy of this role is needed you can clone this repo or copy the role from the install path and follow the documentation.

###
  * Go to this path to check instructions, examples and base files to use for every role: "Use Cases/[Layer_3_Design_1.X,Distributed_Anycast_Gateway_Design_2.X,l3,dag]/[1,2,3,4,Design_1.1,Design_1.2,Design_1.3,Design_1.4]", on each one of this paths, the specific files for each case are stored and documented, the documentation is in the file "README.md".

###
  * **In the path**: "roles/[layer_3,layer_3_loop,dag,dag_loop]/tasks/use_case_[1,2,3,4]/" the documentation for each use case scenario is located in the "README.md" file.

###
  * **In the path**: "roles/[layer_3,layer_3_loop,dag,dag_loop]/tasks/use_case_[1,2,3,4]/base_files/" the base files for each use case scenario are located, to use a specific role the base files needs to be copied to the same path where the roles are located, this way the "roles" folder and the base files are in the same location, this base files are only for a specific scenario, replace all this files as needed or as the use case scenarios get changed, this files are: _ansible.cfg_, _playbook.yml_, _inventory.yml_ and _playbook_clear_all_config.yml_, _playbook_clear_all_config_inner_loop_1.yml_, _playbook_clear_all_config_inner_loop_2.yml_, _playbook_clear_all_config_inner_loop_3.yml_.

###
  * Once the desired use case scenario is chosen and the base files are in place on the same path as the "roles" folder, continue to the next steps.
    * Base files + roles folder example:
      ```SHELL
      ls -lash
      total 120K
      4.0K drwxr-xr-x 3 user user 4.0K Feb 14 20:49 .
      4.0K drwxr-xr-x 6 user user 4.0K Feb 14 20:49 ..
      4.0K -rw-r--r-- 1 user user  127 Feb 14 20:49 ansible.cfg
      4.0K -rw-r--r-- 1 user user 1.8K Feb 14 20:49 inventory.yml
      4.0K -rw-r--r-- 1 user user 2.8K Feb 14 20:49 playbook_clear_all_config.yml
      4.0K -rw-r--r-- 1 user user 2.8K Feb 14 20:49 playbook_clear_all_config_inner_loop_1.yml
      4.0K -rw-r--r-- 1 user user 2.8K Feb 14 20:49 playbook_clear_all_config_inner_loop_2.yml
      4.0K -rw-r--r-- 1 user user 2.8K Feb 14 20:49 playbook_clear_all_config_inner_loop_3.yml
      4.0K -rw-r--r-- 1 user user  112 Feb 14 20:49 playbook.yml
      4.0K drwxr-xr-x 4 user user 4.0K Feb 14 20:49 roles
      ```

###
  * Set the variables as desired in the file _inventory.yml_, on each use case scenario the needed variables are documented.

###
  * The file _ansible.cfg_ references this _inventory.yml_ and SSH configuration to not fail when connecting to the network devices, with the inventory set on this file it's not necessary to run "ansible-playbook -i inventory_file playbook.yml".

###
  * **Base files details**: _ansible.cfg_, _playbook.yml_, _inventory.yml_, _playbook_clear_all_config.yml_.
    * _ansible.cfg_: Ansible configuration file, sets inventory source and SSH options.
    * _playbook.yml_: Specific playbook for each use case scenario.
    * _inventory.yml_: Specific inventory for each use case scenario.
    * _playbook_clear_all_config.yml_: Specific playbook to clear the configuration after each _playbook.yml_ run.
    * _playbook_clear_all_config_inner_loop_1.yml_: Specific support file for clear all config playbook, some use cases need this inner loop file.
    * _playbook_clear_all_config_inner_loop_2.yml_: Specific support file for clear all config playbook, some use cases need this inner loop file.
    * _playbook_clear_all_config_inner_loop_3.yml_: Specific support file for clear all config playbook, some use cases need this inner loop file.

###
  * **To run an specific scenario execute**: "ansible-playbook playbook.yml", examples:
      * Scenario LAYER_3 USE CASE #[1,2,3,4]:
        ```SHELL
        ansible-playbook playbook.yml
        ```
      * Scenario DAG USE CASE #[1,2,3,4]:
        ```SHELL
        ansible-playbook playbook.yml
        ```
      * Scenario LAYER_3_LOOP USE CASE #[1,2,3,4]:
        ```SHELL
        ansible-playbook playbook.yml
        ```
      * Scenario DAG_LOOP USE CASE #[1,2,3,4]:
        ```SHELL
        ansible-playbook playbook.yml
        ```

###
  * Playbook structure, the file _playbook.yml_ changes between use cases, it calls a role and the specific use case within the role, it applies the role to the group "network" in the _inventory.yml_ file. The difference between all the _playbook.yml_ files is two keywords, this keywords change the role and the use case.
    * **Roles and use cases details**:
      * **LAYER3**: role keyword: "layer_3".
        * 1: use case keyword: "one".
        * 2: use case keyword: "two".
        * 3: use case keyword: "three".
        * 4: use case keyword: "four".

      * **LAYER_3_LOOP**: role keyword "layer_3_loop".
        * 1: use case keyword: "one".
        * 2: use case keyword: "two".
        * 3: use case keyword: "three".
        * 4: use case keyword: "four".

      * **DAG**: role keyword: "dag".
        * 1: use case keyword: "one".
        * 2: use case keyword: "two".
        * 3: use case keyword: "three".
        * 4: use case keyword: "four".

      * **DAG_LOOP**: role keyword: "dag_loop".
        * 1: use case keyword: "one".
        * 2: use case keyword: "two".
        * 3: use case keyword: "three".
        * 4: use case keyword: "four".

    * **Diagram**:
      ```SHELL
        roles
          ├──layer_3
          │    ├── one
          │    ├── two
          │    ├── three
          │    └── four
          │
          ├──layer_3_loop
          │    ├── one
          │    ├── two
          │    ├── three
          │    └── four
          │
          ├──dag
          │    ├── one
          │    ├── two
          │    ├── three
          │    └── four
          │
          └──dag_loop
               ├── one
               ├── two
               ├── three
               └── four
        ```

    * Playbook _playbook.yml_ examples:
      * LAYER_3 _playbook.yml_ file:
        ```YAML
        ---
        #
        # SCENARIO LAYER_3 1.1
        - hosts: network
          gather_facts: yes
          vars:
            use_case: one
          roles:
            - layer_3
        #
        ...
        ```
      * LAYER_3_LOOP _playbook.yml_ file:
        ```YAML
        ---
        #
        # SCENARIO LAYER_3_LOOP 1.1
        - hosts: network
          gather_facts: yes
          vars:
            use_case: one
          roles:
            - layer_3_loop
        #
        ...
        ```
      * DAG _playbook.yml_ file:
        ```YAML
        ---
        #
        # SCENARIO DAG 1.1
        - hosts: network
          gather_facts: yes
          vars:
            use_case: one
          roles:
            - dag
        #
        ...
        ```
      * DAG_LOOP _playbook.yml_ file:
        ```YAML
        ---
        #
        # SCENARIO DAG_LOOP 1.1
        - hosts: network
          gather_facts: yes
          vars:
            use_case: one
          roles:
            - dag_loop
        #
        ...
        ```

###
  * Between playbook runs, it's recommended to clean/wipe the overlay config, for this purpose the playbook _playbook_clear_all_config.yml_ exists, this playbook deletes every config on every use case scenario, example:
    ```SHELL
    ansible-playbook playbook_clear_all_config.yml
    ```
    * This playbook must be run before executing a new use case scenario, it must me the one from the last use case scenario executed, if the current scenario runs succesfully or with some failure, the one from the current use case scenario should be used.

###
  * Each _playbook.yml_ contains pre-validations for license type, connectivity between devices(PING) and post-validations for BGP session UPTIME > 00:00:00 or not "never" and correct NEIGHBOORS; the _playbook.yml_ should finish without any failure on any task to be a succesfull run, any failure on any task, means an error of some type on the _inventory.yml_ file, check the examples and documentation to validate the data.

###
  * The folder "backup" on each use case scenario contains configuration backups before & after running the ansible playbook, it can be used if necessary for reference or validation. This folder is created on the first run of _playbook.yml_.

###
  * **License**: TBD.


###
  * **Author Information**: Jose Bogarin, Michael Bonilla. ALTUS CONSULTING for CISCO SYSTEMS.
