---
layout: post
title: Use boto to interact with AWS
comments: True
type: "blog"
short_summary: "One of the best things on AWS is that evertything is can be managed by an API. In this post we will use boto to interact with AWS in python."

---


{% highlight python %}
import time
import boto.ec2
import boto.vpc
import boto.rds2
import os
import shutil
from boto.manage.cmdshell import sshclient_from_instance

###### Configurations ######
PROJECT                    = "Testing"
ACCESS_KEY                 = "SECRET"
SECRET_KEY                 = "SECRET"

REGION_NAME                = 'us-west-2'
FIRST_REGION_NAME          = REGION_NAME + 'a'
SECOND_REGION_NAME         = REGION_NAME + 'b'
THIRD_REGION_NAME          = REGION_NAME + 'c'
AMI_IMAGE                  = "ami-e7527ed7"          # For us-west

DB_AVAILABILITY_ZONE       = FIRST_REGION_NAME
DB_AUTO_BACKUP_DAYS        = 0
DB_PORT                    = 5432
DISK_TYPE                  = "gp2"

MACHINE_SIZE               = "t2.small"
DB_STORAGE_GB               = 10
DB_CLASS                    = "db.t2.small"
###### End configurations #####

def nTag(what):
    return {'Name': PROJECT + str(what), 'PROJECT': PROJECT}

def connect():
    """
    Create the ec2, vpc and rds connections
    """
    e = boto.ec2.connect_to_region(REGION_NAME,
            aws_access_key_id=ACCESS_KEY,
            aws_secret_access_key=SECRET_KEY)

    v = boto.vpc.connect_to_region(REGION_NAME,
            aws_access_key_id=ACCESS_KEY,
            aws_secret_access_key=SECRET_KEY)

    r = boto.rds2.connect_to_region(REGION_NAME,
            aws_access_key_id=ACCESS_KEY,
            aws_secret_access_key=SECRET_KEY)

    return e, v, r

{% endhighlight %}
