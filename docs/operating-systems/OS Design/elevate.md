---
layout: default
title: Elevate
description: Elevate Sys Call
nav_order: 1
parent: Operating System Design
grand_parent: Operating Systems
permalink: /operating-systems/os-design/elevate
---

# Elevate
A simple system call I built in order to elevate a process to root. Lives under `/kernel`. Need to provide a PID. Most simple way to observe is by applying to a bash instance, and then typing `bash` to create a child shell. You should observe the shell running in root

## elevate.c

```c
#include <linux/kernel.h>
#include <linux/syscalls.h>
#include <linux/sched.h>
#include <linux/pid.h>
#include <linux/cred.h>
#include <linux/spinlock_types.h>
#include <linux/spinlock.h>
#include <linux/errno.h>

SYSCALL_DEFINE1(elevate, pid_t, a)
{
	struct task_struct *task;
	struct cred *task_cred;	
	static DEFINE_SPINLOCK(lockForTask);
	
	if(a < 0){
		return ESRCH;
	}

	printk("Entering elevate: process: %d\n", a);
	
	for_each_process(task){
		if(task->pid == a)
		{
			task_cred = task->cred;
			printk("Printing UID %u\n", task_uid(task).val);
			printk("Printing EUID %u\n", task_euid(task).val);

			spin_lock(&lockForTask);
			task_cred->uid.val = 0;
			task_cred->euid.val = 0;
			spin_unlock(&lockForTask);

			printk("Printing altered UID %u\n", task_uid(task).val);
			printk("Printing altered EUID %u\n", task_euid(task).val);
		}
	}

	return 0;
}
```
---

## Include syscall
Add your call to the syscall header file. Found at `/include/linux/syscalls.h `

```c
asmlinkage long sys_elevate(pid_t);
```
---

## Syscall Table Entry
Need to add your syscall to the syscall table. Lives under `/arch/x86/entry/syscalls/syscall_64.tbl`

```
451	common	elevate			sys_elevate
```

---
## Makefile
Modify the Makefile for whatever directory your syscall lives in. Mine was under `/kernel`

```
# SPDX-License-Identifier: GPL-2.0
#
# Makefile for the linux kernel.
#

obj-y     = fork.o exec_domain.o panic.o \
	    cpu.o exit.o softirq.o resource.o \
	    sysctl.o capability.o ptrace.o user.o \
	    signal.o sys.o umh.o workqueue.o pid.o task_work.o \
	    extable.o params.o \
	    kthread.o sys_ni.o nsproxy.o \
	    notifier.o ksysfs.o cred.o reboot.o \
	    async.o range.o smpboot.o ucount.o regset.o \
	    elevate.o stealth.o
```

---

### In use
<img src="{{site.baseurl}}/assets/operating-systems/os-design/elevate.png"  width="120%" height="70%">
