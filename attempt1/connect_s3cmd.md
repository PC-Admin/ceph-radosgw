
# Connect s3cmd to S3 RADOS Gateway

## Install s3cmd
```
pcadmin@workstation:~$ sudo apt install s3cmd
pcadmin@workstation:~$ s3cmd --version
s3cmd version 2.2.0
```


## Ensure DNS is configured
```
pcadmin@workstation:~$ cat /etc/hosts
...
10.1.50.1 ceph01
10.1.50.1 bucket1.ceph01
```


## Configure s3cmd
```
pcadmin@workstation:~$ s3cmd --configure

Enter new values or accept defaults in brackets with Enter.
Refer to user manual for detailed description of all options.

Access key and Secret key are your identifiers for Amazon S3. Leave them empty for using the env variables.
Access Key: 829PO8ABIDBXCRXWDRKQ
Secret Key: kyxEWNRWotUiR4qgmFTwDCMwPqS9AttmSOM0JEKy
Default Region [US]: 

Use "s3.amazonaws.com" for S3 Endpoint and not modify it to the target Amazon S3.
S3 Endpoint [s3.amazonaws.com]: ceph01:8000

Use "%(bucket)s.s3.amazonaws.com" to the target Amazon S3. "%(bucket)s" and "%(location)s" vars can be used
if the target S3 system supports dns based buckets.
DNS-style bucket+hostname:port template for accessing a bucket [%(bucket)s.s3.amazonaws.com]: %(bucket)s.ceph01:8000 

Encryption password is used to protect your files from reading
by unauthorized persons while in transfer to S3
Encryption password: 
Path to GPG program [/usr/bin/gpg]: 

When using secure HTTPS protocol all communication with Amazon S3
servers is protected from 3rd party eavesdropping. This method is
slower than plain HTTP, and can only be proxied with Python 2.7 or newer
Use HTTPS protocol [Yes]: No

On some networks all internet access must go through a HTTP proxy.
Try setting it here if you can't connect to S3 directly
HTTP Proxy server name: 

New settings:
  Access Key: 829PO8ABIDBXCRXWDRKQ
  Secret Key: kyxEWNRWotUiR4qgmFTwDCMwPqS9AttmSOM0JEKy
  Default Region: US
  S3 Endpoint: ceph01:8000
  DNS-style bucket+hostname:port template for accessing a bucket: %(bucket)s.ceph01:8000
  Encryption password: 
  Path to GPG program: /usr/bin/gpg
  Use HTTPS protocol: False
  HTTP Proxy server name: 
  HTTP Proxy server port: 0

Test access with supplied credentials? [Y/n] y
Please wait, attempting to list all buckets...
Success. Your access key and secret key worked fine :-)

Now verifying that encryption works...
Not configured. Never mind.

Save settings? [y/N] y
Configuration saved to '/home/pcadmin/.s3cfg'
```



