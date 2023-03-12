---
layout: default
title: Stealth
description: Stealth Sys Call
nav_order: 2
parent: Operating System Design
grand_parent: Operating Systems
permalink: /operating-systems/os-design/stealth
---

# Stealth
A system call editing the `task_struct` of a process. The idea behind stealth was to either have a 1 or a 0 assigned to a new field in the `task_struct` called ‘stealth’. I wanted to hide the process from within proc which is used by the `ps` bash command to display all running processes on the system. When 0 is assigned to ‘stealth’ in a process it is not hidden, when 1 is assigned to a process it is hidden

---

## Changes to a processes task_struct
Initializes a new process with a stealth attribute in the `task_struct`. Changes made in `include/linux/sched.h`. Pretty simple

```c
int	stealth;
```


Changes were also made to set the stealth property to 0 at initialization. Changes made under `/init/init_task.c`

```c
struct task_struct init_task
#ifdef CONFIG_ARCH_TASK_STRUCT_ON_STACK
    __init_task_data
#endif
    __aligned(L1_CACHE_BYTES)
= {
#ifdef CONFIG_THREAD_INFO_IN_TASK
    .thread_info    = INIT_THREAD_INFO(init_task),
    .stack_refcount = REFCOUNT_INIT(1),
#endif
    .__state    = 0,
    .stealth    = 0,  
```

---

## Hiding the process from Proc
This step tells proc not to display a process, thereby hiding it from `ps`. The change is made under `/fs/proc/base.c`, to the function `proc_read_readdir()` which handles the /proc/TGID directories

```c
static int proc_task_readdir(struct file *file, struct dir_context *ctx)
{
	struct inode *inode = file_inode(file);
	struct task_struct *task;
	struct pid_namespace *ns;
	int tid;

	if (proc_inode_is_dead(inode))
		return -ENOENT;

	if (!dir_emit_dots(file, ctx))
		return 0;

	/* f_version caches the tgid value that the last readdir call couldn't
	 * return. lseek aka telldir automagically resets f_version to 0.
	 */
	ns = proc_pid_ns(inode->i_sb);
	tid = (int)file->f_version;
	file->f_version = 0;
	for (task = first_tid(proc_pid(inode), tid, ctx->pos - 2, ns);
	     task;
	     task = next_tid(task), ctx->pos++) {
		char name[10 + 1];
		unsigned int len;

		tid = task_pid_nr_ns(task, ns);
		//Tanner addition start														***********************************************************************
		if(task->stealth == 1){
			continue;
		}
		//Tanner addition end														***********************************************************************
		if (!tid)
			continue;	/* The task has just exited. */
		len = snprintf(name, sizeof(name), "%u", tid);
		if (!proc_fill_cache(file, ctx, name, len,
				proc_task_instantiate, task, NULL)) {
			/* returning this tgid failed, save it as the first
			 * pid for the next readir call */
			file->f_version = (u64)tid;
			put_task_struct(task);
			break;
		}
	}

	return 0;
}
```
---

## stealth.c
Actual system call, look [here](elevate) for steps on adding entries to make the system call work

```c
#include <linux/kernel.h>
#include <linux/syscalls.h>
#include <linux/sched.h>
#include <linux/pid.h>
#include <linux/cred.h>
#include <linux/list.h>
#include <linux/spinlock_types.h>
#include <linux/spinlock.h>
#include <linux/errno.h>
#include <linux/jiffies.h>


SYSCALL_DEFINE1(stealth, pid_t, process)
{

	static DEFINE_SPINLOCK(lockForTask);
	struct task_struct	 *task;

	if(process < 0){
		return ESRCH;
	}

	
	printk("Stealth syscall on process: %d\n", process);


	task = find_task_by_vpid(process);
	printk("Process pid double check: %d\n", task->pid);


	printk("Process name: %s\n", task->comm);
	printk("Stealth variable from task_struct: %d\n", task->stealth);


	if(task->stealth == 0){
		spin_lock(&lockForTask);
		if(task->timer != 0){
			task->timer = 0;
		}
		task->stealth = 1;
		task->timer = jiffies;
		spin_unlock(&lockForTask);
	}else if(task->stealth == 1){
		spin_lock(&lockForTask);
		task->stealth = 0;
		spin_unlock(&lockForTask);
	}
	
	printk("Stealth variable from task_struct altered: %d\n", task->stealth);

	return 0;
}
```

---

### In use
<img src="{{site.baseurl}}/assets/operating-systems/os-design/stealth.png"  width="120%" height="70%">



