# AWSBucketDump

 #### AWSBucketDump is a tool to quickly enumerate AWS S3 buckets to look for loot. It's similar to a subdomain bruteforcer but is made specifically for S3 buckets and also has some extra features that allow you to grep for delicious files as well as download interesting files if you're not afraid to quickly fill up your hard drive.
 #### [@ok_bye_now](https://twitter.com/ok_bye_now)

## Pre-Requisites
Non-Standard Python Libraries:

* xmltodict
* requests
* argparse

Created with Python 3.6

### Install with virtualenv
```virtualenv-3.6 venv
source venv/bin/activate
pip install -r requirements.txt
```

## General

This is a tool that enumerates Amazon S3 buckets and looks for interesting files. 

I have example wordlists but I haven't put much time into refining them. 

https://github.com/danielmiessler/SecLists will have all the word lists you need. If you are targeting a specific company, you will likely want to use jhaddix's enumall tool which leverages recon-ng and Alt-DNS. 

https://github.com/jhaddix/domain && https://github.com/infosec-au/altdns

As far as word lists for grepping interesting files, that is completely up to you. The one I provided has some basics and yes, those word lists are based on files that I personally have found with this tool.

Using the download feature might fill your hard drive up, you can provide a max file size for each download at the command line when you run the tool. Keep in mind that it is in bytes.

I honestly don't know if Amazon rate limits this, I am guessing they do to some point but I haven't gotten around to figuring out what that limit is.  By default there are two threads for checking buckets and two buckets for downloading.  

After building this tool, I did find an [interesting article](https://community.rapid7.com/community/infosec/blog/2013/03/27/1951-open-s3-buckets) from Rapid7 regarding this research.

## Usage:

    usage: AWSBucketDump.py [-h] [-D] [-t THREADS] -l HOSTLIST [-g GREPWORDS] [-m MAXSIZE]

    optional arguments:
      -h, --help    show this help message and exit
      -D            Download files. This requires significant diskspace
      -d            If set to 1 or True, create directories for each host w/ results
      -t THREADS    number of threads
      -l HOSTLIST
      -g GREPWORDS  Provide a wordlist to grep for
      -m MAXSIZE    Maximum file size to download.
      -b            Write list of open buckets to openbuckets.txt only - don't look at contents.
  
     python3 AWSBucketDump.py -d False -l <wordlistfile> -g grepfile.txt -b

## Generating wordlists

```kali > crunch <min> <max> -f charset s3 -o <output filename>```

stripping out invalid line starts and ends

sed '/^\./ d' <filename> | sed "/\.$/d" | sed '/^-/ d' | sed "/-$/d"

## Patterns in responses
```<Error><Code>NoSuchBucket</Code><Message>The specified bucket does not exist</Message>
<Error><Code>InvalidBucketName</Code><Message>The specified bucket is not valid.</Message>
<Error><Code>AccessDenied</Code><Message>Access Denied</Message>
<Error><Code>AllAccessDisabled</Code><Message>All access to this object has been disabled</Message>
```

## Naming rules
S3 URLs use the format http://bucketname.s3.amazonaws.com . If we replace bucketname with random strings that meet the following rules it is possible to see if buckets exist. Feed this tool with a wordlist of your choice and you're good to go.

Rules from https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html
- Bucket names must be at least 3 and no more than 63 characters long.
- Bucket names must be a series of one or more labels. Adjacent labels are separated by a single period (.). Bucket names can contain lowercase letters, numbers, and hyphens. Each label must start and end with a lowercase letter or a number.
- Bucket names must not be formatted as an IP address (for example, 192.168.5.4).
- When using virtual hosted–style buckets with SSL, the SSL wildcard certificate only matches buckets that do not contain periods. To work around this, use HTTP or write your own certificate verification logic. We recommend that you do not use periods (".") in bucket names.

## Outputs

The script will produce the following three output files in the directory you run the script.

bucket404.txt - buckets not found
bucket403.txt - buckets found but access denied
openbucket.txt - buckets found and content listing enabled

### Contributors

[craighays](https://github.com/craighays)

[jordanpotti](https://github.com/jordanpotti)

[grogsaxle](https://github.com/grogsaxle)

[codingo](https://github.com/codingo)

[aarongorka](https://github.com/aarongorka)

[BHaFSec](https://github.com/BHaFSec)

[paralax](https://github.com/paralax)

[fzzo](https://github.com/fzzo)

[rypb](https://github.com/rypb)

