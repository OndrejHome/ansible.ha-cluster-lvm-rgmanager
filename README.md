ha_cluster_lvm_rgmanager
=========

Role for enabling Highly Available LVM (HA-LVM) configuration in **rgmanager** cluster.
This role can also create LVs and VGs for both tagging and clvm variant, check the examples to see how.

This role is aimed at (this moment pretty old) **rgmanager clusters only**. For pacemaker cluster role check out the [ondrejhome.ha-cluster-lvm](https://github.com/OndrejHome/ansible.ha-cluster-lvm) role instead.

Requirements
------------

RHEL: It is expected that machines will already be registered and subscribed for access to 'High Availability' or 'Resilient storage' channels.

Role Variables
--------------

  - type of HA-LVM, possible options are 'tagging' or 'clvm'. Default is 'tagging'
  
    ```
    HALVMtype: 'tagging'
    ```

  - (optional) - this is applied only when 'tagging' mode is selected. List of VGs that should be included in 'volume_list' additionally to VG on which root filesystem resides. By default this list is empty, below example shows how to specify the list of VGs.
  
    ```
    local_vg_list: [ 'vg1', 'vg2' ]
    ```

  - (RHEL only) enable the repositories containint packages needed
    ```
    enable_repos: true
    ```

  - (RHEL only) enable the extended update (EUS) repositories containint packages needed
    ```
    enable_eus_repos: false
    ```


Example Playbook
----------------

Example playbook for tagging HA-LVM. No LV creation.

    - hosts: servers
      roles:
         - { role: 'ondrejhome.ha_cluster_lvm_rgmanager' }

Example playbook for tagging HA-LVM with extra local VG names 'vg_local'. No LV creation.

    - hosts: servers
      roles:
         - { role: 'ondrejhome.ha_cluster_lvm_rgmanager', local_vg_list: [ 'vg_local' ] }

Example playbook for clvm variant of HA-LVM. No LV creation.

    - hosts: servers
      roles:
         - { role: 'ondrejhome.ha_cluster_lvm_rgmanager', HALVMtype: 'clvm' }

Example of playbook for tagged variant with one VG and one LV on shared VG.

    - hosts: servers
      vars:
        tagging_vgs:
          - name: 'vg_shared'
            pvs: '/dev/sdb'
            lvs:
              - name: 'lv_name1'
                size: '200M'
      roles:
         - { role: 'ondrejhome.ha_cluster_lvm_rgmanager' }

Example of playbook for clvm variant with one VG and one LV on shared VG. IMPORTANT: shared clvm VG must be included in the 'local_vg_list' list, otherwise it won't be possible to create LVs on it.

    - hosts: servers
      vars:
        local_vg_list:
          - 'vg_clvm'
        clvm_vgs:
          - name: 'vg_clvm'
            pvs: '/dev/sdb'
            lvs:
              - name: 'lv_name2'
                size: '200M'
      roles:
         - { role: 'ondrejhome.ha_cluster_lvm_rgmanager', HALVMtype: 'clvm' }


Example playbook for clvm variant combined with tagging variant and creation of VGs/LVs for both clustered and tagging VGs.

    - hosts: servers
      vars:
        local_vg_list:
          - 'vg_clvm'
        clvm_vgs:
          - name: 'vg_clvm'
            pvs: '/dev/sdb'
            lvs:
              - name: 'lv_name3'
                size: '200M'
              - name: 'lv_name4'
                size: '100M'
        tagging_vgs:
          - name: 'vg_shared'
            pvs: '/dev/sdc'
            lvs:
              - name: 'lv_name5'
                size: '200M'
              - name: 'lv_name6'
                size: '100M'
      roles:
         - { role: 'ondrejhome.ha_cluster_lvm_rgmanager', HALVMtype: 'clvm' }


License
-------

GPLv3

Author Information
------------------

- 2022-09: role was tested with all examples from this README on CentOS 6.10 and RHEL 6.10.

To get in touch with author you can use email ondrej-xa2iel8u@famera.cz or create a issue on github when requesting some feature.
