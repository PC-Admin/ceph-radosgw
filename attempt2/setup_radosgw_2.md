
# Ceph RADOSGW Setup

Creating a simple object storage system with ceph-adm.

Generate wildcard SSL cert for *.bestozvapes.com

Have the grafana hosted at grafana.bestozvapes.com
Have the Ceph Dashboard hosted at ceph.bestozvapes.com
Have the Ceph RADOSGW hosted at s3.bestozvapes.com


## First create 3 hosts:

ceph01.bestozvapes.com
ceph02.bestozvapes.com
ceph03.bestozvapes.com


## Generate wildcard SSL cert for *.bestozvapes.com using LetsEncrypt

On an external VPS or bastion server with a public IP address, install certbot and generate a wildcard SSL cert for *.bestozvapes.com

```bash
root@ubuntu-s-1vcpu-1gb-sgp1-01:~# hostnamectl hostname bastion.bestozvapes.com

root@bastion:~# sudo apt install certbot -y
root@bastion:~# sudo certbot certonly --manual --preferred-challenges dns -d "*.bestozvapes.com"

No VM guests are running outdated hypervisor (qemu) binaries on this host.
root@bastion:~# sudo certbot certonly --manual --preferred-challenges dns -d "*.bestozvapes.com"
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Enter email address (used for urgent renewal and security notices)
 (Enter 'c' to cancel): michael_collins@tutanota.com

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.3-September-21-2022.pdf. You must
agree in order to register with the ACME server. Do you agree?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: yes

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Would you be willing, once your first certificate is successfully issued, to
share your email address with the Electronic Frontier Foundation, a founding
partner of the Let's Encrypt project and the non-profit organization that
develops Certbot? We'd like to send you email about our work encrypting the web,
EFF news, campaigns, and ways to support digital freedom.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: No
Account registered.
Requesting a certificate for *.bestozvapes.com

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please deploy a DNS TXT record under the name:

_acme-challenge.bestozvapes.com.

with the following value:

V0RnR9W8I8MYSSw5gCFcpPBnxAjq3Dtjusba1gl6kuQ

Before continuing, verify the TXT record has been deployed. Depending on the DNS
provider, this may take some time, from a few seconds to multiple minutes. You can
check if it has finished deploying with aid of online tools, such as the Google
Admin Toolbox: https://toolbox.googleapps.com/apps/dig/#TXT/_acme-challenge.bestozvapes.com.
Look for one or more bolded line(s) below the line ';ANSWER'. It should show the
value(s) you've just added.
```

Verify the TXT record has been deployed in a seperate terminal window:

```bash
root@bastion:~# dig TXT _acme-challenge.bestozvapes.com +short
"V0RnR9W8I8MYSSw5gCFcpPBnxAjq3Dtjusba1gl6kuQ"
```

and we continue...

```bash
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Press Enter to Continue

Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/bestozvapes.com/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/bestozvapes.com/privkey.pem
This certificate expires on 2024-03-29.
These files will be updated when the certificate renews.

NEXT STEPS:
- This certificate will not be renewed automatically. Autorenewal of --manual certificates requires the use of an authentication hook script (--manual-auth-hook) but one was not provided. To renew this certificate, repeat this same certbot command before the certificate's expiry date.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
 * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
 * Donating to EFF:                    https://eff.org/donate-le
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

```


## Deploy the Certificate to Ceph Hosts

Create a certs folder on each host:
```bash
root@ceph01:~# ls -la /var/lib/ceph/b4d18f96-a568-11ee-bd7b-2736aff7e7d1/
total 416
drwx------ 14 lxd        996   4096 Dec 28 10:24 .
drwxr-x---  3 ceph   ceph      4096 Dec 28 10:06 ..
drwx------  3 nobody nogroup   4096 Dec 28 10:07 alertmanager.ceph01
-rw-r--r--  1 root   root    366903 Dec 28 10:06 cephadm.8b92cafd937eb89681ee011f9e70f85937fd09c4bd61ed4a59981d275a1f255b
drwx------  2    167     167   4096 Dec 28 10:10 config
drwx------  3 lxd        996   4096 Dec 28 10:06 crash
drwx------  2    167     167   4096 Dec 28 10:07 crash.ceph01
drwxr-xr-x 11 root   root      4096 Dec 28 10:24 custom_config_files
drwx------  4 lxd        996   4096 Dec 30 06:56 grafana.ceph01
drwx------  2    167     167   4096 Dec 28 10:06 mgr.ceph01.izgani
drwx------  3    167     167   4096 Dec 30 07:57 mon.ceph01
drwx------  2 nobody nogroup   4096 Dec 28 10:07 node-exporter.ceph01
drwx------  2    167     167   4096 Dec 28 10:14 osd.0
drwx------  4 nobody nogroup   4096 Dec 28 10:08 prometheus.ceph01
drwx------  2    167     167   4096 Dec 28 10:24 rgw.test_rgw.ceph01.jtqjgh

root@ceph01:~# mkdir /var/lib/ceph/b4d18f96-a568-11ee-bd7b-2736aff7e7d1/certs

root@ceph01:~# ls -la /var/lib/ceph/b4d18f96-a568-11ee-bd7b-2736aff7e7d1/certs/
total 8
drwxr-xr-x  2 root root 4096 Dec 30 07:58 .
drwx------ 15 lxd   996 4096 Dec 30 07:58 ..
```

Copy the certs from the bastion server to the ceph hosts:

```bash
pcadmin@workstation:/tmp$ scp root@bastion.bestozvapes.com:/etc/letsencrypt/live/bestozvapes.com/fullchain.pem  /tmp/fullchain.pem
fullchain.pem                                                                                                                                                             100% 5522   111.8KB/s   00:00    
pcadmin@workstation:/tmp$ scp root@bastion.bestozvapes.com:/etc/letsencrypt/live/bestozvapes.com/privkey.pem  /tmp/privkey.pem
privkey.pem                   

pcadmin@workstation:/tmp$ scp ./fullchain.pem ceph01.bestozvapes.com:/var/lib/ceph/b4d18f96-a568-11ee-bd7b-2736aff7e7d1/certs/
fullchain.pem                                                                                                                                                             100% 5522     7.1MB/s   00:00    
pcadmin@workstation:/tmp$ scp ./privkey.pem ceph01.bestozvapes.com:/var/lib/ceph/b4d18f96-a568-11ee-bd7b-2736aff7e7d1/certs/
privkey.pem                                                                                                                                                               100% 1704     2.6MB/s   00:00    

root@ceph01:~# sudo chmod 644 /var/lib/ceph/b4d18f96-a568-11ee-bd7b-2736aff7e7d1/certs/fullchain.pem
root@ceph01:~# sudo chmod 640 /var/lib/ceph/b4d18f96-a568-11ee-bd7b-2736aff7e7d1/certs/privkey.pem 

root@ceph01:~# ls -la /var/lib/ceph/b4d18f96-a568-11ee-bd7b-2736aff7e7d1/certs/
total 20
drwxr-xr-x  2 root root 4096 Dec 30 07:59 .
drwx------ 15 lxd   996 4096 Dec 30 07:58 ..
-rw-r--r--  1 root root 5522 Dec 30 07:59 fullchain.pem
-rw-r-----  1 root root 1704 Dec 30 07:59 privkey.pem
```


## Configure Grafana SSL with ceph config-key PROBABLY WRONG

```bash
root@ceph01:~# ceph config-key set mgr/cephadm/{hostname}/grafana_crt -i /var/lib/ceph/b4d18f96-a568-11ee-bd7b-2736aff7e7d1/certs/fullchain.pem 
set mgr/cephadm/{hostname}/grafana_crt
root@ceph01:~# ceph config-key set mgr/cephadm/{hostname}/grafana_key -i /var/lib/ceph/b4d18f96-a568-11ee-bd7b-2736aff7e7d1/certs/privkey.pem 
set mgr/cephadm/{hostname}/grafana_key
```

Then reconfigure the Grafana container:
```bash
root@ceph01:~# ceph orch reconfig grafana
Scheduled to reconfig grafana.ceph01 on host 'ceph01

root@ceph01:~# ceph orch restart grafana
Scheduled to restart grafana.ceph01 on host 'ceph01'
```

still using self-signed certs... attempting again:
```bash
root@ceph01:~# ceph config-key set mgr/cephadm/ceph01/grafana_crt -i /var/lib/ceph/b4d18f96-a568-11ee-bd7b-2736aff7e7d1/certs/fullchain.pem
set mgr/cephadm/ceph01/grafana_crt
root@ceph01:~# ceph config-key set mgr/cephadm/ceph01/grafana_key -i /var/lib/ceph/b4d18f96-a568-11ee-bd7b-2736aff7e7d1/certs/privkey.pem
set mgr/cephadm/ceph01/grafana_key

root@ceph01:~# ceph config-key get mgr/cephadm/ceph01/grafana_crt

-----BEGIN CERTIFICATE-----
MIIE8DCCA9igAwIBAgISA2rCstOyzmggYTCOGrQ/MnoSMA0GCSqGSIb3DQEBCwUA
MDIxCzAJBgNVBAYTAlVTMRYwFAYDVQQKEw1MZXQncyBFbmNyeXB0MQswCQYDVQQD
...
```


## The real solutioins for Grafana URL and SSL

```bash
root@ceph01:~# ceph dashboard get-grafana-api-ssl-verify
True

root@ceph01:~# ceph dashboard set-ssl-certificate -i /var/lib/ceph/b4d18f96-a568-11ee-bd7b-2736aff7e7d1/certs/fullchain.pem
SSL certificate updated

root@ceph01:~# ceph dashboard set-ssl-certificate-key -i /var/lib/ceph/b4d18f96-a568-11ee-bd7b-2736aff7e7d1/certs/privkey.pem
SSL certificate key updated
```

nope :S


```bash
root@ceph01:~# ceph dashboard get-grafana-api-url
https://ceph01:3000
root@ceph01:~# ceph dashboard get-grafana-frontend-api-url
grafana.bestozvapes.com

root@ceph01:~# ceph dashboard set-grafana-api-url https://grafana.bestozvapes.com
Option GRAFANA_API_URL updated
root@ceph01:~# ceph dashboard set-grafana-frontend-api-url https://grafana.bestozvapes.com
Option GRAFANA_FRONTEND_API_URL updated

root@ceph01:~# ceph dashboard get-grafana-api-url
https://grafana.bestozvapes.com
root@ceph01:~# ceph dashboard get-grafana-frontend-api-url
https://grafana.bestozvapes.com

root@ceph01:~# ceph orch reconfig grafana
Scheduled to reconfig grafana.ceph01 on host 'ceph01'
```

Yay it started working!!! :)



## Change the URL of the Ceph Dashboard

Create a URL for the Ceph Dashboard, 'ceph.bestozvapes.com' and point it to the Ceph01 host: 10.1.50.1

Hmm interestingly the Ceph Dashboard is now available at: https://grafana.bestozvapes.com:8443/#/login

root@ceph01:~# ceph dashboard 
Display all 142 possibilities? (y or n)
...
    get-alertmanager-api-url
    get-grafana-api-url
    get-grafana-dashboard-api-url
    get-prometheus-api-url
    reset-alertmanager-api-url
    reset-grafana-api-url
    reset-grafana-dashboard-api-url
    reset-prometheus-api-url
    set-alertmanager-api-url
    set-grafana-api-url
    set-grafana-dashboard-api-url
    set-prometheus-api-url



```
root@ceph01:~# ceph config set mgr mgr/dashboard/server_addr https://ceph.bestozvapes.com
root@ceph01:~# ceph config get mgr mgr/dashboard/server_addr
https://ceph.bestozvapes.com


root@ceph01:~# ceph dashboard set-ssl-certificate mgr.ceph01.izgani -i /var/lib/ceph/b4d18f96-a568-11ee-bd7b-2736aff7e7d1/certs/fullchain.pem
SSL certificate updated
root@ceph01:~# ceph dashboard set-ssl-certificate-key mgr.ceph01.izgani -i /var/lib/ceph/b4d18f96-a568-11ee-bd7b-2736aff7e7d1/certs/privkey.pem 
SSL certificate key updated

root@ceph01:~# ceph orch reconfig mgr
Scheduled to reconfig mgr.ceph01.izgani on host 'ceph01'
Scheduled to reconfig mgr.ceph02.csojvm on host 'ceph02'
Scheduled to reconfig mgr.ceph03.ljnzsj on host 'ceph03'
```






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

