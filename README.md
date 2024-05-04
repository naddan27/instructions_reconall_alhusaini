# Recon-all FreeSurfer Tutorial Alhusaini Lab
This is a tutorial to running FreeSurfer's recon-all using T1 images. We will be using OSCAR clusters.

## Getting started
First, download the example data that we will be using here: https://drive.google.com/drive/folders/1AG2xq0uPl8BSQZ_SY-HXH6SOVvqwMp0o?usp=sharing

You want to SSH into your cluster. You can do it through the GUI (https://ood.ccv.brown.edu/pun/sys/dashboard) or through your terminal. I recommend using the terminal as it is less glitchy and easier to copy and paste functions.

To SSH, please use the following instructions: https://docs.ccv.brown.edu/oscar/connecting-to-oscar/ssh

As an example using my login:
```
ssh -X dkim39@ssh.ccv.brown.edu
```
This will prompt a password:
```
(dkim39@ssh.ccv.brown.edu) Password: *******
```
We are now connected to the OSCAR clusters via SSH. 

## Loading the Dependencies
Type in the following:
```
module load parallel
module load freesurfer
```

Second, we define where our output will go:
```
export SUBJECTS_DIR=/users/dkim39/data/dkim39/reconall_instructions/data/output
```

Next, we initialize FreeSurfer:
```
source $FREESURFER_HOME/SetUpFreeSurfer.sh
```
This activated the Freesurfer application. 

## Determining which subjects to run recon-all on
List the subject names in the List_subjects.txt file.
```
vim List_subjects.txt
```
Press "i" to go into "insert" mode and type in the subject names separated by a new line.
Press "Esc" and then ":wq" to save the file. An example is shared in this repository. 

## Configuring the Batch Script
You will now determine how much computing power to request. The script is designed to run recon-all on the subjects you requested in paralell. For each patient, I recommend one core, with ~4-5 GB of memory. You can call up to 32 cores on the lab's clusters. If you have more than 32 patients to process, I recommend limiting the subjects listed in the List_subjects.txt to 32 patients. Technically you can put more, and cores that finish running recon-all will take the next subject to process. However, because you are requesting resources, you have to set a time limit on how long you can request these resources. Recon-all can take around 24-36 hours, and I have the current script set to maximum of 48 hours. You can check the CCV website for resource caps, but also note that the more resources you request, the longer your job will wait in queue before executing.

```
vim loop_recon-all
```

Change the number of cores as appropriate. This is the number after "#SBATCH -n". You should also change the number after "parallel --jobs" to the same number.

Change the number of memory as appropriate. This is the number after "#SBATCH --mem=".

Change the data path to where your data is stored. The "*" in the path name are fillers for the path name and indicate that they can accept any value. Note that the input data are DICOM files. If you feed just one slice of the DICOM file, recon-all will find the rest of the DICOM files. You do not need to input NIFTI files.

Now run your script.
```
sbatch ./loop_recon-all
```

You can check the progress of the script by running 
```
myq
```
and seeing the output of your scripts by checking the slurm file. Note that the slurm file will only have output if the script finishes running or if there is an error that breaks the script execution. If you want the progress of a specific patient, go to /$SUBJECT_DIR/scripts. Then read the recon-all.log. For more information, visit https://surfer.nmr.mgh.harvard.edu/fswiki/recon-all. This is a great resource as well: https://andysbrainbook.readthedocs.io/en/latest/FreeSurfer/FS_ShortCourse/FS_01_BasicTerms.html. 
