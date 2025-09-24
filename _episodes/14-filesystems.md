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

* home: where you land when you first login. 50 GB per user. Slower access, backed up. Used to store your work long term. 
* project: shared between all users of a project. Same filesystem as home. 
* scratch: temporary working space. Lustre filesystem with usually faster access, not backed up. Larger quota, but old files might get deleted. DON'T STORE RESULTS HERE!


Here's a synopsis of filesystems on Hawk in Cardiff:

|Name|Path|Default Quota|Disk Size|Backed Up|Filesystem
|-----------------|---|----|-----|---|-----|------|
|Home|/home/user.name|50GB|420TB|Yes|NFS|
|Long-term project|/home/scwXXXX|Negotiable|12TB (same disk as home)|Yes|NFS|
|Short-term project|/scratch/scwXXXX|Negotiable|1200TB (same disk as home)|Yes|Lustre|
|Scratch|/scratch/user.name|3TB + 3million files|1200TB|No|Lustre|

**Important!! Ensure that you don't store anything longer than necessary on scratch, this can negatively affect other people’s jobs on the system.**

There are also collaboration areas (scwcXXXX) that are not linked to projects but to a fixed group of users (for cases where a
project space does not cover all use-cases).

# Accessing your filestore

## How much quota do I have left on my home directory?

Login to a login node (e.g. `hawklogin.cf.ac.uk`) and run the ```myquota``` command. This will tell you how much space is left in your home directory.

~~~
$ myquota
~~~
{: .bash}

~~~
HOME DIRECTORY c.username
     Filesystem   space   quota   limit   grace   files   quota   limit   grace
chnfs-ib:/nfshome/store01
                   104G    500G    520G            221k   1000k   1050k

SCRATCH DIRECTORY c.username
     Filesystem    used   quota   limit   grace   files   quota   limit   grace
       /scratch  1.199T      0k      3T       -  383671       0 3000000       -

SHARED DIR scw1001
     Filesystem   space   quota   limit   grace   files   quota   limit   grace
chnfs-ib:/nfshome/store04
                     4K    100G    105G               1    200k    210k
~~~
{: .output}

## Group Filestore

If you have multiple collaborators working on a particular project and
would like to share common software or data across the project, then
it will be convenient for you to use a shared filestore. These can be
created on `/home` (for long-term use, e.g. software) or on `/scratch`
(for short-term data). If you would like one setup for you, you can
raise a support ticket or speak to one of your local RSEs.

## How much scratch have I used?

The ```df``` command tells you how much disk space is left. The ```-h``` argument makes the output easier to read, it gives human readable units like M, G and T for Megabyte, Gigabyte and Terrabyte instead of just giving output in bytes. By default df will give us the free space on all the drives on a system, but we can just ask for the scratch drive by adding ```/scratch``` as an argument after the ```-h```.

~~~
$ df -h /scratch
~~~
{: .bash}

~~~
Filesystem                                Size  Used Avail Use% Mounted on
172.2.1.51@o2ib:172.2.1.52@o2ib:/scratch  1.2P  606T  548T  53% /scratch
~~~
{: .output}

There is also a limit how many files can be stored on a filesystem.  These are called inodes and can be found with the ```-i``` argument to ```df```.  Usually not a problem but be careful if creating many files and talk to your local support or RSE if your code creates many files (in the 1000s).

More detailed information of Lustre usage can be found with the ```lfs``` command.  This shows that ```/scratch``` is a
parallel fileystem with many storage areas.  Usually a file is *striped* (divided) across these storage areas and provide faster
access especially with larger files.  This can be configured by the user if required.

~~~
$ lfs df -h /scratch
~~~
{: .bash}

~~~
UUID                       bytes        Used   Available Use% Mounted on
scratch-MDT0000_UUID        1.6T       63.4G        1.5T   4% /scratch[MDT:0]
scratch-OST0000_UUID       28.8T       19.9T        8.9T  69% /scratch[OST:0]
scratch-OST0001_UUID       28.8T       20.4T        8.4T  71% /scratch[OST:1]
scratch-OST0002_UUID       28.8T       20.1T        8.7T  70% /scratch[OST:2]
...
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

> ## Using the `myquota` command.
> 1. Login to a login node.
> 2. Run the `myquota` command. 
> 3. How much space have you used and how much do you have left? 
> 4. If you had a job that resulted in 60GB of files would you have enough space to store them?
{: .challenge}


