---
title: "Check GPU whether working and then send email"
date: 2023-12-05
permalink: /posts/2023/12/monitor-server-gpu/
tags:
  - shell
  - nvidia-smi
  - english
---

From one computer monitor all server's GPU.
The server must support `nvidia-smi`.

```bash
###
 # @Author: Ni Runyu & MonkeyDC
 # @Date: 2023-12-04 17:51:19
 # @LastEditors: Ni Runyu & MonkeyDC
 # @LastEditTime: 2023-12-05 11:20:17
 # @Description: check all server's GPU
 #
 # Copyright (c) 2023 by Ni Runyu, All Rights Reserved.
###

funReturnGPUInfo() {
  local total=$(ssh $1 'nvidia-smi --query-gpu=memory.total --format=csv,noheader,nounits')
  local using=$(ssh $1 'nvidia-smi --query-gpu=memory.used --format=csv,noheader,nounits')

  # using \n will cause problem
  total=${total//$'\n'/$'\t'}
  using=${using//$'\n'/$'\t'}

  # Cutting strings into arrays using '\t'
  local IFS=$'\t'

  read -ra total_a <<< "$total"
  read -ra using_a <<< "$using"

  # Prints memory usage and total memory for each GPU
  for i in "${!total_a[@]}"; do
    echo "GPU $((i+1)) Memory Usage: ${using_a[$i]} / ${total_a[$i]}"
  done

}

sever_names[0]=username@servername
sever_names[1]=username@servername2

for server in "${sever_names[@]}"
do
  echo ""$server"*************************************************"
  gpu_info=$(funReturnGPUInfo "$server")
  echo "GPU info for:"$'\n'" $gpu_info"
done
```
