# FreeSurfer via apptainer
To know more about Freesurfer, a surface-based morphometry toolbox, you can visit: https://andysbrainbook.readthedocs.io/en/latest/FreeSurfer/FreeSurfer_Introduction.html.
Here, we will provide information on how to run freesurfer on the HPC cluster, so that everyone can try this approach. 

## Set-up apptainer
Documentation on apptainer/sandbox is made available at: https://ikim-essen.github.io/ClusterDocs/.

Let us get started with setting up the required things to run FreeSurfer.

### Get the sandbox
Instead of building a singularity container, we build a sandbox here, so that we can use fakeroot to make changes inside the /usr/local/freesurfer folder, location where it is available in usually all FreeSurfer containers. Otherwise, with the '.sif', we will not have those previlages. 

In /groups/pni/ create a directory e.g., hpc-containers: 
```sh
cd /groups/pni
mkdir hpc-containers
apptainer build --sandbox /groups/pni/hpc-containers docker://freesurfer/freesurfer:7.3.1
```
