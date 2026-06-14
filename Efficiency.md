# Efficiency

[Category:Tips](Category%3ATips.md)
[Category:Efficiency](Category%3AEfficiency.md)
[Category:GPU](Category%3AGPU.md)

## GPU power limits vs. efficiency

Over the last decade or so, there has been several studies regarding setting arbitrary limits on GPU power consumption (possible via nvidia-smi) in order to increase the amount of performance per watt.

The last 5-10% of power fed to a GPU has significantly diminishing returns compared to the first 90%; this [set of graphs for A4000s and RTX 3090s](https://www.pugetsystems.com/labs/hpc/nvidia-gpu-power-limit-vs-performance-2296/#GPU_Power_Scaling_vs_Performance_Results) shows this general trend.

As an example, [reducing the power limit from 366W to 270~280W on an RTX3090 only loses approx. 5% performance](https://www.pugetsystems.com/labs/hpc/quad-rtx3090-gpu-power-limiting-with-systemd-and-nvidia-smi-1983/).

This effect is **more significant on consumer-oriented cards** (RTX 3090, 4090 etc) due to datacenter cards such as A4000s, A5000s often already being built upon [consumer cards that have been power-limited](https://www.techspot.com/review/2342-nvidia-rtx-a4000/#:~:text=Earlier%20this%20year%20Nvidia%20dropped,192%20TAUs%20and%2096%20ROPs.). For example, the RTX A4000 is a low-power version of an RTX 3070.

Limiting maximum GPU power and using the additional power overhead to add another GPU is one potential option to increase performance where power-limited, or simply to significantly reduce noise/heat without impacting performance.

## Standard power limits in the Wean / Gates Machine rooms (as of 2023-07-20)

### Wean Machine  Room (3611)
Our current policy on how many GPUs can go in a rack in the Wean datacenter is **48** GPUs per 208V 60A PDU. (The real max is 48A, or 17293W sustained, due to 80% rule) 

This makes the general rule of thumb to be around 1 GPU per 360W of power. 

Realistically speaking, machines generally don't pull anywhere near this amount as it requires them to be running full blast, however the reason for this limit is also for reducing the presence of large hotspots in the room (and considering the total power consumption of the room, which is nearing its limit).  

### Gates Machine Room (3301)
The Gates computer room should not have any GPU machines installed in it at this time, due to the entire machine room running on battery backup which has a limited capacity.

This may change in the coming years if new power is added to the Gates machine room.

For detailed questions, please contact Kirk Berthold (Computing Operations manager) and/or Junya Inohara (Datacenter Tech Lead).
