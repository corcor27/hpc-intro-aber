---
title: "Filesystems and Storage"
author: "Colin Sauze, Ed Bennett, Jarno Rantaharju, Thomas Green"
teaching: 15
exercises: 10
questions:
 - "Where can I store my data?"
 - "What is the difference between scratch and home filestore?"
 - "How to use filesystems in job scripts."
objectives:
 - "Understand the difference between home and scratch directories"
keypoints:
 - "The home directory is the default place to store data."
 - "The scratch directory is a larger space for temporary files."
 - "On Hawk in Cardiff home is backed up but is also a slower disk."
 - "Quotas on home are much smaller than scratch."
---


# Filesystems and Storage

## What is a filesystem?
Storage on most compute systems is not what and where you think they are! Physical disks are bundled together into a virtual volume; this virtual volume may represent one filesystem, or may be divided up, or partitioned, into multiple filesystems. And your directories then reside within one of these fileystems. Filesystems are accessed over the network through mount points.

There are multiple storage/filesystems options available for you to do your work. The most common are:
    
* home: where you land when you first login. 100 GB per user. Slower access, backed up. Used to store your work long term.  
* scratch: temporary working space. Lustre filesystem with usually faster access, not backed up. Larger quota, but old files might get deleted. DON'T STORE RESULTS HERE!


Here's a synopsis of filesystems on the cluster at Aberystwyth:

|Name|Path|Default Quota|Disk Size|Backed Up|Filesystem
|-----------------|---|----|-----|---|-----|------|
|Home|/hpc/user.name|100GB|40TB |Yes|NFS|
|Scratch|/scratch/user.name|N/A|100TB|No|NFS|

**Important!! Ensure that you don't store anything longer than necessary on scratch, this can negatively affect other people’s jobs on the system.**


# Accessing your filestore

~~~
$ df -h /scratch
~~~
{: .bash}

## How much scratch have I used?

The ```df``` command tells you how much disk space is left. The ```-h``` argument makes the output easier to read, it gives human readable units like M, G and T for Megabyte, Gigabyte and Terrabyte instead of just giving output in bytes. By default df will give us the free space on all the drives on a system, but we can just ask for the scratch drive by adding ```/scratch``` as an argument after the ```-h```.

~~~
$ df -h /scratch
~~~
{: .bash}

~~~
Filesystem                      Size  Used Avail Use% Mounted on
172.28.4.1:/mnt/zpool1/scratch  100T  189G  100T   1% /scratch
~~~
{: .output}


# Filesystems in job scripts.

With home and scratch filesystems (along with standard local ```/tmp``` filesystem)
the most appropriate filesystem should be used in the job script.

When running jobs all files should normally be copied to ```/scratch``` before running the application.  This makes sure all
files are read on the high performance Lustre filesystem that ```/scratch``` uses.  For example, if ```my_job``` directory in
```$HOME``` contains all files for the job, copy to ```/scratch``` and then run application.
~~~
WDPATH=/scratch/$USER/$SLURM_JOB_ID
rm -fr $WDPATH
mkdir -p $WDPATH
cp -r $HOME/my_job $WDPATH

cd $WDPATH
my_application.exe
echo "INFO: Finished job in $WDPATH"
~~~
{: .bash}

# Exercises

> ## Using the `df` command. 
> 1. Login to a login node
> 2. Run the command `df -h`.
> 3. How much space does /scratch have left?
> 4. If you had to run a large job requiring 10TB of scratch space, would there be enough space for it?
{: .challenge}




