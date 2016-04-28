---
layout : post
title : How to create and setup an EC2 instance with Jupyter
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

##### Library Loading

```python
import boto
from boto.manage.cmdshell import sshclient_from_instance
import time
import pandas as pd
import os
```

**Boto** is the key library here, it is the one that makes the connection between your EC2 and your notebook. You don't have to understand more than that.

##### Connection to EC2

```python
ec2 = boto.connect_ec2(aws_access_key_id=access.loc[0,1], 
                       aws_secret_access_key=access.loc[1,1])
```

In these two lines you make the bridge to the EC2 by giving your aws access key id and your aws secret access key.



