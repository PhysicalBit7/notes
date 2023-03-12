---
layout: default
title: Stealth Proc Entry
description: Stealth Proc Entry
nav_order: 3
parent: Operating System Design
grand_parent: Operating Systems
permalink: /operating-systems/os-design/stealthproc
---
# Stealth Proc Entry
I wanted to make a change to /proc, creating a new file, and displaying processes hidden with the stealth system call, as well as how long they have been hidden. Changes started with adding another attribute to the `task_struct`. This new field was called ‘timer’ and would be tasked with holding a jiffies value in order to calculate time since "stealthed"

---

## Additional Changes to the task_struct
Adds a timer attribute to the task_struct

```c
unsigned long timer;
```

Upon calling the 'stealth' system call, the timer attribute would be initialized with the current system jiffy value

---

## underStealth Proc Entry
The actual proc entry was created utilizing `proc_create_single()`. The following code is ran at initialization, when `fs_initcall()` is called

```c
#include <linux/fs.h>
#include <linux/init.h>
#include <linux/proc_fs.h>
#include <linux/sched.h>
#include <linux/seq_file.h>
#include <linux/time.h>
#include <linux/time_namespace.h>
#include <linux/kernel_stat.h>
#include <linux/sched.h>
#include <linux/jiffies.h>
#include "internal.h"

static int stealthTracker(struct seq_file *m, void *v){

	struct task_struct *task;
	unsigned long pid;
	const char *name;
	unsigned long js;
    
	for_each_process(task){
		if(task->stealth == 1)
		{
			pid = task->pid;
			name = task->comm;
			js = jiffies - task->timer;
			js = js / HZ;

			seq_printf(m, "pid: %lu name: %s secondsHidden: %lu\n",
					pid,
					name,
					js);
		}
	}
	return 0;
}

static int __init stealthTacker_uptime_init(void)
{
	struct proc_dir_entry *pde;

	pde = proc_create_single("underStealth", 0, NULL, stealthTracker);
	pde_make_permanent(pde);
	return 0;
}

fs_initcall(stealthTacker_uptime_init);
```

## Makefile
Changes included to the proc Makefile. Simply add the entry below

```
proc-y  += stealthTracker.o
```

---

### In use
<img src="{{site.baseurl}}/assets/operating-systems/os-design/underStealth.png"  width="120%" height="70%">

*The only bug I have to work out is why the cat command is displayed as well*