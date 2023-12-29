
# Mounting a Ceph Object Storage bucket using s3fs

## Edit /etc/hosts on local machine to point to ceph01

```
pcadmin@workstation:~$ cat /etc/hosts
...
10.1.50.1 ceph01
10.1.50.1 bucket1.ceph01
```


## Install s3fs and mount the bucket

```
pcadmin@workstation:~$ sudo apt install s3fs
pcadmin@workstation:~$ sudo mkdir /mnt/cephobj
pcadmin@workstation:~$ sudo chown pcadmin:pcadmin /mnt/cephobj/
pcadmin@workstation:~$ echo 829PO8ABIDBXCRXWDRKQ:kyxEWNRWotUiR4qgmFTwDCMwPqS9AttmSOM0JEKy > ${HOME}/.s3fs_passwd
pcadmin@workstation:~$ chmod 600 ${HOME}/.s3fs_passwd

pcadmin@workstation:~$ s3fs bucket1 /mnt/cephobj -o passwd_file=${HOME}/.s3fs_passwd -o url=http://ceph01:8000
```

Observe if the mount worked:
```
pcadmin@workstation:~$ mount | grep ceph
s3fs on /mnt/cephobj type fuse.s3fs (rw,nosuid,nodev,relatime,user_id=1000,group_id=1000)

pcadmin@workstation:~$ findmnt -t fuse.s3fs
TARGET       SOURCE FSTYPE    OPTIONS
/mnt/cephobj s3fs   fuse.s3fs rw,nosuid,nodev,relatime,user_id=1000,group_id=1000

pcadmin@workstation:~$ df -h
Filesystem                        Size  Used Avail Use% Mounted on
...
s3fs                               16E     0   16E   0% /mnt/cephobj
```

Strangely touching a file is creating new buckets... I suspect the mount command is wrong!

```
pcadmin@workstation:~$ s3fs /mnt/cephobj -o passwd_file=${HOME}/.s3fs_passwd -o url=http://ceph01:8000 -o bucket=bucket1
pcadmin@workstation:~$ mount | grep ceph
s3fs on /mnt/cephobj type fuse.s3fs (rw,nosuid,nodev,relatime,user_id=1000,group_id=1000)
```

Nope same behaviour...

