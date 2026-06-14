# Training Material

### Connecting to Cluster
Each team will be assigned with a temporary Andrew id. You will need the Andrew id to access Cluster. 

Follow the [Instructions](Connecting%20to%20the%20Cluster.md) to connect to Cluster

### Instruction with SLURM
SLURM (Simple Linux Utility for Resource Management) is a job scheduler and resource management system commonly used in high-performance computing (HPC) environments. You will need to get familiar with basic usage of SLURM in order to interact with the Cluster

Refer to [Beginner's guide to the SLURM workload manager](Slurm.md) for more details.

### Monitoring
To monitor cluster activities such as jobs, compute resources, and disk usage, learn about essential techniques and tools for effective cluster monitoring here: [Monitoring](Monitoring.md).

### LLM Deployment Demo

#### LLaMA
Released this past February by Meta Research, LLaMA is the latest and greatest in open-source pre-trained LLMs. The LLaMA models range in size from 7 billion to 65 billion parameters. It was trained mostly on scraped internet data, with some other data sources, such as Github code, Books, and academic papers thrown in. Since LLaMA's release, it has also been finetuned for instruction-following and conversational alignment

*Accessibility***:** LLaMA is more efficient and less resource-intensive than other models, and it is available under a non-commercial license to researchers and other organizations. LLaMA is available in various sizes(7B, 13B, 33B, and 65B parameters), making it accessible to a range of computing resources.

*Open-source Community*: LLaMA models are part of the open-source ecosystem, users can benefit from the extensive community support, documentation, and shared resources available through platforms like HuggingFace.

#### Setting up LLaMA on Babel:
*Server*

1. Clone repo: https://github.com/neulab/lti-llm-deployment
1. Checkout to  `update-lamma`  branch
1. (If not already installed) Install pip using https://pip.pypa.io/en/stable/installation/
1. Create a virtual environment with `Miniconda`
    1. `wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh`
    1. `bash Miniconda3-latest-Linux-x86_64.sh`
    1. `conda create --name myenv`
    1. `conda activate myenv`
1. Start a shell in compute node:  `srun -G <num_of_gpu> -t <num_of_hours> -p <partition_name> -w <node_name> --pty $SHELL`
    - `num_of_gpu` : The number of GPUs you would like to allocate, each team have 8 GPUs. 
    - `num_of_hours` : Time limits (days) for the job, maximum value is 20 days.
    - `partition_name` , `node_name` : Each team is assigned with a specific partition and compute node, consulting your mentor if you don't have them.
1. Activate the conda environment: `conda activate myenv`
1. Change directory to use lti-llm-deployment   `cd lti-llm-deployment` 
1. Install dependencies ([README](https://github.com/neulab/lti-llm-deployment/blob/main/inference_server/README.md)):
    1. `pip install flask flask_api gunicorn pydantic accelerate huggingface_hub>=0.9.0 deepspeed>=0.7.3 deepspeed-mii==0.0.4`
    1. `pip install sentencepiece`
1. Set environment variable (Required for models that have greater than 7B params):   `export TRANSFORMERS_CACHE=/data/user_data/<andrew_id>`
1. Run the desired scripts to launch the LLMs   `bash launch_llama7b_fp16_2gpu_server.sh` Upon successful execution, the message *“model loaded”* is printed on the terminal

*Client*

1. Start another session on babel to set up the client-server
1. Create a python file with the following contents
```python
import llm_client

client = llm_client.Client(address=”babel-0-23”, port = 5000) 
text = “CMU students are” 
output = client.prompt([text]) 
tokens, scores = client.score([text]) 
print(text) 
print(output[0].text) 
for tok, s in zip(tokens, scores): 
    print(f"[{tok}]: {s:.2f}")
```

1. Run `python filename.py`
