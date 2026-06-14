# MCDS

[Category:Babel](Category%3ABabel.md)
[Category:Clusters](Category%3AClusters.md)
[Category:MCDS](Category%3AMCDS.md)

### Evan's favorite commands

#### I need N GPUs in an interactive session for D days!

Note: only use as many GPUs as you truly need
Run: 
```bash
srun --pty --gres=gpu:N --time=D-00:00:00 --partition=babel-shared bash
```

#### I need a process to continue while my laptop is shut!
Run:
```bash
screen -R <screen_name>
```
This opens a new "screen." A screen is an interactive bash that will continue to process even while you don't have it open! 

- To leave a screen, press CTRL+A then D. 
- To scroll around within a screen, press CTRL+ESC then scroll around.
- To back to "normal" press ESC.

See all active screens:
```bash
screen -ls
```
To rejoin a screen that you left:
```bash
screen -r <screen_name>
```

#### I need to know how much GPU RAM is used and by who?
Run:
```bash
nvidia-smi
```
