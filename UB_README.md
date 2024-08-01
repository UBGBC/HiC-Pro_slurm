# How to run on UB servers:

1. You will need to create a cache for your singluarity images and containers as follows:
   ```
   export SINGULARITY_LOCALCACHEDIR=/projects/academic/gbcstaff/utils/singularity-cache/[CCRusername]/tmp
   export SINGULARITY_CACHEDIR=/projects/academic/gbcstaff/utils/singularity-cache/[CCRusername]/tmp
   export SINGULARITY_TMPDIR=/projects/academic/gbcstaff/utils/singularity-cache/[CCRusername]/tmp
   ```
2. Pull the image from docker and convert to singularity image:

   `singularity pull hicpro.sif docker://ubgbc/hicpro_slurm:3.1.0`
   
   Make sure when pulling the image you are in the Overall_dir directory
4. Make all the paths and folders accessible once inside the container
   a. Make sure all the references are found within the directory to be mounted
     ```
     Overall_dir/
       |_ hicpro.sif
       |_ References/
       |_ data/
          |_ Sample1/
              sample1_R1.fastq.gz
              sample1_R2.fastq.gz
          |_ Sample2/
              sample2_R1.fastq.gz
              sample2_R2.fastq.gz
       |_ hicpro-config.txt - edited from this repo
       |_ all other necessary files and directories you might need
     ```
   b. Edit the hicpro-config.txt file to fit your needs - see normal documentation for this

     For Cluster running make sure the PARAMS: CLUSTER QOS JOB_QUEUE and ACCT are set properly:

     For the *gbc* partition:

     `CLUSTER = faculty QOS = gbc JOB_QUEUE = gbc ACCT = gbcstaff`

     For the *general-compute* partition:

     `CLUSTER = ub-hpc QOS = general-compute JOB_QUEUE = general-compute`
   
6. For running the image interactively you can run the following command

   `singularity run -B /path/to/directory/on/server:/path/within/container hicpro.sif`

   Where -B is the mount point flag. Typically you can use /file as the path on the container

   This will open an interactive container that in which you will do the following:

   a. run:  `export PATH=/HiC-Pro_3.0.0/bin/:$PATH` to add HiC-Pro to the ENV variables
   b. change directories to the one we mounted e.g.: `cd /file`
   c. run the hicpro call:
      ```
      HiC-Pro -i data/ -o hicpro_results/ -c hicpro-config.txt -p
      ```

 8. For running the image directly on the server:

    Make sure the file reference paths are to those on the server.
    ```
    Overall_dir/
      |_ hicpro.sif
      |_ data/
          |_ Sample1/
              sample1_R1.fastq.gz
              sample1_R2.fastq.gz
          |_ Sample2/
              sample2_R1.fastq.gz
              sample2_R2.fastq.gz
       |_ hicpro-config.txt - edited from this repo
       |_ all other necessary files and directories you might need

    ../.../UCSC/hg38
       |_ all references - that are found in the hicpro-config.txt file
    ```

    Now we can run the container:

    `singularity exec hicpro.sif HiC-Pro -i data/ -o hicpro_results/ -c hicpro-config.txt -p`

Both versions will take a few hours to run so be patient :) 
