
USAGE: ./hsbench [OPTIONS]

OPTIONS:
  -a string
    	Access key
  -b int
    	Number of buckets to distribute IOs across (default 1)
  -bp string
    	Prefix for buckets (default "hotsauce-bench")
  -d int
    	Maximum test duration in seconds <-1 for unlimited> (default 60)
  -j string
    	Write JSON output to this file
  -l int
    	Number of times to repeat test (default 1)
  -m string
    	Run modes in order.  See NOTES for more info (default "cxiplgdcx")
  -mk int
    	Maximum number of keys to retreive at once for bucket listings (default 1000)
  -n int
    	Maximum number of objects <-1 for unlimited> (default -1)
  -o string
    	Write CSV output to this file
  -op string
    	Prefix for objects
  -r string
    	Region for testing (default "us-east-1")
  -ri float
    	Number of seconds between report intervals (default 1)
  -s string
    	Secret key
  -t int
    	Number of threads to run (default 1)
  -u string
    	URL for host with method prefix
  -z string
    	Size of objects in bytes with postfix K, M, and G (default "1M")

NOTES:
  - Valid mode types for the -m mode string are:
    c: clear all existing objects from buckets (requires lookups)
    x: delete buckets
    i: initialize buckets 
    p: put objects in buckets
    l: list objects in buckets
    g: get objects from buckets
    d: delete objects from buckets 

    These modes are processed in-order and can be repeated, ie "ippgd" will
    initialize the buckets, put the objects, reput the objects, get the
    objects, and then delete the objects.  The repeat flag will repeat this
    whole process the specified number of times.

  - When performing bucket listings, many S3 storage systems limit the
    maximum number of keys returned to 1000 even if MaxKeys is set higher.
    hsbench will attempt to set MaxKeys to whatever value is passed via the 
    "mk" flag, but it's likely that any values above 1000 will be ignored.