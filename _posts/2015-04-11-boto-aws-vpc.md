---
layout: post
title: Use boto to interact with AWS
comments: True
type: "blog"
short_summary: "One of the best things on AWS is that evertything is can be managed by an API. In this post we will use boto to interact with AWS in python."

---

In this post we are going to create a virtual private network (VPC) on AWS, then create two instances and a database.

* First step is to install boto:

{% highlight python %}
sudo pip install boto
{% endhighlight %}

 * If using debian/derivative please note that the boto version could be really old. It is better to just use pip.

* Now open a python (or better ipython) terminal and lets interact AWS. 
 * First import the modules for interacting with aws ec2, vpc and rds2

{% highlight python %}
import boto.ec2
import boto.vpc
import boto.rds2
{% endhighlight %}

* We can now set some constants we will use for this test:

{% highlight python %}
###### Configurations ######
PROJECT                    = "Testing"            # This is the tag name for all resources
ACCESS_KEY                 = "SECRET"
SECRET_KEY                 = "SECRET"

REGION_NAME                = 'us-west-2'
FIRST_AZ                   = REGION_NAME + 'a'
SECOND_AZ                  = REGION_NAME + 'b'
THIRD_AZ                   = REGION_NAME + 'c'
AMI_IMAGE                  = "ami-e7527ed7"       # For us-west

DB_AVAILABILITY_ZONE       = FIRST_AZ
DB_AUTO_BACKUP_DAYS        = 0
DB_PORT                    = 5432
DB_USER                    = "user"
DB_PASSWORD                = "SECRET"
DB_NAME                    = "TestDB"
DISK_TYPE                  = "gp2"

MACHINE_SIZE               = "t2.small"
DB_STORAGE_GB               = 10
DB_CLASS                    = "db.t2.small"
###### End configurations #####
{% endhighlight %}

* Now let's sign in to AWS and get a connection object for ec2, vpc and rds.

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

* Create the private network and allow instances to have public dns and ips:

{% highlight python %}
vpc = v.create_vpc('10.2.0.0/16')
v.modify_vpc_attribute(vpc.id, enable_dns_support=True)
v.modify_vpc_attribute(vpc.id, enable_dns_hostnames=True)
{% endhighlight %}

* Create the network subnets (one in each region, and connect then all to the internet gateway)

{% highlight python %}
# Create network acl
network_acl = v.create_network_acl(vpc.id)

# Create subnet
subnet_first = v.create_subnet(vpc.id, '10.2.0.0/24', availability_zone=FIRST_AZ)

subnet_second = v.create_subnet(vpc.id, '10.2.1.0/24', availability_zone=SECOND_AZ)

subnet_third = v.create_subnet(vpc.id, '10.2.2.0/24', availability_zone=THIRD_AZ)

# Create a Route Table
route_table = v.create_route_table(vpc.id)

# Create a internet gateway
gateway = v.create_internet_gateway()
v.attach_internet_gateway(gateway.id, vpc.id)

# Connect the subnets to the gateway
v.create_route(route_table.id, destination_cidr_block="0.0.0.0/0", gateway_id=gateway.id)

v.associate_route_table(route_table.id, subnet_first.id)
v.associate_route_table(route_table.id, subnet_second.id)
v.associate_route_table(route_table.id, subnet_third.id)
{% endhighlight %}

* Create the security groups that our instances will be in and their ssh keys

{% highlight python %}
# Create a new VPC security group
security_group = v.create_security_group(PROJECT+"SecurityGroup",
        'Security group for ' + PROJECT,
        vpc.id)

# Create new ssh keys
keypair = e.create_key_pair(PROJECT+"Keypair")
keypair.save('someDirectory')
{% endhighlight %}

Finally create the instances (and their interfaces with a public ip):

{% highlight python %}
interface = boto.ec2.networkinterface.NetworkInterfaceSpecification(subnet_id=subnet_first.id,
        groups=[security_group.id,],
        associate_public_ip_address=True)
interfaces = boto.ec2.networkinterface.NetworkInterfaceCollection(interface)

reservation = e.run_instances(AMI_IMAGE,
        key_name=keypair.name,
        instance_type=MACHINE_SIZE,
        network_interfaces=interfaces)

machine_A = reservation.instances[0]

interface = boto.ec2.networkinterface.NetworkInterfaceSpecification(subnet_id=subnet_first.id,
        groups=[security_group.id,],
        associate_public_ip_address=True)
interfaces = boto.ec2.networkinterface.NetworkInterfaceCollection(interface)

reservation = e.run_instances(AMI_IMAGE,
        key_name=keypair.name,
        instance_type=MACHINE_SIZE,
        network_interfaces=interfaces)

machine_B = reservation.instances[0]
{% endhighlight %}

Let's allow ssh originating from anywhere to reach the instances, and machines to talk to each other via port 21 (because they are in the same security group).

{% highlight python %}
security_group.authorize(ip_protocol='tcp', from_port=22, to_port=22, cidr_ip='0.0.0.0/0')
security_group.authorize(ip_protocol='tcp', from_port=21, to_port=21, src_group=security_group)
{% endhighlight %}

Just a small note, up until now we have created a series of aws entities but have now easy way of searching which were created for these project.
To solve this just tag these entities like this:

{% highlight python %}
def nTag(what):
    return {'Name': PROJECT + str(what), 'Project': PROJECT}

vpc.add_tags(nTag("VPC"))
network_acl.add_tags(nTag("Acl"))
subnet_first.add_tags(nTag("SubnetFirst"))
subnet_second.add_tags(nTag("SubnetSecond"))
subnet_third.add_tags(nTag("SubnetThird"))
route_table.add_tags(nTag("RouteTable"))
gateway.add_tags(nTag("Gateway"))
security_group.add_tags(nTag("SecurityGroup"))
machine_A.add_tags(nTag("Machine_A"))
machine_B.add_tags(nTag("Machine_B"))
{% endhighlight %}

We can now search for the Tag Project in the aws web GUI and see the entities created by this project:

<p><img width="95%" src="/img/blog/aws_web_console_filter_project.png" class="blog-image" alt="Aws filter by tag" /></p>

Finally let's take care of the database. First create a security group for the machines to be able to talk with the database (port 5432).

{% highlight python %}
database_security_group = e.create_security_group(PROJECT+"DatabaseSecurityGroup",
        'Database Security group for ' + PROJECT,
        vpc.id)
database_security_group.add_tags(nTag("DatabaseSecurityGroup"))
database_security_group.authorize(ip_protocol='tcp', from_port=5432, to_port=5432, src_group=security_group)

# Create database subnet
database_subnet_name = PROJECT + "DatabaseSubnetGroupName"
database_subnet = r.create_db_subnet_group(
        db_subnet_group_name = database_subnet_name,
        db_subnet_group_description = "Database Subnet for " + PROJECT,
        subnet_ids = [subnet_first.id, subnet_second.id, subnet_third.id],
        tags=nTag("DatabaseSubnetGroupName"))
elements['database_subnet_name'] = database_subnet_name

# Create the database
database_name = "db" + PROJECT
database = r.create_db_instance(
        db_instance_identifier     = "db" + FLAVOUR,
        allocated_storage          = DB_STORAGE_GB,
        db_instance_class          = DB_CLASS,
        engine                     = "postgres",
        master_username            = DB_USER,
        master_user_password       = DB_PASSWORD,
        db_name                    = DB_NAME,
        vpc_security_group_ids     = [database_security_group.id,],
        availability_zone          = DB_AVAILABILITY_ZONE,
        db_subnet_group_name       = database_subnet_name,
        backup_retention_period    = DB_AUTO_BACKUP_DAYS,
        port                       = 5432,
        multi_az                   = False,
        auto_minor_version_upgrade = True,
        publicly_accessible        = False,
        tags                       = nTag("Database"))
{% endhighlight %}

And there you have it, two machines and a subnet running in a VPC.
Right now everything is running on the same Availability zone, using the same subnet. 
But new machines/databases could be created on the other AZs.

If you want to see all together here is the gist:

{% gist joninvski/33f1dc02ff1d2f0d1e89%}
