# DISTRIBUTED ANYCAST GATEWAY (DAG) USE CASE #3

##

### Playbook details:
  * This playbook covers the DAG USE CASE #3 with this configuration:
    * **DHCP Relay**: Global
    * **Overlay Type**: DAG
    * **VRF AF**: IPv4
    * **Overlay Topology**: Full Mesh
    * **BUM**: Multicast
    * **MAC VRF RT Export**: Auto
    * **MAC VRF RT Import**: Auto
    * **IP VRF RT Export**: "ASN:CORE_VLAN_ID"
    * **IP VRF RT Import**: "ASN:CORE_VLAN_ID"
    * **Layer 3 Multicast**: Anycast RP TRM
    * **Border Handoff**: L3 VRF (U/M)
    * **Core VLAN**: L3VNI

### Topology:
  * The topology consist of several nodes, classified as _spines_ (one or more) and _leafs_ (one or more), the _spines_ are the main network nodes aka "cores", the _leafs_ are the nodes where end devices connect aka "access", the _leafs_ can be a simple "leaf" to connect end devices or a "border" leaf to connect to other networks.

### Variables:
  * The playbook variables are configured in the file _inventory.yml_, this YAML file is divided in two groups, _leafs_ for the leafs nodes and _spines_ for the _spines_ nodes, this two groups are joined on a single group called "network" which contains the whole network to be configured.

### Variables location:
  * **The playbook variables can be located at**:
    * An specific node for a per node variable, specific to that node.
    * An specific group, under the "vars" section of the group, for variables for the whole group.
    * The "network" group, under the "vars" section of this group, for variables of the whole network.

### Per node variables for _leafs_ nodes:
  * **The node name**: Ansible name for the device, usually the hostname of the device, example: "sw-access-leaf-1".
  * **ansible_host**: the DNS FQDN or IP ADDRESS, used to access and configure the device by Ansible.
  * **role**: leafs nodes classification as a simple "leaf" or a "border" leaf.
  * **rid**: BGP Router ID (RID), unique per node.
  * **ip_address**: IPv4 address for the L3 EDGE VLAN, unique per node.
  * **ip_mask**: IPv4 mask for the L3 EDGE VLAN.
  * **dag_mac**: MAC address for the L2VNI EDGE VLAN, unique per node.
  * **dag_ipv4_address**: DAG IPv4 address for the L2VNI EDGE VLAN, unique per node.
  * **dag_ipv4_mask**: DAG IPv4 mask for the L2VNI EDGE VLAN.
  * **Example**: two leaf nodes, one simple "leaf", one "border" leaf:
    ```YAML
    #
    leafs:
    #
      hosts:
    #
        sw-access-leaf-1:
          ansible_host: 10.1.3.15
          role: leaf
          rid: 10.255.255.253
          ip_address: 10.252.255.1
          ip_mask: 255.255.255.0
          dag_mac: 0012.3456.7890
          dag_ipv4_address: 10.251.255.1
          dag_ipv4_mask: 255.255.255.0
    #
        sw-access-leaf-2:
          ansible_host: 10.1.3.7
          role: border
          rid: 10.255.255.252
          ip_address: 10.252.255.2
          ip_mask: 255.255.255.0
          dag_mac: 0011.2233.4455
          dag_ipv4_address: 10.251.255.2
          dag_ipv4_mask: 255.255.255.0
    #
      vars:
    #
    ```
      * Note the indentation for YAML (two spaces).
      * Note that common variables like **ip_mask**, **dag_ipv4_mask** and can be removed from the individual nodes and moved to the "vars" section of the group.

### Per node variables for _spines_ nodes:
  * **The node name**: Ansible name for the device, usually the hostname of the device, example: "sw-core-spine-1".
  * **ansible_host**: the DNS FQDN or IP ADDRESS, used to access and configure the device by Ansible.
  * **rid**: BGP Router ID (RID), unique per node.
  * **Example**: two spine nodes:
    ```YAML
    #
    spines:
    #
      hosts:
    #
        sw-core-spine-1:
          ansible_host: 10.1.3.6
          rid: 10.255.255.254
    #
        sw-core-spine-2:
          ansible_host: 10.1.3.9
          rid: 10.255.255.244
    #
      vars:
    #
    ```
      * Note the indentation for YAML (two spaces).

### Group variables for "network" group (whole network, all nodes):
  * **backups_path**: file system path used by Ansible to save a backup before & after the playbook run.
  * **ansible_connection**: Ansible connection type for the playbook, in this case "ansible.netcommon.network_cli".
  * **ansible_network_os**: Ansible NOS to be used by specific Ansible modules in the playbook, in this case "cisco.ios.ios".
  * **ansible_user**: user ID to access the network devices or nodes, used by Ansible.
  * **ansible_password**: user password to access the network devices or nodes, used by Ansible.
  * **ansible_become**: flag variable to let Ansible know if it has to elevate privileges once connected to the network devices or not, "yes" or "no".
  * **ansible_become_method**: method used by Ansible to elevate privileges on the network devices, in Cisco case is "enable".
  * **ansible_become_password**: become password, used by Ansible to elevate privileges, in Cisco case is the "enable secret" password or "enable password" password.
  * **ping_count**: Pre-verification PING number to be send between devices to validate underlay connectivity, "1000" in the example.
  * **ping_loss**: Pre-verification PING maximum loss to be tolerated, loss bigger than this number will make the playbook fail, "10" percent or 100 PING in the example.
  * **vrf_name**: Name string for the VRF.
  * **bgp_asn**: BGP ASN number.
  * **remote_as**: Remote BGP ASN number.
  * **core_vlan_id**: Core VLAN ID number.
  * **l3_vni_id**: L3 VNI ID number.
  * **edge_vlan_id**: Edge VLAN ID number.
  * **primary_dhcp_server**: Primary DHCP IP address.
  * **secondary_dhcp_server**: Secondary DHCP IP address.
  * **nve_id**: NVE ID number to be used for the VXLAN aggregation interface.
  * **border_ip**: BGP border IP neighboor address to be used in the "border" leafs.
  * **border_remote_as**: BGP ASN remote number to be used in the "border" leafs.
  * **mdt_group**: MDT GROUP for the VRF.
  * **anycast_rp**: ANYCAST RENDEZVOUS POINT (RP) for multicast.
  * **loopback**: Loopback interface to be used on several SVI Interfaces.
  * **bgp_source**: BGP source interface.
  * **l2vpn_evpn_repl_type**: L2VPN EVPN replication type, "ingress" or "static".
  * **l2vni_mcast_grp**: L2VNI multicast group, needed if "l2vpn_evpn_repl_type" variable is set to "static".
  * **l2vni_edge_vlan_id**: L2VNI EDGE VLAN ID number.
  * **l2vni_instance_id**: L2VNI instance ID number.
  * **Example**: "network" group, conformed by _leafs_ and _spines_ groups as "children" plus all the variables needed by this group under the section "vars":
    ```YAML
    #
    network:
    #
      children:
    #
        leafs:
    #
        spines:
    #
      vars:
    #
        backups_path: ./backup/
        ansible_connection: ansible.netcommon.network_cli
        ansible_network_os: cisco.ios.ios
        ansible_user: authorized_user
        ansible_password: authorized_password
        ansible_become: no
        ansible_become_method: enable
        ansible_become_password: authorized_enable_password
        ping_count: 1000
        ping_loss: 10
        vrf_name: VRF_OVERLAY_2010
        bgp_asn: 64512
        remote_as: 64512
        core_vlan_id: 2010
        l3_vni_id: 20010
        edge_vlan_id: 2020
        primary_dhcp_server: 10.10.10.10
        secondary_dhcp_server: 10.20.20.20
        nve_id: 1
        border_ip: 10.9.8.7
        border_remote_as: 64513
        mdt_group: 225.1.2.3
        anycast_rp: 10.253.255.254
        loopback: Loopback1
        bgp_source: Loopback0
        l2vpn_evpn_repl_type: ingress
        l2vni_mcast_grp: 225.4.5.6
        l2vni_edge_vlan_id: 2030
        l2vni_instance_id: 20030
    #
    ```
     * Note the indentation for YAML (two spaces).
     * Note that common variables like **ip_mask**, **dag_ipv4_mask** and can be removed from the individual nodes and moved to the "vars" section for the whole network under the group "network", as long as the value of this variables is the same.

### Playbook pre-validations:
  * This playbook has this pre-validations:
    * The underlay network needs to be pre-configured, this is not executed by the playbook, it's expected to be already configured, tested and in place.
    * **Licensing**: this playbook validates the correct license on the network devices, this needs to be met to configure the overlay network. The license needed is "network-advantage".
    * **Connectivity**: this playbook validates with a PING the connectivity between the _leafs_ and _spines_ and viceversa, this needs to be met to configure the overlay network, the PING source and destination IP addressess are the BGP RID "rid" variable of each node defined in the _inventory.yml_, this needs to a reachable IP address between all the nodes in the network, leafs, borders and spines.
    * **Underlay network reference configuration**:
      ```CISCO
      !# Underlay IP
      !
      interface Loopback 0
      description CONNECTED TO UNDERLAY LOOPBACK NETWORK
      ip address <IP> <MASK>
      !
      interface Loopback 1
      description CONNECTED TO VXLAN NVE LOOPBACK NETWORK
      ip address <IP> <MASK>
      !
      interface <ID>
       description CONNECTED TO UNDERLAY IP CORE NETWORK
       no switchport
       ip address <IP> <MASK>
      !
      !
      !# Underlay IP Routing
      !
      ! UNDERLAY IPv4 OSPFv2 ROUTING
      !
      router ospf <PID>
       router-id <LOOPBACK-0>
       log-adjacency-changes detail
       nsf
      !
      interface Loopback 0
      description CONNECTED TO UNDERLAY LOOPBACK NETWORK
      ip ospf <PID> area <ID>
      ip ospf network point-to-point
      !
      interface Loopback 1
      description CONNECTED TO VXLAN NVE LOOPBACK NETWORK
      ip ospf <PID> area <ID>
      ip ospf network point-to-point
      !
      interface <ID>
       description CONNECTED TO UNDERLAY IP CORE NETWORK
       ip ospf <PID> area <ID>
       ip ospf network point-to-point
      !
      !
      !# Underlay IP Multicast
      !
      ! UNDERLAY IPv4 PIM-SM ROUTING
      !
      ip multicast-routing
      !
      ip pim rp-address <RP-ADDRESS>
      !
      interface Loopback 0
      description CONNECTED TO UNDERLAY LOOPBACK NETWORK
      ip pim sparse-mode
      ip pim passive
      !
      interface Loopback 1
      description CONNECTED TO VXLAN NVE LOOPBACK NETWORK
      ip pim sparse-mode
      ip pim passive
      !
      interface <ID>
       description CONNECTED TO UNDERLAY IP CORE NETWORK
       ip pim sparse-mode
      !
      ```
    * **License validation example**:
      ```SHELL
      TASK [dag : SHOW LICENSE SUMMARY] ********************************************************************
      ok: [sw-core-spine-1]
      ok: [sw-access-leaf-1]
      ok: [sw-access-leaf-2]

      TASK [dag : ASSERT SUCCESS OR FAILURE BASED ON LICENSE TYPE] *****************************************
      ok: [sw-access-leaf-1] => {
          "changed": false,
          "msg": "---> Device meet license requirement to enable EVPN <---"
      }
      ok: [sw-access-leaf-2] => {
          "changed": false,
          "msg": "---> Device meet license requirement to enable EVPN <---"
      }
      ok: [sw-core-spine-1] => {
          "changed": false,
          "msg": "---> Device meet license requirement to enable EVPN <---"
      }

      TASK [dag : DEBUG] ***********************************************************************************
      ok: [sw-access-leaf-1] => {
          "msg": [
              {
                  "changed": false,
                  "failed": false,
                  "stdout": [
                      "License Usage:\n  License                 Entitlement Tag               Count Status\network-advantage       (advantagek9-C9400)               1 IN USE\n  dna-advantage           (dna_adv            0 NOT IN USE\n  C9400 DNA Essentials    (dna_essentials-C9400)            0 NOT IN USE"
                  ],
                  "stdout_lines": [
                      [
                          "License Usage:",
                          "  License                 Entitlement Tag               Count Status",
                          "  -----------------------------------------------------------------------------",
                          "  network-advantage       (advantagek9-C9400)               1 IN USE",
                          "  dna-advantage           (dna_advantage-C9400)             1 IN USE",
                          "  C9400 Network Essent... (essentialsk9-C9400)              0 NOT IN USE",
                          "  C9400 DNA Essentials    (dna_essentials-C9400)            0 NOT IN USE"
                      ]
                  ]
              }
          ]
      }
      ok: [sw-core-spine-1] => {
          "msg": [
              {
                  "changed": false,
                  "failed": false,
                  "stdout": [
                      "License Usage:\n  License                 Entitlement Tag               Count Status\network-advantage       (C9500 Network Advantage)         1 IN USE\n  dna-advantage           (C9500-1als)        0 NOT IN USE"
                  ],
                  "stdout_lines": [
                      [
                          "License Usage:",
                          "  License                 Entitlement Tag               Count Status",
                          "  -----------------------------------------------------------------------------",
                          "  network-advantage       (C9500 Network Advantage)         1 IN USE",
                          "  dna-advantage           (C9500-12Q DNA Advantage)         1 IN USE",
                          "  C9500 Network Essent... (C9500 Network Essentials)        0 NOT IN USE"
                      ]
                  ]
              }
          ]
      }
      ok: [sw-access-leaf-2] => {
          "msg": [
              {
                  "changed": false,
                  "failed": false,
                  "stdout": [
                      "License Usage:\n  License                 Entitlement Tag               Count Status\network-advantage       (C9500 Network Advantage)         1 IN USE\n  dna-advantage           (C9500-1als)        0 NOT IN USE"
                  ],
                  "stdout_lines": [
                      [
                          "License Usage:",
                          "  License                 Entitlement Tag               Count Status",
                          "  -----------------------------------------------------------------------------",
                          "  network-advantage       (C9500 Network Advantage)         1 IN USE",
                          "  dna-advantage           (C9500-12Q DNA Advantage)         1 IN USE",
                          "  C9500 Network Essent... (C9500 Network Essentials)        0 NOT IN USE"
                      ]
                  ]
              }
          ]
      }
      ```
    * **Connectivity validation example**:
      ```SHELL
      TASK [dag : PING LEAFS TO SPINES] ********************************************************************
      skipping: [sw-core-spine-1] => (item=sw-core-spine-1)
      ok: [sw-access-leaf-2] => (item=sw-core-spine-1)
      ok: [sw-access-leaf-1] => (item=sw-core-spine-1)

      TASK [dag : SHOW PING DETAILS] ***********************************************************************
      skipping: [sw-core-spine-1] => (item=sw-core-spine-1)
      skipping: [sw-core-spine-1]
      ok: [sw-access-leaf-2] => (item=sw-core-spine-1) => {
          "msg": [
              "PING DESTINATION: 10.255.255.254",
              "PING SOURCE: 10.255.255.252",
              "PING COUNT: 1000",
              "PING MAX LOSS: 10%",
              "NETWORK DEVICE IP: 10.1.3.7",
              "NETWORK DEVICE HOSTNAME: SW-ACCESS-LEAF-2"
          ]
      }
      ok: [sw-access-leaf-1] => (item=sw-core-spine-1) => {
          "msg": [
              "PING DESTINATION: 10.255.255.254",
              "PING SOURCE: 10.255.255.253",
              "PING COUNT: 1000",
              "PING MAX LOSS: 10%",
              "NETWORK DEVICE IP: 10.1.3.15",
              "NETWORK DEVICE HOSTNAME: SW-ACCESS-LEAF-1"
          ]
      }

      TASK [dag : ASSERT SUCCESS OR FAILURE PING LEAFS TO SPINES] ******************************************
      skipping: [sw-core-spine-1] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional r
      ok: [sw-access-leaf-1] => (item={'commands': ['ping 10.255.255.254 repeat 1000 source 10.255.255.253']g': 1, 'max': 1}, 'invocation': {'module_args': {'dest': '10.255.255.254', 'source': '10.255.255.253',vider': None}}, 'failed': False, 'changed': False, 'item': 'sw-core-spine-1', 'ansible_loop_var': 'ite
          "ansible_loop_var": "item",
          "changed": false,
          "item": {
              "ansible_loop_var": "item",
              "changed": false,
              "commands": [
                  "ping 10.255.255.254 repeat 1000 source 10.255.255.253"
              ],
              "failed": false,
              "invocation": {
                  "module_args": {
                      "count": 1000,
                      "dest": "10.255.255.254",
                      "df_bit": false,
                      "provider": null,
                      "size": null,
                      "source": "10.255.255.253",
                      "state": "present",
                      "vrf": null
                  }
              },
              "item": "sw-core-spine-1",
              "packet_loss": "0%",
              "packets_rx": 1000,
              "packets_tx": 1000,
              "rtt": {
                  "avg": 1,
                  "max": 1,
                  "min": 1
              }
          },
          "msg": [
              "---> LESS THAN 10% LOSS ON TEST PING!, CHECK PASSED! <---"
          ]
      }
      ok: [sw-access-leaf-2] => (item={'commands': ['ping 10.255.255.254 repeat 1000 source 10.255.255.252']g': 1, 'max': 1}, 'invocation': {'module_args': {'dest': '10.255.255.254', 'source': '10.255.255.252',vider': None}}, 'failed': False, 'changed': False, 'item': 'sw-core-spine-1', 'ansible_loop_var': 'ite
          "ansible_loop_var": "item",
          "changed": false,
          "item": {
              "ansible_loop_var": "item",
              "changed": false,
              "commands": [
                  "ping 10.255.255.254 repeat 1000 source 10.255.255.252"
              ],
              "failed": false,
              "invocation": {
                  "module_args": {
                      "count": 1000,
                      "dest": "10.255.255.254",
                      "df_bit": false,
                      "provider": null,
                      "size": null,
                      "source": "10.255.255.252",
                      "state": "present",
                      "vrf": null
                  }
              },
              "item": "sw-core-spine-1",
              "packet_loss": "0%",
              "packets_rx": 1000,
              "packets_tx": 1000,
              "rtt": {
                  "avg": 1,
                  "max": 1,
                  "min": 1
              }
          },
          "msg": [
              "---> LESS THAN 10% LOSS ON TEST PING!, CHECK PASSED! <---"
          ]
      }

      TASK [dag : PING SPINES TO LEAFS] ********************************************************************
      skipping: [sw-access-leaf-1] => (item=sw-access-leaf-1)
      skipping: [sw-access-leaf-1] => (item=sw-access-leaf-2)
      skipping: [sw-access-leaf-2] => (item=sw-access-leaf-1)
      skipping: [sw-access-leaf-2] => (item=sw-access-leaf-2)
      ok: [sw-core-spine-1] => (item=sw-access-leaf-1)
      ok: [sw-core-spine-1] => (item=sw-access-leaf-2)

      TASK [dag : SHOW PING DETAILS] ***********************************************************************
      skipping: [sw-access-leaf-1] => (item=sw-access-leaf-1)
      skipping: [sw-access-leaf-1] => (item=sw-access-leaf-2)
      skipping: [sw-access-leaf-1]
      skipping: [sw-access-leaf-2] => (item=sw-access-leaf-1)
      skipping: [sw-access-leaf-2] => (item=sw-access-leaf-2)
      skipping: [sw-access-leaf-2]
      ok: [sw-core-spine-1] => (item=sw-access-leaf-1) => {
          "msg": [
              "PING DESTINATION: 10.255.255.253",
              "PING SOURCE: 10.255.255.254",
              "PING COUNT: 1000",
              "PING MAX LOSS: 10%",
              "NETWORK DEVICE IP: 10.1.3.6",
              "NETWORK DEVICE HOSTNAME: S1-CORE-SPINE"
          ]
      }
      ok: [sw-core-spine-1] => (item=sw-access-leaf-2) => {
          "msg": [
              "PING DESTINATION: 10.255.255.252",
              "PING SOURCE: 10.255.255.254",
              "PING COUNT: 1000",
              "PING MAX LOSS: 10%",
              "NETWORK DEVICE IP: 10.1.3.6",
              "NETWORK DEVICE HOSTNAME: S1-CORE-SPINE"
          ]
      }

      TASK [dag : ASSERT SUCCESS OR FAILURE PING SPINES TO LEAFS] ******************************************
      skipping: [sw-access-leaf-1] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional
      skipping: [sw-access-leaf-1] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional
      skipping: [sw-access-leaf-2] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional
      skipping: [sw-access-leaf-2] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional
      ok: [sw-core-spine-1] => (item={'commands': ['ping 10.255.255.253 repeat 1000 source 10.255.255.254'],': 1, 'max': 1}, 'invocation': {'module_args': {'dest': '10.255.255.253', 'source': '10.255.255.254', ider': None}}, 'failed': False, 'changed': False, 'item': 'sw-access-leaf-1', 'ansible_loop_var': 'ite
          "ansible_loop_var": "item",
          "changed": false,
          "item": {
              "ansible_loop_var": "item",
              "changed": false,
              "commands": [
                  "ping 10.255.255.253 repeat 1000 source 10.255.255.254"
              ],
              "failed": false,
              "invocation": {
                  "module_args": {
                      "count": 1000,
                      "dest": "10.255.255.253",
                      "df_bit": false,
                      "provider": null,
                      "size": null,
                      "source": "10.255.255.254",
                      "state": "present",
                      "vrf": null
                  }
              },
              "item": "sw-access-leaf-1",
              "packet_loss": "0%",
              "packets_rx": 1000,
              "packets_tx": 1000,
              "rtt": {
                  "avg": 1,
                  "max": 1,
                  "min": 1
              }
          },
          "msg": [
              "---> LESS THAN 10% LOSS ON TEST PING!, CHECK PASSED! <---"
          ]
      }
      ok: [sw-core-spine-1] => (item={'commands': ['ping 10.255.255.252 repeat 1000 source 10.255.255.254'],': 1, 'max': 2}, 'invocation': {'module_args': {'dest': '10.255.255.252', 'source': '10.255.255.254', ider': None}}, 'failed': False, 'changed': False, 'item': 'sw-access-leaf-2', 'ansible_loop_var': 'ite
          "ansible_loop_var": "item",
          "changed": false,
          "item": {
              "ansible_loop_var": "item",
              "changed": false,
              "commands": [
                  "ping 10.255.255.252 repeat 1000 source 10.255.255.254"
              ],
              "failed": false,
              "invocation": {
                  "module_args": {
                      "count": 1000,
                      "dest": "10.255.255.252",
                      "df_bit": false,
                      "provider": null,
                      "size": null,
                      "source": "10.255.255.254",
                      "state": "present",
                      "vrf": null
                  }
              },
              "item": "sw-access-leaf-2",
              "packet_loss": "0%",
              "packets_rx": 1000,
              "packets_tx": 1000,
              "rtt": {
                  "avg": 1,
                  "max": 2,
                  "min": 1
              }
          },
          "msg": [
              "---> LESS THAN 10% LOSS ON TEST PING!, CHECK PASSED! <---"
          ]
      }
      ```

### Playbook post-validations:
  * This playbook validates this data post-execution:
    * BGP session timer greather than 00:00:00 on the _spines_ nodes.
    * BGP session timer is not "never" on the _spines_ nodes.
    * BGP IPv4 address of the neighboors (leafs nodes) listed correctly in the BGP session on the _spines_ nodes.
    * Ansible tasks status, all the Ansible tasks defined and executed by the playbook must be sucessfull, if any task fails the playbook execution will be stopped.
    * BGP session output on _spines_ nodes, timer and neighboors validation example:
      ```SHELL
      TASK [dag : SHOW IP BGP L2VPN EVPN SUMMARY] **********************************************************
      ok: [sw-core-spine-1]

      TASK [dag : DEBUG] ***********************************************************************************
      ok: [sw-core-spine-1] => {
          "msg": [
              {
                  "changed": false,
                  "failed": false,
                  "stdout": [
                      "Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxR  0\n10.255.255.253  4        64512       2       2        1    0    0 00:00:14        0"
                  ],
                  "stdout_lines": [
                      [
                          "Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/
                          "10.255.255.252  4        64512       2       2        1    0    0 00:00:07
                          "10.255.255.253  4        64512       2       2        1    0    0 00:00:14
                      ]
                  ]
              }
          ]
      }

      TASK [dag : ASSERT SUCCESS OR FAILURE BASED ON BGP OUTPUT: UPTIME > 00:00:00 AND NEIGHBOORS LEAFS LIST
      ok: [sw-core-spine-1] => (item=sw-access-leaf-1) => {
          "ansible_loop_var": "item",
          "changed": false,
          "item": "sw-access-leaf-1",
          "msg": "---> BGP SESSIONS ESTABLISHED! <---"
      }
      ok: [sw-core-spine-1] => (item=sw-access-leaf-2) => {
          "ansible_loop_var": "item",
          "changed": false,
          "item": "sw-access-leaf-2",
          "msg": "---> BGP SESSIONS ESTABLISHED! <---"
      }

      TASK [dag : DEBUG] ***********************************************************************************
      ok: [sw-core-spine-1] => {
          "msg": [
              {
                  "changed": false,
                  "msg": "All items completed",
                  "results": [
                      {
                          "ansible_loop_var": "item",
                          "changed": false,
                          "failed": false,
                          "item": "sw-access-leaf-1",
                          "msg": "---> BGP SESSIONS ESTABLISHED! <---"
                      },
                      {
                          "ansible_loop_var": "item",
                          "changed": false,
                          "failed": false,
                          "item": "sw-access-leaf-2",
                          "msg": "---> BGP SESSIONS ESTABLISHED! <---"
                      }
                  ],
                  "skipped": false
              }
          ]
      }
    * **Playbook tasks validation example**:
      * Succesfull execution (note 0 task failed):
        ```SHELL
        PLAY RECAP *******************************************************************************************
        sw-access-leaf-1           : ok=60   changed=17   unreachable=0    failed=0    skipped=7    rescued=0    ignored=0
        sw-access-leaf-2           : ok=64   changed=18   unreachable=0    failed=0    skipped=6    rescued=0    ignored=0
        sw-core-spine-1            : ok=22   changed=3    unreachable=0    failed=0    skipped=15   rescued=0    ignored=0
        ```
      * Failed execution (note 1 task failed on sw-access-leaf-2):
        ```SHELL
        PLAY RECAP *******************************************************************************************
        sw-access-leaf-1           : ok=60   changed=17   unreachable=0    failed=0    skipped=7    rescued=0    ignored=0
        sw-access-leaf-2           : ok=63   changed=18   unreachable=0    failed=1    skipped=6    rescued=0    ignored=0
        sw-core-spine-1            : ok=22   changed=3    unreachable=0    failed=0    skipped=15   rescued=0    ignored=0
        ```

### Inventory example:
  * A full inventory, three leafs, two simple leafs, one border leaf and two spines, with all the needed variables looks like this:
    ```YAML
    ---
    #
    leafs:
    #
      hosts:
    #
        sw-access-leaf-1:
          ansible_host: 10.1.3.15
          role: leaf
          rid: 10.255.255.253
          ip_address: 10.252.255.1
          ip_mask: 255.255.255.0
          dag_mac: 0012.3456.7890
          dag_ipv4_address: 10.251.255.1
          dag_ipv4_mask: 255.255.255.0
    #
        sw-access-leaf-2:
          ansible_host: 10.1.3.7
          role: leaf
          rid: 10.255.255.252
          ip_address: 10.252.255.2
          ip_mask: 255.255.255.0
          dag_mac: 0011.2233.4455
          dag_ipv4_address: 10.251.255.2
          dag_ipv4_mask: 255.255.255.0
    #
        sw-access-leaf-3:
          ansible_host: 10.1.3.77
          role: border
          rid: 10.255.255.222
          ip_address: 10.252.255.3
          ip_mask: 255.255.255.0
          dag_mac: 0011.4431.4235
          dag_ipv4_address: 10.251.255.3
          dag_ipv4_mask: 255.255.255.0
    #
      vars:
    #
    spines:
    #
      hosts:
    #
        sw-core-spine-1:
          ansible_host: 10.1.3.6
          rid: 10.255.255.254
    #
        sw-core-spine-2:
          ansible_host: 10.1.3.66
          rid: 10.255.255.244
    #
      vars:
    #
    network:
    #
      children:
    #
        leafs:
    #
        spines:
    #
      vars:
        backups_path: ./backup/
        ansible_connection: ansible.netcommon.network_cli
        ansible_network_os: cisco.ios.ios
        ansible_user: authorized_user
        ansible_password: authorized_password
        ansible_become: no
        ansible_become_method: enable
        ansible_become_password: authorized_enable_password
        ping_count: 1000
        ping_loss: 10
        vrf_name: VRF_OVERLAY_2010
        bgp_asn: 64512
        remote_as: 64512
        core_vlan_id: 2010
        l3_vni_id: 20010
        edge_vlan_id: 2020
        primary_dhcp_server: 10.10.10.10
        secondary_dhcp_server: 10.20.20.20
        nve_id: 1
        border_ip: 10.9.8.7
        border_remote_as: 64513
        mdt_group: 225.1.2.3
        anycast_rp: 10.253.255.254
        loopback: Loopback1
        bgp_source: Loopback0
        l2vpn_evpn_repl_type: ingress
        l2vni_mcast_grp: 225.4.5.6
        l2vni_edge_vlan_id: 2030
        l2vni_instance_id: 20030
    #
    ...
    ```
      * Note the indentation for YAML (two spaces).
      * Note that common variables like **ip_mask**, **dag_ipv4_mask** and can be removed from the individual nodes and moved to the "vars" section of the group or to the "vars" section for the whole network under the group "network", as long as the value of this variables is the same.

### Main playbook execution:
  * The main playbook to run all the configuration on this use case scenario is called _playbook.yml_.
  * **To execute this playbook the following commands must be run**:
    ```SHELL
    ansible-playbook playbook.yml
    ```
      * Note the execution of "ansible-playbook" without the "-i" option, this option usually calls the inventory file, but this file is already defined in the _ansible.cfg_ file, there's no need to call it from the playbook execution.
  * The playbook execution should finish without any errors or failed tasks, if a single task fails the execution will be aborted.
  * **Playbook execution example**:
    * Succesfull execution (note 0 task failed):
      ```SHELL
      PLAY RECAP *******************************************************************************************
      sw-access-leaf-1           : ok=60   changed=17   unreachable=0    failed=0    skipped=7    rescued=0    ignored=0
      sw-access-leaf-2           : ok=64   changed=18   unreachable=0    failed=0    skipped=6    rescued=0    ignored=0
      sw-core-spine-1            : ok=22   changed=3    unreachable=0    failed=0    skipped=15   rescued=0    ignored=0
      ```
    * Failed execution (note 1 task failed on sw-access-leaf-2):
      ```SHELL
      PLAY RECAP *******************************************************************************************
      sw-access-leaf-1           : ok=60   changed=17   unreachable=0    failed=0    skipped=7    rescued=0    ignored=0
      sw-access-leaf-2           : ok=63   changed=18   unreachable=0    failed=1    skipped=6    rescued=0    ignored=0
      sw-core-spine-1            : ok=22   changed=3    unreachable=0    failed=0    skipped=15   rescued=0    ignored=0
      ```

### Utility playbook execution (configuration cleanup):
  * There's an utility playbook called _playbook_clear_all_config.yml_ to clear the main playbook _playbook.yml_ configuration on all the network nodes, this playbook should be run between executions or when a different configuration is needed on the nodes.
  * It's recommended to run this playbook before the main playbook or before testing any other use case scenario, this way the network nodes will be clean before applying the main playbook in this scenario or any other scenario, this playbook is dependent on the _inventory.yml_, do not change variables without executing this playbook first.
  * **To execute this playbook the following commands must be run**:
    ```SHELL
    ansible-playbook playbook_clear_all_config.yml
    ```
      * Note the execution of "ansible-playbook" without the "-i" option, this option usually calls the inventory file, but this file is already defined in the _ansible.cfg_ file, there's no need to call it from the playbook execution.
  * The playbook execution should finish without any errors or failed tasks, if a single task fails the execution will be aborted.
  * **Playbook execution example**:
    * Succesfull execution (note 0 task failed):
      ```SHELL
      PLAY RECAP *******************************************************************************************
      sw-access-leaf-1           : ok=15   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
      sw-access-leaf-2           : ok=15   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
      sw-core-spine-1            : ok=15   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
      ```
    * Failed execution (note 1 task failed on sw-access-leaf-1):
      ```SHELL
      PLAY RECAP *******************************************************************************************
      sw-access-leaf-1           : ok=14   changed=8    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0
      sw-access-leaf-2           : ok=15   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
      sw-core-spine-1            : ok=15   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
      ```

### Network nodes configuration backup:
  * Before and after the main playbook execution a configuration backup is taken, this configuration backup is saved on the folder "backup", this backups are date/time named.
  * **Backups folder example**:
    ```SHELL
    ls -lash backup/
    total 108K
    4.0K drwxrwxr-x 2 netadmin netadmin 4.0K Feb 13 19:48 .
    4.0K drwxrwxr-x 4 netadmin netadmin 4.0K Feb 13 19:47 ..
    16K -rw-rw-r-- 1 netadmin netadmin  15K Feb 13 19:47 sw-access-leaf-1_config.2022-02-13@19:47:23
    20K -rw-rw-r-- 1 netadmin netadmin  17K Feb 13 19:48 sw-access-leaf-1_config.2022-02-13@19:48:43
    16K -rw-rw-r-- 1 netadmin netadmin  14K Feb 13 19:47 sw-access-leaf-2_config.2022-02-13@19:47:23
    16K -rw-rw-r-- 1 netadmin netadmin  16K Feb 13 19:48 sw-access-leaf-2_config.2022-02-13@19:48:43
    16K -rw-rw-r-- 1 netadmin netadmin  13K Feb 13 19:47 sw-core-spine-1_config.2022-02-13@19:47:23
    16K -rw-rw-r-- 1 netadmin netadmin  14K Feb 13 19:48 sw-core-spine-1_config.2022-02-13@19:48:43
    ```
      * Note the two files per network node, in this case the first file is the backup before the main playbook is applied and the second file is the backup after the main playbook changed the network nodes configurations.

### Main playbook execution example:
  * The network in this example has one spine and two leaf nodes, one simple leaf node, one border leaf node.
  * **The main playbook execution should look like this**:
    ```SHELL
    ansible-playbook playbook.yml

    PLAY [network] ***************************************************************************************

    TASK [Gathering Facts] *******************************************************************************
    [WARNING]: Ignoring timeout(10) for cisco.ios.ios_facts
    [WARNING]: Ignoring timeout(10) for cisco.ios.ios_facts
    [WARNING]: Ignoring timeout(10) for cisco.ios.ios_facts
    ok: [sw-core-spine-1]
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [dag : SHOW LICENSE SUMMARY] ********************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-core-spine-1]
    ok: [sw-access-leaf-1]

    TASK [dag : ASSERT SUCCESS OR FAILURE BASED ON LICENSE TYPE] *****************************************
    ok: [sw-access-leaf-1] => {
        "changed": false,
        "msg": "---> Device meet license requirement to enable EVPN <---"
    }
    ok: [sw-access-leaf-2] => {
        "changed": false,
        "msg": "---> Device meet license requirement to enable EVPN <---"
    }
    ok: [sw-core-spine-1] => {
        "changed": false,
        "msg": "---> Device meet license requirement to enable EVPN <---"
    }

    TASK [dag : DEBUG] ***********************************************************************************
    ok: [sw-access-leaf-1] => {
        "msg": [
            {
                "changed": false,
                "failed": false,
                "stdout": [
                    "License Usage:\n  License                 Entitlement Tag               Count Status\n  -----------------------------------------------------------------------------\n  network-advantage       (advantagek9-C9400)               1 IN USE\n  dna-advantage           (dna_advantage-C9400)             1 IN USE\n  C9400 Network Essent... (essentialsk9-C9400)              0 NOT IN USE\n  C9400 DNA Essentials    (dna_essentials-C9400)            0 NOT IN USE"
                ],
                "stdout_lines": [
                    [
                        "License Usage:",
                        "  License                 Entitlement Tag               Count Status",
                        "  -----------------------------------------------------------------------------",
                        "  network-advantage       (advantagek9-C9400)               1 IN USE",
                        "  dna-advantage           (dna_advantage-C9400)             1 IN USE",
                        "  C9400 Network Essent... (essentialsk9-C9400)              0 NOT IN USE",
                        "  C9400 DNA Essentials    (dna_essentials-C9400)            0 NOT IN USE"
                    ]
                ]
            }
        ]
    }
    ok: [sw-access-leaf-2] => {
        "msg": [
            {
                "changed": false,
                "failed": false,
                "stdout": [
                    "License Usage:\n  License                 Entitlement Tag               Count Status\n  -----------------------------------------------------------------------------\n  network-advantage       (C9500 Network Advantage)         1 IN USE\n  dna-advantage           (C9500-12Q DNA Advantage)         1 IN USE\n  C9500 Network Essent... (C9500 Network Essentials)        0 NOT IN USE"
                ],
                "stdout_lines": [
                    [
                        "License Usage:",
                        "  License                 Entitlement Tag               Count Status",
                        "  -----------------------------------------------------------------------------",
                        "  network-advantage       (C9500 Network Advantage)         1 IN USE",
                        "  dna-advantage           (C9500-12Q DNA Advantage)         1 IN USE",
                        "  C9500 Network Essent... (C9500 Network Essentials)        0 NOT IN USE"
                    ]
                ]
            }
        ]
    }
    ok: [sw-core-spine-1] => {
        "msg": [
            {
                "changed": false,
                "failed": false,
                "stdout": [
                    "License Usage:\n  License                 Entitlement Tag               Count Status\n  -----------------------------------------------------------------------------\n  network-advantage       (C9500 Network Advantage)         1 IN USE\n  dna-advantage           (C9500-12Q DNA Advantage)         1 IN USE\n  C9500 Network Essent... (C9500 Network Essentials)        0 NOT IN USE"
                ],
                "stdout_lines": [
                    [
                        "License Usage:",
                        "  License                 Entitlement Tag               Count Status",
                        "  -----------------------------------------------------------------------------",
                        "  network-advantage       (C9500 Network Advantage)         1 IN USE",
                        "  dna-advantage           (C9500-12Q DNA Advantage)         1 IN USE",
                        "  C9500 Network Essent... (C9500 Network Essentials)        0 NOT IN USE"
                    ]
                ]
            }
        ]
    }

    TASK [dag : PING LEAFS TO SPINES] ********************************************************************
    skipping: [sw-core-spine-1] => (item=sw-core-spine-1)
    ok: [sw-access-leaf-2] => (item=sw-core-spine-1)
    ok: [sw-access-leaf-1] => (item=sw-core-spine-1)

    TASK [dag : SHOW PING DETAILS] ***********************************************************************
    skipping: [sw-core-spine-1] => (item=sw-core-spine-1)
    skipping: [sw-core-spine-1]
    ok: [sw-access-leaf-2] => (item=sw-core-spine-1) => {
        "msg": [
            "PING DESTINATION: 10.255.255.254",
            "PING SOURCE: 10.255.255.252",
            "PING COUNT: 1000",
            "PING MAX LOSS: 10%",
            "NETWORK DEVICE IP: 10.1.3.7",
            "NETWORK DEVICE HOSTNAME: SW-ACCESS-LEAF-2"
        ]
    }
    ok: [sw-access-leaf-1] => (item=sw-core-spine-1) => {
        "msg": [
            "PING DESTINATION: 10.255.255.254",
            "PING SOURCE: 10.255.255.253",
            "PING COUNT: 1000",
            "PING MAX LOSS: 10%",
            "NETWORK DEVICE IP: 10.1.3.15",
            "NETWORK DEVICE HOSTNAME: SW-ACCESS-LEAF-1"
        ]
    }

    TASK [dag : ASSERT SUCCESS OR FAILURE PING LEAFS TO SPINES] ******************************************
    skipping: [sw-core-spine-1] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': 'sw-core-spine-1', 'ansible_loop_var': 'item'})
    ok: [sw-access-leaf-1] => (item={'commands': ['ping 10.255.255.254 repeat 1000 source 10.255.255.253'], 'packet_loss': '0%', 'packets_rx': 1000, 'packets_tx': 1000, 'rtt': {'min': 1, 'avg': 1, 'max': 1}, 'invocation': {'module_args': {'dest': '10.255.255.254', 'source': '10.255.255.253', 'count': 1000, 'state': 'present', 'df_bit': False, 'size': None, 'vrf': None, 'provider': None}}, 'failed': False, 'changed': False, 'item': 'sw-core-spine-1', 'ansible_loop_var': 'item'}) => {
        "ansible_loop_var": "item",
        "changed": false,
        "item": {
            "ansible_loop_var": "item",
            "changed": false,
            "commands": [
                "ping 10.255.255.254 repeat 1000 source 10.255.255.253"
            ],
            "failed": false,
            "invocation": {
                "module_args": {
                    "count": 1000,
                    "dest": "10.255.255.254",
                    "df_bit": false,
                    "provider": null,
                    "size": null,
                    "source": "10.255.255.253",
                    "state": "present",
                    "vrf": null
                }
            },
            "item": "sw-core-spine-1",
            "packet_loss": "0%",
            "packets_rx": 1000,
            "packets_tx": 1000,
            "rtt": {
                "avg": 1,
                "max": 1,
                "min": 1
            }
        },
        "msg": [
            "---> LESS THAN 10% LOSS ON TEST PING!, CHECK PASSED! <---"
        ]
    }
    ok: [sw-access-leaf-2] => (item={'commands': ['ping 10.255.255.254 repeat 1000 source 10.255.255.252'], 'packet_loss': '0%', 'packets_rx': 1000, 'packets_tx': 1000, 'rtt': {'min': 1, 'avg': 1, 'max': 1}, 'invocation': {'module_args': {'dest': '10.255.255.254', 'source': '10.255.255.252', 'count': 1000, 'state': 'present', 'df_bit': False, 'size': None, 'vrf': None, 'provider': None}}, 'failed': False, 'changed': False, 'item': 'sw-core-spine-1', 'ansible_loop_var': 'item'}) => {
        "ansible_loop_var": "item",
        "changed": false,
        "item": {
            "ansible_loop_var": "item",
            "changed": false,
            "commands": [
                "ping 10.255.255.254 repeat 1000 source 10.255.255.252"
            ],
            "failed": false,
            "invocation": {
                "module_args": {
                    "count": 1000,
                    "dest": "10.255.255.254",
                    "df_bit": false,
                    "provider": null,
                    "size": null,
                    "source": "10.255.255.252",
                    "state": "present",
                    "vrf": null
                }
            },
            "item": "sw-core-spine-1",
            "packet_loss": "0%",
            "packets_rx": 1000,
            "packets_tx": 1000,
            "rtt": {
                "avg": 1,
                "max": 1,
                "min": 1
            }
        },
        "msg": [
            "---> LESS THAN 10% LOSS ON TEST PING!, CHECK PASSED! <---"
        ]
    }

    TASK [dag : PING SPINES TO LEAFS] ********************************************************************
    skipping: [sw-access-leaf-1] => (item=sw-access-leaf-1)
    skipping: [sw-access-leaf-1] => (item=sw-access-leaf-2)
    skipping: [sw-access-leaf-2] => (item=sw-access-leaf-1)
    skipping: [sw-access-leaf-2] => (item=sw-access-leaf-2)
    ok: [sw-core-spine-1] => (item=sw-access-leaf-1)
    ok: [sw-core-spine-1] => (item=sw-access-leaf-2)

    TASK [dag : SHOW PING DETAILS] ***********************************************************************
    skipping: [sw-access-leaf-1] => (item=sw-access-leaf-1)
    skipping: [sw-access-leaf-1] => (item=sw-access-leaf-2)
    skipping: [sw-access-leaf-1]
    skipping: [sw-access-leaf-2] => (item=sw-access-leaf-1)
    skipping: [sw-access-leaf-2] => (item=sw-access-leaf-2)
    skipping: [sw-access-leaf-2]
    ok: [sw-core-spine-1] => (item=sw-access-leaf-1) => {
        "msg": [
            "PING DESTINATION: 10.255.255.253",
            "PING SOURCE: 10.255.255.254",
            "PING COUNT: 1000",
            "PING MAX LOSS: 10%",
            "NETWORK DEVICE IP: 10.1.3.6",
            "NETWORK DEVICE HOSTNAME: S1-CORE-SPINE"
        ]
    }
    ok: [sw-core-spine-1] => (item=sw-access-leaf-2) => {
        "msg": [
            "PING DESTINATION: 10.255.255.252",
            "PING SOURCE: 10.255.255.254",
            "PING COUNT: 1000",
            "PING MAX LOSS: 10%",
            "NETWORK DEVICE IP: 10.1.3.6",
            "NETWORK DEVICE HOSTNAME: S1-CORE-SPINE"
        ]
    }

    TASK [dag : ASSERT SUCCESS OR FAILURE PING SPINES TO LEAFS] ******************************************
    skipping: [sw-access-leaf-1] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': 'sw-access-leaf-1', 'ansible_loop_var': 'item'})
    skipping: [sw-access-leaf-1] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': 'sw-access-leaf-2', 'ansible_loop_var': 'item'})
    skipping: [sw-access-leaf-2] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': 'sw-access-leaf-1', 'ansible_loop_var': 'item'})
    skipping: [sw-access-leaf-2] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': 'sw-access-leaf-2', 'ansible_loop_var': 'item'})
    ok: [sw-core-spine-1] => (item={'commands': ['ping 10.255.255.253 repeat 1000 source 10.255.255.254'], 'packet_loss': '0%', 'packets_rx': 1000, 'packets_tx': 1000, 'rtt': {'min': 1, 'avg': 1, 'max': 1}, 'invocation': {'module_args': {'dest': '10.255.255.253', 'source': '10.255.255.254', 'count': 1000, 'state': 'present', 'df_bit': False, 'size': None, 'vrf': None, 'provider': None}}, 'failed': False, 'changed': False, 'item': 'sw-access-leaf-1', 'ansible_loop_var': 'item'}) => {
        "ansible_loop_var": "item",
        "changed": false,
        "item": {
            "ansible_loop_var": "item",
            "changed": false,
            "commands": [
                "ping 10.255.255.253 repeat 1000 source 10.255.255.254"
            ],
            "failed": false,
            "invocation": {
                "module_args": {
                    "count": 1000,
                    "dest": "10.255.255.253",
                    "df_bit": false,
                    "provider": null,
                    "size": null,
                    "source": "10.255.255.254",
                    "state": "present",
                    "vrf": null
                }
            },
            "item": "sw-access-leaf-1",
            "packet_loss": "0%",
            "packets_rx": 1000,
            "packets_tx": 1000,
            "rtt": {
                "avg": 1,
                "max": 1,
                "min": 1
            }
        },
        "msg": [
            "---> LESS THAN 10% LOSS ON TEST PING!, CHECK PASSED! <---"
        ]
    }
    ok: [sw-core-spine-1] => (item={'commands': ['ping 10.255.255.252 repeat 1000 source 10.255.255.254'], 'packet_loss': '0%', 'packets_rx': 1000, 'packets_tx': 1000, 'rtt': {'min': 1, 'avg': 1, 'max': 1}, 'invocation': {'module_args': {'dest': '10.255.255.252', 'source': '10.255.255.254', 'count': 1000, 'state': 'present', 'df_bit': False, 'size': None, 'vrf': None, 'provider': None}}, 'failed': False, 'changed': False, 'item': 'sw-access-leaf-2', 'ansible_loop_var': 'item'}) => {
        "ansible_loop_var": "item",
        "changed": false,
        "item": {
            "ansible_loop_var": "item",
            "changed": false,
            "commands": [
                "ping 10.255.255.252 repeat 1000 source 10.255.255.254"
            ],
            "failed": false,
            "invocation": {
                "module_args": {
                    "count": 1000,
                    "dest": "10.255.255.252",
                    "df_bit": false,
                    "provider": null,
                    "size": null,
                    "source": "10.255.255.254",
                    "state": "present",
                    "vrf": null
                }
            },
            "item": "sw-access-leaf-2",
            "packet_loss": "0%",
            "packets_rx": 1000,
            "packets_tx": 1000,
            "rtt": {
                "avg": 1,
                "max": 1,
                "min": 1
            }
        },
        "msg": [
            "---> LESS THAN 10% LOSS ON TEST PING!, CHECK PASSED! <---"
        ]
    }

    TASK [dag : BACKUP CONFIG] ***************************************************************************
    changed: [sw-access-leaf-2]
    changed: [sw-core-spine-1]
    changed: [sw-access-leaf-1]

    TASK [dag : DEBUG] ***********************************************************************************
    ok: [sw-access-leaf-1] => {
        "msg": [
            {
                "backup_path": "./backup/sw-access-leaf-1_config.2022-02-15@18:20:19",
                "changed": true,
                "date": "2022-02-15",
                "failed": false,
                "filename": "sw-access-leaf-1_config.2022-02-15@18:20:19",
                "shortname": "./backup/sw-access-leaf-1_config",
                "time": "18:20:19"
            }
        ]
    }
    ok: [sw-access-leaf-2] => {
        "msg": [
            {
                "backup_path": "./backup/sw-access-leaf-2_config.2022-02-15@18:20:19",
                "changed": true,
                "date": "2022-02-15",
                "failed": false,
                "filename": "sw-access-leaf-2_config.2022-02-15@18:20:19",
                "shortname": "./backup/sw-access-leaf-2_config",
                "time": "18:20:19"
            }
        ]
    }
    ok: [sw-core-spine-1] => {
        "msg": [
            {
                "backup_path": "./backup/sw-core-spine-1_config.2022-02-15@18:20:19",
                "changed": true,
                "date": "2022-02-15",
                "failed": false,
                "filename": "sw-core-spine-1_config.2022-02-15@18:20:19",
                "shortname": "./backup/sw-core-spine-1_config",
                "time": "18:20:19"
            }
        ]
    }

    TASK [dag : include_tasks] ***************************************************************************
    skipping: [sw-core-spine-1]
    included: /home/netadmin/ansible/bgp-evpn-ansible/use_cases/dag/3/roles/dag/tasks/use_case_3/leaf_core_vrf_ipv4_multicast_config.yml for sw-access-leaf-1, sw-access-leaf-2

    TASK [dag : CONFIG VRF IPV4] *************************************************************************
    [WARNING]: To ensure idempotency and correct diff the input configuration lines should be similar to
    how they appear if present in the running configuration on device
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [dag : SHOW CONFIG] *****************************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [dag : DEBUG] ***********************************************************************************
    ok: [sw-access-leaf-1] => {
        "msg": [
            [
                [
                    "vrf definition VRF_OVERLAY_2010",
                    " rd 10.255.255.253:2010",
                    " !",
                    " address-family ipv4",
                    "  mdt auto-discovery vxlan",
                    "  mdt default vxlan 225.1.2.3",
                    "  mdt overlay use-bgp spt-only",
                    "  route-target export 64512:2010",
                    "  route-target import 64512:2010",
                    "  route-target export 64512:2010 stitching",
                    "  route-target import 64512:2010 stitching",
                    " exit-address-family"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0",
                    "  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => {
        "msg": [
            [
                [
                    "vrf definition VRF_OVERLAY_2010",
                    " rd 10.255.255.252:2010",
                    " !",
                    " address-family ipv4",
                    "  mdt auto-discovery vxlan",
                    "  mdt default vxlan 225.1.2.3",
                    "  mdt overlay use-bgp spt-only",
                    "  route-target export 64512:2010",
                    "  route-target import 64512:2010",
                    "  route-target export 64512:2010 stitching",
                    "  route-target import 64512:2010 stitching",
                    " exit-address-family"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0",
                    "  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4"
                ]
            ]
        ]
    }

    TASK [dag : include_tasks] ***************************************************************************
    skipping: [sw-core-spine-1]
    included: /home/netadmin/ansible/bgp-evpn-ansible/use_cases/dag/3/roles/dag/tasks/use_case_3/multicast_config_global.yml for sw-access-leaf-1, sw-access-leaf-2

    TASK [dag : CONFIG MULTICAST GLOBAL] *****************************************************************
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [dag : SHOW CONFIG] *****************************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [dag : DEBUG] ***********************************************************************************
    ok: [sw-access-leaf-1] => {
        "msg": [
            [
                [
                    "ip multicast-routing vrf VRF_OVERLAY_2010"
                ],
                [
                    "ip pim vrf VRF_OVERLAY_2010 rp-address 10.253.255.254"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => {
        "msg": [
            [
                [
                    "ip multicast-routing vrf VRF_OVERLAY_2010"
                ],
                [
                    "ip pim vrf VRF_OVERLAY_2010 rp-address 10.253.255.254"
                ]
            ]
        ]
    }

    TASK [dag : include_tasks] ***************************************************************************
    skipping: [sw-core-spine-1]
    included: /home/netadmin/ansible/bgp-evpn-ansible/use_cases/dag/3/roles/dag/tasks/use_case_3/repl_mode.yml for sw-access-leaf-1, sw-access-leaf-2

    TASK [dag : CONFIG L2VPN EVPN REPLICATION MODE] ******************************************************
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [dag : SHOW CONFIG] *****************************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [dag : DEBUG] ***********************************************************************************
    ok: [sw-access-leaf-1] => {
        "msg": [
            [
                [
                    "l2vpn evpn",
                    " replication-type static"
                ],
                [
                    "replication-type static"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => {
        "msg": [
            [
                [
                    "l2vpn evpn",
                    " replication-type static"
                ],
                [
                    "replication-type static"
                ]
            ]
        ]
    }

    TASK [dag : include_tasks] ***************************************************************************
    skipping: [sw-core-spine-1]
    included: /home/netadmin/ansible/bgp-evpn-ansible/use_cases/dag/3/roles/dag/tasks/use_case_3/l2vni_config_ipv4.yml for sw-access-leaf-1, sw-access-leaf-2

    TASK [dag : CONFIG VTP MODE] *************************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [dag : CONFIG L2VNI EDGE VLAN] ******************************************************************
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [dag : CONFIG L2VNI EDGE VLAN MEMBER] ***********************************************************
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [dag : CONFIG L2VNI L2VPN EVPN INSTANCE] ********************************************************
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [dag : CONFIG L2VNI L3 INTERFACE EDGE VLAN SVI] *************************************************
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [dag : SHOW CONFIG] *****************************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [dag : DEBUG] ***********************************************************************************
    ok: [sw-access-leaf-2] => {
        "msg": [
            [
                [
                    "vlan 2030",
                    " name 2030"
                ],
                [
                    "vlan configuration 2030",
                    " member evpn-instance 2030 vni 20030"
                ],
                [
                    "l2vpn evpn instance 20030 vlan-based",
                    " encapsulation vxlan"
                ],
                [
                    "interface Vlan2030",
                    " description 2030 SVI-INTERFACE",
                    " mac-address 0011.2233.4455",
                    " vrf forwarding VRF_OVERLAY_2010",
                    " ip address 10.251.255.2 255.255.255.0"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-1] => {
        "msg": [
            [
                [
                    "vlan 2030",
                    " name 2030"
                ],
                [
                    "vlan configuration 2030",
                    " member evpn-instance 2030 vni 20030"
                ],
                [
                    "l2vpn evpn instance 20030 vlan-based",
                    " encapsulation vxlan"
                ],
                [
                    "interface Vlan2030",
                    " description 2030 SVI-INTERFACE",
                    " mac-address 0012.3456.7890",
                    " vrf forwarding VRF_OVERLAY_2010",
                    " ip address 10.251.255.1 255.255.255.0"
                ]
            ]
        ]
    }

    TASK [dag : include_tasks] ***************************************************************************
    skipping: [sw-core-spine-1]
    included: /home/netadmin/ansible/bgp-evpn-ansible/use_cases/dag/3/roles/dag/tasks/use_case_3/l2vni_dhcp_config.yml for sw-access-leaf-1, sw-access-leaf-2

    TASK [dag : CONFIG DHCP L2VNI L3 INTERFACE EDGE VLAN SVI] ********************************************
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [dag : SHOW CONFIG] *****************************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [dag : DEBUG] ***********************************************************************************
    ok: [sw-access-leaf-1] => {
        "msg": [
            [
                [
                    "vlan 2030",
                    " name 2030"
                ],
                [
                    "vlan configuration 2030",
                    " member evpn-instance 2030 vni 20030"
                ],
                [
                    "l2vpn evpn instance 20030 vlan-based",
                    " encapsulation vxlan"
                ],
                [
                    "interface Vlan2030",
                    " description 2030 SVI-INTERFACE",
                    " mac-address 0012.3456.7890",
                    " vrf forwarding VRF_OVERLAY_2010",
                    " ip dhcp relay information option vpn-id ",
                    " ip address 10.251.255.1 255.255.255.0",
                    " ip helper-address global 10.10.10.10"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => {
        "msg": [
            [
                [
                    "vlan 2030",
                    " name 2030"
                ],
                [
                    "vlan configuration 2030",
                    " member evpn-instance 2030 vni 20030"
                ],
                [
                    "l2vpn evpn instance 20030 vlan-based",
                    " encapsulation vxlan"
                ],
                [
                    "interface Vlan2030",
                    " description 2030 SVI-INTERFACE",
                    " mac-address 0011.2233.4455",
                    " vrf forwarding VRF_OVERLAY_2010",
                    " ip dhcp relay information option vpn-id ",
                    " ip address 10.251.255.2 255.255.255.0",
                    " ip helper-address global 10.10.10.10"
                ]
            ]
        ]
    }

    TASK [dag : include_tasks] ***************************************************************************
    skipping: [sw-core-spine-1]
    included: /home/netadmin/ansible/bgp-evpn-ansible/use_cases/dag/3/roles/dag/tasks/use_case_3/leaf_core_vlan_ipv4_multicast_config.yml for sw-access-leaf-1, sw-access-leaf-2

    TASK [dag : CONFIG VTP MODE] *************************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [dag : CONFIG L2 CORE VLAN] *********************************************************************
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [dag : CONFIG L2 CORE VLAN MEMBER L3 VNI] *******************************************************
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [dag : CONFIG L3 SVI INTERFACE CORE VLAN] *******************************************************
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [dag : SHOW CONFIG] *****************************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [dag : DEBUG] ***********************************************************************************
    ok: [sw-access-leaf-2] => {
        "msg": [
            [
                [
                    "vtp mode transparent"
                ],
                [
                    "vlan 2010",
                    " name 2010"
                ],
                [
                    "vlan configuration 2010",
                    " member vni 20010"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 150 bytes",
                    "!",
                    "interface Vlan2010",
                    " description 2010 SVI Interface",
                    " vrf forwarding VRF_OVERLAY_2010",
                    " ip unnumbered Loopback1",
                    " ip pim sparse-mode",
                    " no autostate",
                    "end"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-1] => {
        "msg": [
            [
                [
                    "vtp mode transparent"
                ],
                [
                    "vlan 2010",
                    " name 2010"
                ],
                [
                    "vlan configuration 2010",
                    " member vni 20010"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 150 bytes",
                    "!",
                    "interface Vlan2010",
                    " description 2010 SVI Interface",
                    " vrf forwarding VRF_OVERLAY_2010",
                    " ip unnumbered Loopback1",
                    " ip pim sparse-mode",
                    " no autostate",
                    "end"
                ]
            ]
        ]
    }

    TASK [dag : include_tasks] ***************************************************************************
    skipping: [sw-core-spine-1]
    included: /home/netadmin/ansible/bgp-evpn-ansible/use_cases/dag/3/roles/dag/tasks/use_case_3/dhcp_config.yml for sw-access-leaf-1, sw-access-leaf-2

    TASK [dag : CONFIG DHCP GLOBAL] **********************************************************************
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [dag : SHOW CONFIG] *****************************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [dag : DEBUG] ***********************************************************************************
    ok: [sw-access-leaf-1] => {
        "msg": [
            [
                [
                    "ip dhcp relay information option vpn",
                    " ip dhcp relay information option vpn-id"
                ],
                [
                    "ip dhcp relay information option vpn",
                    "ip dhcp relay information option",
                    " ip dhcp relay information option vpn-id"
                ],
                [
                    "ip dhcp compatibility suboption link-selection standard"
                ],
                [
                    "ip dhcp compatibility suboption server-override standard"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => {
        "msg": [
            [
                [
                    "ip dhcp relay information option vpn",
                    " ip dhcp relay information option vpn-id"
                ],
                [
                    "ip dhcp relay information option vpn",
                    "ip dhcp relay information option",
                    " ip dhcp relay information option vpn-id"
                ],
                [
                    "ip dhcp compatibility suboption link-selection standard"
                ],
                [
                    "ip dhcp compatibility suboption server-override standard"
                ]
            ]
        ]
    }

    TASK [dag : include_tasks] ***************************************************************************
    skipping: [sw-core-spine-1]
    included: /home/netadmin/ansible/bgp-evpn-ansible/use_cases/dag/3/roles/dag/tasks/use_case_3/nve_interface.yml for sw-access-leaf-1, sw-access-leaf-2

    TASK [dag : CONFIG NVE INTERFACE] ********************************************************************
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [dag : SHOW CONFIG] *****************************************************************************
    ok: [sw-access-leaf-1]
    ok: [sw-access-leaf-2]

    TASK [dag : DEBUG] ***********************************************************************************
    ok: [sw-access-leaf-1] => {
        "msg": [
            [
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 177 bytes",
                    "!",
                    "interface nve1",
                    " description VXLAN AGGREGATION INTERFACE",
                    " no ip address",
                    " source-interface Loopback1",
                    " host-reachability protocol bgp",
                    " member vni 20010 vrf VRF_OVERLAY_2010",
                    "end"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => {
        "msg": [
            [
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 177 bytes",
                    "!",
                    "interface nve1",
                    " description VXLAN AGGREGATION INTERFACE",
                    " no ip address",
                    " source-interface Loopback1",
                    " host-reachability protocol bgp",
                    " member vni 20010 vrf VRF_OVERLAY_2010",
                    "end"
                ]
            ]
        ]
    }

    TASK [dag : include_tasks] ***************************************************************************
    skipping: [sw-access-leaf-1]
    skipping: [sw-access-leaf-2]
    skipping: [sw-core-spine-1]

    TASK [dag : include_tasks] ***************************************************************************
    skipping: [sw-core-spine-1]
    included: /home/netadmin/ansible/bgp-evpn-ansible/use_cases/dag/3/roles/dag/tasks/use_case_3/nve_interface_mcast_repl.yml for sw-access-leaf-1, sw-access-leaf-2

    TASK [dag : CONFIG NVE INTERFACE MCAST REPL] *********************************************************
    changed: [sw-access-leaf-1]
    changed: [sw-access-leaf-2]

    TASK [dag : SHOW CONFIG] *****************************************************************************
    ok: [sw-access-leaf-1]
    ok: [sw-access-leaf-2]

    TASK [dag : DEBUG] ***********************************************************************************
    ok: [sw-access-leaf-1] => {
        "msg": [
            [
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 217 bytes",
                    "!",
                    "interface nve1",
                    " description VXLAN AGGREGATION INTERFACE",
                    " no ip address",
                    " source-interface Loopback1",
                    " host-reachability protocol bgp",
                    " member vni 20010 vrf VRF_OVERLAY_2010",
                    " member vni 20030 mcast-group 225.4.5.6",
                    "end"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => {
        "msg": [
            [
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 217 bytes",
                    "!",
                    "interface nve1",
                    " description VXLAN AGGREGATION INTERFACE",
                    " no ip address",
                    " source-interface Loopback1",
                    " host-reachability protocol bgp",
                    " member vni 20010 vrf VRF_OVERLAY_2010",
                    " member vni 20030 mcast-group 225.4.5.6",
                    "end"
                ]
            ]
        ]
    }

    TASK [dag : include_tasks] ***************************************************************************
    skipping: [sw-core-spine-1]
    included: /home/netadmin/ansible/bgp-evpn-ansible/use_cases/dag/3/roles/dag/tasks/use_case_3/bgp_config_ipv4_multicast_config.yml for sw-access-leaf-1, sw-access-leaf-2

    TASK [dag : CONFIG BGP IPV4 LEAF] ********************************************************************
    changed: [sw-access-leaf-1] => (item=sw-core-spine-1)
    changed: [sw-access-leaf-2] => (item=sw-core-spine-1)

    TASK [dag : SHOW CONFIG] *****************************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [dag : DEBUG] ***********************************************************************************
    ok: [sw-access-leaf-1] => {
        "msg": [
            [
                [
                    "router bgp 64512",
                    " template peer-policy VTEP-EVPN-PEER-POLICY",
                    "  soft-reconfiguration inbound",
                    "  send-community both",
                    " exit-peer-policy",
                    " !",
                    " template peer-session VTEP-EVPN-PEER-SESSION",
                    "  remote-as 64512",
                    "  description BGP EVPN PEERS",
                    "  log-neighbor-changes",
                    "  update-source Loopback0",
                    " exit-peer-session",
                    " !",
                    " bgp router-id 10.255.255.253",
                    " bgp log-neighbor-changes",
                    " no bgp default ipv4-unicast",
                    " neighbor 10.255.255.254 inherit peer-session VTEP-EVPN-PEER-SESSION",
                    " !",
                    " address-family ipv4",
                    " exit-address-family",
                    " !",
                    " address-family ipv4 mvpn",
                    "  neighbor 10.255.255.254 activate",
                    "  neighbor 10.255.255.254 send-community both",
                    " exit-address-family",
                    " !",
                    " address-family l2vpn evpn",
                    "  neighbor 10.255.255.254 activate",
                    "  neighbor 10.255.255.254 send-community extended",
                    "  neighbor 10.255.255.254 inherit peer-policy VTEP-EVPN-PEER-POLICY",
                    " exit-address-family",
                    " !",
                    " address-family ipv4 vrf VRF_OVERLAY_2010",
                    "  advertise l2vpn evpn",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    " exit-address-family"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => {
        "msg": [
            [
                [
                    "router bgp 64512",
                    " template peer-policy VTEP-EVPN-PEER-POLICY",
                    "  soft-reconfiguration inbound",
                    "  send-community both",
                    " exit-peer-policy",
                    " !",
                    " template peer-session VTEP-EVPN-PEER-SESSION",
                    "  remote-as 64512",
                    "  description BGP EVPN PEERS",
                    "  log-neighbor-changes",
                    "  update-source Loopback0",
                    " exit-peer-session",
                    " !",
                    " bgp router-id 10.255.255.252",
                    " bgp log-neighbor-changes",
                    " no bgp default ipv4-unicast",
                    " neighbor 10.255.255.254 inherit peer-session VTEP-EVPN-PEER-SESSION",
                    " !",
                    " address-family ipv4",
                    " exit-address-family",
                    " !",
                    " address-family ipv4 mvpn",
                    "  neighbor 10.255.255.254 activate",
                    "  neighbor 10.255.255.254 send-community both",
                    " exit-address-family",
                    " !",
                    " address-family l2vpn evpn",
                    "  neighbor 10.255.255.254 activate",
                    "  neighbor 10.255.255.254 send-community extended",
                    "  neighbor 10.255.255.254 inherit peer-policy VTEP-EVPN-PEER-POLICY",
                    " exit-address-family",
                    " !",
                    " address-family ipv4 vrf VRF_OVERLAY_2010",
                    "  advertise l2vpn evpn",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    " exit-address-family"
                ]
            ]
        ]
    }

    TASK [dag : include_tasks] ***************************************************************************
    skipping: [sw-access-leaf-1]
    skipping: [sw-core-spine-1]
    included: /home/netadmin/ansible/bgp-evpn-ansible/use_cases/dag/3/roles/dag/tasks/use_case_3/leaf_border_handoff_multicast_config.yml for sw-access-leaf-2

    TASK [dag : CONFIG BGP LEAF BORDER HANDOFF] **********************************************************
    changed: [sw-access-leaf-2] => (item=sw-core-spine-1)

    TASK [dag : SHOW CONFIG] *****************************************************************************
    ok: [sw-access-leaf-2]

    TASK [dag : DEBUG] ***********************************************************************************
    ok: [sw-access-leaf-2] => {
        "msg": [
            [
                [
                    "router bgp 64512",
                    " template peer-policy VTEP-EVPN-PEER-POLICY",
                    "  soft-reconfiguration inbound",
                    "  send-community both",
                    " exit-peer-policy",
                    " !",
                    " template peer-session VTEP-EVPN-PEER-SESSION",
                    "  remote-as 64512",
                    "  description BGP EVPN PEERS",
                    "  log-neighbor-changes",
                    "  update-source Loopback0",
                    " exit-peer-session",
                    " !",
                    " bgp router-id 10.255.255.252",
                    " bgp log-neighbor-changes",
                    " no bgp default ipv4-unicast",
                    " neighbor 10.255.255.254 inherit peer-session VTEP-EVPN-PEER-SESSION",
                    " !",
                    " address-family ipv4",
                    " exit-address-family",
                    " !",
                    " address-family ipv4 mvpn",
                    "  neighbor 10.255.255.254 activate",
                    "  neighbor 10.255.255.254 send-community both",
                    " exit-address-family",
                    " !",
                    " address-family l2vpn evpn",
                    "  neighbor 10.255.255.254 activate",
                    "  neighbor 10.255.255.254 send-community extended",
                    "  neighbor 10.255.255.254 inherit peer-policy VTEP-EVPN-PEER-POLICY",
                    " exit-address-family",
                    " !",
                    " address-family ipv4 vrf VRF_OVERLAY_2010",
                    "  advertise l2vpn evpn",
                    "  redistribute connected",
                    "  neighbor 10.9.8.7 remote-as 64513",
                    "  neighbor 10.9.8.7 activate",
                    "  neighbor 10.9.8.7 send-community both",
                    "  maximum-paths 4",
                    " exit-address-family"
                ]
            ]
        ]
    }

    TASK [dag : include_tasks] ***************************************************************************
    skipping: [sw-access-leaf-1]
    skipping: [sw-access-leaf-2]
    included: /home/netadmin/ansible/bgp-evpn-ansible/use_cases/dag/3/roles/dag/tasks/use_case_3/spine_bgp_ipv4_core.yml for sw-core-spine-1

    TASK [dag : CONFIG BGP IPV4 SPINE] *******************************************************************
    changed: [sw-core-spine-1] => (item=sw-access-leaf-1)
    changed: [sw-core-spine-1] => (item=sw-access-leaf-2)

    TASK [dag : SHOW CONFIG] *****************************************************************************
    ok: [sw-core-spine-1]

    TASK [dag : DEBUG] ***********************************************************************************
    ok: [sw-core-spine-1] => {
        "msg": [
            [
                [
                    "router bgp 64512",
                    " template peer-policy VTEP-EVPN-PEER-POLICY",
                    "  route-reflector-client",
                    "  soft-reconfiguration inbound",
                    "  send-community both",
                    " exit-peer-policy",
                    " !",
                    " template peer-session VTEP-EVPN-PEER-SESSION",
                    "  remote-as 64512",
                    "  description BGP EVPN PEERS",
                    "  log-neighbor-changes",
                    "  update-source Loopback0",
                    " exit-peer-session",
                    " !",
                    " bgp router-id 10.255.255.254",
                    " bgp log-neighbor-changes",
                    " neighbor 10.255.255.252 inherit peer-session VTEP-EVPN-PEER-SESSION",
                    " neighbor 10.255.255.253 inherit peer-session VTEP-EVPN-PEER-SESSION",
                    " !",
                    " address-family l2vpn evpn",
                    "  neighbor 10.255.255.252 activate",
                    "  neighbor 10.255.255.252 send-community extended",
                    "  neighbor 10.255.255.252 inherit peer-policy VTEP-EVPN-PEER-POLICY",
                    "  neighbor 10.255.255.253 activate",
                    "  neighbor 10.255.255.253 send-community extended",
                    "  neighbor 10.255.255.253 inherit peer-policy VTEP-EVPN-PEER-POLICY",
                    " exit-address-family"
                ]
            ]
        ]
    }

    TASK [dag : SAVE CONFIG] *****************************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-core-spine-1]
    ok: [sw-access-leaf-1]

    TASK [dag : DEBUG] ***********************************************************************************
    ok: [sw-access-leaf-1] => {
        "msg": [
            [
                [
                    "Building configuration...",
                    "[OK]"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => {
        "msg": [
            [
                [
                    "Building configuration...",
                    "[OK]"
                ]
            ]
        ]
    }
    ok: [sw-core-spine-1] => {
        "msg": [
            [
                [
                    "Building configuration...",
                    "[OK]"
                ]
            ]
        ]
    }

    TASK [dag : BACKUP CONFIG] ***************************************************************************
    changed: [sw-core-spine-1]
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [dag : DEBUG] ***********************************************************************************
    ok: [sw-access-leaf-1] => {
        "msg": [
            {
                "backup_path": "./backup/sw-access-leaf-1_config.2022-02-15@18:21:45",
                "changed": true,
                "date": "2022-02-15",
                "failed": false,
                "filename": "sw-access-leaf-1_config.2022-02-15@18:21:45",
                "shortname": "./backup/sw-access-leaf-1_config",
                "time": "18:21:45"
            }
        ]
    }
    ok: [sw-access-leaf-2] => {
        "msg": [
            {
                "backup_path": "./backup/sw-access-leaf-2_config.2022-02-15@18:21:45",
                "changed": true,
                "date": "2022-02-15",
                "failed": false,
                "filename": "sw-access-leaf-2_config.2022-02-15@18:21:45",
                "shortname": "./backup/sw-access-leaf-2_config",
                "time": "18:21:45"
            }
        ]
    }
    ok: [sw-core-spine-1] => {
        "msg": [
            {
                "backup_path": "./backup/sw-core-spine-1_config.2022-02-15@18:21:45",
                "changed": true,
                "date": "2022-02-15",
                "failed": false,
                "filename": "sw-core-spine-1_config.2022-02-15@18:21:45",
                "shortname": "./backup/sw-core-spine-1_config",
                "time": "18:21:45"
            }
        ]
    }

    TASK [dag : include_tasks] ***************************************************************************
    skipping: [sw-access-leaf-1]
    skipping: [sw-access-leaf-2]
    included: /home/netadmin/ansible/bgp-evpn-ansible/use_cases/dag/3/roles/common/tasks/verification_bgp.yml for sw-core-spine-1

    TASK [dag : SHOW IP BGP L2VPN EVPN SUMMARY] **********************************************************
    ok: [sw-core-spine-1]

    TASK [dag : DEBUG] ***********************************************************************************
    ok: [sw-core-spine-1] => {
        "msg": [
            {
                "changed": false,
                "failed": false,
                "stdout": [
                    "Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd\n10.255.255.252  4        64512       2       2        1    0    0 00:00:03        0\n10.255.255.253  4        64512       2       2        1    0    0 00:00:06        0"
                ],
                "stdout_lines": [
                    [
                        "Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd",
                        "10.255.255.252  4        64512       2       2        1    0    0 00:00:03        0",
                        "10.255.255.253  4        64512       2       2        1    0    0 00:00:06        0"
                    ]
                ]
            }
        ]
    }

    TASK [dag : ASSERT SUCCESS OR FAILURE BASED ON BGP OUTPUT: UPTIME > 00:00:00 AND NEIGHBOORS LEAFS LISTED CORRECTLY] ***
    ok: [sw-core-spine-1] => (item=sw-access-leaf-1) => {
        "ansible_loop_var": "item",
        "changed": false,
        "item": "sw-access-leaf-1",
        "msg": "---> BGP SESSIONS ESTABLISHED! <---"
    }
    ok: [sw-core-spine-1] => (item=sw-access-leaf-2) => {
        "ansible_loop_var": "item",
        "changed": false,
        "item": "sw-access-leaf-2",
        "msg": "---> BGP SESSIONS ESTABLISHED! <---"
    }

    TASK [dag : DEBUG] ***********************************************************************************
    ok: [sw-core-spine-1] => {
        "msg": [
            {
                "changed": false,
                "msg": "All items completed",
                "results": [
                    {
                        "ansible_loop_var": "item",
                        "changed": false,
                        "failed": false,
                        "item": "sw-access-leaf-1",
                        "msg": "---> BGP SESSIONS ESTABLISHED! <---"
                    },
                    {
                        "ansible_loop_var": "item",
                        "changed": false,
                        "failed": false,
                        "item": "sw-access-leaf-2",
                        "msg": "---> BGP SESSIONS ESTABLISHED! <---"
                    }
                ],
                "skipped": false
            }
        ]
    }

    PLAY RECAP *******************************************************************************************
    sw-access-leaf-1           : ok=60   changed=17   unreachable=0    failed=0    skipped=7    rescued=0    ignored=0
    sw-access-leaf-2           : ok=64   changed=18   unreachable=0    failed=0    skipped=6    rescued=0    ignored=0
    sw-core-spine-1            : ok=22   changed=3    unreachable=0    failed=0    skipped=15   rescued=0    ignored=0
    ```

### Utility playbook execution example:
  * The network in this example has one spine and two leaf nodes, one simple leaf node, one border leaf node.
  * **The utility playbook execution should look like this**:
    ```SHELL
    ansible-playbook playbook_clear_all_config.yml

    PLAY [network] ***************************************************************************************

    TASK [CLEAR CONFIG BGP IPV4 SPINE] *******************************************************************
    [WARNING]: To ensure idempotency and correct diff the input configuration lines should be similar to
    how they appear if present in the running configuration on device
    changed: [sw-core-spine-1]
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [CLEAR CONFIG BGP LEAF BORDER HANDOFF] **********************************************************
    changed: [sw-core-spine-1]
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [CLEAR CONFIG BGP IPV4 LEAF] ********************************************************************
    changed: [sw-core-spine-1]
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [CLEAR CONFIG NVE INTERFACE MCAST REPL] *********************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-core-spine-1]
    ok: [sw-access-leaf-1]

    TASK [CLEAR CONFIG NVE INTERFACE INGRESS REPL] *******************************************************
    ok: [sw-core-spine-1]
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [CLEAR CONFIG NVE INTERFACE] ********************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-core-spine-1]
    ok: [sw-access-leaf-1]

    TASK [CLEAR CONFIG DHCP GLOBAL] **********************************************************************
    changed: [sw-access-leaf-2]
    changed: [sw-core-spine-1]
    changed: [sw-access-leaf-1]

    TASK [CLEAR CONFIG LEAF_CORE_VLAN_IPV4_MULTICAST_CONFIG] *********************************************
    ok: [sw-core-spine-1]
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [CLEAR CONFIG DHCP L2VNI L3 INTERFACE EDGE VLAN SVI] ********************************************
    ok: [sw-core-spine-1]
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [CLEAR CONFIG L2VNI_CONFIG_IPV4 1] **************************************************************
    ok: [sw-core-spine-1]
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [CLEAR CONFIG L2VNI_CONFIG_IPV4 2] **************************************************************
    changed: [sw-access-leaf-2]
    changed: [sw-core-spine-1]
    changed: [sw-access-leaf-1]

    TASK [CLEAR CONFIG L2VPN EVPN REPLICATION MODE] ******************************************************
    changed: [sw-access-leaf-2]
    changed: [sw-core-spine-1]
    changed: [sw-access-leaf-1]

    TASK [CLEAR CONFIG MULTICAST GLOBAL] *****************************************************************
    changed: [sw-core-spine-1]
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [CLEAR CONFIG VRF IPV4] *************************************************************************
    changed: [sw-access-leaf-2]
    changed: [sw-core-spine-1]
    changed: [sw-access-leaf-1]

    TASK [SHOW VRF] **************************************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-core-spine-1]
    ok: [sw-access-leaf-1]

    PLAY RECAP *******************************************************************************************
    sw-access-leaf-1           : ok=15   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    sw-access-leaf-2           : ok=15   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    sw-core-spine-1            : ok=15   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```