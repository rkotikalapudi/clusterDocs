# FreeSurfer via apptainer
To know more about Freesurfer, a surface-based morphometry toolbox, you can visit: [FreeSurfer-tutorial](https://andysbrainbook.readthedocs.io/en/latest/FreeSurfer/FreeSurfer_Introduction.html).
Here, we will provide information on how to run freesurfer on the HPC cluster, so that everyone can try this approach. 

## Set-up apptainer
Documentation on apptainer/sandbox is made available at: [IKIMDocs](https://ikim-essen.github.io/ClusterDocs/).

Let us get started with setting up the required things to run FreeSurfer.

### Get the sandbox
Instead of building a singularity container, we build a sandbox here, so that we can use fakeroot to make changes inside the /usr/local/freesurfer folder, location where it is available in usually all FreeSurfer containers. Otherwise, with the '.sif', we will not have those previlages. You can find the containers at [DockerHub](https://hub.docker.com/).

In /groups/pni/ create a directory e.g., hpc-containers: 
```sh
cd /groups/pni
mkdir hpc-containers
cd hpc-containers
apptainer build --sandbox freesurfer_7.3.1 docker://freesurfer/freesurfer:7.3.1
```

### Get your FreeSurfer license from: 
https://surfer.nmr.mgh.harvard.edu/registration.html.
Afer getting the license to your email, download it and copy it to the location of **Freesurfer** folder inside the sandbox,
Assuming the license.txt file is in the IKIM cluster:
```sh
cp license.txt /groups/pni/hpc-containers/freesurfer_7.3.1/usr/local/freesurfer/
```

Now, the sandbox is ready with the FreeSurfer license.

### Make an .sh script 
Make an .sh script with the following lines (modify according to your needs):
```sh
#!/bin/bash
#SBATCH --mail-user=abc.efg@uk-essen.de
#SBATCH --mail-type=none
#SBATCH --job-name='freesurfer'

# Define your variables
var1=$1 # e.g. study-A01xxxx
var2=$2 # e.g. sub-123xxx
# Set environment variables
export SINGULARITY_BINDPATH="/groups/pni:/groups/pni"
CONTAINER="/groups/pni/hpc-containers/freesurfer_7.3.1"

# Enter the Singularity container and execute commands
singularity shell $CONTAINER <<EOF
export SUBJECTS_DIR="/groups/pni/datasets/${var1}/derivatives/freesurfer"
recon-all -s $var2 -i /groups/pni/datasets/${var1}/${var2}/anat/${var2}_run-01_T1w.nii.gz -all
EOF
```

**_save this script, e.g. freesurfer_job.sh_**

### Submit job
Login to one of the submission nodes (e.g., ssh shellhost - _assuming that you have the shellhost setup from the IKIM docs help_)
```sh
sbatch freesurfer_job.sh study-A01xxx sub-123xxx
```

 Note that the .sh script already has the absolute path of the study-A01xxx directory, which is usually /groups/pni/datasets/ in our case. 
 Make changes accordingly to run in batches, for example:
 1. create an ids.txt file with a list of the name of subjects, and simply submit it as a batch, e.g.:
```sh
for i in $(cat ids.txt);do sbatch freesurfer_job.sh study-A01xxx $i;done
```

There might be few mistakes or errors in this document. If you have encountered any, we can correct the document accordingly.
Hope this helps..



