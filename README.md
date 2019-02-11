# ubi-cluster-use
This repo is designed to get you startet working on the Uni Bielefeld compute cluster. It also contains example scripts.
## Setup
### Access
First you need to request access from the RBG support. Simply send an email containing your techfak-login to:
```
support@techfak.net
```
Note: If you are not Citec-staff and need the cluster for writing your thesis, either have your supervisor write the email on your behalf or mention that you are writing a thesis for them in your mail. 
### Login
Afterwards, you can access the cluster by ssh-ing to the login node:
```
ssh mmuster@login.gpu.cit-ec.net
```
If you are outside the techfak-net and want to access the cluster you need to ssh to i.e. ```compute.techfak.de``` first.
After logging in, you should be inside your home directory on the cluster ```/media/compute/homes/mmuster```.
### Environment
The cluster runs the standard netboot installation. There are additional modules you can load. Most notably Cuda.
To check what is available run:
```
module avail
```
Anything else you need to setup yourself. 
If you are using python I recommend [anaconda](https://www.anaconda.com/distribution/) to keep your environment on the cluster easy to maintain. Alternatively use ```virtuelenv```.
### File Sync
To keep your code updated I think it is best to just use a git repo.
For other data, i.e. training data, use rsync:
```
rsync <Path>/mydata -avz mmuster@login.gpu.cit-ec.net:
```
## Slurm
[Slurm](https://slurm.schedmd.com/) is the scheduler running on the cluster. You need to start and schedule your jobs via slurm.
### SBATCH
To get started create your own sbatch-script. Here you can specify what your job is and which ressources it needs.
example.sbatch:
```
#!/bin/bash
#SBATCH --gres=gpu:1
#SBATCH --job-name=train_example
#SBATCH --time=5:00:00

srun train_example.sh
```
In this example we request one gpu for five hours for our job ``` train_example``.
You can schedule your job by using
```
sbatch example.sbatch
```
Using
```
squeue
```
you should see your job in the list
```
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
              2115       gpu script.s   **      PD       0:00     1 (Resources)
              2116       gpu train_ex   mmuster PD       0:00     1 (Priority)
              2118       cpu isy-run.   **      PD       0:00     1 (Resources)
              1816       cpu     bash   **      R 14-02:39:13     1 hefty
              1931       gpu script.s   **      R 5-08:12:19      1 handy
              1933       gpu script.s   **      R 5-07:55:56      1 handy
              1964       cpu     bash   **      R 4-06:42:14      1 handy
            2016_6       gpu   flow2a   **      R    4:40:27      1 papaschlumpf
              2047       cpu     bash   **      R 2-04:47:06      1 handy
              2062       cpu     bash   **      R 2-03:49:09      1 papaschlumpf
              2066       cpu     bash   **      R 2-03:44:27      1 papaschlumpf
              2076       gpu batchpro   **      R   15:58:47      1 clumsy
              2077       gpu script.s   **      R   15:54:00      1 clumsy
              2110       gpu script.s   **      R      22:28      1 schlaubi
              2112       gpu script.s   **      R      20:22      1 hefty
              2113       gpu script.s   **      R      19:49      1 hefty
              2114       gpu script.s   **      R      16:09      1 schlaubi

```
Right now your job is not running yet. It will be as soon as your requested ressources are available.
You can see the output of your ```train_example.sh``` script in your home directory in ```slurm-2116.out```.

You can be informed about the status of your job via email by adding
```
#SBATCH --mail-user=mmuster@techfak.uni-bielefeld.de
#SBATCH --mail-type=END
```
to your sbatch-script. Only techfak addresses work. In this example you will recieve an email when your job ends that means either fails, completes or runs out of time. See https://slurm.schedmd.com/sbatch.html for a list of available mail types.
