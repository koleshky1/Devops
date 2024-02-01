# AWS NETWORK IMPLEMENTATION

# What is an Amazon VPC?

An Amazon Virtual Private Cloud (VPC) is like your own private section of the Amazon cloud, where you can place and manage your resources (like servers or databases). You control who and what can go in and out, just like a gated community.

The essential steps to creating a VPC and configuring core network services. The topics to be covered are:

The Default VPC
Creating a new VPC
Creating and configuring subnets

# The Default VPC

The Default VPC is like a starter pack provided by Amazon for your cloud resources. It's a pre-configured space in the Amazon cloud where you can immediately start deploying your applications or services. It has built-in security and network settings to help you get up and running quickly, but you can adjust these as you see fit.

![Alt text](<images/default vpc.png>)


# CREATING A NEW VPC

Creating a new VPC As we want to learn step by step and observe the components, choose the "VPC only" option, we'll use the "VPC and more" option later. Enter "first-vpc" as the name tag and "10.0.0.0/16" as the IPv4 CIDR. The "10.0.0.0/16" will be the primary IPv4 block and you can add a secondary IPv4 block e.g., "100.64.0.0/16". The use case of secondary CIDR block could be because you're running out of IPs and need to add additional block, or there's a VPC with overlapping CIDR which you need to peer or connect. See this blog post on how a secondary CIDR block is being used in an overlapping IP scenario


![Alt text](<images/create new vpc.png>)


![Alt text](<images/new vpc created.png>)


# Now you have a VPC and a route table, but you won't be able to put anything inside. If you try to create an EC2 instance for subnets.

# Creating and configuring subnets

# What are Subnets?

Subnets are like smaller segments example, you can't proceed as it requires within a VPC that help you organize and manage your resources. Subnets are like dividing an office building into smaller sections, where each section represents a department. In this analogy, subnets are created to organize and manage the network effectively.

