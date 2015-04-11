---
layout: post
title: Use boto to interact with AWS
comments: True
type: "blog"
short_summary: "One of the best things on AWS is that evertything is can be managed by an API. In this post we will use boto to interact with AWS in python."

---

In this post we are going to create a virtual private network (VPC) on AWS, then create two instances and a database.


First step is to install boto:

{% highlight python %}
sudo pip install boto
{% endhighlight %}

If using debian/derivative please note that the boto version could be really old. It is better to just use pip.

Now open a python (or better ipython) terminal and lets interact AWS. 

First import the modules for interacting with aws ec2, vpc and rds2

{% highlight python %}
import boto.ec2
import boto.vpc
import boto.rds2
{% endhighlight %}

We can now set some constants we will use for this test:

{% highlight python %}
###### Configurations ######
PROJECT                    = "Testing"            # This is the tag name for all resources
ACCESS_KEY                 = "SECRET"
SECRET_KEY                 = "SECRET"

REGION_NAME                = 'us-west-2'
FIRST_REGION_NAME          = REGION_NAME + 'a'
SECOND_REGION_NAME         = REGION_NAME + 'b'
THIRD_REGION_NAME          = REGION_NAME + 'c'
AMI_IMAGE                  = "ami-e7527ed7"       # For us-west

DB_AVAILABILITY_ZONE       = FIRST_REGION_NAME
DB_AUTO_BACKUP_DAYS        = 0
DB_PORT                    = 5432
DISK_TYPE                  = "gp2"

MACHINE_SIZE               = "t2.small"
DB_STORAGE_GB               = 10
DB_CLASS                    = "db.t2.small"
###### End configurations #####
{% endhighlight %}


First step let's sign in to AWS and get a connection object for ec2, vpc and rds.

{% highlight python %}
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

e, v, r = connect()
{% endhighlight %}
