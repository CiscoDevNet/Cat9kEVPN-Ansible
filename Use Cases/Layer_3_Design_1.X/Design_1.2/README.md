# LAYER3 LOOP (L3_LOOP) USE CASE #2

##

### Playbook details:
This playbook covers the L3 LOOP USE CASE #2 with this configuration:
  * **DHCP Relay**: Global
  * **Overlay Type**: L3 Routed
  * **VRF AF**: IPv4 + IPv6
  * **Overlay Topology**: Full Mesh
  * **IP VRF RT Export**: "ASN:CORE_VLAN_ID"
  * **IP VRF RT Import**: "ASN:CORE_VLAN_ID"
  * **Layer 3 Multicast**: None
  * **Border Handoff**: L3 VRF
  * **Core VLAN**: L3VNI

### Topology:
The topology consist of several nodes, classified as _spines_ (one or more) and _leafs_ (one or more), the _spines_ are the main network nodes aka "cores", the _leafs_ are the nodes where end devices connect aka "access", the _leafs_ can be a simple "leaf" to connect end devices or a "border" leaf to connect to other networks.

### Variables:
The playbook variables are configured in the file _inventory.yml_, this YAML file is divided in two groups, _leafs_ for the leafs nodes and _spines_ for the _spines_ nodes, this two groups are joined on a single group called "network" which contains the whole network to be configured.

### Variables location:
The playbook variables can be located at:
 * An specific node for a per node variable, specific to that node.
 * An specific group, under the "vars" section of the group, for variables for the whole group.
 * The "network" group, under the "vars" section of this group, for variables of the whole network.

### Per node variables for _leafs_ nodes:
  * **The node name**: Ansible name for the device, usually the hostname of the device, example: "sw-access-leaf-1".
  * **ansible_host**: the DNS FQDN or IP ADDRESS, used to access and configure the device by Ansible.
  * **role**: leafs nodes classification as a simple "leaf" or a "border" leaf.
  * **rid**: BGP Router ID (RID), unique per node.
  * **vrfs**: Array variable containing one or more VRFs, with their respective variables like name, CORE VLAN, L3VNI and EDGE VLAN with ID, IP and MASK. This sub-variables are listed here:
    * **vrf_name**: Name string for the VRF.
    * **vrf_core_vlan_id**: Core VLAN ID number.
    * **vrf_l3_vni_id**: L3 VNI ID number.
    * **vrf_edge_vlan**: Edge VLAN sub-array with one or more ID, IP and MASK:
      * **id**: EDGE VLAN ID number.
      * **ip**: IPv4 address for the L3 EDGE VLAN, unique per VRF and node.
      * **mask**: IPv4 mask for the L3 EDGE VLAN.
      * **ipv6**: IPv6 address for the L3 EDGE VLAN, unique per VRF and node.
      * **prefix**: IPv6 prefix for the L3 EDGE VLAN.
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
          vrfs:
            - vrf_name: VRF_OVERLAY_2010
              vrf_core_vlan_id: 2010
              vrf_l3_vni_id: 20010
              vrf_edge_vlan:
                - id: 10
                  ip: 10.10.10.1
                  mask: 255.255.255.0
                  ipv6: 1111:2222:3333:4444:5555:6666:7777:8881
                  prefix: 48
                - id: 20
                  ip: 10.20.20.1
                  mask: 255.255.255.0
                  ipv6: 2222:3333:4444:5555:6666:7777:8888:9991
                  prefix: 48
                - id: 30
                  ip: 10.30.30.1
                  mask: 255.255.255.0
                  ipv6: 3333:4444:5555:6666:7777:8888:9999:1111
                  prefix: 48
            - vrf_name: VRF_OVERLAY_2020
              vrf_core_vlan_id: 2020
              vrf_l3_vni_id: 20020
              vrf_edge_vlan:
                - id: 40
                  ip: 10.40.40.1
                  mask: 255.255.255.0
                  ipv6: 4444:5555:6666:7777:8888:9999:1111:2221
                  prefix: 48
                - id: 50
                  ip: 10.50.50.1
                  mask: 255.255.255.0
                  ipv6: 5555:6666:7777:8888:9999:1111:2222:3331
                  prefix: 48
            - vrf_name: VRF_OVERLAY_2030
              vrf_core_vlan_id: 2030
              vrf_l3_vni_id: 20030
              vrf_edge_vlan:
                - id: 60
                  ip: 10.60.60.1
                  mask: 255.255.255.0
                  ipv6: 6666:7777:8888:9999:1111:2222:3333:4441
                  prefix: 48
    #
        sw-access-leaf-2:
          ansible_host: 10.1.3.7
          role: border
          rid: 10.255.255.252
          vrfs:
            - vrf_name: VRF_OVERLAY_2010
              vrf_core_vlan_id: 2010
              vrf_l3_vni_id: 20010
              vrf_edge_vlan:
                - id: 10
                  ip: 10.10.10.2
                  mask: 255.255.255.0
                  ipv6: 1111:2222:3333:4444:5555:6666:7777:8882
                  prefix: 48
                - id: 20
                  ip: 10.20.20.2
                  mask: 255.255.255.0
                  ipv6: 2222:3333:4444:5555:6666:7777:8888:9992
                  prefix: 48
                - id: 30
                  ip: 10.30.30.2
                  mask: 255.255.255.0
                  ipv6: 3333:4444:5555:6666:7777:8888:9999:1112
                  prefix: 48
            - vrf_name: VRF_OVERLAY_2020
              vrf_core_vlan_id: 2020
              vrf_l3_vni_id: 20020
              vrf_edge_vlan:
                - id: 40
                  ip: 10.40.40.2
                  mask: 255.255.255.0
                  ipv6: 4444:5555:6666:7777:8888:9999:1111:2222
                  prefix: 48
                - id: 50
                  ip: 10.50.50.2
                  mask: 255.255.255.0
                  ipv6: 5555:6666:7777:8888:9999:1111:2222:3332
                  prefix: 48
            - vrf_name: VRF_OVERLAY_2030
              vrf_core_vlan_id: 2030
              vrf_l3_vni_id: 20030
              vrf_edge_vlan:
                - id: 60
                  ip: 10.60.60.2
                  mask: 255.255.255.0
                  ipv6: 6666:7777:8888:9999:1111:2222:3333:4442
                  prefix: 48
    #
    ```
      * Note the indentation for YAML (two spaces).

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
  * **bgp_asn**: BGP ASN number.
  * **remote_as**: Remote BGP ASN number.
  * **primary_dhcp_server**: Primary DHCP IP address.
  * **secondary_dhcp_server**: Secondary DHCP IP address.
  * **nve_id**: NVE ID number to be used for the VXLAN aggregation interface.
  * **border_ip**: BGP border IP neighboor address to be used in the "border" leafs.
  * **border_remote_as**: BGP ASN remote number to be used in the "border" leafs.
  * **loopback**: Loopback interface to be used on several SVI Interfaces.
  * **bgp_source**: BGP source interface.
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
        bgp_asn: 64512
        remote_as: 64512
        primary_dhcp_server: 10.10.10.10
        secondary_dhcp_server: 10.20.20.20
        nve_id: 1
        border_ip: 10.9.8.7
        border_remote_as: 64513
        loopback: Loopback1
        bgp_source: Loopback0
    #
    ```
     * Note the indentation for YAML (two spaces).

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
      TASK [layer_3_loop: SHOW LICENSE SUMMARY] ********************************************************************
      ok: [sw-core-spine-1]
      ok: [sw-access-leaf-1]
      ok: [sw-access-leaf-2]

      TASK [layer_3_loop: ASSERT SUCCESS OR FAILURE BASED ON LICENSE TYPE] *****************************************
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

      TASK [layer_3_loop: DEBUG] ***********************************************************************************
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
      TASK [layer_3_loop: PING LEAFS TO SPINES] ********************************************************************
      skipping: [sw-core-spine-1] => (item=sw-core-spine-1)
      ok: [sw-access-leaf-2] => (item=sw-core-spine-1)
      ok: [sw-access-leaf-1] => (item=sw-core-spine-1)

      TASK [layer_3_loop: SHOW PING DETAILS] ***********************************************************************
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

      TASK [layer_3_loop: ASSERT SUCCESS OR FAILURE PING LEAFS TO SPINES] ******************************************
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

      TASK [layer_3_loop: PING SPINES TO LEAFS] ********************************************************************
      skipping: [sw-access-leaf-1] => (item=sw-access-leaf-1)
      skipping: [sw-access-leaf-1] => (item=sw-access-leaf-2)
      skipping: [sw-access-leaf-2] => (item=sw-access-leaf-1)
      skipping: [sw-access-leaf-2] => (item=sw-access-leaf-2)
      ok: [sw-core-spine-1] => (item=sw-access-leaf-1)
      ok: [sw-core-spine-1] => (item=sw-access-leaf-2)

      TASK [layer_3_loop: SHOW PING DETAILS] ***********************************************************************
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

      TASK [layer_3_loop: ASSERT SUCCESS OR FAILURE PING SPINES TO LEAFS] ******************************************
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
      TASK [layer_3_loop: SHOW IP BGP L2VPN EVPN SUMMARY] **********************************************************
      ok: [sw-core-spine-1]

      TASK [layer_3_loop: DEBUG] ***********************************************************************************
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

      TASK [layer_3_loop : ASSERT SUCCESS OR FAILURE BASED ON BGP OUTPUT: UPTIME > 00:00:00 OR NOT NEVER AND NEIGHBOORS LEAFS LISTED CORRECTLY]
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

      TASK [layer_3_loop: DEBUG] ***********************************************************************************
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
      ```
    * **Playbook tasks validation example**:
      * Succesfull execution (note 0 task failed):
        ```SHELL
        PLAY RECAP *****************************************************************************************
        sw-access-leaf-1           : ok=68   changed=22   unreachable=0    failed=0    skipped=6    rescued=0    ignored=0
        sw-access-leaf-2           : ok=74   changed=25   unreachable=0    failed=0    skipped=5    rescued=0    ignored=0
        sw-core-spine-1            : ok=22   changed=3    unreachable=0    failed=0    skipped=11   rescued=0    ignored=0
        ```
      * Failed execution (note 1 task failed on sw-access-leaf-1 and sw-access-leaf-2):
        ```SHELL
        PLAY RECAP *****************************************************************************************
        sw-access-leaf-1           : ok=67   changed=22   unreachable=0    failed=1    skipped=6    rescued=0    ignored=0
        sw-access-leaf-2           : ok=73   changed=25   unreachable=0    failed=1    skipped=5    rescued=0    ignored=0
        sw-core-spine-1            : ok=22   changed=3    unreachable=0    failed=0    skipped=11   rescued=0    ignored=0
        ```

### Inventory example:
  * A full inventory, three leafs, two simple leafs, one border leaf and two spines, with all the needed variables looks like this:
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
          vrfs:
            - vrf_name: VRF_OVERLAY_2010
              vrf_core_vlan_id: 2010
              vrf_l3_vni_id: 20010
              vrf_edge_vlan:
                - id: 10
                  ip: 10.10.10.1
                  mask: 255.255.255.0
                  ipv6: 1111:2222:3333:4444:5555:6666:7777:8881
                  prefix: 48
                - id: 20
                  ip: 10.20.20.1
                  mask: 255.255.255.0
                  ipv6: 2222:3333:4444:5555:6666:7777:8888:9991
                  prefix: 48
                - id: 30
                  ip: 10.30.30.1
                  mask: 255.255.255.0
                  ipv6: 3333:4444:5555:6666:7777:8888:9999:1111
                  prefix: 48
            - vrf_name: VRF_OVERLAY_2020
              vrf_core_vlan_id: 2020
              vrf_l3_vni_id: 20020
              vrf_edge_vlan:
                - id: 40
                  ip: 10.40.40.1
                  mask: 255.255.255.0
                  ipv6: 4444:5555:6666:7777:8888:9999:1111:2221
                  prefix: 48
                - id: 50
                  ip: 10.50.50.1
                  mask: 255.255.255.0
                  ipv6: 5555:6666:7777:8888:9999:1111:2222:3331
                  prefix: 48
            - vrf_name: VRF_OVERLAY_2030
              vrf_core_vlan_id: 2030
              vrf_l3_vni_id: 20030
              vrf_edge_vlan:
                - id: 60
                  ip: 10.60.60.1
                  mask: 255.255.255.0
                  ipv6: 6666:7777:8888:9999:1111:2222:3333:4441
                  prefix: 48
    #
        sw-access-leaf-2:
          ansible_host: 10.1.3.7
          role: border
          rid: 10.255.255.252
          vrfs:
            - vrf_name: VRF_OVERLAY_2010
              vrf_core_vlan_id: 2010
              vrf_l3_vni_id: 20010
              vrf_edge_vlan:
                - id: 10
                  ip: 10.10.10.2
                  mask: 255.255.255.0
                  ipv6: 1111:2222:3333:4444:5555:6666:7777:8882
                  prefix: 48
                - id: 20
                  ip: 10.20.20.2
                  mask: 255.255.255.0
                  ipv6: 2222:3333:4444:5555:6666:7777:8888:9992
                  prefix: 48
                - id: 30
                  ip: 10.30.30.2
                  mask: 255.255.255.0
                  ipv6: 3333:4444:5555:6666:7777:8888:9999:1112
                  prefix: 48
            - vrf_name: VRF_OVERLAY_2020
              vrf_core_vlan_id: 2020
              vrf_l3_vni_id: 20020
              vrf_edge_vlan:
                - id: 40
                  ip: 10.40.40.2
                  mask: 255.255.255.0
                  ipv6: 4444:5555:6666:7777:8888:9999:1111:2222
                  prefix: 48
                - id: 50
                  ip: 10.50.50.2
                  mask: 255.255.255.0
                  ipv6: 5555:6666:7777:8888:9999:1111:2222:3332
                  prefix: 48
            - vrf_name: VRF_OVERLAY_2030
              vrf_core_vlan_id: 2030
              vrf_l3_vni_id: 20030
              vrf_edge_vlan:
                - id: 60
                  ip: 10.60.60.2
                  mask: 255.255.255.0
                  ipv6: 6666:7777:8888:9999:1111:2222:3333:4442
                  prefix: 48
    #
        sw-access-leaf-3:
          ansible_host: 10.1.3.3
          role: border
          rid: 10.255.255.251
          vrfs:
            - vrf_name: VRF_OVERLAY_2010
              vrf_core_vlan_id: 2010
              vrf_l3_vni_id: 20010
              vrf_edge_vlan:
                - id: 10
                  ip: 10.10.10.3
                  mask: 255.255.255.0
                  ipv6: 1111:2222:3333:4444:5555:6666:7777:8883
                  prefix: 48
                - id: 20
                  ip: 10.20.20.3
                  mask: 255.255.255.0
                  ipv6: 2222:3333:4444:5555:6666:7777:8888:9993
                  prefix: 48
                - id: 30
                  ip: 10.30.30.3
                  mask: 255.255.255.0
                  ipv6: 3333:4444:5555:6666:7777:8888:9999:1113
                  prefix: 48
            - vrf_name: VRF_OVERLAY_2020
              vrf_core_vlan_id: 2020
              vrf_l3_vni_id: 20020
              vrf_edge_vlan:
                - id: 40
                  ip: 10.40.40.3
                  mask: 255.255.255.0
                  ipv6: 4444:5555:6666:7777:8888:9999:1111:2223
                  prefix: 48
                - id: 50
                  ip: 10.50.50.3
                  mask: 255.255.255.0
                  ipv6: 5555:6666:7777:8888:9999:1111:2222:3333
                  prefix: 48
            - vrf_name: VRF_OVERLAY_2030
              vrf_core_vlan_id: 2030
              vrf_l3_vni_id: 20030
              vrf_edge_vlan:
                - id: 60
                  ip: 10.60.60.3
                  mask: 255.255.255.0
                  ipv6: 6666:7777:8888:9999:1111:2222:3333:4443
                  prefix: 48
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
          ansible_host: 10.1.3.9
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
        bgp_asn: 64512
        remote_as: 64512
        primary_dhcp_server: 10.10.10.10
        secondary_dhcp_server: 10.20.20.20
        nve_id: 1
        border_ip: 10.9.8.7
        border_remote_as: 64513
        loopback: Loopback1
        bgp_source: Loopback0
    #
    ...
    ```
      * Note the indentation for YAML (two spaces).

### Main playbook execution:
#### New deployment:
  * To configure a new network or a green field deployment follow the next steps.
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
      PLAY RECAP *****************************************************************************************
      sw-access-leaf-1           : ok=68   changed=22   unreachable=0    failed=0    skipped=6    rescued=0    ignored=0
      sw-access-leaf-2           : ok=74   changed=25   unreachable=0    failed=0    skipped=5    rescued=0    ignored=0
      sw-core-spine-1            : ok=22   changed=3    unreachable=0    failed=0    skipped=11   rescued=0    ignored=0
      ```
    * Failed execution (note 1 task failed on sw-access-leaf-1 and sw-access-leaf-2):
      ```SHELL
      PLAY RECAP *****************************************************************************************
      sw-access-leaf-1           : ok=67   changed=22   unreachable=0    failed=1    skipped=6    rescued=0    ignored=0
      sw-access-leaf-2           : ok=73   changed=25   unreachable=0    failed=1    skipped=5    rescued=0    ignored=0
      sw-core-spine-1            : ok=22   changed=3    unreachable=0    failed=0    skipped=11   rescued=0    ignored=0
      ```
#### Add a new VRF, CORE VLAN, L3VNI and EDGE VLAN:
  * After executing the new config or a green field deployment, if there's a need to add a new VRF, CORE VLAN, L3VNI and EDGE VLAN, like a "day_two" modification to the configuration, execute the next steps.
  * The main playbook to run all the configuration on this use case scenario is called _playbook.yml_.
  * **To execute this playbook the following commands must be run**:
    ```SHELL
    ansible-playbook playbook.yml --tags "day_two"
    ```
      * Note the execution of "ansible-playbook" without the "-i" option, this option usually calls the inventory file, but this file is already defined in the _ansible.cfg_ file, there's no need to call it from the playbook execution.
  * Before executing the playbook, the _inventory.yml_ needs to be modified, the new VRF, CORE VLAN, L3VNI and EDGE VLAN data needs to be added to this file, the old VRF, CORE VLAN, L3VNI and EDGE VLAN data needs to be commented out (#) or deleted from _inventory.yml_ to modify the network.
      * **Example**: VRF_OVERLAY_2010 already exists, it was added in the first run or new deployment, all the data for this VRF is commented out with "#", the VRF_OVERLAY_2020 is a new VRF to be added to the network, all data for this new VRF is added to the _leafs_ nodes.
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
              vrfs:
                # - vrf_name: VRF_OVERLAY_2010
                  # vrf_core_vlan_id: 2010
                  # vrf_l3_vni_id: 20010
                  # vrf_edge_vlan:
                    # - id: 10
                      # ip: 10.10.10.1
                      # mask: 255.255.255.0
                      # ipv6: 1111:2222:3333:4444:5555:6666:7777:8881
                      # prefix: 48
                    # - id: 20
                      # ip: 10.20.20.1
                      # mask: 255.255.255.0
                      # ipv6: 2222:3333:4444:5555:6666:7777:8888:9991
                      # prefix: 48
                    # - id: 30
                      # ip: 10.30.30.1
                      # mask: 255.255.255.0
                      # ipv6: 3333:4444:5555:6666:7777:8888:9999:1111
                      # prefix: 48
                - vrf_name: VRF_OVERLAY_2020
                  vrf_core_vlan_id: 2020
                  vrf_l3_vni_id: 20020
                  vrf_edge_vlan:
                    - id: 40
                      ip: 10.40.40.1
                      mask: 255.255.255.0
                      ipv6: 4444:5555:6666:7777:8888:9999:1111:2221
                      prefix: 48
                    - id: 50
                      ip: 10.50.50.1
                      mask: 255.255.255.0
                      ipv6: 5555:6666:7777:8888:9999:1111:2222:1111
                      prefix: 48
        #
            sw-access-leaf-2:
              ansible_host: 10.1.3.7
              role: border
              rid: 10.255.255.252
              vrfs:
                # - vrf_name: VRF_OVERLAY_2010
                  # vrf_core_vlan_id: 2010
                  # vrf_l3_vni_id: 20010
                  # vrf_edge_vlan:
                    # - id: 10
                      # ip: 10.10.10.2
                      # mask: 255.255.255.0
                      # ipv6: 1111:2222:3333:4444:5555:6666:7777:8882
                      # prefix: 48
                    # - id: 20
                      # ip: 10.20.20.2
                      # mask: 255.255.255.0
                      # ipv6: 2222:3333:4444:5555:6666:7777:8888:9992
                      # prefix: 48
                    # - id: 30
                      # ip: 10.30.30.2
                      # mask: 255.255.255.0
                      # ipv6: 3333:4444:5555:6666:7777:8888:9999:1112
                      # prefix: 48
                - vrf_name: VRF_OVERLAY_2020
                  vrf_core_vlan_id: 2020
                  vrf_l3_vni_id: 20020
                  vrf_edge_vlan:
                    - id: 40
                      ip: 10.40.40.2
                      mask: 255.255.255.0
                      ipv6: 4444:5555:6666:7777:8888:9999:1111:2222
                      prefix: 48
                    - id: 50
                      ip: 10.50.50.2
                      mask: 255.255.255.0
                      ipv6: 5555:6666:7777:8888:9999:1111:2222:1112
                      prefix: 48
        #
        ```
          * Note the indentation for YAML (two spaces).
  * The playbook execution should finish without any errors or failed tasks, if a single task fails the execution will be aborted.
  * **Playbook execution example**:
    * Succesfull execution (note 0 task failed):
      ```SHELL
      PLAY RECAP *******************************************************************************************
      sw-access-leaf-1           : ok=53   changed=16   unreachable=0    failed=0    skipped=6    rescued=0    ignored=0
      sw-access-leaf-2           : ok=59   changed=19   unreachable=0    failed=0    skipped=5    rescued=0    ignored=0
      sw-core-spine-1            : ok=22   changed=3    unreachable=0    failed=0    skipped=10   rescued=0    ignored=0
      ```
    * Failed execution (note 1 task failed on sw-access-leaf-1 and sw-access-leaf-2):
      ```SHELL
      PLAY RECAP *******************************************************************************************
      sw-access-leaf-1           : ok=10   changed=1    unreachable=0    failed=1    skipped=3    rescued=0    ignored=0
      sw-access-leaf-2           : ok=10   changed=1    unreachable=0    failed=1    skipped=3    rescued=0    ignored=0
      sw-core-spine-1            : ok=9    changed=1    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0
      ```
#### Add a new EDGE VLAN to an existing VRF:
  * After executing the new config or a green field deployment, if there's a need to add a new or more EDGE VLAN(s) into an existing VRF, execute the next steps.
  * The main playbook to run all the configuration on this use case scenario is called _playbook.yml_.
  * **To execute this playbook the following commands must be run**:
    ```SHELL
    ansible-playbook playbook.yml --tags "add_edge_vlan"
    ```
      * Note the execution of "ansible-playbook" without the "-i" option, this option usually calls the inventory file, but this file is already defined in the _ansible.cfg_ file, there's no need to call it from the playbook execution.
  * Before executing the playbook, the _inventory.yml_ needs to be modified, the existing VRF, existing CORE VLAN and existing L3VNI remain in place in the _inventory.yml_ file, the original EDGE VLAN(s) needs to be deleted or commented out (#) from the _inventory.yml_ file and the new EDGE VLAN(s) data needs to be added to the the same _inventory.yml_ file, this way the network can be modified.
      * **Example**: VRF_OVERLAY_2010 already exists, it was added in the first run or new deployment, the existing EDGE VLAN(s) of this VRF are commented out with "#", the new EDGE VLAN(s) data is added to the _inventory.yml_ file, in this case VLAN#99, this new EDGE VLAN is added to the _leafs_ nodes with the respective ID, IP and MASK.
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
              vrfs:
                - vrf_name: VRF_OVERLAY_2010
                  vrf_core_vlan_id: 2010
                  vrf_l3_vni_id: 20010
                  vrf_edge_vlan:
                    # - id: 10
                      # ip: 10.10.10.1
                      # mask: 255.255.255.0
                      # ipv6: 1111:2222:3333:4444:5555:6666:7777:8881
                      # prefix: 48
                    # - id: 20
                      # ip: 10.20.20.1
                      # mask: 255.255.255.0
                      # ipv6: 2222:3333:4444:5555:6666:7777:8888:9991
                      # prefix: 48
                    # - id: 30
                      # ip: 10.30.30.1
                      # mask: 255.255.255.0
                      # ipv6: 3333:4444:5555:6666:7777:8888:9999:1111
                      # prefix: 48
                    - id: 99
                      ip: 10.99.99.1
                      mask: 255.255.255.0
                      ipv6: 4444:5555:6666:7777:8888:9999:1111:2221
                      prefix: 48
        #
            sw-access-leaf-2:
              ansible_host: 10.1.3.7
              role: border
              rid: 10.255.255.252
              vrfs:
                - vrf_name: VRF_OVERLAY_2010
                  vrf_core_vlan_id: 2010
                  vrf_l3_vni_id: 20010
                  vrf_edge_vlan:
                    # - id: 10
                      # ip: 10.10.10.2
                      # mask: 255.255.255.0
                      # ipv6: 1111:2222:3333:4444:5555:6666:7777:8882
                      # prefix: 48
                    # - id: 20
                      # ip: 10.20.20.2
                      # mask: 255.255.255.0
                      # ipv6: 2222:3333:4444:5555:6666:7777:8888:9992
                      # prefix: 48
                    # - id: 30
                      # ip: 10.30.30.2
                      # mask: 255.255.255.0
                      # ipv6: 3333:4444:5555:6666:7777:8888:9999:1112
                      # prefix: 48
                    - id: 99
                      ip: 10.99.99.2
                      mask: 255.255.255.0
                      ipv6: 4444:5555:6666:7777:8888:9999:1111:2222
                      prefix: 48
        #
        ```
          * Note the indentation for YAML (two spaces).
  * The playbook execution should finish without any errors or failed tasks, if a single task fails the execution will be aborted.
  * **Playbook execution example**:
    * Succesfull execution (note 0 task failed):
      ```SHELL
      PLAY RECAP *******************************************************************************************
      sw-access-leaf-1           : ok=53   changed=16   unreachable=0    failed=0    skipped=6    rescued=0    ignored=0
      sw-access-leaf-2           : ok=59   changed=19   unreachable=0    failed=0    skipped=5    rescued=0    ignored=0
      sw-core-spine-1            : ok=22   changed=3    unreachable=0    failed=0    skipped=10   rescued=0    ignored=0
      ```
    * Failed execution (note 1 task failed on sw-access-leaf-1 and sw-access-leaf-2):
      ```SHELL
      PLAY RECAP *******************************************************************************************
      sw-access-leaf-1           : ok=10   changed=1    unreachable=0    failed=1    skipped=3    rescued=0    ignored=0
      sw-access-leaf-2           : ok=10   changed=1    unreachable=0    failed=1    skipped=3    rescued=0    ignored=0
      sw-core-spine-1            : ok=9    changed=1    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0


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
      PLAY RECAP ****************************************************************************************************
      sw-access-leaf-1           : ok=26   changed=20   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
      sw-access-leaf-2           : ok=26   changed=20   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
      sw-core-spine-1            : ok=6    changed=5    unreachable=0    failed=0    skipped=6    rescued=0    ignored=0
      ```
    * Failed execution (note 1 task failed on sw-access-leaf-1):
      ```SHELL
      PLAY RECAP ****************************************************************************************************
      sw-access-leaf-1           : ok=25   changed=20   unreachable=0    failed=1    skipped=0    rescued=0    ignored=0
      sw-access-leaf-2           : ok=26   changed=20   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
      sw-core-spine-1            : ok=6    changed=5    unreachable=0    failed=0    skipped=6    rescued=0    ignored=0
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

    PLAY [network] ***************************************************************************************************************************************************************************

    TASK [Gathering Facts] *******************************************************************************************************************************************************************
    [WARNING]: Ignoring timeout(10) for cisco.ios.ios_facts
    [WARNING]: Ignoring timeout(10) for cisco.ios.ios_facts
    [WARNING]: Ignoring timeout(10) for cisco.ios.ios_facts
    ok: [sw-core-spine-1]
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [layer_3_loop : SHOW LICENSE SUMMARY] ***********************************************************************************************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-core-spine-1]
    ok: [sw-access-leaf-1]

    TASK [layer_3_loop : ASSERT SUCCESS OR FAILURE BASED ON LICENSE TYPE] ********************************************************************************************************************
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

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
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

    TASK [layer_3_loop : PING LEAFS TO SPINES] ***********************************************************************************************************************************************
    skipping: [sw-core-spine-1] => (item=sw-core-spine-1)
    ok: [sw-access-leaf-2] => (item=sw-core-spine-1)
    ok: [sw-access-leaf-1] => (item=sw-core-spine-1)

    TASK [layer_3_loop : SHOW PING DETAILS] **************************************************************************************************************************************************
    skipping: [sw-core-spine-1] => (item=sw-core-spine-1)
    skipping: [sw-core-spine-1]
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

    TASK [layer_3_loop : ASSERT SUCCESS OR FAILURE PING LEAFS TO SPINES] *********************************************************************************************************************
    skipping: [sw-core-spine-1] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': 'sw-core-spine-1', 'ansible_loop_var': 'item'})
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

    TASK [layer_3_loop : PING SPINES TO LEAFS] ***********************************************************************************************************************************************
    skipping: [sw-access-leaf-1] => (item=sw-access-leaf-1)
    skipping: [sw-access-leaf-1] => (item=sw-access-leaf-2)
    skipping: [sw-access-leaf-2] => (item=sw-access-leaf-1)
    skipping: [sw-access-leaf-2] => (item=sw-access-leaf-2)
    ok: [sw-core-spine-1] => (item=sw-access-leaf-1)
    ok: [sw-core-spine-1] => (item=sw-access-leaf-2)

    TASK [layer_3_loop : SHOW PING DETAILS] **************************************************************************************************************************************************
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

    TASK [layer_3_loop : ASSERT SUCCESS OR FAILURE PING SPINES TO LEAFS] *********************************************************************************************************************
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
    ok: [sw-core-spine-1] => (item={'commands': ['ping 10.255.255.252 repeat 1000 source 10.255.255.254'], 'packet_loss': '0%', 'packets_rx': 1000, 'packets_tx': 1000, 'rtt': {'min': 1, 'avg': 1, 'max': 2}, 'invocation': {'module_args': {'dest': '10.255.255.252', 'source': '10.255.255.254', 'count': 1000, 'state': 'present', 'df_bit': False, 'size': None, 'vrf': None, 'provider': None}}, 'failed': False, 'changed': False, 'item': 'sw-access-leaf-2', 'ansible_loop_var': 'item'}) => {
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

    TASK [layer_3_loop : BACKUP CONFIG] ******************************************************************************************************************************************************
    changed: [sw-core-spine-1]
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
    ok: [sw-access-leaf-1] => {
        "msg": [
            {
                "backup_path": "./backup/sw-access-leaf-1_config.2022-04-17@22:22:25",
                "changed": true,
                "date": "2022-04-17",
                "failed": false,
                "filename": "sw-access-leaf-1_config.2022-04-17@22:22:25",
                "shortname": "./backup/sw-access-leaf-1_config",
                "time": "22:22:25"
            }
        ]
    }
    ok: [sw-access-leaf-2] => {
        "msg": [
            {
                "backup_path": "./backup/sw-access-leaf-2_config.2022-04-17@22:22:25",
                "changed": true,
                "date": "2022-04-17",
                "failed": false,
                "filename": "sw-access-leaf-2_config.2022-04-17@22:22:25",
                "shortname": "./backup/sw-access-leaf-2_config",
                "time": "22:22:25"
            }
        ]
    }
    ok: [sw-core-spine-1] => {
        "msg": [
            {
                "backup_path": "./backup/sw-core-spine-1_config.2022-04-17@22:22:25",
                "changed": true,
                "date": "2022-04-17",
                "failed": false,
                "filename": "sw-core-spine-1_config.2022-04-17@22:22:25",
                "shortname": "./backup/sw-core-spine-1_config",
                "time": "22:22:25"
            }
        ]
    }

    TASK [layer_3_loop : include_tasks] ******************************************************************************************************************************************************
    skipping: [sw-core-spine-1]
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/layer_3_loop/tasks/use_case_2/ipv6_global_config.yml for sw-access-leaf-1, sw-access-leaf-2

    TASK [layer_3_loop : CONFIG IPV6 ROUTING SUPPORT] ****************************************************************************************************************************************
    [WARNING]: To ensure idempotency and correct diff the input configuration lines should be similar to how they appear if present in the running configuration on device
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [layer_3_loop : SHOW CONFIG] ********************************************************************************************************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
    ok: [sw-access-leaf-1] => {
        "msg": [
            [
                [
                    "ipv6 unicast-routing"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => {
        "msg": [
            [
                [
                    "ipv6 unicast-routing"
                ]
            ]
        ]
    }

    TASK [layer_3_loop : include_tasks] ******************************************************************************************************************************************************
    skipping: [sw-core-spine-1]
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/layer_3_loop/tasks/use_case_2/leaf_core_vrf_ipv4_ipv6.yml for sw-access-leaf-1, sw-access-leaf-2

    TASK [layer_3_loop : CONFIG VRF IPV4] ****************************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}]})

    TASK [layer_3_loop : CONFIG VRF IPV6] ****************************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}]})

    TASK [layer_3_loop : SHOW CONFIG] ********************************************************************************************************************************************************
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]})
    ok: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}]})
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]})
    ok: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}]})
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]})
    ok: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}]})
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]})
    ok: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}]})
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]})
    ok: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}]})

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
    ok: [sw-access-leaf-2] => (item={'changed': False, 'stdout': ['vrf definition VRF_OVERLAY_2010\n rd 10.255.255.252:2010\n !\n address-family ipv4\n  route-target export 64512:2010\n  route-target import 64512:2010\n  route-target export 64512:2010 stitching\n  route-target import 64512:2010 stitching\n exit-address-family\n !\n address-family ipv6\n  route-target export 64512:2010\n  route-target import 64512:2010\n  route-target export 64512:2010 stitching\n  route-target import 64512:2010 stitching\n exit-address-family', 'Name                             Default RD            Protocols   Interfaces\n  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0\n  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4,ipv6   \n  VRF_OVERLAY_2020                 10.255.255.252:2020   ipv4,ipv6   \n  VRF_OVERLAY_2030                 10.255.255.252:2030   ipv4,ipv6   \n  VRF_OVERLAY_2040                 10.255.255.252:2040   ipv4,ipv6   \n  VRF_OVERLAY_2050                 10.255.255.252:2050   ipv4,ipv6', 'Name                             Default RD            Protocols   Interfaces\n  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4,ipv6'], 'stdout_lines': [['vrf definition VRF_OVERLAY_2010', ' rd 10.255.255.252:2010', ' !', ' address-family ipv4', '  route-target export 64512:2010', '  route-target import 64512:2010', '  route-target export 64512:2010 stitching', '  route-target import 64512:2010 stitching', ' exit-address-family', ' !', ' address-family ipv6', '  route-target export 64512:2010', '  route-target import 64512:2010', '  route-target export 64512:2010 stitching', '  route-target import 64512:2010 stitching', ' exit-address-family'], ['Name                             Default RD            Protocols   Interfaces', '  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0', '  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4,ipv6   ', '  VRF_OVERLAY_2020                 10.255.255.252:2020   ipv4,ipv6   ', '  VRF_OVERLAY_2030                 10.255.255.252:2030   ipv4,ipv6   ', '  VRF_OVERLAY_2040                 10.255.255.252:2040   ipv4,ipv6   ', '  VRF_OVERLAY_2050                 10.255.255.252:2050   ipv4,ipv6'], ['Name                             Default RD            Protocols   Interfaces', '  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4,ipv6']], 'invocation': {'module_args': {'commands': ['show running-config | section vrf definition VRF_OVERLAY_2010', 'show vrf', 'show vrf VRF_OVERLAY_2010'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vrf definition VRF_OVERLAY_2010",
                    " rd 10.255.255.252:2010",
                    " !",
                    " address-family ipv4",
                    "  route-target export 64512:2010",
                    "  route-target import 64512:2010",
                    "  route-target export 64512:2010 stitching",
                    "  route-target import 64512:2010 stitching",
                    " exit-address-family",
                    " !",
                    " address-family ipv6",
                    "  route-target export 64512:2010",
                    "  route-target import 64512:2010",
                    "  route-target export 64512:2010 stitching",
                    "  route-target import 64512:2010 stitching",
                    " exit-address-family"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0",
                    "  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2020                 10.255.255.252:2020   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2030                 10.255.255.252:2030   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2040                 10.255.255.252:2040   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2050                 10.255.255.252:2050   ipv4,ipv6"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4,ipv6"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-1] => (item={'changed': False, 'stdout': ['vrf definition VRF_OVERLAY_2010\n rd 10.255.255.253:2010\n !\n address-family ipv4\n  route-target export 64512:2010\n  route-target import 64512:2010\n  route-target export 64512:2010 stitching\n  route-target import 64512:2010 stitching\n exit-address-family\n !\n address-family ipv6\n  route-target export 64512:2010\n  route-target import 64512:2010\n  route-target export 64512:2010 stitching\n  route-target import 64512:2010 stitching\n exit-address-family', 'Name                             Default RD            Protocols   Interfaces\n  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0\n  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4,ipv6   \n  VRF_OVERLAY_2020                 10.255.255.253:2020   ipv4,ipv6   \n  VRF_OVERLAY_2030                 10.255.255.253:2030   ipv4,ipv6   \n  VRF_OVERLAY_2040                 10.255.255.253:2040   ipv4,ipv6   \n  VRF_OVERLAY_2050                 10.255.255.253:2050   ipv4,ipv6', 'Name                             Default RD            Protocols   Interfaces\n  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4,ipv6'], 'stdout_lines': [['vrf definition VRF_OVERLAY_2010', ' rd 10.255.255.253:2010', ' !', ' address-family ipv4', '  route-target export 64512:2010', '  route-target import 64512:2010', '  route-target export 64512:2010 stitching', '  route-target import 64512:2010 stitching', ' exit-address-family', ' !', ' address-family ipv6', '  route-target export 64512:2010', '  route-target import 64512:2010', '  route-target export 64512:2010 stitching', '  route-target import 64512:2010 stitching', ' exit-address-family'], ['Name                             Default RD            Protocols   Interfaces', '  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0', '  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4,ipv6   ', '  VRF_OVERLAY_2020                 10.255.255.253:2020   ipv4,ipv6   ', '  VRF_OVERLAY_2030                 10.255.255.253:2030   ipv4,ipv6   ', '  VRF_OVERLAY_2040                 10.255.255.253:2040   ipv4,ipv6   ', '  VRF_OVERLAY_2050                 10.255.255.253:2050   ipv4,ipv6'], ['Name                             Default RD            Protocols   Interfaces', '  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4,ipv6']], 'invocation': {'module_args': {'commands': ['show running-config | section vrf definition VRF_OVERLAY_2010', 'show vrf', 'show vrf VRF_OVERLAY_2010'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vrf definition VRF_OVERLAY_2010",
                    " rd 10.255.255.253:2010",
                    " !",
                    " address-family ipv4",
                    "  route-target export 64512:2010",
                    "  route-target import 64512:2010",
                    "  route-target export 64512:2010 stitching",
                    "  route-target import 64512:2010 stitching",
                    " exit-address-family",
                    " !",
                    " address-family ipv6",
                    "  route-target export 64512:2010",
                    "  route-target import 64512:2010",
                    "  route-target export 64512:2010 stitching",
                    "  route-target import 64512:2010 stitching",
                    " exit-address-family"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0",
                    "  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2020                 10.255.255.253:2020   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2030                 10.255.255.253:2030   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2040                 10.255.255.253:2040   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2050                 10.255.255.253:2050   ipv4,ipv6"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4,ipv6"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => (item={'changed': False, 'stdout': ['vrf definition VRF_OVERLAY_2020\n rd 10.255.255.252:2020\n !\n address-family ipv4\n  route-target export 64512:2020\n  route-target import 64512:2020\n  route-target export 64512:2020 stitching\n  route-target import 64512:2020 stitching\n exit-address-family\n !\n address-family ipv6\n  route-target export 64512:2020\n  route-target import 64512:2020\n  route-target export 64512:2020 stitching\n  route-target import 64512:2020 stitching\n exit-address-family', 'Name                             Default RD            Protocols   Interfaces\n  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0\n  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4,ipv6   \n  VRF_OVERLAY_2020                 10.255.255.252:2020   ipv4,ipv6   \n  VRF_OVERLAY_2030                 10.255.255.252:2030   ipv4,ipv6   \n  VRF_OVERLAY_2040                 10.255.255.252:2040   ipv4,ipv6   \n  VRF_OVERLAY_2050                 10.255.255.252:2050   ipv4,ipv6', 'Name                             Default RD            Protocols   Interfaces\n  VRF_OVERLAY_2020                 10.255.255.252:2020   ipv4,ipv6'], 'stdout_lines': [['vrf definition VRF_OVERLAY_2020', ' rd 10.255.255.252:2020', ' !', ' address-family ipv4', '  route-target export 64512:2020', '  route-target import 64512:2020', '  route-target export 64512:2020 stitching', '  route-target import 64512:2020 stitching', ' exit-address-family', ' !', ' address-family ipv6', '  route-target export 64512:2020', '  route-target import 64512:2020', '  route-target export 64512:2020 stitching', '  route-target import 64512:2020 stitching', ' exit-address-family'], ['Name                             Default RD            Protocols   Interfaces', '  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0', '  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4,ipv6   ', '  VRF_OVERLAY_2020                 10.255.255.252:2020   ipv4,ipv6   ', '  VRF_OVERLAY_2030                 10.255.255.252:2030   ipv4,ipv6   ', '  VRF_OVERLAY_2040                 10.255.255.252:2040   ipv4,ipv6   ', '  VRF_OVERLAY_2050                 10.255.255.252:2050   ipv4,ipv6'], ['Name                             Default RD            Protocols   Interfaces', '  VRF_OVERLAY_2020                 10.255.255.252:2020   ipv4,ipv6']], 'invocation': {'module_args': {'commands': ['show running-config | section vrf definition VRF_OVERLAY_2020', 'show vrf', 'show vrf VRF_OVERLAY_2020'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vrf definition VRF_OVERLAY_2020",
                    " rd 10.255.255.252:2020",
                    " !",
                    " address-family ipv4",
                    "  route-target export 64512:2020",
                    "  route-target import 64512:2020",
                    "  route-target export 64512:2020 stitching",
                    "  route-target import 64512:2020 stitching",
                    " exit-address-family",
                    " !",
                    " address-family ipv6",
                    "  route-target export 64512:2020",
                    "  route-target import 64512:2020",
                    "  route-target export 64512:2020 stitching",
                    "  route-target import 64512:2020 stitching",
                    " exit-address-family"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0",
                    "  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2020                 10.255.255.252:2020   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2030                 10.255.255.252:2030   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2040                 10.255.255.252:2040   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2050                 10.255.255.252:2050   ipv4,ipv6"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  VRF_OVERLAY_2020                 10.255.255.252:2020   ipv4,ipv6"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-1] => (item={'changed': False, 'stdout': ['vrf definition VRF_OVERLAY_2020\n rd 10.255.255.253:2020\n !\n address-family ipv4\n  route-target export 64512:2020\n  route-target import 64512:2020\n  route-target export 64512:2020 stitching\n  route-target import 64512:2020 stitching\n exit-address-family\n !\n address-family ipv6\n  route-target export 64512:2020\n  route-target import 64512:2020\n  route-target export 64512:2020 stitching\n  route-target import 64512:2020 stitching\n exit-address-family', 'Name                             Default RD            Protocols   Interfaces\n  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0\n  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4,ipv6   \n  VRF_OVERLAY_2020                 10.255.255.253:2020   ipv4,ipv6   \n  VRF_OVERLAY_2030                 10.255.255.253:2030   ipv4,ipv6   \n  VRF_OVERLAY_2040                 10.255.255.253:2040   ipv4,ipv6   \n  VRF_OVERLAY_2050                 10.255.255.253:2050   ipv4,ipv6', 'Name                             Default RD            Protocols   Interfaces\n  VRF_OVERLAY_2020                 10.255.255.253:2020   ipv4,ipv6'], 'stdout_lines': [['vrf definition VRF_OVERLAY_2020', ' rd 10.255.255.253:2020', ' !', ' address-family ipv4', '  route-target export 64512:2020', '  route-target import 64512:2020', '  route-target export 64512:2020 stitching', '  route-target import 64512:2020 stitching', ' exit-address-family', ' !', ' address-family ipv6', '  route-target export 64512:2020', '  route-target import 64512:2020', '  route-target export 64512:2020 stitching', '  route-target import 64512:2020 stitching', ' exit-address-family'], ['Name                             Default RD            Protocols   Interfaces', '  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0', '  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4,ipv6   ', '  VRF_OVERLAY_2020                 10.255.255.253:2020   ipv4,ipv6   ', '  VRF_OVERLAY_2030                 10.255.255.253:2030   ipv4,ipv6   ', '  VRF_OVERLAY_2040                 10.255.255.253:2040   ipv4,ipv6   ', '  VRF_OVERLAY_2050                 10.255.255.253:2050   ipv4,ipv6'], ['Name                             Default RD            Protocols   Interfaces', '  VRF_OVERLAY_2020                 10.255.255.253:2020   ipv4,ipv6']], 'invocation': {'module_args': {'commands': ['show running-config | section vrf definition VRF_OVERLAY_2020', 'show vrf', 'show vrf VRF_OVERLAY_2020'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vrf definition VRF_OVERLAY_2020",
                    " rd 10.255.255.253:2020",
                    " !",
                    " address-family ipv4",
                    "  route-target export 64512:2020",
                    "  route-target import 64512:2020",
                    "  route-target export 64512:2020 stitching",
                    "  route-target import 64512:2020 stitching",
                    " exit-address-family",
                    " !",
                    " address-family ipv6",
                    "  route-target export 64512:2020",
                    "  route-target import 64512:2020",
                    "  route-target export 64512:2020 stitching",
                    "  route-target import 64512:2020 stitching",
                    " exit-address-family"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0",
                    "  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2020                 10.255.255.253:2020   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2030                 10.255.255.253:2030   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2040                 10.255.255.253:2040   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2050                 10.255.255.253:2050   ipv4,ipv6"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  VRF_OVERLAY_2020                 10.255.255.253:2020   ipv4,ipv6"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-1] => (item={'changed': False, 'stdout': ['vrf definition VRF_OVERLAY_2030\n rd 10.255.255.253:2030\n !\n address-family ipv4\n  route-target export 64512:2030\n  route-target import 64512:2030\n  route-target export 64512:2030 stitching\n  route-target import 64512:2030 stitching\n exit-address-family\n !\n address-family ipv6\n  route-target export 64512:2030\n  route-target import 64512:2030\n  route-target export 64512:2030 stitching\n  route-target import 64512:2030 stitching\n exit-address-family', 'Name                             Default RD            Protocols   Interfaces\n  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0\n  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4,ipv6   \n  VRF_OVERLAY_2020                 10.255.255.253:2020   ipv4,ipv6   \n  VRF_OVERLAY_2030                 10.255.255.253:2030   ipv4,ipv6   \n  VRF_OVERLAY_2040                 10.255.255.253:2040   ipv4,ipv6   \n  VRF_OVERLAY_2050                 10.255.255.253:2050   ipv4,ipv6', 'Name                             Default RD            Protocols   Interfaces\n  VRF_OVERLAY_2030                 10.255.255.253:2030   ipv4,ipv6'], 'stdout_lines': [['vrf definition VRF_OVERLAY_2030', ' rd 10.255.255.253:2030', ' !', ' address-family ipv4', '  route-target export 64512:2030', '  route-target import 64512:2030', '  route-target export 64512:2030 stitching', '  route-target import 64512:2030 stitching', ' exit-address-family', ' !', ' address-family ipv6', '  route-target export 64512:2030', '  route-target import 64512:2030', '  route-target export 64512:2030 stitching', '  route-target import 64512:2030 stitching', ' exit-address-family'], ['Name                             Default RD            Protocols   Interfaces', '  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0', '  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4,ipv6   ', '  VRF_OVERLAY_2020                 10.255.255.253:2020   ipv4,ipv6   ', '  VRF_OVERLAY_2030                 10.255.255.253:2030   ipv4,ipv6   ', '  VRF_OVERLAY_2040                 10.255.255.253:2040   ipv4,ipv6   ', '  VRF_OVERLAY_2050                 10.255.255.253:2050   ipv4,ipv6'], ['Name                             Default RD            Protocols   Interfaces', '  VRF_OVERLAY_2030                 10.255.255.253:2030   ipv4,ipv6']], 'invocation': {'module_args': {'commands': ['show running-config | section vrf definition VRF_OVERLAY_2030', 'show vrf', 'show vrf VRF_OVERLAY_2030'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vrf definition VRF_OVERLAY_2030",
                    " rd 10.255.255.253:2030",
                    " !",
                    " address-family ipv4",
                    "  route-target export 64512:2030",
                    "  route-target import 64512:2030",
                    "  route-target export 64512:2030 stitching",
                    "  route-target import 64512:2030 stitching",
                    " exit-address-family",
                    " !",
                    " address-family ipv6",
                    "  route-target export 64512:2030",
                    "  route-target import 64512:2030",
                    "  route-target export 64512:2030 stitching",
                    "  route-target import 64512:2030 stitching",
                    " exit-address-family"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0",
                    "  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2020                 10.255.255.253:2020   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2030                 10.255.255.253:2030   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2040                 10.255.255.253:2040   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2050                 10.255.255.253:2050   ipv4,ipv6"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  VRF_OVERLAY_2030                 10.255.255.253:2030   ipv4,ipv6"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => (item={'changed': False, 'stdout': ['vrf definition VRF_OVERLAY_2030\n rd 10.255.255.252:2030\n !\n address-family ipv4\n  route-target export 64512:2030\n  route-target import 64512:2030\n  route-target export 64512:2030 stitching\n  route-target import 64512:2030 stitching\n exit-address-family\n !\n address-family ipv6\n  route-target export 64512:2030\n  route-target import 64512:2030\n  route-target export 64512:2030 stitching\n  route-target import 64512:2030 stitching\n exit-address-family', 'Name                             Default RD            Protocols   Interfaces\n  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0\n  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4,ipv6   \n  VRF_OVERLAY_2020                 10.255.255.252:2020   ipv4,ipv6   \n  VRF_OVERLAY_2030                 10.255.255.252:2030   ipv4,ipv6   \n  VRF_OVERLAY_2040                 10.255.255.252:2040   ipv4,ipv6   \n  VRF_OVERLAY_2050                 10.255.255.252:2050   ipv4,ipv6', 'Name                             Default RD            Protocols   Interfaces\n  VRF_OVERLAY_2030                 10.255.255.252:2030   ipv4,ipv6'], 'stdout_lines': [['vrf definition VRF_OVERLAY_2030', ' rd 10.255.255.252:2030', ' !', ' address-family ipv4', '  route-target export 64512:2030', '  route-target import 64512:2030', '  route-target export 64512:2030 stitching', '  route-target import 64512:2030 stitching', ' exit-address-family', ' !', ' address-family ipv6', '  route-target export 64512:2030', '  route-target import 64512:2030', '  route-target export 64512:2030 stitching', '  route-target import 64512:2030 stitching', ' exit-address-family'], ['Name                             Default RD            Protocols   Interfaces', '  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0', '  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4,ipv6   ', '  VRF_OVERLAY_2020                 10.255.255.252:2020   ipv4,ipv6   ', '  VRF_OVERLAY_2030                 10.255.255.252:2030   ipv4,ipv6   ', '  VRF_OVERLAY_2040                 10.255.255.252:2040   ipv4,ipv6   ', '  VRF_OVERLAY_2050                 10.255.255.252:2050   ipv4,ipv6'], ['Name                             Default RD            Protocols   Interfaces', '  VRF_OVERLAY_2030                 10.255.255.252:2030   ipv4,ipv6']], 'invocation': {'module_args': {'commands': ['show running-config | section vrf definition VRF_OVERLAY_2030', 'show vrf', 'show vrf VRF_OVERLAY_2030'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vrf definition VRF_OVERLAY_2030",
                    " rd 10.255.255.252:2030",
                    " !",
                    " address-family ipv4",
                    "  route-target export 64512:2030",
                    "  route-target import 64512:2030",
                    "  route-target export 64512:2030 stitching",
                    "  route-target import 64512:2030 stitching",
                    " exit-address-family",
                    " !",
                    " address-family ipv6",
                    "  route-target export 64512:2030",
                    "  route-target import 64512:2030",
                    "  route-target export 64512:2030 stitching",
                    "  route-target import 64512:2030 stitching",
                    " exit-address-family"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0",
                    "  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2020                 10.255.255.252:2020   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2030                 10.255.255.252:2030   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2040                 10.255.255.252:2040   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2050                 10.255.255.252:2050   ipv4,ipv6"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  VRF_OVERLAY_2030                 10.255.255.252:2030   ipv4,ipv6"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-1] => (item={'changed': False, 'stdout': ['vrf definition VRF_OVERLAY_2040\n rd 10.255.255.253:2040\n !\n address-family ipv4\n  route-target export 64512:2040\n  route-target import 64512:2040\n  route-target export 64512:2040 stitching\n  route-target import 64512:2040 stitching\n exit-address-family\n !\n address-family ipv6\n  route-target export 64512:2040\n  route-target import 64512:2040\n  route-target export 64512:2040 stitching\n  route-target import 64512:2040 stitching\n exit-address-family', 'Name                             Default RD            Protocols   Interfaces\n  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0\n  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4,ipv6   \n  VRF_OVERLAY_2020                 10.255.255.253:2020   ipv4,ipv6   \n  VRF_OVERLAY_2030                 10.255.255.253:2030   ipv4,ipv6   \n  VRF_OVERLAY_2040                 10.255.255.253:2040   ipv4,ipv6   \n  VRF_OVERLAY_2050                 10.255.255.253:2050   ipv4,ipv6', 'Name                             Default RD            Protocols   Interfaces\n  VRF_OVERLAY_2040                 10.255.255.253:2040   ipv4,ipv6'], 'stdout_lines': [['vrf definition VRF_OVERLAY_2040', ' rd 10.255.255.253:2040', ' !', ' address-family ipv4', '  route-target export 64512:2040', '  route-target import 64512:2040', '  route-target export 64512:2040 stitching', '  route-target import 64512:2040 stitching', ' exit-address-family', ' !', ' address-family ipv6', '  route-target export 64512:2040', '  route-target import 64512:2040', '  route-target export 64512:2040 stitching', '  route-target import 64512:2040 stitching', ' exit-address-family'], ['Name                             Default RD            Protocols   Interfaces', '  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0', '  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4,ipv6   ', '  VRF_OVERLAY_2020                 10.255.255.253:2020   ipv4,ipv6   ', '  VRF_OVERLAY_2030                 10.255.255.253:2030   ipv4,ipv6   ', '  VRF_OVERLAY_2040                 10.255.255.253:2040   ipv4,ipv6   ', '  VRF_OVERLAY_2050                 10.255.255.253:2050   ipv4,ipv6'], ['Name                             Default RD            Protocols   Interfaces', '  VRF_OVERLAY_2040                 10.255.255.253:2040   ipv4,ipv6']], 'invocation': {'module_args': {'commands': ['show running-config | section vrf definition VRF_OVERLAY_2040', 'show vrf', 'show vrf VRF_OVERLAY_2040'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vrf definition VRF_OVERLAY_2040",
                    " rd 10.255.255.253:2040",
                    " !",
                    " address-family ipv4",
                    "  route-target export 64512:2040",
                    "  route-target import 64512:2040",
                    "  route-target export 64512:2040 stitching",
                    "  route-target import 64512:2040 stitching",
                    " exit-address-family",
                    " !",
                    " address-family ipv6",
                    "  route-target export 64512:2040",
                    "  route-target import 64512:2040",
                    "  route-target export 64512:2040 stitching",
                    "  route-target import 64512:2040 stitching",
                    " exit-address-family"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0",
                    "  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2020                 10.255.255.253:2020   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2030                 10.255.255.253:2030   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2040                 10.255.255.253:2040   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2050                 10.255.255.253:2050   ipv4,ipv6"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  VRF_OVERLAY_2040                 10.255.255.253:2040   ipv4,ipv6"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => (item={'changed': False, 'stdout': ['vrf definition VRF_OVERLAY_2040\n rd 10.255.255.252:2040\n !\n address-family ipv4\n  route-target export 64512:2040\n  route-target import 64512:2040\n  route-target export 64512:2040 stitching\n  route-target import 64512:2040 stitching\n exit-address-family\n !\n address-family ipv6\n  route-target export 64512:2040\n  route-target import 64512:2040\n  route-target export 64512:2040 stitching\n  route-target import 64512:2040 stitching\n exit-address-family', 'Name                             Default RD            Protocols   Interfaces\n  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0\n  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4,ipv6   \n  VRF_OVERLAY_2020                 10.255.255.252:2020   ipv4,ipv6   \n  VRF_OVERLAY_2030                 10.255.255.252:2030   ipv4,ipv6   \n  VRF_OVERLAY_2040                 10.255.255.252:2040   ipv4,ipv6   \n  VRF_OVERLAY_2050                 10.255.255.252:2050   ipv4,ipv6', 'Name                             Default RD            Protocols   Interfaces\n  VRF_OVERLAY_2040                 10.255.255.252:2040   ipv4,ipv6'], 'stdout_lines': [['vrf definition VRF_OVERLAY_2040', ' rd 10.255.255.252:2040', ' !', ' address-family ipv4', '  route-target export 64512:2040', '  route-target import 64512:2040', '  route-target export 64512:2040 stitching', '  route-target import 64512:2040 stitching', ' exit-address-family', ' !', ' address-family ipv6', '  route-target export 64512:2040', '  route-target import 64512:2040', '  route-target export 64512:2040 stitching', '  route-target import 64512:2040 stitching', ' exit-address-family'], ['Name                             Default RD            Protocols   Interfaces', '  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0', '  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4,ipv6   ', '  VRF_OVERLAY_2020                 10.255.255.252:2020   ipv4,ipv6   ', '  VRF_OVERLAY_2030                 10.255.255.252:2030   ipv4,ipv6   ', '  VRF_OVERLAY_2040                 10.255.255.252:2040   ipv4,ipv6   ', '  VRF_OVERLAY_2050                 10.255.255.252:2050   ipv4,ipv6'], ['Name                             Default RD            Protocols   Interfaces', '  VRF_OVERLAY_2040                 10.255.255.252:2040   ipv4,ipv6']], 'invocation': {'module_args': {'commands': ['show running-config | section vrf definition VRF_OVERLAY_2040', 'show vrf', 'show vrf VRF_OVERLAY_2040'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vrf definition VRF_OVERLAY_2040",
                    " rd 10.255.255.252:2040",
                    " !",
                    " address-family ipv4",
                    "  route-target export 64512:2040",
                    "  route-target import 64512:2040",
                    "  route-target export 64512:2040 stitching",
                    "  route-target import 64512:2040 stitching",
                    " exit-address-family",
                    " !",
                    " address-family ipv6",
                    "  route-target export 64512:2040",
                    "  route-target import 64512:2040",
                    "  route-target export 64512:2040 stitching",
                    "  route-target import 64512:2040 stitching",
                    " exit-address-family"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0",
                    "  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2020                 10.255.255.252:2020   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2030                 10.255.255.252:2030   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2040                 10.255.255.252:2040   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2050                 10.255.255.252:2050   ipv4,ipv6"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  VRF_OVERLAY_2040                 10.255.255.252:2040   ipv4,ipv6"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-1] => (item={'changed': False, 'stdout': ['vrf definition VRF_OVERLAY_2050\n rd 10.255.255.253:2050\n !\n address-family ipv4\n  route-target export 64512:2050\n  route-target import 64512:2050\n  route-target export 64512:2050 stitching\n  route-target import 64512:2050 stitching\n exit-address-family\n !\n address-family ipv6\n  route-target export 64512:2050\n  route-target import 64512:2050\n  route-target export 64512:2050 stitching\n  route-target import 64512:2050 stitching\n exit-address-family', 'Name                             Default RD            Protocols   Interfaces\n  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0\n  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4,ipv6   \n  VRF_OVERLAY_2020                 10.255.255.253:2020   ipv4,ipv6   \n  VRF_OVERLAY_2030                 10.255.255.253:2030   ipv4,ipv6   \n  VRF_OVERLAY_2040                 10.255.255.253:2040   ipv4,ipv6   \n  VRF_OVERLAY_2050                 10.255.255.253:2050   ipv4,ipv6', 'Name                             Default RD            Protocols   Interfaces\n  VRF_OVERLAY_2050                 10.255.255.253:2050   ipv4,ipv6'], 'stdout_lines': [['vrf definition VRF_OVERLAY_2050', ' rd 10.255.255.253:2050', ' !', ' address-family ipv4', '  route-target export 64512:2050', '  route-target import 64512:2050', '  route-target export 64512:2050 stitching', '  route-target import 64512:2050 stitching', ' exit-address-family', ' !', ' address-family ipv6', '  route-target export 64512:2050', '  route-target import 64512:2050', '  route-target export 64512:2050 stitching', '  route-target import 64512:2050 stitching', ' exit-address-family'], ['Name                             Default RD            Protocols   Interfaces', '  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0', '  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4,ipv6   ', '  VRF_OVERLAY_2020                 10.255.255.253:2020   ipv4,ipv6   ', '  VRF_OVERLAY_2030                 10.255.255.253:2030   ipv4,ipv6   ', '  VRF_OVERLAY_2040                 10.255.255.253:2040   ipv4,ipv6   ', '  VRF_OVERLAY_2050                 10.255.255.253:2050   ipv4,ipv6'], ['Name                             Default RD            Protocols   Interfaces', '  VRF_OVERLAY_2050                 10.255.255.253:2050   ipv4,ipv6']], 'invocation': {'module_args': {'commands': ['show running-config | section vrf definition VRF_OVERLAY_2050', 'show vrf', 'show vrf VRF_OVERLAY_2050'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vrf definition VRF_OVERLAY_2050",
                    " rd 10.255.255.253:2050",
                    " !",
                    " address-family ipv4",
                    "  route-target export 64512:2050",
                    "  route-target import 64512:2050",
                    "  route-target export 64512:2050 stitching",
                    "  route-target import 64512:2050 stitching",
                    " exit-address-family",
                    " !",
                    " address-family ipv6",
                    "  route-target export 64512:2050",
                    "  route-target import 64512:2050",
                    "  route-target export 64512:2050 stitching",
                    "  route-target import 64512:2050 stitching",
                    " exit-address-family"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0",
                    "  VRF_OVERLAY_2010                 10.255.255.253:2010   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2020                 10.255.255.253:2020   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2030                 10.255.255.253:2030   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2040                 10.255.255.253:2040   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2050                 10.255.255.253:2050   ipv4,ipv6"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  VRF_OVERLAY_2050                 10.255.255.253:2050   ipv4,ipv6"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => (item={'changed': False, 'stdout': ['vrf definition VRF_OVERLAY_2050\n rd 10.255.255.252:2050\n !\n address-family ipv4\n  route-target export 64512:2050\n  route-target import 64512:2050\n  route-target export 64512:2050 stitching\n  route-target import 64512:2050 stitching\n exit-address-family\n !\n address-family ipv6\n  route-target export 64512:2050\n  route-target import 64512:2050\n  route-target export 64512:2050 stitching\n  route-target import 64512:2050 stitching\n exit-address-family', 'Name                             Default RD            Protocols   Interfaces\n  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0\n  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4,ipv6   \n  VRF_OVERLAY_2020                 10.255.255.252:2020   ipv4,ipv6   \n  VRF_OVERLAY_2030                 10.255.255.252:2030   ipv4,ipv6   \n  VRF_OVERLAY_2040                 10.255.255.252:2040   ipv4,ipv6   \n  VRF_OVERLAY_2050                 10.255.255.252:2050   ipv4,ipv6', 'Name                             Default RD            Protocols   Interfaces\n  VRF_OVERLAY_2050                 10.255.255.252:2050   ipv4,ipv6'], 'stdout_lines': [['vrf definition VRF_OVERLAY_2050', ' rd 10.255.255.252:2050', ' !', ' address-family ipv4', '  route-target export 64512:2050', '  route-target import 64512:2050', '  route-target export 64512:2050 stitching', '  route-target import 64512:2050 stitching', ' exit-address-family', ' !', ' address-family ipv6', '  route-target export 64512:2050', '  route-target import 64512:2050', '  route-target export 64512:2050 stitching', '  route-target import 64512:2050 stitching', ' exit-address-family'], ['Name                             Default RD            Protocols   Interfaces', '  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0', '  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4,ipv6   ', '  VRF_OVERLAY_2020                 10.255.255.252:2020   ipv4,ipv6   ', '  VRF_OVERLAY_2030                 10.255.255.252:2030   ipv4,ipv6   ', '  VRF_OVERLAY_2040                 10.255.255.252:2040   ipv4,ipv6   ', '  VRF_OVERLAY_2050                 10.255.255.252:2050   ipv4,ipv6'], ['Name                             Default RD            Protocols   Interfaces', '  VRF_OVERLAY_2050                 10.255.255.252:2050   ipv4,ipv6']], 'invocation': {'module_args': {'commands': ['show running-config | section vrf definition VRF_OVERLAY_2050', 'show vrf', 'show vrf VRF_OVERLAY_2050'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vrf definition VRF_OVERLAY_2050",
                    " rd 10.255.255.252:2050",
                    " !",
                    " address-family ipv4",
                    "  route-target export 64512:2050",
                    "  route-target import 64512:2050",
                    "  route-target export 64512:2050 stitching",
                    "  route-target import 64512:2050 stitching",
                    " exit-address-family",
                    " !",
                    " address-family ipv6",
                    "  route-target export 64512:2050",
                    "  route-target import 64512:2050",
                    "  route-target export 64512:2050 stitching",
                    "  route-target import 64512:2050 stitching",
                    " exit-address-family"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  Mgmt-vrf                         <not set>             ipv4,ipv6   Gi0/0",
                    "  VRF_OVERLAY_2010                 10.255.255.252:2010   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2020                 10.255.255.252:2020   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2030                 10.255.255.252:2030   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2040                 10.255.255.252:2040   ipv4,ipv6   ",
                    "  VRF_OVERLAY_2050                 10.255.255.252:2050   ipv4,ipv6"
                ],
                [
                    "Name                             Default RD            Protocols   Interfaces",
                    "  VRF_OVERLAY_2050                 10.255.255.252:2050   ipv4,ipv6"
                ]
            ]
        ]
    }

    TASK [layer_3_loop : include_tasks] ******************************************************************************************************************************************************
    skipping: [sw-core-spine-1]
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/layer_3_loop/tasks/use_case_2/leaf_core_vlan_ipv4_ipv6.yml for sw-access-leaf-1, sw-access-leaf-2

    TASK [layer_3_loop : CONFIG VTP MODE] ****************************************************************************************************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [layer_3_loop : CONFIG L2 CORE VLAN] ************************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}]})

    TASK [layer_3_loop : CONFIG L2 CORE VLAN MEMBER L3 VNI] **********************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}]})

    TASK [layer_3_loop : CONFIG L3 SVI INTERFACE CORE VLAN] **********************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}]})

    TASK [layer_3_loop : SHOW CONFIG] ********************************************************************************************************************************************************
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]})
    ok: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}]})
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]})
    ok: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}]})
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]})
    ok: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}]})
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]})
    ok: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}]})
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]})
    ok: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}]})

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
    ok: [sw-access-leaf-2] => (item={'changed': False, 'stdout': ['vtp mode transparent', 'vlan 2010\n name 2010', 'vlan configuration 2010\n member vni 20010', 'Building configuration...\n\nCurrent configuration : 143 bytes\n!\ninterface Vlan2010\n description 2010 SVI Interface\n vrf forwarding VRF_OVERLAY_2010\n ip unnumbered Loopback1\n ipv6 enable\n no autostate\nend'], 'stdout_lines': [['vtp mode transparent'], ['vlan 2010', ' name 2010'], ['vlan configuration 2010', ' member vni 20010'], ['Building configuration...', '', 'Current configuration : 143 bytes', '!', 'interface Vlan2010', ' description 2010 SVI Interface', ' vrf forwarding VRF_OVERLAY_2010', ' ip unnumbered Loopback1', ' ipv6 enable', ' no autostate', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vtp mode transparent', 'show running-config | section vlan 2010', 'show running-config | section vlan configuration 2010', 'show running-config interface Vlan2010'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
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
                    "Current configuration : 143 bytes",
                    "!",
                    "interface Vlan2010",
                    " description 2010 SVI Interface",
                    " vrf forwarding VRF_OVERLAY_2010",
                    " ip unnumbered Loopback1",
                    " ipv6 enable",
                    " no autostate",
                    "end"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-1] => (item={'changed': False, 'stdout': ['vtp mode transparent', 'vlan 2010\n name 2010', 'vlan configuration 2010\n member vni 20010', 'Building configuration...\n\nCurrent configuration : 143 bytes\n!\ninterface Vlan2010\n description 2010 SVI Interface\n vrf forwarding VRF_OVERLAY_2010\n ip unnumbered Loopback1\n ipv6 enable\n no autostate\nend'], 'stdout_lines': [['vtp mode transparent'], ['vlan 2010', ' name 2010'], ['vlan configuration 2010', ' member vni 20010'], ['Building configuration...', '', 'Current configuration : 143 bytes', '!', 'interface Vlan2010', ' description 2010 SVI Interface', ' vrf forwarding VRF_OVERLAY_2010', ' ip unnumbered Loopback1', ' ipv6 enable', ' no autostate', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vtp mode transparent', 'show running-config | section vlan 2010', 'show running-config | section vlan configuration 2010', 'show running-config interface Vlan2010'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
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
                    "Current configuration : 143 bytes",
                    "!",
                    "interface Vlan2010",
                    " description 2010 SVI Interface",
                    " vrf forwarding VRF_OVERLAY_2010",
                    " ip unnumbered Loopback1",
                    " ipv6 enable",
                    " no autostate",
                    "end"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => (item={'changed': False, 'stdout': ['vtp mode transparent', 'vlan 2020\n name 2020', 'vlan configuration 2020\n member vni 20020', 'Building configuration...\n\nCurrent configuration : 143 bytes\n!\ninterface Vlan2020\n description 2020 SVI Interface\n vrf forwarding VRF_OVERLAY_2020\n ip unnumbered Loopback1\n ipv6 enable\n no autostate\nend'], 'stdout_lines': [['vtp mode transparent'], ['vlan 2020', ' name 2020'], ['vlan configuration 2020', ' member vni 20020'], ['Building configuration...', '', 'Current configuration : 143 bytes', '!', 'interface Vlan2020', ' description 2020 SVI Interface', ' vrf forwarding VRF_OVERLAY_2020', ' ip unnumbered Loopback1', ' ipv6 enable', ' no autostate', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vtp mode transparent', 'show running-config | section vlan 2020', 'show running-config | section vlan configuration 2020', 'show running-config interface Vlan2020'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vtp mode transparent"
                ],
                [
                    "vlan 2020",
                    " name 2020"
                ],
                [
                    "vlan configuration 2020",
                    " member vni 20020"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 143 bytes",
                    "!",
                    "interface Vlan2020",
                    " description 2020 SVI Interface",
                    " vrf forwarding VRF_OVERLAY_2020",
                    " ip unnumbered Loopback1",
                    " ipv6 enable",
                    " no autostate",
                    "end"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => (item={'changed': False, 'stdout': ['vtp mode transparent', 'vlan 2030\n name 2030', 'vlan configuration 2030\n member vni 20030', 'Building configuration...\n\nCurrent configuration : 143 bytes\n!\ninterface Vlan2030\n description 2030 SVI Interface\n vrf forwarding VRF_OVERLAY_2030\n ip unnumbered Loopback1\n ipv6 enable\n no autostate\nend'], 'stdout_lines': [['vtp mode transparent'], ['vlan 2030', ' name 2030'], ['vlan configuration 2030', ' member vni 20030'], ['Building configuration...', '', 'Current configuration : 143 bytes', '!', 'interface Vlan2030', ' description 2030 SVI Interface', ' vrf forwarding VRF_OVERLAY_2030', ' ip unnumbered Loopback1', ' ipv6 enable', ' no autostate', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vtp mode transparent', 'show running-config | section vlan 2030', 'show running-config | section vlan configuration 2030', 'show running-config interface Vlan2030'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vtp mode transparent"
                ],
                [
                    "vlan 2030",
                    " name 2030"
                ],
                [
                    "vlan configuration 2030",
                    " member vni 20030"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 143 bytes",
                    "!",
                    "interface Vlan2030",
                    " description 2030 SVI Interface",
                    " vrf forwarding VRF_OVERLAY_2030",
                    " ip unnumbered Loopback1",
                    " ipv6 enable",
                    " no autostate",
                    "end"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-1] => (item={'changed': False, 'stdout': ['vtp mode transparent', 'vlan 2020\n name 2020', 'vlan configuration 2020\n member vni 20020', 'Building configuration...\n\nCurrent configuration : 143 bytes\n!\ninterface Vlan2020\n description 2020 SVI Interface\n vrf forwarding VRF_OVERLAY_2020\n ip unnumbered Loopback1\n ipv6 enable\n no autostate\nend'], 'stdout_lines': [['vtp mode transparent'], ['vlan 2020', ' name 2020'], ['vlan configuration 2020', ' member vni 20020'], ['Building configuration...', '', 'Current configuration : 143 bytes', '!', 'interface Vlan2020', ' description 2020 SVI Interface', ' vrf forwarding VRF_OVERLAY_2020', ' ip unnumbered Loopback1', ' ipv6 enable', ' no autostate', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vtp mode transparent', 'show running-config | section vlan 2020', 'show running-config | section vlan configuration 2020', 'show running-config interface Vlan2020'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vtp mode transparent"
                ],
                [
                    "vlan 2020",
                    " name 2020"
                ],
                [
                    "vlan configuration 2020",
                    " member vni 20020"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 143 bytes",
                    "!",
                    "interface Vlan2020",
                    " description 2020 SVI Interface",
                    " vrf forwarding VRF_OVERLAY_2020",
                    " ip unnumbered Loopback1",
                    " ipv6 enable",
                    " no autostate",
                    "end"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => (item={'changed': False, 'stdout': ['vtp mode transparent', 'vlan 2040\n name 2040', 'vlan configuration 2040\n member vni 20040', 'Building configuration...\n\nCurrent configuration : 143 bytes\n!\ninterface Vlan2040\n description 2040 SVI Interface\n vrf forwarding VRF_OVERLAY_2040\n ip unnumbered Loopback1\n ipv6 enable\n no autostate\nend'], 'stdout_lines': [['vtp mode transparent'], ['vlan 2040', ' name 2040'], ['vlan configuration 2040', ' member vni 20040'], ['Building configuration...', '', 'Current configuration : 143 bytes', '!', 'interface Vlan2040', ' description 2040 SVI Interface', ' vrf forwarding VRF_OVERLAY_2040', ' ip unnumbered Loopback1', ' ipv6 enable', ' no autostate', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vtp mode transparent', 'show running-config | section vlan 2040', 'show running-config | section vlan configuration 2040', 'show running-config interface Vlan2040'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vtp mode transparent"
                ],
                [
                    "vlan 2040",
                    " name 2040"
                ],
                [
                    "vlan configuration 2040",
                    " member vni 20040"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 143 bytes",
                    "!",
                    "interface Vlan2040",
                    " description 2040 SVI Interface",
                    " vrf forwarding VRF_OVERLAY_2040",
                    " ip unnumbered Loopback1",
                    " ipv6 enable",
                    " no autostate",
                    "end"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-1] => (item={'changed': False, 'stdout': ['vtp mode transparent', 'vlan 2030\n name 2030', 'vlan configuration 2030\n member vni 20030', 'Building configuration...\n\nCurrent configuration : 143 bytes\n!\ninterface Vlan2030\n description 2030 SVI Interface\n vrf forwarding VRF_OVERLAY_2030\n ip unnumbered Loopback1\n ipv6 enable\n no autostate\nend'], 'stdout_lines': [['vtp mode transparent'], ['vlan 2030', ' name 2030'], ['vlan configuration 2030', ' member vni 20030'], ['Building configuration...', '', 'Current configuration : 143 bytes', '!', 'interface Vlan2030', ' description 2030 SVI Interface', ' vrf forwarding VRF_OVERLAY_2030', ' ip unnumbered Loopback1', ' ipv6 enable', ' no autostate', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vtp mode transparent', 'show running-config | section vlan 2030', 'show running-config | section vlan configuration 2030', 'show running-config interface Vlan2030'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vtp mode transparent"
                ],
                [
                    "vlan 2030",
                    " name 2030"
                ],
                [
                    "vlan configuration 2030",
                    " member vni 20030"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 143 bytes",
                    "!",
                    "interface Vlan2030",
                    " description 2030 SVI Interface",
                    " vrf forwarding VRF_OVERLAY_2030",
                    " ip unnumbered Loopback1",
                    " ipv6 enable",
                    " no autostate",
                    "end"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => (item={'changed': False, 'stdout': ['vtp mode transparent', 'vlan 2050\n name 2050', 'vlan configuration 2050\n member vni 20050', 'Building configuration...\n\nCurrent configuration : 143 bytes\n!\ninterface Vlan2050\n description 2050 SVI Interface\n vrf forwarding VRF_OVERLAY_2050\n ip unnumbered Loopback1\n ipv6 enable\n no autostate\nend'], 'stdout_lines': [['vtp mode transparent'], ['vlan 2050', ' name 2050'], ['vlan configuration 2050', ' member vni 20050'], ['Building configuration...', '', 'Current configuration : 143 bytes', '!', 'interface Vlan2050', ' description 2050 SVI Interface', ' vrf forwarding VRF_OVERLAY_2050', ' ip unnumbered Loopback1', ' ipv6 enable', ' no autostate', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vtp mode transparent', 'show running-config | section vlan 2050', 'show running-config | section vlan configuration 2050', 'show running-config interface Vlan2050'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vtp mode transparent"
                ],
                [
                    "vlan 2050",
                    " name 2050"
                ],
                [
                    "vlan configuration 2050",
                    " member vni 20050"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 143 bytes",
                    "!",
                    "interface Vlan2050",
                    " description 2050 SVI Interface",
                    " vrf forwarding VRF_OVERLAY_2050",
                    " ip unnumbered Loopback1",
                    " ipv6 enable",
                    " no autostate",
                    "end"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-1] => (item={'changed': False, 'stdout': ['vtp mode transparent', 'vlan 2040\n name 2040', 'vlan configuration 2040\n member vni 20040', 'Building configuration...\n\nCurrent configuration : 143 bytes\n!\ninterface Vlan2040\n description 2040 SVI Interface\n vrf forwarding VRF_OVERLAY_2040\n ip unnumbered Loopback1\n ipv6 enable\n no autostate\nend'], 'stdout_lines': [['vtp mode transparent'], ['vlan 2040', ' name 2040'], ['vlan configuration 2040', ' member vni 20040'], ['Building configuration...', '', 'Current configuration : 143 bytes', '!', 'interface Vlan2040', ' description 2040 SVI Interface', ' vrf forwarding VRF_OVERLAY_2040', ' ip unnumbered Loopback1', ' ipv6 enable', ' no autostate', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vtp mode transparent', 'show running-config | section vlan 2040', 'show running-config | section vlan configuration 2040', 'show running-config interface Vlan2040'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vtp mode transparent"
                ],
                [
                    "vlan 2040",
                    " name 2040"
                ],
                [
                    "vlan configuration 2040",
                    " member vni 20040"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 143 bytes",
                    "!",
                    "interface Vlan2040",
                    " description 2040 SVI Interface",
                    " vrf forwarding VRF_OVERLAY_2040",
                    " ip unnumbered Loopback1",
                    " ipv6 enable",
                    " no autostate",
                    "end"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-1] => (item={'changed': False, 'stdout': ['vtp mode transparent', 'vlan 2050\n name 2050', 'vlan configuration 2050\n member vni 20050', 'Building configuration...\n\nCurrent configuration : 143 bytes\n!\ninterface Vlan2050\n description 2050 SVI Interface\n vrf forwarding VRF_OVERLAY_2050\n ip unnumbered Loopback1\n ipv6 enable\n no autostate\nend'], 'stdout_lines': [['vtp mode transparent'], ['vlan 2050', ' name 2050'], ['vlan configuration 2050', ' member vni 20050'], ['Building configuration...', '', 'Current configuration : 143 bytes', '!', 'interface Vlan2050', ' description 2050 SVI Interface', ' vrf forwarding VRF_OVERLAY_2050', ' ip unnumbered Loopback1', ' ipv6 enable', ' no autostate', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vtp mode transparent', 'show running-config | section vlan 2050', 'show running-config | section vlan configuration 2050', 'show running-config interface Vlan2050'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}]}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vtp mode transparent"
                ],
                [
                    "vlan 2050",
                    " name 2050"
                ],
                [
                    "vlan configuration 2050",
                    " member vni 20050"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 143 bytes",
                    "!",
                    "interface Vlan2050",
                    " description 2050 SVI Interface",
                    " vrf forwarding VRF_OVERLAY_2050",
                    " ip unnumbered Loopback1",
                    " ipv6 enable",
                    " no autostate",
                    "end"
                ]
            ]
        ]
    }

    TASK [layer_3_loop : include_tasks] ******************************************************************************************************************************************************
    skipping: [sw-core-spine-1]
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/layer_3_loop/tasks/use_case_2/l3_edge_vlan_ipv4_ipv6.yml for sw-access-leaf-1, sw-access-leaf-2

    TASK [layer_3_loop : include_tasks] ******************************************************************************************************************************************************
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/layer_3_loop/tasks/use_case_2/l3_edge_vlan_ipv4_ipv6_inner_loop.yml for sw-access-leaf-1 => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}]})
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/layer_3_loop/tasks/use_case_2/l3_edge_vlan_ipv4_ipv6_inner_loop.yml for sw-access-leaf-1 => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}]})
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/layer_3_loop/tasks/use_case_2/l3_edge_vlan_ipv4_ipv6_inner_loop.yml for sw-access-leaf-1 => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}]})
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/layer_3_loop/tasks/use_case_2/l3_edge_vlan_ipv4_ipv6_inner_loop.yml for sw-access-leaf-1 => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}]})
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/layer_3_loop/tasks/use_case_2/l3_edge_vlan_ipv4_ipv6_inner_loop.yml for sw-access-leaf-1 => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}]})
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/layer_3_loop/tasks/use_case_2/l3_edge_vlan_ipv4_ipv6_inner_loop.yml for sw-access-leaf-2 => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]})
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/layer_3_loop/tasks/use_case_2/l3_edge_vlan_ipv4_ipv6_inner_loop.yml for sw-access-leaf-2 => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]})
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/layer_3_loop/tasks/use_case_2/l3_edge_vlan_ipv4_ipv6_inner_loop.yml for sw-access-leaf-2 => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]})
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/layer_3_loop/tasks/use_case_2/l3_edge_vlan_ipv4_ipv6_inner_loop.yml for sw-access-leaf-2 => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]})
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/layer_3_loop/tasks/use_case_2/l3_edge_vlan_ipv4_ipv6_inner_loop.yml for sw-access-leaf-2 => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]})

    TASK [layer_3_loop : CONFIG L2 EDGE VLAN] ************************************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48})
    changed: [sw-access-leaf-1] => (item={'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48})
    changed: [sw-access-leaf-1] => (item={'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48})

    TASK [layer_3_loop : CONFIG L3 SVI INTERFACE EDGE VLAN] **********************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48})
    changed: [sw-access-leaf-1] => (item={'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48})
    changed: [sw-access-leaf-1] => (item={'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48})

    TASK [layer_3_loop : SHOW CONFIG] ********************************************************************************************************************************************************
    ok: [sw-access-leaf-1] => (item={'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48})
    ok: [sw-access-leaf-1] => (item={'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48})
    ok: [sw-access-leaf-1] => (item={'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48})

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
    ok: [sw-access-leaf-1] => (item={'changed': False, 'stdout': ['vlan 10\n name 10\n switchport trunk allowed vlan 10,20,30', 'Building configuration...\n\nCurrent configuration : 265 bytes\n!\ninterface Vlan10\n description VLAN10 SVI-INTERFACE\n vrf forwarding VRF_OVERLAY_2010\n ip dhcp relay information option vpn-id \n ip address 10.10.10.1 255.255.255.0\n ip helper-address global 10.10.10.10\n ipv6 address 1111:2222:3333:4444:5555:6666:7777:8881/48\nend'], 'stdout_lines': [['vlan 10', ' name 10', ' switchport trunk allowed vlan 10,20,30'], ['Building configuration...', '', 'Current configuration : 265 bytes', '!', 'interface Vlan10', ' description VLAN10 SVI-INTERFACE', ' vrf forwarding VRF_OVERLAY_2010', ' ip dhcp relay information option vpn-id ', ' ip address 10.10.10.1 255.255.255.0', ' ip helper-address global 10.10.10.10', ' ipv6 address 1111:2222:3333:4444:5555:6666:7777:8881/48', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vlan 10', 'show running-config interface Vlan10'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vlan 10",
                    " name 10",
                    " switchport trunk allowed vlan 10,20,30"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 265 bytes",
                    "!",
                    "interface Vlan10",
                    " description VLAN10 SVI-INTERFACE",
                    " vrf forwarding VRF_OVERLAY_2010",
                    " ip dhcp relay information option vpn-id ",
                    " ip address 10.10.10.1 255.255.255.0",
                    " ip helper-address global 10.10.10.10",
                    " ipv6 address 1111:2222:3333:4444:5555:6666:7777:8881/48",
                    "end"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-1] => (item={'changed': False, 'stdout': ['vlan 20\n name 20\nvlan 2010\n name 2010\nvlan 2020\n name 2020\nvlan 2030\n name 2030\nvlan 2040\n name 2040\nvlan 2050\n name 2050\n switchport access vlan 20\n switchport access vlan 20\n switchport access vlan 20\n switchport access vlan 20', 'Building configuration...\n\nCurrent configuration : 265 bytes\n!\ninterface Vlan20\n description VLAN20 SVI-INTERFACE\n vrf forwarding VRF_OVERLAY_2010\n ip dhcp relay information option vpn-id \n ip address 10.20.20.1 255.255.255.0\n ip helper-address global 10.10.10.10\n ipv6 address 2222:3333:4444:5555:6666:7777:8888:9991/48\nend'], 'stdout_lines': [['vlan 20', ' name 20', 'vlan 2010', ' name 2010', 'vlan 2020', ' name 2020', 'vlan 2030', ' name 2030', 'vlan 2040', ' name 2040', 'vlan 2050', ' name 2050', ' switchport access vlan 20', ' switchport access vlan 20', ' switchport access vlan 20', ' switchport access vlan 20'], ['Building configuration...', '', 'Current configuration : 265 bytes', '!', 'interface Vlan20', ' description VLAN20 SVI-INTERFACE', ' vrf forwarding VRF_OVERLAY_2010', ' ip dhcp relay information option vpn-id ', ' ip address 10.20.20.1 255.255.255.0', ' ip helper-address global 10.10.10.10', ' ipv6 address 2222:3333:4444:5555:6666:7777:8888:9991/48', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vlan 20', 'show running-config interface Vlan20'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vlan 20",
                    " name 20",
                    "vlan 2010",
                    " name 2010",
                    "vlan 2020",
                    " name 2020",
                    "vlan 2030",
                    " name 2030",
                    "vlan 2040",
                    " name 2040",
                    "vlan 2050",
                    " name 2050",
                    " switchport access vlan 20",
                    " switchport access vlan 20",
                    " switchport access vlan 20",
                    " switchport access vlan 20"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 265 bytes",
                    "!",
                    "interface Vlan20",
                    " description VLAN20 SVI-INTERFACE",
                    " vrf forwarding VRF_OVERLAY_2010",
                    " ip dhcp relay information option vpn-id ",
                    " ip address 10.20.20.1 255.255.255.0",
                    " ip helper-address global 10.10.10.10",
                    " ipv6 address 2222:3333:4444:5555:6666:7777:8888:9991/48",
                    "end"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-1] => (item={'changed': False, 'stdout': ['vlan 30\n name 30', 'Building configuration...\n\nCurrent configuration : 265 bytes\n!\ninterface Vlan30\n description VLAN30 SVI-INTERFACE\n vrf forwarding VRF_OVERLAY_2010\n ip dhcp relay information option vpn-id \n ip address 10.30.30.1 255.255.255.0\n ip helper-address global 10.10.10.10\n ipv6 address 3333:4444:5555:6666:7777:8888:9999:1111/48\nend'], 'stdout_lines': [['vlan 30', ' name 30'], ['Building configuration...', '', 'Current configuration : 265 bytes', '!', 'interface Vlan30', ' description VLAN30 SVI-INTERFACE', ' vrf forwarding VRF_OVERLAY_2010', ' ip dhcp relay information option vpn-id ', ' ip address 10.30.30.1 255.255.255.0', ' ip helper-address global 10.10.10.10', ' ipv6 address 3333:4444:5555:6666:7777:8888:9999:1111/48', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vlan 30', 'show running-config interface Vlan30'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vlan 30",
                    " name 30"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 265 bytes",
                    "!",
                    "interface Vlan30",
                    " description VLAN30 SVI-INTERFACE",
                    " vrf forwarding VRF_OVERLAY_2010",
                    " ip dhcp relay information option vpn-id ",
                    " ip address 10.30.30.1 255.255.255.0",
                    " ip helper-address global 10.10.10.10",
                    " ipv6 address 3333:4444:5555:6666:7777:8888:9999:1111/48",
                    "end"
                ]
            ]
        ]
    }

    TASK [layer_3_loop : CONFIG L2 EDGE VLAN] ************************************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'})
    changed: [sw-access-leaf-1] => (item={'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48})

    TASK [layer_3_loop : CONFIG L3 SVI INTERFACE EDGE VLAN] **********************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'})
    changed: [sw-access-leaf-1] => (item={'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48})

    TASK [layer_3_loop : SHOW CONFIG] ********************************************************************************************************************************************************
    ok: [sw-access-leaf-1] => (item={'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'})
    ok: [sw-access-leaf-1] => (item={'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48})

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
    ok: [sw-access-leaf-1] => (item={'changed': False, 'stdout': ['vlan 40\n name 40', 'Building configuration...\n\nCurrent configuration : 265 bytes\n!\ninterface Vlan40\n description VLAN40 SVI-INTERFACE\n vrf forwarding VRF_OVERLAY_2020\n ip dhcp relay information option vpn-id \n ip address 10.40.40.1 255.255.255.0\n ip helper-address global 10.10.10.10\n ipv6 address 4444:5555:6666:7777:8888:9999:1111:2221/48\nend'], 'stdout_lines': [['vlan 40', ' name 40'], ['Building configuration...', '', 'Current configuration : 265 bytes', '!', 'interface Vlan40', ' description VLAN40 SVI-INTERFACE', ' vrf forwarding VRF_OVERLAY_2020', ' ip dhcp relay information option vpn-id ', ' ip address 10.40.40.1 255.255.255.0', ' ip helper-address global 10.10.10.10', ' ipv6 address 4444:5555:6666:7777:8888:9999:1111:2221/48', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vlan 40', 'show running-config interface Vlan40'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vlan 40",
                    " name 40"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 265 bytes",
                    "!",
                    "interface Vlan40",
                    " description VLAN40 SVI-INTERFACE",
                    " vrf forwarding VRF_OVERLAY_2020",
                    " ip dhcp relay information option vpn-id ",
                    " ip address 10.40.40.1 255.255.255.0",
                    " ip helper-address global 10.10.10.10",
                    " ipv6 address 4444:5555:6666:7777:8888:9999:1111:2221/48",
                    "end"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-1] => (item={'changed': False, 'stdout': ['vlan 50\n name 50', 'Building configuration...\n\nCurrent configuration : 265 bytes\n!\ninterface Vlan50\n description VLAN50 SVI-INTERFACE\n vrf forwarding VRF_OVERLAY_2020\n ip dhcp relay information option vpn-id \n ip address 10.50.50.1 255.255.255.0\n ip helper-address global 10.10.10.10\n ipv6 address 5555:6666:7777:8888:9999:1111:2222:3331/48\nend'], 'stdout_lines': [['vlan 50', ' name 50'], ['Building configuration...', '', 'Current configuration : 265 bytes', '!', 'interface Vlan50', ' description VLAN50 SVI-INTERFACE', ' vrf forwarding VRF_OVERLAY_2020', ' ip dhcp relay information option vpn-id ', ' ip address 10.50.50.1 255.255.255.0', ' ip helper-address global 10.10.10.10', ' ipv6 address 5555:6666:7777:8888:9999:1111:2222:3331/48', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vlan 50', 'show running-config interface Vlan50'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vlan 50",
                    " name 50"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 265 bytes",
                    "!",
                    "interface Vlan50",
                    " description VLAN50 SVI-INTERFACE",
                    " vrf forwarding VRF_OVERLAY_2020",
                    " ip dhcp relay information option vpn-id ",
                    " ip address 10.50.50.1 255.255.255.0",
                    " ip helper-address global 10.10.10.10",
                    " ipv6 address 5555:6666:7777:8888:9999:1111:2222:3331/48",
                    "end"
                ]
            ]
        ]
    }

    TASK [layer_3_loop : CONFIG L2 EDGE VLAN] ************************************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48})

    TASK [layer_3_loop : CONFIG L3 SVI INTERFACE EDGE VLAN] **********************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48})

    TASK [layer_3_loop : SHOW CONFIG] ********************************************************************************************************************************************************
    ok: [sw-access-leaf-1] => (item={'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48})

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
    ok: [sw-access-leaf-1] => (item={'changed': False, 'stdout': ['vlan 60\n name 60', 'Building configuration...\n\nCurrent configuration : 265 bytes\n!\ninterface Vlan60\n description VLAN60 SVI-INTERFACE\n vrf forwarding VRF_OVERLAY_2030\n ip dhcp relay information option vpn-id \n ip address 10.60.60.1 255.255.255.0\n ip helper-address global 10.10.10.10\n ipv6 address 6666:7777:8888:9999:1111:2222:3333:4441/48\nend'], 'stdout_lines': [['vlan 60', ' name 60'], ['Building configuration...', '', 'Current configuration : 265 bytes', '!', 'interface Vlan60', ' description VLAN60 SVI-INTERFACE', ' vrf forwarding VRF_OVERLAY_2030', ' ip dhcp relay information option vpn-id ', ' ip address 10.60.60.1 255.255.255.0', ' ip helper-address global 10.10.10.10', ' ipv6 address 6666:7777:8888:9999:1111:2222:3333:4441/48', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vlan 60', 'show running-config interface Vlan60'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vlan 60",
                    " name 60"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 265 bytes",
                    "!",
                    "interface Vlan60",
                    " description VLAN60 SVI-INTERFACE",
                    " vrf forwarding VRF_OVERLAY_2030",
                    " ip dhcp relay information option vpn-id ",
                    " ip address 10.60.60.1 255.255.255.0",
                    " ip helper-address global 10.10.10.10",
                    " ipv6 address 6666:7777:8888:9999:1111:2222:3333:4441/48",
                    "end"
                ]
            ]
        ]
    }

    TASK [layer_3_loop : CONFIG L2 EDGE VLAN] ************************************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48})

    TASK [layer_3_loop : CONFIG L3 SVI INTERFACE EDGE VLAN] **********************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48})

    TASK [layer_3_loop : SHOW CONFIG] ********************************************************************************************************************************************************
    ok: [sw-access-leaf-1] => (item={'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48})

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
    ok: [sw-access-leaf-1] => (item={'changed': False, 'stdout': ['vlan 70\n name 70', 'Building configuration...\n\nCurrent configuration : 265 bytes\n!\ninterface Vlan70\n description VLAN70 SVI-INTERFACE\n vrf forwarding VRF_OVERLAY_2040\n ip dhcp relay information option vpn-id \n ip address 10.70.70.1 255.255.255.0\n ip helper-address global 10.10.10.10\n ipv6 address 7777:8888:9999:1111:2222:3333:4444:5551/48\nend'], 'stdout_lines': [['vlan 70', ' name 70'], ['Building configuration...', '', 'Current configuration : 265 bytes', '!', 'interface Vlan70', ' description VLAN70 SVI-INTERFACE', ' vrf forwarding VRF_OVERLAY_2040', ' ip dhcp relay information option vpn-id ', ' ip address 10.70.70.1 255.255.255.0', ' ip helper-address global 10.10.10.10', ' ipv6 address 7777:8888:9999:1111:2222:3333:4444:5551/48', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vlan 70', 'show running-config interface Vlan70'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vlan 70",
                    " name 70"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 265 bytes",
                    "!",
                    "interface Vlan70",
                    " description VLAN70 SVI-INTERFACE",
                    " vrf forwarding VRF_OVERLAY_2040",
                    " ip dhcp relay information option vpn-id ",
                    " ip address 10.70.70.1 255.255.255.0",
                    " ip helper-address global 10.10.10.10",
                    " ipv6 address 7777:8888:9999:1111:2222:3333:4444:5551/48",
                    "end"
                ]
            ]
        ]
    }

    TASK [layer_3_loop : CONFIG L2 EDGE VLAN] ************************************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48})

    TASK [layer_3_loop : CONFIG L3 SVI INTERFACE EDGE VLAN] **********************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48})

    TASK [layer_3_loop : SHOW CONFIG] ********************************************************************************************************************************************************
    ok: [sw-access-leaf-1] => (item={'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48})

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
    ok: [sw-access-leaf-1] => (item={'changed': False, 'stdout': ['vlan 80\n name 80', 'Building configuration...\n\nCurrent configuration : 265 bytes\n!\ninterface Vlan80\n description VLAN80 SVI-INTERFACE\n vrf forwarding VRF_OVERLAY_2050\n ip dhcp relay information option vpn-id \n ip address 10.80.80.1 255.255.255.0\n ip helper-address global 10.10.10.10\n ipv6 address 8888:9999:1111:2222:3333:4444:5555:6661/48\nend'], 'stdout_lines': [['vlan 80', ' name 80'], ['Building configuration...', '', 'Current configuration : 265 bytes', '!', 'interface Vlan80', ' description VLAN80 SVI-INTERFACE', ' vrf forwarding VRF_OVERLAY_2050', ' ip dhcp relay information option vpn-id ', ' ip address 10.80.80.1 255.255.255.0', ' ip helper-address global 10.10.10.10', ' ipv6 address 8888:9999:1111:2222:3333:4444:5555:6661/48', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vlan 80', 'show running-config interface Vlan80'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vlan 80",
                    " name 80"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 265 bytes",
                    "!",
                    "interface Vlan80",
                    " description VLAN80 SVI-INTERFACE",
                    " vrf forwarding VRF_OVERLAY_2050",
                    " ip dhcp relay information option vpn-id ",
                    " ip address 10.80.80.1 255.255.255.0",
                    " ip helper-address global 10.10.10.10",
                    " ipv6 address 8888:9999:1111:2222:3333:4444:5555:6661/48",
                    "end"
                ]
            ]
        ]
    }

    TASK [layer_3_loop : CONFIG L2 EDGE VLAN] ************************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48})
    changed: [sw-access-leaf-2] => (item={'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48})
    changed: [sw-access-leaf-2] => (item={'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48})

    TASK [layer_3_loop : CONFIG L3 SVI INTERFACE EDGE VLAN] **********************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48})
    changed: [sw-access-leaf-2] => (item={'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48})
    changed: [sw-access-leaf-2] => (item={'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48})

    TASK [layer_3_loop : SHOW CONFIG] ********************************************************************************************************************************************************
    ok: [sw-access-leaf-2] => (item={'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48})
    ok: [sw-access-leaf-2] => (item={'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48})
    ok: [sw-access-leaf-2] => (item={'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48})

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
    ok: [sw-access-leaf-2] => (item={'changed': False, 'stdout': ['vlan 10\n name 10\n switchport trunk allowed vlan 10,20,30', 'Building configuration...\n\nCurrent configuration : 265 bytes\n!\ninterface Vlan10\n description VLAN10 SVI-INTERFACE\n vrf forwarding VRF_OVERLAY_2010\n ip dhcp relay information option vpn-id \n ip address 10.10.10.2 255.255.255.0\n ip helper-address global 10.10.10.10\n ipv6 address 1111:2222:3333:4444:5555:6666:7777:8882/48\nend'], 'stdout_lines': [['vlan 10', ' name 10', ' switchport trunk allowed vlan 10,20,30'], ['Building configuration...', '', 'Current configuration : 265 bytes', '!', 'interface Vlan10', ' description VLAN10 SVI-INTERFACE', ' vrf forwarding VRF_OVERLAY_2010', ' ip dhcp relay information option vpn-id ', ' ip address 10.10.10.2 255.255.255.0', ' ip helper-address global 10.10.10.10', ' ipv6 address 1111:2222:3333:4444:5555:6666:7777:8882/48', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vlan 10', 'show running-config interface Vlan10'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vlan 10",
                    " name 10",
                    " switchport trunk allowed vlan 10,20,30"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 265 bytes",
                    "!",
                    "interface Vlan10",
                    " description VLAN10 SVI-INTERFACE",
                    " vrf forwarding VRF_OVERLAY_2010",
                    " ip dhcp relay information option vpn-id ",
                    " ip address 10.10.10.2 255.255.255.0",
                    " ip helper-address global 10.10.10.10",
                    " ipv6 address 1111:2222:3333:4444:5555:6666:7777:8882/48",
                    "end"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => (item={'changed': False, 'stdout': ['vlan 20\n name 20\nvlan 2010\n name 2010\nvlan 2020\n name 2020\nvlan 2030\n name 2030\nvlan 2040\n name 2040\nvlan 2050\n name 2050\n switchport trunk allowed vlan 20,40\n switchport trunk allowed vlan 20,40', 'Building configuration...\n\nCurrent configuration : 265 bytes\n!\ninterface Vlan20\n description VLAN20 SVI-INTERFACE\n vrf forwarding VRF_OVERLAY_2010\n ip dhcp relay information option vpn-id \n ip address 10.20.20.2 255.255.255.0\n ip helper-address global 10.10.10.10\n ipv6 address 2222:3333:4444:5555:6666:7777:8888:9992/48\nend'], 'stdout_lines': [['vlan 20', ' name 20', 'vlan 2010', ' name 2010', 'vlan 2020', ' name 2020', 'vlan 2030', ' name 2030', 'vlan 2040', ' name 2040', 'vlan 2050', ' name 2050', ' switchport trunk allowed vlan 20,40', ' switchport trunk allowed vlan 20,40'], ['Building configuration...', '', 'Current configuration : 265 bytes', '!', 'interface Vlan20', ' description VLAN20 SVI-INTERFACE', ' vrf forwarding VRF_OVERLAY_2010', ' ip dhcp relay information option vpn-id ', ' ip address 10.20.20.2 255.255.255.0', ' ip helper-address global 10.10.10.10', ' ipv6 address 2222:3333:4444:5555:6666:7777:8888:9992/48', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vlan 20', 'show running-config interface Vlan20'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vlan 20",
                    " name 20",
                    "vlan 2010",
                    " name 2010",
                    "vlan 2020",
                    " name 2020",
                    "vlan 2030",
                    " name 2030",
                    "vlan 2040",
                    " name 2040",
                    "vlan 2050",
                    " name 2050",
                    " switchport trunk allowed vlan 20,40",
                    " switchport trunk allowed vlan 20,40"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 265 bytes",
                    "!",
                    "interface Vlan20",
                    " description VLAN20 SVI-INTERFACE",
                    " vrf forwarding VRF_OVERLAY_2010",
                    " ip dhcp relay information option vpn-id ",
                    " ip address 10.20.20.2 255.255.255.0",
                    " ip helper-address global 10.10.10.10",
                    " ipv6 address 2222:3333:4444:5555:6666:7777:8888:9992/48",
                    "end"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => (item={'changed': False, 'stdout': ['vlan 30\n name 30', 'Building configuration...\n\nCurrent configuration : 265 bytes\n!\ninterface Vlan30\n description VLAN30 SVI-INTERFACE\n vrf forwarding VRF_OVERLAY_2010\n ip dhcp relay information option vpn-id \n ip address 10.30.30.2 255.255.255.0\n ip helper-address global 10.10.10.10\n ipv6 address 3333:4444:5555:6666:7777:8888:9999:1112/48\nend'], 'stdout_lines': [['vlan 30', ' name 30'], ['Building configuration...', '', 'Current configuration : 265 bytes', '!', 'interface Vlan30', ' description VLAN30 SVI-INTERFACE', ' vrf forwarding VRF_OVERLAY_2010', ' ip dhcp relay information option vpn-id ', ' ip address 10.30.30.2 255.255.255.0', ' ip helper-address global 10.10.10.10', ' ipv6 address 3333:4444:5555:6666:7777:8888:9999:1112/48', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vlan 30', 'show running-config interface Vlan30'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vlan 30",
                    " name 30"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 265 bytes",
                    "!",
                    "interface Vlan30",
                    " description VLAN30 SVI-INTERFACE",
                    " vrf forwarding VRF_OVERLAY_2010",
                    " ip dhcp relay information option vpn-id ",
                    " ip address 10.30.30.2 255.255.255.0",
                    " ip helper-address global 10.10.10.10",
                    " ipv6 address 3333:4444:5555:6666:7777:8888:9999:1112/48",
                    "end"
                ]
            ]
        ]
    }

    TASK [layer_3_loop : CONFIG L2 EDGE VLAN] ************************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48})
    changed: [sw-access-leaf-2] => (item={'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48})

    TASK [layer_3_loop : CONFIG L3 SVI INTERFACE EDGE VLAN] **********************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48})
    changed: [sw-access-leaf-2] => (item={'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48})

    TASK [layer_3_loop : SHOW CONFIG] ********************************************************************************************************************************************************
    ok: [sw-access-leaf-2] => (item={'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48})
    ok: [sw-access-leaf-2] => (item={'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48})

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
    ok: [sw-access-leaf-2] => (item={'changed': False, 'stdout': ['vlan 40\n name 40', 'Building configuration...\n\nCurrent configuration : 265 bytes\n!\ninterface Vlan40\n description VLAN40 SVI-INTERFACE\n vrf forwarding VRF_OVERLAY_2020\n ip dhcp relay information option vpn-id \n ip address 10.40.40.2 255.255.255.0\n ip helper-address global 10.10.10.10\n ipv6 address 4444:5555:6666:7777:8888:9999:1111:2222/48\nend'], 'stdout_lines': [['vlan 40', ' name 40'], ['Building configuration...', '', 'Current configuration : 265 bytes', '!', 'interface Vlan40', ' description VLAN40 SVI-INTERFACE', ' vrf forwarding VRF_OVERLAY_2020', ' ip dhcp relay information option vpn-id ', ' ip address 10.40.40.2 255.255.255.0', ' ip helper-address global 10.10.10.10', ' ipv6 address 4444:5555:6666:7777:8888:9999:1111:2222/48', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vlan 40', 'show running-config interface Vlan40'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vlan 40",
                    " name 40"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 265 bytes",
                    "!",
                    "interface Vlan40",
                    " description VLAN40 SVI-INTERFACE",
                    " vrf forwarding VRF_OVERLAY_2020",
                    " ip dhcp relay information option vpn-id ",
                    " ip address 10.40.40.2 255.255.255.0",
                    " ip helper-address global 10.10.10.10",
                    " ipv6 address 4444:5555:6666:7777:8888:9999:1111:2222/48",
                    "end"
                ]
            ]
        ]
    }
    ok: [sw-access-leaf-2] => (item={'changed': False, 'stdout': ['vlan 50\n name 50', 'Building configuration...\n\nCurrent configuration : 265 bytes\n!\ninterface Vlan50\n description VLAN50 SVI-INTERFACE\n vrf forwarding VRF_OVERLAY_2020\n ip dhcp relay information option vpn-id \n ip address 10.50.50.2 255.255.255.0\n ip helper-address global 10.10.10.10\n ipv6 address 5555:6666:7777:8888:9999:1111:2222:3332/48\nend'], 'stdout_lines': [['vlan 50', ' name 50'], ['Building configuration...', '', 'Current configuration : 265 bytes', '!', 'interface Vlan50', ' description VLAN50 SVI-INTERFACE', ' vrf forwarding VRF_OVERLAY_2020', ' ip dhcp relay information option vpn-id ', ' ip address 10.50.50.2 255.255.255.0', ' ip helper-address global 10.10.10.10', ' ipv6 address 5555:6666:7777:8888:9999:1111:2222:3332/48', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vlan 50', 'show running-config interface Vlan50'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vlan 50",
                    " name 50"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 265 bytes",
                    "!",
                    "interface Vlan50",
                    " description VLAN50 SVI-INTERFACE",
                    " vrf forwarding VRF_OVERLAY_2020",
                    " ip dhcp relay information option vpn-id ",
                    " ip address 10.50.50.2 255.255.255.0",
                    " ip helper-address global 10.10.10.10",
                    " ipv6 address 5555:6666:7777:8888:9999:1111:2222:3332/48",
                    "end"
                ]
            ]
        ]
    }

    TASK [layer_3_loop : CONFIG L2 EDGE VLAN] ************************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48})

    TASK [layer_3_loop : CONFIG L3 SVI INTERFACE EDGE VLAN] **********************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48})

    TASK [layer_3_loop : SHOW CONFIG] ********************************************************************************************************************************************************
    ok: [sw-access-leaf-2] => (item={'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48})

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
    ok: [sw-access-leaf-2] => (item={'changed': False, 'stdout': ['vlan 60\n name 60', 'Building configuration...\n\nCurrent configuration : 265 bytes\n!\ninterface Vlan60\n description VLAN60 SVI-INTERFACE\n vrf forwarding VRF_OVERLAY_2030\n ip dhcp relay information option vpn-id \n ip address 10.60.60.2 255.255.255.0\n ip helper-address global 10.10.10.10\n ipv6 address 6666:7777:8888:9999:1111:2222:3333:4442/48\nend'], 'stdout_lines': [['vlan 60', ' name 60'], ['Building configuration...', '', 'Current configuration : 265 bytes', '!', 'interface Vlan60', ' description VLAN60 SVI-INTERFACE', ' vrf forwarding VRF_OVERLAY_2030', ' ip dhcp relay information option vpn-id ', ' ip address 10.60.60.2 255.255.255.0', ' ip helper-address global 10.10.10.10', ' ipv6 address 6666:7777:8888:9999:1111:2222:3333:4442/48', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vlan 60', 'show running-config interface Vlan60'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vlan 60",
                    " name 60"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 265 bytes",
                    "!",
                    "interface Vlan60",
                    " description VLAN60 SVI-INTERFACE",
                    " vrf forwarding VRF_OVERLAY_2030",
                    " ip dhcp relay information option vpn-id ",
                    " ip address 10.60.60.2 255.255.255.0",
                    " ip helper-address global 10.10.10.10",
                    " ipv6 address 6666:7777:8888:9999:1111:2222:3333:4442/48",
                    "end"
                ]
            ]
        ]
    }

    TASK [layer_3_loop : CONFIG L2 EDGE VLAN] ************************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48})

    TASK [layer_3_loop : CONFIG L3 SVI INTERFACE EDGE VLAN] **********************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48})

    TASK [layer_3_loop : SHOW CONFIG] ********************************************************************************************************************************************************
    ok: [sw-access-leaf-2] => (item={'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48})

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
    ok: [sw-access-leaf-2] => (item={'changed': False, 'stdout': ['vlan 70\n name 70', 'Building configuration...\n\nCurrent configuration : 265 bytes\n!\ninterface Vlan70\n description VLAN70 SVI-INTERFACE\n vrf forwarding VRF_OVERLAY_2040\n ip dhcp relay information option vpn-id \n ip address 10.70.70.2 255.255.255.0\n ip helper-address global 10.10.10.10\n ipv6 address 7777:8888:9999:1111:2222:3333:4444:5552/48\nend'], 'stdout_lines': [['vlan 70', ' name 70'], ['Building configuration...', '', 'Current configuration : 265 bytes', '!', 'interface Vlan70', ' description VLAN70 SVI-INTERFACE', ' vrf forwarding VRF_OVERLAY_2040', ' ip dhcp relay information option vpn-id ', ' ip address 10.70.70.2 255.255.255.0', ' ip helper-address global 10.10.10.10', ' ipv6 address 7777:8888:9999:1111:2222:3333:4444:5552/48', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vlan 70', 'show running-config interface Vlan70'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vlan 70",
                    " name 70"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 265 bytes",
                    "!",
                    "interface Vlan70",
                    " description VLAN70 SVI-INTERFACE",
                    " vrf forwarding VRF_OVERLAY_2040",
                    " ip dhcp relay information option vpn-id ",
                    " ip address 10.70.70.2 255.255.255.0",
                    " ip helper-address global 10.10.10.10",
                    " ipv6 address 7777:8888:9999:1111:2222:3333:4444:5552/48",
                    "end"
                ]
            ]
        ]
    }

    TASK [layer_3_loop : CONFIG L2 EDGE VLAN] ************************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48})

    TASK [layer_3_loop : CONFIG L3 SVI INTERFACE EDGE VLAN] **********************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48})

    TASK [layer_3_loop : SHOW CONFIG] ********************************************************************************************************************************************************
    ok: [sw-access-leaf-2] => (item={'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48})

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
    ok: [sw-access-leaf-2] => (item={'changed': False, 'stdout': ['vlan 80\n name 80', 'Building configuration...\n\nCurrent configuration : 265 bytes\n!\ninterface Vlan80\n description VLAN80 SVI-INTERFACE\n vrf forwarding VRF_OVERLAY_2050\n ip dhcp relay information option vpn-id \n ip address 10.80.80.2 255.255.255.0\n ip helper-address global 10.10.10.10\n ipv6 address 8888:9999:1111:2222:3333:4444:5555:6662/48\nend'], 'stdout_lines': [['vlan 80', ' name 80'], ['Building configuration...', '', 'Current configuration : 265 bytes', '!', 'interface Vlan80', ' description VLAN80 SVI-INTERFACE', ' vrf forwarding VRF_OVERLAY_2050', ' ip dhcp relay information option vpn-id ', ' ip address 10.80.80.2 255.255.255.0', ' ip helper-address global 10.10.10.10', ' ipv6 address 8888:9999:1111:2222:3333:4444:5555:6662/48', 'end']], 'invocation': {'module_args': {'commands': ['show running-config | section vlan 80', 'show running-config interface Vlan80'], 'match': 'all', 'retries': 10, 'interval': 1, 'wait_for': None, 'provider': None}}, 'failed': False, 'item': {'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}, 'ansible_loop_var': 'item'}) => {
        "msg": [
            [
                [
                    "vlan 80",
                    " name 80"
                ],
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 265 bytes",
                    "!",
                    "interface Vlan80",
                    " description VLAN80 SVI-INTERFACE",
                    " vrf forwarding VRF_OVERLAY_2050",
                    " ip dhcp relay information option vpn-id ",
                    " ip address 10.80.80.2 255.255.255.0",
                    " ip helper-address global 10.10.10.10",
                    " ipv6 address 8888:9999:1111:2222:3333:4444:5555:6662/48",
                    "end"
                ]
            ]
        ]
    }

    TASK [layer_3_loop : include_tasks] ******************************************************************************************************************************************************
    skipping: [sw-core-spine-1]
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/layer_3_loop/tasks/use_case_2/dhcp_config.yml for sw-access-leaf-1, sw-access-leaf-2

    TASK [layer_3_loop : CONFIG DHCP GLOBAL] *************************************************************************************************************************************************
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [layer_3_loop : SHOW CONFIG] ********************************************************************************************************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
    ok: [sw-access-leaf-1] => {
        "msg": [
            [
                [
                    "ip dhcp relay information option vpn",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id"
                ],
                [
                    "ip dhcp relay information option vpn",
                    "ip dhcp relay information option",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
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
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id"
                ],
                [
                    "ip dhcp relay information option vpn",
                    "ip dhcp relay information option",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
                    " ip dhcp relay information option vpn-id ",
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

    TASK [layer_3_loop : include_tasks] ******************************************************************************************************************************************************
    skipping: [sw-core-spine-1]
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/layer_3_loop/tasks/use_case_2/nve_interface.yml for sw-access-leaf-1, sw-access-leaf-2

    TASK [layer_3_loop : CONFIG NVE INTERFACE] ***********************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}]})

    TASK [layer_3_loop : SHOW CONFIG] ********************************************************************************************************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
    ok: [sw-access-leaf-1] => {
        "msg": [
            [
                [
                    "Building configuration...",
                    "",
                    "Current configuration : 333 bytes",
                    "!",
                    "interface nve1",
                    " description VXLAN AGGREGATION INTERFACE",
                    " no ip address",
                    " source-interface Loopback1",
                    " host-reachability protocol bgp",
                    " member vni 20010 vrf VRF_OVERLAY_2010",
                    " member vni 20020 vrf VRF_OVERLAY_2020",
                    " member vni 20030 vrf VRF_OVERLAY_2030",
                    " member vni 20040 vrf VRF_OVERLAY_2040",
                    " member vni 20050 vrf VRF_OVERLAY_2050",
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
                    "Current configuration : 333 bytes",
                    "!",
                    "interface nve1",
                    " description VXLAN AGGREGATION INTERFACE",
                    " no ip address",
                    " source-interface Loopback1",
                    " host-reachability protocol bgp",
                    " member vni 20010 vrf VRF_OVERLAY_2010",
                    " member vni 20020 vrf VRF_OVERLAY_2020",
                    " member vni 20030 vrf VRF_OVERLAY_2030",
                    " member vni 20040 vrf VRF_OVERLAY_2040",
                    " member vni 20050 vrf VRF_OVERLAY_2050",
                    "end"
                ]
            ]
        ]
    }

    TASK [layer_3_loop : include_tasks] ******************************************************************************************************************************************************
    skipping: [sw-core-spine-1]
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/layer_3_loop/tasks/use_case_2/bgp_config_ipv4_ipv6.yml for sw-access-leaf-1, sw-access-leaf-2

    TASK [layer_3_loop : CONFIG BGP IPV4 IPV6 LEAF] ******************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item=sw-core-spine-1)
    changed: [sw-access-leaf-1] => (item=sw-core-spine-1)

    TASK [layer_3_loop : CONFIG BGP IPV4 VRF LEAF] *******************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}]})

    TASK [layer_3_loop : CONFIG BGP IPV6 VRF LEAF] *******************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}]})

    TASK [layer_3_loop : SHOW CONFIG] ********************************************************************************************************************************************************
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
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
                    " exit-address-family",
                    " !",
                    " address-family ipv6 vrf VRF_OVERLAY_2010",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    "  advertise l2vpn evpn",
                    " exit-address-family",
                    " !",
                    " address-family ipv4 vrf VRF_OVERLAY_2020",
                    "  advertise l2vpn evpn",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    " exit-address-family",
                    " !",
                    " address-family ipv6 vrf VRF_OVERLAY_2020",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    "  advertise l2vpn evpn",
                    " exit-address-family",
                    " !",
                    " address-family ipv4 vrf VRF_OVERLAY_2030",
                    "  advertise l2vpn evpn",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    " exit-address-family",
                    " !",
                    " address-family ipv6 vrf VRF_OVERLAY_2030",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    "  advertise l2vpn evpn",
                    " exit-address-family",
                    " !",
                    " address-family ipv4 vrf VRF_OVERLAY_2040",
                    "  advertise l2vpn evpn",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    " exit-address-family",
                    " !",
                    " address-family ipv6 vrf VRF_OVERLAY_2040",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    "  advertise l2vpn evpn",
                    " exit-address-family",
                    " !",
                    " address-family ipv4 vrf VRF_OVERLAY_2050",
                    "  advertise l2vpn evpn",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    " exit-address-family",
                    " !",
                    " address-family ipv6 vrf VRF_OVERLAY_2050",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    "  advertise l2vpn evpn",
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
                    " exit-address-family",
                    " !",
                    " address-family ipv6 vrf VRF_OVERLAY_2010",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    "  advertise l2vpn evpn",
                    " exit-address-family",
                    " !",
                    " address-family ipv4 vrf VRF_OVERLAY_2020",
                    "  advertise l2vpn evpn",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    " exit-address-family",
                    " !",
                    " address-family ipv6 vrf VRF_OVERLAY_2020",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    "  advertise l2vpn evpn",
                    " exit-address-family",
                    " !",
                    " address-family ipv4 vrf VRF_OVERLAY_2030",
                    "  advertise l2vpn evpn",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    " exit-address-family",
                    " !",
                    " address-family ipv6 vrf VRF_OVERLAY_2030",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    "  advertise l2vpn evpn",
                    " exit-address-family",
                    " !",
                    " address-family ipv4 vrf VRF_OVERLAY_2040",
                    "  advertise l2vpn evpn",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    " exit-address-family",
                    " !",
                    " address-family ipv6 vrf VRF_OVERLAY_2040",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    "  advertise l2vpn evpn",
                    " exit-address-family",
                    " !",
                    " address-family ipv4 vrf VRF_OVERLAY_2050",
                    "  advertise l2vpn evpn",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    " exit-address-family",
                    " !",
                    " address-family ipv6 vrf VRF_OVERLAY_2050",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    "  advertise l2vpn evpn",
                    " exit-address-family"
                ]
            ]
        ]
    }

    TASK [layer_3_loop : include_tasks] ******************************************************************************************************************************************************
    skipping: [sw-access-leaf-1]
    skipping: [sw-core-spine-1]
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/layer_3_loop/tasks/use_case_2/leaf_border_handoff.yml for sw-access-leaf-2

    TASK [layer_3_loop : CONFIG BGP LEAF BORDER HANDOFF IPV4 IPV6] ***************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item=sw-core-spine-1)

    TASK [layer_3_loop : CONFIG BGP LEAF BORDER HANDOFF VRF IPV4] ****************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]})

    TASK [layer_3_loop : CONFIG BGP LEAF BORDER HANDOFF VRF IPV6] ****************************************************************************************************************************
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]})
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]})
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]})
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]})
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]})

    TASK [layer_3_loop : SHOW CONFIG] ********************************************************************************************************************************************************
    ok: [sw-access-leaf-2]

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
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
                    " exit-address-family",
                    " !",
                    " address-family ipv6 vrf VRF_OVERLAY_2010",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    "  advertise l2vpn evpn",
                    " exit-address-family",
                    " !",
                    " address-family ipv4 vrf VRF_OVERLAY_2020",
                    "  advertise l2vpn evpn",
                    "  redistribute connected",
                    "  neighbor 10.9.8.7 remote-as 64513",
                    "  neighbor 10.9.8.7 activate",
                    "  neighbor 10.9.8.7 send-community both",
                    "  maximum-paths 4",
                    " exit-address-family",
                    " !",
                    " address-family ipv6 vrf VRF_OVERLAY_2020",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    "  advertise l2vpn evpn",
                    " exit-address-family",
                    " !",
                    " address-family ipv4 vrf VRF_OVERLAY_2030",
                    "  advertise l2vpn evpn",
                    "  redistribute connected",
                    "  neighbor 10.9.8.7 remote-as 64513",
                    "  neighbor 10.9.8.7 activate",
                    "  neighbor 10.9.8.7 send-community both",
                    "  maximum-paths 4",
                    " exit-address-family",
                    " !",
                    " address-family ipv6 vrf VRF_OVERLAY_2030",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    "  advertise l2vpn evpn",
                    " exit-address-family",
                    " !",
                    " address-family ipv4 vrf VRF_OVERLAY_2040",
                    "  advertise l2vpn evpn",
                    "  redistribute connected",
                    "  neighbor 10.9.8.7 remote-as 64513",
                    "  neighbor 10.9.8.7 activate",
                    "  neighbor 10.9.8.7 send-community both",
                    "  maximum-paths 4",
                    " exit-address-family",
                    " !",
                    " address-family ipv6 vrf VRF_OVERLAY_2040",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    "  advertise l2vpn evpn",
                    " exit-address-family",
                    " !",
                    " address-family ipv4 vrf VRF_OVERLAY_2050",
                    "  advertise l2vpn evpn",
                    "  redistribute connected",
                    "  neighbor 10.9.8.7 remote-as 64513",
                    "  neighbor 10.9.8.7 activate",
                    "  neighbor 10.9.8.7 send-community both",
                    "  maximum-paths 4",
                    " exit-address-family",
                    " !",
                    " address-family ipv6 vrf VRF_OVERLAY_2050",
                    "  redistribute connected",
                    "  maximum-paths 4",
                    "  advertise l2vpn evpn",
                    " exit-address-family"
                ]
            ]
        ]
    }

    TASK [layer_3_loop : include_tasks] ******************************************************************************************************************************************************
    skipping: [sw-access-leaf-1]
    skipping: [sw-access-leaf-2]
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/layer_3_loop/tasks/use_case_2/spine_bgp_ipv4_core.yml for sw-core-spine-1

    TASK [layer_3_loop : CONFIG BGP IPV4 SPINE] **********************************************************************************************************************************************
    changed: [sw-core-spine-1] => (item=sw-access-leaf-1)
    changed: [sw-core-spine-1] => (item=sw-access-leaf-2)

    TASK [layer_3_loop : SHOW CONFIG] ********************************************************************************************************************************************************
    ok: [sw-core-spine-1]

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
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

    TASK [layer_3_loop : SAVE CONFIG] ********************************************************************************************************************************************************
    ok: [sw-core-spine-1]
    ok: [sw-access-leaf-2]
    ok: [sw-access-leaf-1]

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
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

    TASK [layer_3_loop : BACKUP CONFIG] ******************************************************************************************************************************************************
    changed: [sw-core-spine-1]
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
    ok: [sw-access-leaf-1] => {
        "msg": [
            {
                "backup_path": "./backup/sw-access-leaf-1_config.2022-04-17@22:25:53",
                "changed": true,
                "date": "2022-04-17",
                "failed": false,
                "filename": "sw-access-leaf-1_config.2022-04-17@22:25:53",
                "shortname": "./backup/sw-access-leaf-1_config",
                "time": "22:25:53"
            }
        ]
    }
    ok: [sw-core-spine-1] => {
        "msg": [
            {
                "backup_path": "./backup/sw-core-spine-1_config.2022-04-17@22:25:53",
                "changed": true,
                "date": "2022-04-17",
                "failed": false,
                "filename": "sw-core-spine-1_config.2022-04-17@22:25:53",
                "shortname": "./backup/sw-core-spine-1_config",
                "time": "22:25:53"
            }
        ]
    }
    ok: [sw-access-leaf-2] => {
        "msg": [
            {
                "backup_path": "./backup/sw-access-leaf-2_config.2022-04-17@22:25:53",
                "changed": true,
                "date": "2022-04-17",
                "failed": false,
                "filename": "sw-access-leaf-2_config.2022-04-17@22:25:53",
                "shortname": "./backup/sw-access-leaf-2_config",
                "time": "22:25:53"
            }
        ]
    }

    TASK [layer_3_loop : include_tasks] ******************************************************************************************************************************************************
    skipping: [sw-access-leaf-1]
    skipping: [sw-access-leaf-2]
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/roles/common/tasks/verification_bgp.yml for sw-core-spine-1

    TASK [layer_3_loop : SHOW IP BGP L2VPN EVPN SUMMARY] *************************************************************************************************************************************
    ok: [sw-core-spine-1]

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
    ok: [sw-core-spine-1] => {
        "msg": [
            {
                "changed": false,
                "failed": false,
                "stdout": [
                    "Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd\n10.255.255.252  4        64512       2       2        1    0    0 00:00:06        0\n10.255.255.253  4        64512       2       2        1    0    0 00:00:11        0"
                ],
                "stdout_lines": [
                    [
                        "Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd",
                        "10.255.255.252  4        64512       2       2        1    0    0 00:00:06        0",
                        "10.255.255.253  4        64512       2       2        1    0    0 00:00:11        0"
                    ]
                ]
            }
        ]
    }

    TASK [layer_3_loop : ASSERT SUCCESS OR FAILURE BASED ON BGP OUTPUT: UPTIME > 00:00:00 OR NOT NEVER AND NEIGHBOORS LEAFS LISTED CORRECTLY] ************************************************
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

    TASK [layer_3_loop : DEBUG] **************************************************************************************************************************************************************
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

    PLAY RECAP *******************************************************************************************************************************************************************************
    sw-access-leaf-1           : ok=69   changed=23   unreachable=0    failed=0    skipped=6    rescued=0    ignored=0
    sw-access-leaf-2           : ok=75   changed=25   unreachable=0    failed=0    skipped=5    rescued=0    ignored=0
    sw-core-spine-1            : ok=22   changed=3    unreachable=0    failed=0    skipped=11   rescued=0    ignored=0
    ```

### Utility playbook execution example:
  * The network in this example has one spine and two leaf nodes, one simple leaf node, one border leaf node.
  * **The utility playbook execution should look like this**:
    ```SHELL
    ansible-playbook playbook_clear_all_config.yml

    PLAY [network] ***************************************************************************************************************************************************************************

    TASK [CLEAR CONFIG BGP IPV4 SPINE] *******************************************************************************************************************************************************
    [WARNING]: To ensure idempotency and correct diff the input configuration lines should be similar to how they appear if present in the running configuration on device
    changed: [sw-core-spine-1]
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [CLEAR CONFIG BGP LEAF BORDER HANDOFF IPV4 IPV6] ************************************************************************************************************************************
    changed: [sw-core-spine-1]
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [CLEAR CONFIG BGP IPV4 IPV6 LEAF] ***************************************************************************************************************************************************
    changed: [sw-core-spine-1]
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [CLEAR CONFIG NVE INTERFACE] ********************************************************************************************************************************************************
    ok: [sw-core-spine-1]
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]

    TASK [CLEAR CONFIG DHCP GLOBAL] **********************************************************************************************************************************************************
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]
    changed: [sw-core-spine-1]

    TASK [CLEAR L3/L2 EDGE VLAN IPV4 IPV6] ***************************************************************************************************************************************************
    skipping: [sw-core-spine-1]
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/playbook_clear_all_config_inner_loop.yml for sw-access-leaf-1 => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}]})
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/playbook_clear_all_config_inner_loop.yml for sw-access-leaf-1 => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}]})
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/playbook_clear_all_config_inner_loop.yml for sw-access-leaf-1 => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}]})
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/playbook_clear_all_config_inner_loop.yml for sw-access-leaf-1 => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}]})
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/playbook_clear_all_config_inner_loop.yml for sw-access-leaf-1 => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}]})
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/playbook_clear_all_config_inner_loop.yml for sw-access-leaf-2 => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]})
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/playbook_clear_all_config_inner_loop.yml for sw-access-leaf-2 => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]})
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/playbook_clear_all_config_inner_loop.yml for sw-access-leaf-2 => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]})
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/playbook_clear_all_config_inner_loop.yml for sw-access-leaf-2 => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]})
    included: /home/netadmin/bgp-evpn-ansible/example_use_cases/l3_loop/2/playbook_clear_all_config_inner_loop.yml for sw-access-leaf-2 => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]})

    TASK [CLEAR CONFIG L3 SVI INTERFACE EDGE VLAN] *******************************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48})
    changed: [sw-access-leaf-1] => (item={'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48})
    changed: [sw-access-leaf-1] => (item={'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48})

    TASK [CLEAR CONFIG L2 EDGE VLAN] *********************************************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48})
    changed: [sw-access-leaf-1] => (item={'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48})
    changed: [sw-access-leaf-1] => (item={'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48})

    TASK [CLEAR CONFIG L3 SVI INTERFACE EDGE VLAN] *******************************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'})
    changed: [sw-access-leaf-1] => (item={'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48})

    TASK [CLEAR CONFIG L2 EDGE VLAN] *********************************************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'})
    changed: [sw-access-leaf-1] => (item={'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48})

    TASK [CLEAR CONFIG L3 SVI INTERFACE EDGE VLAN] *******************************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48})

    TASK [CLEAR CONFIG L2 EDGE VLAN] *********************************************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48})

    TASK [CLEAR CONFIG L3 SVI INTERFACE EDGE VLAN] *******************************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48})

    TASK [CLEAR CONFIG L2 EDGE VLAN] *********************************************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48})

    TASK [CLEAR CONFIG L3 SVI INTERFACE EDGE VLAN] *******************************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48})

    TASK [CLEAR CONFIG L2 EDGE VLAN] *********************************************************************************************************************************************************
    changed: [sw-access-leaf-1] => (item={'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48})

    TASK [CLEAR CONFIG L3 SVI INTERFACE EDGE VLAN] *******************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48})
    changed: [sw-access-leaf-2] => (item={'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48})
    changed: [sw-access-leaf-2] => (item={'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48})

    TASK [CLEAR CONFIG L2 EDGE VLAN] *********************************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48})
    changed: [sw-access-leaf-2] => (item={'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48})
    changed: [sw-access-leaf-2] => (item={'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48})

    TASK [CLEAR CONFIG L3 SVI INTERFACE EDGE VLAN] *******************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48})
    changed: [sw-access-leaf-2] => (item={'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48})

    TASK [CLEAR CONFIG L2 EDGE VLAN] *********************************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48})
    changed: [sw-access-leaf-2] => (item={'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48})

    TASK [CLEAR CONFIG L3 SVI INTERFACE EDGE VLAN] *******************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48})

    TASK [CLEAR CONFIG L2 EDGE VLAN] *********************************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48})

    TASK [CLEAR CONFIG L3 SVI INTERFACE EDGE VLAN] *******************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48})

    TASK [CLEAR CONFIG L2 EDGE VLAN] *********************************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48})

    TASK [CLEAR CONFIG L3 SVI INTERFACE EDGE VLAN] *******************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48})

    TASK [CLEAR CONFIG L2 EDGE VLAN] *********************************************************************************************************************************************************
    changed: [sw-access-leaf-2] => (item={'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48})

    TASK [CLEAR CONFIG L3 SVI INTERFACE CORE VLAN] *******************************************************************************************************************************************
    skipping: [sw-core-spine-1]
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}]})

    TASK [CLEAR CONFIG L2 CORE VLAN MEMBER L3 VNI] *******************************************************************************************************************************************
    skipping: [sw-core-spine-1]
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}]})

    TASK [CLEAR CONFIG L2 CORE VLAN] *********************************************************************************************************************************************************
    skipping: [sw-core-spine-1]
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}]})

    TASK [CLEAR CONFIG VRF] ******************************************************************************************************************************************************************
    skipping: [sw-core-spine-1]
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}]})
    changed: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]})
    changed: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}]})

    TASK [WAIT FOR CLEAR CONFIG VRF] *********************************************************************************************************************************************************
    skipping: [sw-core-spine-1]
    ok: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.1', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8881', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.1', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9991', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.1', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1111', 'prefix': 48}]})
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2010', 'vrf_core_vlan_id': 2010, 'vrf_l3_vni_id': 20010, 'vrf_edge_vlan': [{'id': 10, 'ip': '10.10.10.2', 'mask': '255.255.255.0', 'ipv6': '1111:2222:3333:4444:5555:6666:7777:8882', 'prefix': 48}, {'id': 20, 'ip': '10.20.20.2', 'mask': '255.255.255.0', 'ipv6': '2222:3333:4444:5555:6666:7777:8888:9992', 'prefix': 48}, {'id': 30, 'ip': '10.30.30.2', 'mask': '255.255.255.0', 'ipv6': '3333:4444:5555:6666:7777:8888:9999:1112', 'prefix': 48}]})
    ok: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.1', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2221', 'prefix': 48, 'mask': '255.255.255.0'}, {'id': 50, 'ip': '10.50.50.1', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3331', 'prefix': 48}]})
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2020', 'vrf_core_vlan_id': 2020, 'vrf_l3_vni_id': 20020, 'vrf_edge_vlan': [{'id': 40, 'ip': '10.40.40.2', 'mask': '255.255.255.0', 'ipv6': '4444:5555:6666:7777:8888:9999:1111:2222', 'prefix': 48}, {'id': 50, 'ip': '10.50.50.2', 'mask': '255.255.255.0', 'ipv6': '5555:6666:7777:8888:9999:1111:2222:3332', 'prefix': 48}]})
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.2', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4442', 'prefix': 48}]})
    ok: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2030', 'vrf_core_vlan_id': 2030, 'vrf_l3_vni_id': 20030, 'vrf_edge_vlan': [{'id': 60, 'ip': '10.60.60.1', 'mask': '255.255.255.0', 'ipv6': '6666:7777:8888:9999:1111:2222:3333:4441', 'prefix': 48}]})
    ok: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.1', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5551', 'prefix': 48}]})
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2040', 'vrf_core_vlan_id': 2040, 'vrf_l3_vni_id': 20040, 'vrf_edge_vlan': [{'id': 70, 'ip': '10.70.70.2', 'mask': '255.255.255.0', 'ipv6': '7777:8888:9999:1111:2222:3333:4444:5552', 'prefix': 48}]})
    ok: [sw-access-leaf-1] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.1', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6661', 'prefix': 48}]})
    ok: [sw-access-leaf-2] => (item={'vrf_name': 'VRF_OVERLAY_2050', 'vrf_core_vlan_id': 2050, 'vrf_l3_vni_id': 20050, 'vrf_edge_vlan': [{'id': 80, 'ip': '10.80.80.2', 'mask': '255.255.255.0', 'ipv6': '8888:9999:1111:2222:3333:4444:5555:6662', 'prefix': 48}]})

    TASK [CLEAR CONFIG IPV6 ROUTING SUPPORT] *************************************************************************************************************************************************
    changed: [sw-access-leaf-2]
    changed: [sw-access-leaf-1]
    changed: [sw-core-spine-1]

    PLAY RECAP *******************************************************************************************************************************************************************************
    sw-access-leaf-1           : ok=26   changed=20   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    sw-access-leaf-2           : ok=26   changed=20   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    sw-core-spine-1            : ok=6    changed=5    unreachable=0    failed=0    skipped=6    rescued=0    ignored=0
    ```