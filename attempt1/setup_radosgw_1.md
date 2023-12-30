
# Ceph RADOSGW Setup

Creating a simple object storage system with ceph-adm.


## First create 3 hosts:

ceph01.bestozvapes.com
ceph02.bestozvapes.com
ceph03.bestozvapes.com


## Perform Initial Setup

`pcadmin@workstation:~/cephadm-ansible$ ansible-playbook -i ./inventory/hosts cephadm-preflight.yml --flush-cache`


## Running the Bootstrap Command

```bash
root@ceph01:~# cephadm bootstrap --mon-ip 10.1.50.1
...

Ceph Dashboard is now available at:

	     URL: https://ceph01:8443/
	    User: admin
	Password: 10xa5ll9f6

...

You can access the Ceph CLI as following in case of multi-cluster or non-default config:

	sudo /usr/sbin/cephadm shell --fsid b4d18f96-a568-11ee-bd7b-2736aff7e7d1 -c /etc/ceph/ceph.conf -k /etc/ceph/ceph.client.admin.keyring

Or, if you are only running a single cluster on this host:

	sudo /usr/sbin/cephadm shell 
```


## Lost Password

If you loose the password you can set it from a files contents like so:
```bash
root@ceph01:~# nano ./admin-password
root@ceph01:~# ceph dashboard ac-user-set-password admin -i ./admin-password
```


## Cephadm Adding Hosts

https://docs.ceph.com/en/latest/cephadm/host-management/#cephadm-adding-hosts

First we need to copy over the clusters SSH key to every other hosts...

```bash
root@ceph01:~# cat /etc/ceph/ceph.pub
ssh-rsa AAAAB3NzaC1yc2...

root@ceph02:~# echo 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCwtrKW+pLmIWHGdd9tI9GWLdzLjENPZvM+jZoWmCRbC2RKDlz8zI1osVOCMqIGO0PathXlE6jpjaFf2soFX1ijoM3gZ8frsTg6FeLqdr2Ctp6NJ24ZPBPoMiR8xWto0/eCyQ3Rno/nFQKW7kWe5dsLeUnfQdU3SpPq8QN+AKT2ayt1d0H/DQlgZqKqf1HFtGuytGMaCTCQf1tP250UCmUb8HEdIvHl0t+5WaNIjIgjrKhQEeB3u+1miyQna6X8/kmhvnQL/fC7+hHeoHazNNC3PkBAjgYshV16V8Xbzga/rYgqmrTuZVLRSCXYHpPgYPIlZEp2AHrsh3m57nxJRqDUOehtz4zJ/5LlYhYH2pOcn10D7RtmtbZxtQWOKlEJfUu9FAk+APBvvgmA8gxqVGobUePR0oATx8n6JYi3mf26FSnpgCs2Be8v7QV+q7sooyRaCYNVXKTVr12LQfnERsMdBCcABJ9uEgmgyYIgBNt9Yz+RgQp3l+XofI5Eo56er0E= ceph-b4d18f96-a568-11ee-bd7b-2736aff7e7d1' >> ~/.ssh/authorized_keys

root@ceph03:~# echo 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCwtrKW+pLmIWHGdd9tI9GWLdzLjENPZvM+jZoWmCRbC2RKDlz8zI1osVOCMqIGO0PathXlE6jpjaFf2soFX1ijoM3gZ8frsTg6FeLqdr2Ctp6NJ24ZPBPoMiR8xWto0/eCyQ3Rno/nFQKW7kWe5dsLeUnfQdU3SpPq8QN+AKT2ayt1d0H/DQlgZqKqf1HFtGuytGMaCTCQf1tP250UCmUb8HEdIvHl0t+5WaNIjIgjrKhQEeB3u+1miyQna6X8/kmhvnQL/fC7+hHeoHazNNC3PkBAjgYshV16V8Xbzga/rYgqmrTuZVLRSCXYHpPgYPIlZEp2AHrsh3m57nxJRqDUOehtz4zJ/5LlYhYH2pOcn10D7RtmtbZxtQWOKlEJfUu9FAk+APBvvgmA8gxqVGobUePR0oATx8n6JYi3mf26FSnpgCs2Be8v7QV+q7sooyRaCYNVXKTVr12LQfnERsMdBCcABJ9uEgmgyYIgBNt9Yz+RgQp3l+XofI5Eo56er0E= ceph-b4d18f96-a568-11ee-bd7b-2736aff7e7d1' >> ~/.ssh/authorized_keys
```

Then we can tell our bootstrap node add these other hosts.
```bash
root@ceph01:~# ceph orch host ls
HOST    ADDR       LABELS  STATUS  
ceph01  10.1.50.1  _admin          
1 hosts in cluster

root@ceph01:~# ceph orch host add ceph02 10.1.50.2 _admin
Added host 'ceph02' with addr '10.1.50.2'
root@ceph01:~# ceph orch host add ceph03 10.1.50.3 _admin
Added host 'ceph03' with addr '10.1.50.3'

root@ceph01:~# ceph orch host ls
HOST    ADDR       LABELS  STATUS  
ceph01  10.1.50.1  _admin          
ceph02  10.1.50.2  _admin          
ceph03  10.1.50.3  _admin          
3 hosts in cluster
```


## Ensure 3x Monitors Exist on the Right Hosts

```bash
root@ceph01:~# ceph -s
  cluster:
    id:     b4d18f96-a568-11ee-bd7b-2736aff7e7d1
    health: HEALTH_WARN
            OSD count 0 < osd_pool_default_size 3
 
  services:
    mon: 3 daemons, quorum ceph01,ceph02,ceph03 (age 82s)
    mgr: ceph01.izgani(active, since 3m), standbys: ceph02.csojvm
    osd: 0 osds: 0 up, 0 in
 
  data:
    pools:   0 pools, 0 pgs
    objects: 0 objects, 0 B
    usage:   0 B used, 0 B / 0 B avail
    pgs: 
```

Yup, 3x monitors in the right places.


## Cool, Let's Add Some OSDs!

/dev/disk/by-id/scsi-0QEMU_QEMU_HARDDISK_drive-scsi1 (64GB) on all 3x hosts.

```bash
root@ceph01:~# ceph orch daemon add osd ceph01:/dev/disk/by-id/scsi-0QEMU_QEMU_HARDDISK_drive-scsi1
Created osd(s) 0 on host 'ceph01'
root@ceph01:~# ceph orch daemon add osd ceph02:/dev/disk/by-id/scsi-0QEMU_QEMU_HARDDISK_drive-scsi1
Created osd(s) 1 on host 'ceph02'
root@ceph01:~# ceph orch daemon add osd ceph03:/dev/disk/by-id/scsi-0QEMU_QEMU_HARDDISK_drive-scsi1
Created osd(s) 2 on host 'ceph03'

root@ceph01:~# ceph -s
  cluster:
    id:     b4d18f96-a568-11ee-bd7b-2736aff7e7d1
    health: HEALTH_OK
 
  services:
    mon: 3 daemons, quorum ceph01,ceph02,ceph03 (age 4m)
    mgr: ceph01.izgani(active, since 6m), standbys: ceph02.csojvm
    osd: 3 osds: 3 up (since 4s), 3 in (since 23s)
 
  data:
    pools:   0 pools, 0 pgs
    objects: 0 objects, 0 B
    usage:   1.2 GiB used, 191 GiB / 192 GiB avail
    pgs: 
```


## Looks good, lets make all 3x hosts a manager

```bash
root@ceph01:~# ceph orch apply mgr ceph01,ceph02,ceph03
Scheduled mgr update...
```


## Label Designated Gateways

```bash
root@ceph01:~# ceph orch host label add ceph01 rgw
Added label rgw to host ceph01
root@ceph01:~# ceph orch host label add ceph02 rgw
Added label rgw to host ceph02
root@ceph01:~# ceph orch host label add ceph03 rgw
Added label rgw to host ceph03
```


## Deploy RADOS Gateway

```bash
root@ceph01:~# ceph orch apply rgw test_rgw --placement="label:rgw" --port=8000
Scheduled rgw.test_rgw update...

root@ceph01:~# ceph -s
  cluster:
    id:     b4d18f96-a568-11ee-bd7b-2736aff7e7d1
    health: HEALTH_OK
 
  services:
    mon: 3 daemons, quorum ceph01,ceph02,ceph03 (age 14m)
    mgr: ceph01.izgani(active, since 16m), standbys: ceph02.csojvm, ceph03.ljnzsj
    osd: 3 osds: 3 up (since 10m), 3 in (since 10m)
    rgw: 3 daemons active (3 hosts, 1 zones)
 
  data:
    pools:   5 pools, 5 pgs
    objects: 196 objects, 583 KiB
    usage:   885 MiB used, 191 GiB / 192 GiB avail
    pgs:     5 active+clean
```


## Ensure rgw is running on all 3x hosts

```bash
root@ceph01:~# ceph orch ps | grep rgw
rgw.test_rgw.ceph01.jtqjgh  ceph01  *:8000       running (52s)    47s ago  52s    16.6M        -  17.2.7   4c9b44e95067  c835bd88235d  
rgw.test_rgw.ceph02.beryyn  ceph02  *:8000       running (53s)    47s ago  53s    16.3M        -  17.2.7   4c9b44e95067  37ac0a562013  
rgw.test_rgw.ceph03.xfrpce  ceph03  *:8000       running (54s)    47s ago  54s    16.2M        -  17.2.7   4c9b44e95067  b02144fd9a6c
```


## Create a User with Access and Secret Keys

```bash
root@ceph01:~# radosgw-admin user create --uid="myuser" --display-name="My User"
{
    "user_id": "myuser",
    "display_name": "My User",
    "email": "",
    "suspended": 0,
    "max_buckets": 1000,
    "subusers": [],
    "keys": [
        {
            "user": "myuser",
            "access_key": "829PO8ABIDBXCRXWDRKQ",
            "secret_key": "kyxEWNRWotUiR4qgmFTwDCMwPqS9AttmSOM0JEKy"
        }
    ],
    "swift_keys": [],
    "caps": [],
    "op_mask": "read, write, delete",
    "default_placement": "",
    "default_storage_class": "",
    "placement_tags": [],
    "bucket_quota": {
        "enabled": false,
        "check_on_raw": false,
        "max_size": -1,
        "max_size_kb": 0,
        "max_objects": -1
    },
    "user_quota": {
        "enabled": false,
        "check_on_raw": false,
        "max_size": -1,
        "max_size_kb": 0,
        "max_objects": -1
    },
    "temp_url_keys": [],
    "type": "rgw",
    "mfa_ids": []
}
```


## Create a Bucket

Through the Ceph Dashboard GUI, create a bucket called 'bucket1' for user 'myuser'.

![Screenshot from 2023-12-28 18-58-52](https://github.com/PC-Admin/ceph-radosgw/assets/29645145/1efe4b74-8d11-46e6-a084-3bb53f1466d3)
