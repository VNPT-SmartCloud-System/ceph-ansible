# Hướng dẫn cài đặt Ceph sử dụng Ceph-ansible
## 1. Git Clone

## 2. Sửa file inventory, thay đổi `IP` và `hostname` theo topo của mình
```sh
[mons]
ceph01 ansible_host=192.168.40.141
ceph02 ansible_host=192.168.40.142
ceph03 ansible_host=192.168.40.143

[osds]
ceph01 ansible_host=192.168.40.141
ceph02 ansible_host=192.168.40.142
ceph03 ansible_host=192.168.40.143

[mgrs]
ceph01 ansible_host=192.168.40.141
ceph02 ansible_host=192.168.40.142
ceph03 ansible_host=192.168.40.143
```
## 3. Sửa file group_vars/all.yml như sau
```sh
dummy:
ceph_origin: repository
ceph_repository: community
ntp_service_enabled: true
ceph_repository_type: cdn
ceph_stable_release: nautilus
#Sua lai dai network cho phu hop
monitor_address_block: 192.168.40.0/24
public_network: 192.168.40.0/24
cluster_network: 10.10.10.0/24
# Khai bao backend cho Ceph
osd_objectstore: bluestore
devices:
  - '/dev/vdb'
  - '/dev/vdc'
dedicated_devices:
  - '/dev/vdd'
dashboard_enabled: yes
configure_firewall: no
openstack_config: yes
ceph_conf_overrides:
 global:
   osd_pool_default_size: 2
   osd_pool_default_min_size: 1
   osd_pool_default_pg_num: 64
openstack_pools:
  - "{{ openstack_glance_pool }}"
  - "{{ openstack_cinder_pool }}"
  - "{{ openstack_nova_pool }}"
  - "{{ openstack_cinder_backup_pool }}"
dashboard_protocol: https
dashboard_admin_user: admin
dashboard_admin_password: admin
```
