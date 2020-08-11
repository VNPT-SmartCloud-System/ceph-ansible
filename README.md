# Hướng dẫn cài đặt Ceph Nautilus sử dụng Ceph-ansible
## 1. Git Clone
```sh
https://github.com/VNPT-SmartCloud-System/ceph-ansible.git
cd ceph-ansible
```
## 2. Sửa file inventory, thay đổi `IP` và `hostname` theo topo của mình

Dải IP trong file inventory là dải management network

```sh
$ vim inventory.yml
[mons]
ceph01 ansible_host=192.168.20.141
ceph02 ansible_host=192.168.20.142
ceph03 ansible_host=192.168.20.143

[osds]
ceph01 ansible_host=192.168.20.141
ceph02 ansible_host=192.168.20.142
ceph03 ansible_host=192.168.20.143

[mgrs]
ceph01 ansible_host=192.168.20.141
ceph02 ansible_host=192.168.20.142
ceph03 ansible_host=192.168.20.143
```
## 3. Sửa file group_vars/all.yml như sau
```sh
$ vim group_vars/all.yml
```
### 3.1 Khai báo Network cho Ceph
```sh
monitor_address_block: 192.168.40.0/24
public_network: 192.168.40.0/24
cluster_network: 10.10.10.0/24
```
Trong đó:
- public_network: Dải Network Public
- cluster_network: Replicate dữ liệu giữa các OSD
- monitor_address_block: Dải network cho Monitor

### 3.2 Khai báo backend cho Ceph
```sh
osd_objectstore: filestore
```
### 3.3 Khai báo OSD cho Ceph
```sh
devices:
  - '/dev/vdb'
  - '/dev/vdc'
dedicated_devices:
  - '/dev/vdd'
```
Trong đó:
- devices: Danh sách device sử dụng làm OSD
- dedicated_devices: Danh sách device sử dụng làm filestore

### 3.4 Khai báo cấu hình cho Ceph
```sh
ceph_conf_overrides:
 global:
   osd_pool_default_size: 2
   osd_pool_default_min_size: 1
   osd_pool_default_pg_num: 64
```
### 3.5 Khai báo pool cho Openstack
```sh
openstack_pools:
  - "{{ openstack_glance_pool }}"
  - "{{ openstack_cinder_pool }}"
  - "{{ openstack_nova_pool }}"
  - "{{ openstack_cinder_backup_pool }}"
```
### 3.6 Khai báo account/password cho Ceph-dashboard
```sh
dashboard_admin_user: admin
dashboard_admin_password: admin
```
## 4. Thực hiện deploy
```sh
ansible-playbook playbook.yml -i inventory.yml
```
