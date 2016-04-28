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

### Jupyter Notebook

As explained above the jupyter notebook leads to the creation of the VM and to sending the proper files on it. In our case it will send 

- install.sh : a bash file that performs all the installation
- main.py : the main script that in this case reads from the S3 and save into it





