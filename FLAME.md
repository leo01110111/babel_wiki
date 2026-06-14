# FLAME

## Multi-node Training
### Launch Script
Here's an example of a launch script to start a multi-node training job with three H100 nodes using deepspeed:
 #!/bin/bash
 #SBATCH --job-name=flame
 #SBATCH --output=flame.out
 #SBATCH --error=flame.err
 #SBATCH --partition=flame
 #SBATCH --nodes=3
 #SBATCH --ntasks-per-node=1
 #SBATCH --cpus-per-task=12
 #SBATCH --mem=2000G
 #SBATCH --gres=gpu:H100:8
 #SBATCH --time=2-00:00:00
```
srun --jobid $SLURM_JOB_ID bash -c 'source ~/.bashrc && conda activate <your conda env> (optional: && WANDB__SERVICE_WAIT=500 WANDB_PROJECT=<wandb project> WANDB_ENTITY=<wandb entity> WANDB_NAME=<wandb name>) python -u -m torch.distributed.run \
--nproc_per_node 8 (number of gpus per node) \
--nnodes 3 (number of nodes) \
--rdzv_endpoint flame-10-7:6000 (master node and port) \
--rdzv_backend c10d \
--max_restarts 0 \
--tee 3 \
--node_rank $SLURM_PROCID \
(Following is the command to launch training, you may have your own command. The simple example below uses Llamafactory)
LLaMA-Factory/src/train_bash.py     --deepspeed ds_config_3.json     --stage sft     --model_name_or_path <model path>     --do_train     --dataset <dataset>     --train_size <train size>   --dataset_dir <dataset directory>  --template llama2     --finetuning_type full     --output_dir <output directory>    --per_device_train_batch_size <per device train batch size>     --per_device_eval_batch_size <per device eval batch size>    --gradient_accumulation_steps <gradient accumulation step>     --gradient_checkpointing True     --lr_scheduler_type cosine     --evaluation_strategy "steps"     --save_strategy "steps"     --save_steps 500     --logging_steps "30"     --save_total_limit <save total limit>     --preprocessing_num_workers 16     --learning_rate <lr>     --weight_decay <weight decay>     --warmup_ratio <warmup ratio>     --num_train_epochs <epochs>     --plot_loss     --bf16 True     --cutoff_len <cutoff length>    --flash_attn True   --report_to "wandb"  --seed <seed>'
> output.txt 2>&1
```

Hostfile (create a file name “hostfile” with the following content)
```
flame-10-7 slots=8
flame-10-15 slots=8
flame-10-23 slots=8
```

### Potential Errors and Fixes
- To debug, first set NCCL logging to INFO: export NCCL_DEBUG=INFO

- ERROR ibv_create_cq(cqe=4096) failed: Cannot allocate memory
    - This error occurs when max locked memory is not sufficient. You can check how much max locked memory you have by running ulimit -a. On Babel, the login node has max locked memory = 64 on login node and max locked memory = unlimited on compute node. So to avoid this problem, run from a compute node.
