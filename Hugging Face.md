# Hugging Face

# Hugging Face Cache Configuration on Babel HPC

This page outlines how to configure and use Hugging Face (HF) models and datasets in the Babel HPC Environment, leveraging shared and personal storage options to optimize resource use.

## Overview
Babel provides a shared directory for Hugging Face models and datasets to prevent duplication across users. Additionally, personal storage is available for user-specific caches or larger data needs. Note that the `/data/user_data/<username>/` directory is limited and only mounts on compute nodes.

## Storage Locations
The cluster offers the following storage paths for Hugging Face usage:

- **Shared HF Cache**: `/data/hf_cache/`
  * Base path for shared Hugging Face models and datasets.
  * Accessible to all users in the `hpcgroup` group.
  * Use this for storing and accessing pre-downloaded models and datasets to avoid redundant downloads.
- **Personal $HOME**: `/home/<username>`
  * Mounted only on all nodes.
  * Limited to 100 GB.
  * Suitable for small, user-specific files (e.g., authentication tokens), but not for large caches or compute tasks.
- **Personal Data Space**: `/data/user_data/<username>`
  * Available on compute nodes only.
  * Intended for larger user-specific cache needs or temporary files.
See: [Filesystem Layout](Filesystem.md#filesystem_layout)

## Key Considerations
- **Login vs. Compute Nodes**: The `/home/<username>` directory is only available on login nodes. All computation, including running Hugging Face jobs, must occur on compute nodes where `$HOME` is not mounted.
- **Storage Limits**: Due to the 100 GB limit on `$HOME`, avoid using it for large model/dataset caches. Use `/data/user_data/<username>` instead for personal needs beyond tokens or small configs.
- **Other Datasets**: Note that `/data/datasets/` contains other non-Hugging Face datasets; use `/data/hf_cache/` specifically for HF resources (confirmed as the designated shared HF path).

## Configuring Hugging Face
To use the shared and personal storage effectively with Hugging Face libraries (`transformers`, `datasets`, `huggingface_hub`), set the following environment variables:

### Recommended Setup
For most users, configure your environment to use the shared HF cache for models/datasets and a personal directory for other files:

```
 export HF_HOME=/data/user_data/<username>/.hf_cache
 export HF_HUB_CACHE=/data/hf_cache/hub
 export HF_DATASETS_CACHE=/data/hf_cache/datasets
 export HF_HUB_OFFLINE=1  # Optional\
```

- Replace `<username>` with your actual username (e.g., `dvosler` → `/data/user_data/dvosler/.hf_cache`).
- Add these lines to your `.bashrc` on the login node or include them in your SLURM job scripts.

### Explanation of Variables
- `HF_HOME`: Base directory for all HF files (default: `~/.cache/huggingface`). Set to a personal path to keep user-specific files separate.
- `HF_HUB_CACHE`: Directory for cached models from the Hugging Face Hub.
- `HF_DATASETS_CACHE`: Directory for cached datasets.
- `HF_HUB_OFFLINE`: Set to `1` to force offline mode, relying only on cached files.

### Example SLURM Script
Below is an example SLURM script to run a Hugging Face job on a compute node:

  #!/bin/bash
  #SBATCH --job-name=hf_test
  #SBATCH --output=/home/%u/logs/sbatch/output_%A_%a.out
  #SBATCH --error=/home/%u/logs/sbatch/error_%A_%a.err
  #SBATCH --partition=&lt;partition_name&gt;
  #SBATCH --time=1-10:05:00
  #SBATCH --ntasks=2
  #SBATCH --mem=69G
  
  # Set Hugging Face environment variables
```
 export HF_HOME=/data/user_data/$USER/.hf_cache
 export HF_HUB_CACHE=/data/hf_cache/hub
 export HF_DATASETS_CACHE=/data/hf_cache/datasets
 export HF_HUB_OFFLINE=1
```
  
  # activate python env
```
 eval "$(conda shell.bash hook)"
 conda activate hf_stuff
```
  
  # Run your script
```
 python my_hf_script.py
```

## Usage Guidelines
- **Shared Cache**: The first user to download a model/dataset to `/data/hf_cache/` caches it for everyone. Subsequent users reuse it automatically if `HF_HUB_CACHE` and `HF_DATASETS_CACHE` are set correctly.
- **Personal Cache**: Use `/data/user_data/<username>` for larger personal files (e.g., custom model checkpoints) or if you need isolation from other users.
- **Pre-Downloading Models**: Admins or users can pre-populate the shared cache:

```
 from huggingface_hub import snapshot_download
 snapshot_download(repo_id="bert-base-uncased", cache_dir="/data/hf_cache/hub")
```

## Permissions
- The `/data/hf_cache/` directory uses ACLs with permissions 770 (dirs) and 660 (files), owned by the initial user and the `hpcgroup` group.
- Ensure you’re in the `hpcgroup` group (`groups` command) to access and write to the shared directory.

## Troubleshooting
- **Model Not Found**: Verify your `HF_HUB_CACHE` path and ensure the model is cached or that you have internet access on the login node to download it.
- **Permission Denied**: Check group membership and directory permissions with `ls -l /data/hf_cache`.
- **Quota Issues**: If `$HOME` fills up, move cache to `/data/user_data/<username>`.

## See Also
- [Model Fine-tuning](Model%20Fine-tuning.md)
- [SLURM Usage Guide](Slurm.md)
- [Storage Policies](Filesystem.md)
- [Face Hub Documentation](https://huggingface.co/docs/hub/index|Hugging)

[Category:HPC Documentation](Category%3AHPC%20Documentation.md)
[Category:Hugging Face](Category%3AHugging%20Face.md)
