# MRIQC via apptainer  
We will try to utilize apptainer for MRIQC in this wiki. For this, unlike the FreeSurfer, we do not need a sandbox (i.e., getting fakeroot). 
Simply making a sigularity container will suffice.

Infact, the containers /.sif image is already present in (read/execute only)
```sh
/groups/pni/sandboxes
```

But, you can make your own container, in a folder e.g., 
```sh
/groups/pni/username/
```

Anyways, let us first get the container from DockerHub via:
```sh
apptainer build /groups/pni/username/myMRIQC.sif docker://poldracklab/mriqc:latest
```
'latest' refers to their latest deployed container on DockerHub. You can replace it with your own required version, e.g.,
docker://poldracklab/mriqc:0.16.1

## create required directories first
- Make sure the data is in BIDS format.
- Also create the mriqc folder inside the derivatives folder per study. 

## Create a .sh script for cluster
```sh
#!/bin/bash
#SBATCH --mail-user=raviteja.kotikalapudi@uk-essen.de
#SBATCH --mail-type=none
#SBATCH --job-name='mriqc'


# Define your variables
var1=$1 # bids root
var2=$2 # sub-xxx

# Set environment variables
export SINGULARITY_BINDPATH="/groups/pni:/groups/pni"
CONTAINER="/groups/pni/sandboxes/mriqc_v22.0.6.sif"

# Enter the Singularity container and execute commands
singularity exec $CONTAINER mriqc \
        /groups/pni/datasets/${var1} \
        /groups/pni/datasets/${var1}/derivatives/mriqc \
        participant --participant-label ${var2}\
        --no-sub
```
Please note that here, I simply use the already avaialbe container i.e., /groups/pni/sandboxes/mriqc_v22.0.6.sif Change it if you want.

## Send the script to cluster with sbatch
```sh
sbatch /path/to/script.sh study-A1xxxx sub-xxxx
```

Now, MRIQC will run on sub-xxxx present in study-A1xxxx on the cluster. Presently, each sub-xxxx will occupy 1/64 cores from 1 node/CPU.
By default it will do QC for T1 and BOLD images, and generates important quality control metrics. 
To find more, [click here](https://mriqc.readthedocs.io/en/latest/).


