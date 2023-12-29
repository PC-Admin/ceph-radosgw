
# Running Hotsauce S3 Benchmark

How to install and run: https://github.com/markhpc/hsbench


## Install hsbench

First install the latest version of Go, follow the steps here: https://go.dev/doc/install
```bash
pcadmin@workstation:~$ go version
go version go1.21.5 linux/amd64
```

```
pcadmin@workstation:~$ go get github.com/markhpc/hsbench
go: go.mod file not found in current directory or any parent directory.
	'go get' is no longer supported outside a module.
	To build and install a command, use 'go install' with a version,
	like 'go install example.com/cmd@latest'
	For more information, see https://golang.org/doc/go-get-install-deprecation
	or run 'go help get' or 'go help install'.

pcadmin@workstation:~$ go install github.com/markhpc/hsbench
go: 'go install' requires a version when current directory is not in a module
	Try 'go install github.com/markhpc/hsbench@latest' to install the latest version

pcadmin@workstation:~$ go install github.com/markhpc/hsbench@latest
go: downloading github.com/markhpc/hsbench v0.0.0-20210617120320-7d667f7871ed
go: finding module for package github.com/aws/aws-sdk-go/service/s3
go: finding module for package github.com/aws/aws-sdk-go/aws/session
go: finding module for package code.cloudfoundry.org/bytefmt
go: finding module for package github.com/aws/aws-sdk-go/aws
go: finding module for package github.com/aws/aws-sdk-go/aws/credentials
go: downloading github.com/aws/aws-sdk-go v1.49.12
go: downloading code.cloudfoundry.org/bytefmt v0.0.0-20231017140541-3b893ed0421b
go: found code.cloudfoundry.org/bytefmt in code.cloudfoundry.org/bytefmt v0.0.0-20231017140541-3b893ed0421b
go: found github.com/aws/aws-sdk-go/aws in github.com/aws/aws-sdk-go v1.49.12
go: found github.com/aws/aws-sdk-go/aws/credentials in github.com/aws/aws-sdk-go v1.49.12
go: found github.com/aws/aws-sdk-go/aws/session in github.com/aws/aws-sdk-go v1.49.12
go: found github.com/aws/aws-sdk-go/service/s3 in github.com/aws/aws-sdk-go v1.49.12
go: downloading github.com/jmespath/go-jmespath v0.4.0

pcadmin@workstation:~$ cd ~/go/bin/
pcadmin@workstation:~/go/bin$ ls
hsbench

pcadmin@workstation:~/go/bin$ ./hsbench --help

USAGE: ./hsbench [OPTIONS]

OPTIONS:
  -a string
    	Access key
...
```

## Run hsbench

Below is an example run of the benchmark using a 10s test duration, 10 threads, 10 buckets, and a 4K object size against a Ceph RadosGW:
```
$ ./hsbench -a 829PO8ABIDBXCRXWDRKQ -s kyxEWNRWotUiR4qgmFTwDCMwPqS9AttmSOM0JEKy -u http://ceph01:8000 -z 4K -d 10 -t 10 -b 10
2023/12/29 13:05:05 Hotsauce S3 Benchmark Version 0.1
2023/12/29 13:05:05 Parameters:
2023/12/29 13:05:05 url=http://ceph01:8000
2023/12/29 13:05:05 object_prefix=
2023/12/29 13:05:05 bucket_prefix=hotsauce-bench
2023/12/29 13:05:05 region=us-east-1
2023/12/29 13:05:05 modes=cxiplgdcx
2023/12/29 13:05:05 output=
2023/12/29 13:05:05 json_output=
2023/12/29 13:05:05 max_keys=1000
2023/12/29 13:05:05 object_count=-1
2023/12/29 13:05:05 bucket_count=10
2023/12/29 13:05:05 duration=10
2023/12/29 13:05:05 threads=10
2023/12/29 13:05:05 loops=1
2023/12/29 13:05:05 size=4K
2023/12/29 13:05:05 interval=1.000000
2023/12/29 13:05:05 Running Loop 0 BUCKET CLEAR TEST
2023/12/29 13:05:05 Loop: 0, Int: TOTAL, Dur(s): 0.0, Mode: BCLR, Ops: 0, MB/s: 0.00, IO/s: 0, Lat(ms): [ min: 0.0, avg: 0.0, 99%: 0.0, max: 0.0 ], Slowdowns: 0
2023/12/29 13:05:05 Running Loop 0 BUCKET DELETE TEST
2023/12/29 13:05:05 Loop: 0, Int: TOTAL, Dur(s): 0.0, Mode: BDEL, Ops: 0, MB/s: 0.00, IO/s: 0, Lat(ms): [ min: 0.0, avg: 0.0, 99%: 0.0, max: 0.0 ], Slowdowns: 0
2023/12/29 13:05:05 Running Loop 0 BUCKET INIT TEST
2023/12/29 13:05:06 Loop: 0, Int: TOTAL, Dur(s): 0.1, Mode: BINIT, Ops: 10, MB/s: 0.00, IO/s: 89, Lat(ms): [ min: 57.8, avg: 87.0, 99%: 112.4, max: 112.4 ], Slowdowns: 0
2023/12/29 13:05:06 Running Loop 0 OBJECT PUT TEST
2023/12/29 13:05:07 Loop: 0, Int: 0, Dur(s): 1.0, Mode: PUT, Ops: 346, MB/s: 1.35, IO/s: 346, Lat(ms): [ min: 18.9, avg: 28.5, 99%: 36.2, max: 43.6 ], Slowdowns: 0
2023/12/29 13:05:08 Loop: 0, Int: 1, Dur(s): 1.0, Mode: PUT, Ops: 335, MB/s: 1.31, IO/s: 335, Lat(ms): [ min: 19.5, avg: 29.8, 99%: 63.1, max: 64.3 ], Slowdowns: 0
2023/12/29 13:05:09 Loop: 0, Int: 2, Dur(s): 1.0, Mode: PUT, Ops: 328, MB/s: 1.28, IO/s: 328, Lat(ms): [ min: 21.2, avg: 30.3, 99%: 53.6, max: 56.5 ], Slowdowns: 0
2023/12/29 13:05:10 Loop: 0, Int: 3, Dur(s): 1.0, Mode: PUT, Ops: 342, MB/s: 1.34, IO/s: 342, Lat(ms): [ min: 21.7, avg: 29.4, 99%: 38.9, max: 39.6 ], Slowdowns: 0
2023/12/29 13:05:11 Loop: 0, Int: 4, Dur(s): 1.0, Mode: PUT, Ops: 349, MB/s: 1.36, IO/s: 349, Lat(ms): [ min: 20.9, avg: 28.7, 99%: 34.9, max: 38.1 ], Slowdowns: 0
2023/12/29 13:05:12 Loop: 0, Int: 5, Dur(s): 1.0, Mode: PUT, Ops: 351, MB/s: 1.37, IO/s: 351, Lat(ms): [ min: 19.5, avg: 28.2, 99%: 35.1, max: 35.6 ], Slowdowns: 0
2023/12/29 13:05:13 Loop: 0, Int: 6, Dur(s): 1.0, Mode: PUT, Ops: 331, MB/s: 1.29, IO/s: 331, Lat(ms): [ min: 20.5, avg: 30.3, 99%: 60.5, max: 65.8 ], Slowdowns: 0
2023/12/29 13:05:14 Loop: 0, Int: 7, Dur(s): 1.0, Mode: PUT, Ops: 345, MB/s: 1.35, IO/s: 345, Lat(ms): [ min: 18.6, avg: 29.0, 99%: 37.5, max: 40.1 ], Slowdowns: 0
2023/12/29 13:05:15 Loop: 0, Int: 8, Dur(s): 1.0, Mode: PUT, Ops: 315, MB/s: 1.23, IO/s: 315, Lat(ms): [ min: 20.1, avg: 31.7, 99%: 59.7, max: 60.7 ], Slowdowns: 0
2023/12/29 13:05:16 Loop: 0, Int: 9, Dur(s): 1.0, Mode: PUT, Ops: 350, MB/s: 1.37, IO/s: 350, Lat(ms): [ min: 20.6, avg: 28.5, 99%: 35.4, max: 38.0 ], Slowdowns: 0
2023/12/29 13:05:16 Loop: 0, Int: TOTAL, Dur(s): 10.0, Mode: PUT, Ops: 3402, MB/s: 1.33, IO/s: 339, Lat(ms): [ min: 18.6, avg: 29.4, 99%: 52.5, max: 65.8 ], Slowdowns: 0
2023/12/29 13:05:16 Running Loop 0 BUCKET LIST TEST
2023/12/29 13:05:16 Loop: 0, Int: TOTAL, Dur(s): 0.0, Mode: LIST, Ops: 10, MB/s: 0.00, IO/s: 229, Lat(ms): [ min: 23.8, avg: 37.1, 99%: 43.5, max: 43.5 ], Slowdowns: 0
2023/12/29 13:05:16 Running Loop 0 OBJECT GET TEST
2023/12/29 13:05:17 Loop: 0, Int: 0, Dur(s): 1.0, Mode: GET, Ops: 2160, MB/s: 8.44, IO/s: 2160, Lat(ms): [ min: 1.7, avg: 4.3, 99%: 9.7, max: 15.9 ], Slowdowns: 0
2023/12/29 13:05:17 Loop: 0, Int: TOTAL, Dur(s): 1.6, Mode: GET, Ops: 3402, MB/s: 8.28, IO/s: 2121, Lat(ms): [ min: 1.6, avg: 4.3, 99%: 10.9, max: 19.8 ], Slowdowns: 0
2023/12/29 13:05:17 Running Loop 0 OBJECT DELETE TEST
2023/12/29 13:05:18 Loop: 0, Int: 0, Dur(s): 1.0, Mode: DEL, Ops: 335, MB/s: 1.31, IO/s: 335, Lat(ms): [ min: 20.5, avg: 29.4, 99%: 41.4, max: 50.0 ], Slowdowns: 0
2023/12/29 13:05:19 Loop: 0, Int: 1, Dur(s): 1.0, Mode: DEL, Ops: 345, MB/s: 1.35, IO/s: 345, Lat(ms): [ min: 19.0, avg: 28.8, 99%: 60.7, max: 61.2 ], Slowdowns: 0
2023/12/29 13:05:20 Loop: 0, Int: 2, Dur(s): 1.0, Mode: DEL, Ops: 368, MB/s: 1.44, IO/s: 368, Lat(ms): [ min: 18.3, avg: 27.4, 99%: 34.4, max: 41.0 ], Slowdowns: 0
2023/12/29 13:05:21 Loop: 0, Int: 3, Dur(s): 1.0, Mode: DEL, Ops: 351, MB/s: 1.37, IO/s: 351, Lat(ms): [ min: 17.5, avg: 28.5, 99%: 35.3, max: 40.5 ], Slowdowns: 0
2023/12/29 13:05:22 Loop: 0, Int: 4, Dur(s): 1.0, Mode: DEL, Ops: 356, MB/s: 1.39, IO/s: 356, Lat(ms): [ min: 20.6, avg: 28.0, 99%: 35.5, max: 35.7 ], Slowdowns: 0
2023/12/29 13:05:23 Loop: 0, Int: 5, Dur(s): 1.0, Mode: DEL, Ops: 339, MB/s: 1.32, IO/s: 339, Lat(ms): [ min: 19.2, avg: 29.5, 99%: 58.0, max: 62.4 ], Slowdowns: 0
2023/12/29 13:05:24 Loop: 0, Int: 6, Dur(s): 1.0, Mode: DEL, Ops: 342, MB/s: 1.34, IO/s: 342, Lat(ms): [ min: 20.0, avg: 29.4, 99%: 58.5, max: 61.7 ], Slowdowns: 0
2023/12/29 13:05:25 Loop: 0, Int: 7, Dur(s): 1.0, Mode: DEL, Ops: 354, MB/s: 1.38, IO/s: 354, Lat(ms): [ min: 20.9, avg: 28.0, 99%: 32.3, max: 38.7 ], Slowdowns: 0
2023/12/29 13:05:26 Loop: 0, Int: 8, Dur(s): 1.0, Mode: DEL, Ops: 347, MB/s: 1.36, IO/s: 347, Lat(ms): [ min: 19.3, avg: 28.7, 99%: 37.3, max: 40.1 ], Slowdowns: 0
2023/12/29 13:05:27 Loop: 0, Int: TOTAL, Dur(s): 9.8, Mode: DEL, Ops: 3402, MB/s: 1.36, IO/s: 348, Lat(ms): [ min: 17.5, avg: 28.7, 99%: 41.3, max: 62.4 ], Slowdowns: 0
2023/12/29 13:05:27 Running Loop 0 BUCKET CLEAR TEST
2023/12/29 13:05:27 Loop: 0, Int: TOTAL, Dur(s): 0.1, Mode: BCLR, Ops: 0, MB/s: 0.00, IO/s: 0, Lat(ms): [ min: 0.0, avg: 0.0, 99%: 0.0, max: 0.0 ], Slowdowns: 0
2023/12/29 13:05:27 Running Loop 0 BUCKET DELETE TEST
2023/12/29 13:05:28 Loop: 0, Int: TOTAL, Dur(s): 0.7, Mode: BDEL, Ops: 10, MB/s: 0.00, IO/s: 15, Lat(ms): [ min: 605.9, avg: 647.0, 99%: 681.3, max: 681.3 ], Slowdowns: 0
```

