---
title: "Check processes are still working"
date: 2023-12-14
permalink: /posts/2023/12/check-process-send-email/
tags:
  - python
  - linux
  - english
---

I found there's a problem with checking GPU's memory to judge whether the program is running.

When running some "small" program, it's easy to misjudge.

So I consider judging the number of processes to determine whether the program is over. Because I always use bash to run program, I chose to use number of process instead of process id.

Example code:

```python
"""
Author: Ni Runyu & MonkeyDC
Date: 2023-09-14 14:44:30
LastEditors: Ni Runyu & MonkeyDC
LastEditTime: 2023-12-14 14:34:30
Description: Check working process number by top

Copyright (c) 2023 by Ni Runyu, All Rights Reserved.
"""

import os
import re
import time

import yagmail

def top_process_info(user_name):
    cpu_info = os.popen(f"top -n 1 -u {user_name}").readlines()
    processes = list()
    for line in cpu_info:
        if 'python' in line:
            processes.append(re.findall(r"\d+", line)[0]) # save the Pid

    return processes


if __name__ == "__main__":
    user_name = os.getlogin()
    server_name = os.uname()[1]

    processes_begin = top_process_info(user_name)
    patience_times = 3 #
    if not len(processes_begin):
        print("no python process")
    else:
        processes_num = len(processes_begin)
        break_times = 0

        while True:
            processes_now = top_process_info(user_name)
            print(f"On {server_name}: {time.asctime()} : {len(processes_now)} working process")
            # as using bash, so according number of processes to judge.
            if len(processes_now) < processes_num:
                break_times += 1
            else:
                break_times = 0

            if break_times >= patience_times:
                try:
                    mail_server = yagmail.SMTP(
                        user="send_email_address",
                        password="SMTP_password",
                        port=587,
                        smtp_starttls=True,
                        smtp_ssl=None,
                    )
                    email_address = ["receive_email_address"]
                    title = ["some of %s experiment finished" % (server_name)]
                    content = [" "]
                    mail_server.send(to=email_address, subject=title, contents=content)
                    mail_server.close()
                    print("sending finished")
                except Exception as e:
                    print(repr(e))
                break
            time.sleep(120)

```
