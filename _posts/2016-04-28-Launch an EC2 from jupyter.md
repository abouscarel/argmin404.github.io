---
layout : post
title : How to create and setup an EC2 instance directly from your Jupyter notebook
subtitle : The easiest way to launch an amazon EC2 and to run your python code
---

### Introduction and Workflow

As data scientist you are quickly exposed to machine learning algorithm and to programming language (python or R usually) and most of tutorial / classes stop there. However being able to run cloud computation using an EC2 can be a very important skills in real life. Starting an EC2 is trivial, you simply have to go on AWS select your machine and there you go but this is far from what you want. What you want as a professional data scientist is a proper machine in the cloud with a proper environment ready to receive your python code and to launch it, without the strugle to run everything line by line. A few months ago I found myself desperate to find a tutorial in order to automate the creation and the configuration of a virtual machine on AWS as well as being able to transfer files, launch scripts and recolt the results. 

After a lot of research I was finally able to produce a jupyter notebook that can **pilot** your activity. At the end of tutorial you will have the tools to have a workflow such has :

1. [jupyter notebook] Launch an EC2
2. [jupyter notebook] Transfer the proper files and scripts including the install.sh 
3. [ssh] Install all the necessary softwares and libraries
4. [ssh] Run your python script - that will save the results in your S3
5. [jupyter notebook] Close the EC

#### CAREFUL

EC2 is no joke, it is linked to your credit card and you should be very very very cautious with your credentials (AWS secret key and ID). You will see later in this tutorial that you need to setup a file "root key.csv" where lies these informations, **do not share them**. When I was experiencing with this technology I pushed by mistakes on my git this file, 1 min later I had 80 big servers running on amazon for a total cost of 180$/hours, fortunately I was able to quickly call amazon and settle this issue, but be really careful with this. 

### Requirements

In order to follow this tutorial you will need :

- Jupyter Notebook with a python2 kernel and a python3 kernel
- An AWS account
- In a folder the following files:
  - root key.csv : a file containing your aws credentials
  - LaunchEC2.ipynb : the jupyter notebook that pilot the activity


### The Jupyter Notebook

As explained above the jupyter notebook leads to the creation of the VM and to sending the proper files on it. In our case it will send 

- install.sh : a bash file that performs all the installation
- main.py : the main script that in this case reads from the S3 and save into it


Let's look deeper into the code : 

#### Library Loading

```python
import boto
from boto.manage.cmdshell import sshclient_from_instance
import time
import pandas as pd
import os
```

**Boto** is the key library here, it is the one that makes the connection between your EC2 and your notebook. You don't have to understand more than that.

#### Connection to EC2

```python
ec2 = boto.connect_ec2(aws_access_key_id=access.loc[0,1], 
                       aws_secret_access_key=access.loc[1,1])
```

In these two lines you make the bridge to the EC2 by giving your aws access key id and your aws secret access key.

#### Access your current instances 

```python
reservations = ec2.get_all_instances()
instance = reservations[0].instances[0]
```

These lines simply get all your running instances, if **you don't have any instance running** this will cause **an error** you will need these lines in a very specific situation : you closed your python kernel, then re-opened it again and you want to close your EC2 from your notebook. 

#### SSH Mapping

In order to be able to access your amazon machine and push file you need to create a proper ssh key and link it to your AWS, the next few lines do it for you : 

```python
key_pair = ec2.create_key_pair('aws-key')
key_pair.save('~/.ssh')
group = ec2.create_security_group('root', 'A group that allows SSH access')
group.authorize('tcp', 22, 22, '0.0.0.0/0') # to properly authorize ssh
```

**NB**: the first two lines of code only work on python2 (for some reasons), if you only have python3 you can create the key using the following lines in your shell (then follow the instruction): 

```shell
ssh-keygen -t rsa -f ~/.ssh/aws-key
```

#### Run the Instance

This is the turning point, these few lines actually run the instance. The usual approach I suggest is to first use a *t2.micro* instance to make sure everything works and then run on a bigger one. 

```python
## Running a classical Amazon t2.micro Linux Instance
reservation = ec2.run_instances(image_id='ami-60b6c60a', 
                                key_name='aws-key', 
                                instance_type = 't2.micro',
                                security_groups = ['root'])

instance = reservation.instances[0]
print('waiting for instance')
while instance.state != 'running':
    #print ('.', end = "")
    print('.'),
    time.sleep(5)
    instance.update()
print('done')
```

**To change the instance**, you simply need to change the argument *t2.micro* for something else, I strongly suggest to use the amazon machines. You can look into this table to see the list of the available instances : https://aws.amazon.com/fr/ec2/instance-types/. 