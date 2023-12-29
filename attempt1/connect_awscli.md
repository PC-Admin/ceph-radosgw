
# Connecting awscli to RADOS Gateway


## Install awscli
```
pcadmin@workstation:~$ sudo apt install awscli
```


## Configure awscli
```
pcadmin@workstation:~$ aws configure
AWS Access Key ID [None]: 829PO8ABIDBXCRXWDRKQ
AWS Secret Access Key [None]: kyxEWNRWotUiR4qgmFTwDCMwPqS9AttmSOM0JEKy
Default region name [None]: 
Default output format [None]: 

pcadmin@workstation:~$ aws --endpoint-url http://ceph01:8000 s3 mb s3://bucket2
make_bucket: bucket2
```


## List buckets
```
pcadmin@workstation:~$ aws --endpoint-url http://ceph01:8000 s3 ls
2023-12-28 18:58:55 bucket1
2023-12-29 12:29:16 bucket2
```


## Get Bucket Policy
```
pcadmin@workstation:~$ aws --endpoint-url http://ceph01:8000 s3api get-bucket-policy --bucket bucket1

An error occurred (NoSuchBucketPolicy) when calling the GetBucketPolicy operation: The bucket policy does not exist
```


## Put Object in Bucket
```
pcadmin@workstation:~$ echo "Hello World" > hello.txt
pcadmin@workstation:~$ aws --endpoint-url http://ceph01:8000 s3 cp hello.txt s3://bucket1
upload: ./hello.txt to s3://bucket1/hello.txt
pcadmin@workstation:~$ aws --endpoint-url http://ceph01:8000 s3 cp /home/pcadmin/Downloads/IMG_6210.mp4 s3://bucket1
upload: Downloads/IMG_6210.mp4 to s3://bucket1/IMG_6210.mp4  
```


## List objects in bucket
```
pcadmin@workstation:~$ aws --endpoint-url http://ceph01:8000 s3 ls s3://bucket1
2023-12-29 12:41:39   26895361 IMG_6210.mp4
2023-12-29 12:32:37         12 hello.txt
```

