Operating System From Scratch
-----------------------------

Step 06: Process
````````````````

Here comes the real cool thing: process.
You will see three simple processes doing their own jobs simultaneously.
A simple process scheduling policy is also introduced.

The first process
'''''''''''''''''

We'll create our first process. There're several stuff we should consider.

What does a process execute?
""""""""""""""""""""""""""""

Apparently a process should have something to execute.
It's good to keep our first process as small as possible, so we'll prepare a very simple function.
The process will run it. See ``a/kernel/main.c``.

How does our OS manage the processes?
"""""""""""""""""""""""""""""""""""""

Processes must be managed.
The creation, scheduling, destroying must be done in the OS kernel.
We must have some metadata for each process.
This metadata is called PCB (Process Control Block).
In Orange'S the PCB is implemented as a structure ``struct s_proc``, see ``a/include/proc.h``.
You can see in the code what we store for a process in the PCB.

In what privilege level will the processes be running?
""""""""""""""""""""""""""""""""""""""""""""""""""""""

The x86 protect mode provides 4 levels: ring0, ring1, ring2 and ring3.
The kernel will run in ring0.
System processes (aka. TASKS) and user processes (aka. PROCS) will run in ring1 and ring3, respectively.

When the kernel gets control from the boot loader, it's in ring0, so if we want to run a process in ring1 or ring3, we must change the privilege level.
Therefore we have pretty much things to prepare, such as GDT, LDT, TSS, etc.
We have learned how to manipulate these stuf in `Step 03`_.
If you still have any questions, you may want to read Intel's IA-32 Architectures Software Developer's Manual, which can be downloaded from Intel's website.

Start the first process
"""""""""""""""""""""""

When we have prepared the PCB and all the stuff around it, we can start the process.
It would look like this:

.. image:: http://osfromscratch.org/snapshots/original/%E5%9B%BE06.10%20%E8%BF%9B%E7%A8%8B%E5%BC%80%E5%A7%8B%E8%BF%90%E8%A1%8C.png

In this snapshot, the process keeps printing chars followed by increasing numbers.

More processes
''''''''''''''

Once we have one process, we want more.

You'll see how we get more in the code. It's not hard, but we must be very careful when handling the details, such as switching between the kernel stack and the process stack (see ``b/``),
kernel re-enter (see ``c/``), saving and storing PCBs of processes (see ``d/``), switching PCBs in the clock interrupt handler (see ``e/``), etc.

At the end of ``e/``, we already have two processes:

.. image:: http://osfromscratch.org/snapshots/original/%E5%9B%BE06.18%20%E5%AE%9E%E7%8E%B0%E5%A4%9A%E8%BF%9B%E7%A8%8B.png

It's easy to add the third, we just

+ prepare the code the process will execute, and
+ add an item in the PCB table

and it's done (See ``f/``).

System call
'''''''''''

We've got a kernel and several processes.
Can these processes communicate with the kernel?
Yes, via system calls.
Processes to kernel is like clients to server.
A process sends some request and the kernel responds.
Now let's implement a system call (see ``l/`` and ``m/``) and make use of it (see ``n/``).


Scheduling
''''''''''

Currently all the processes are equal.
All of them have the same chances to run.
But sometimes we want some of them run more and some less.
You'll see how we achieve this in ``p/``.
The scheduling algorithm is slightly improved in ``r/``.

Here are three processes A, B, C with priorities 15, 5, 3 (bigger number implies higher priority) running simultaneously:

.. image:: http://osfromscratch.org/snapshots/original/%E5%9B%BE06.32%20%E4%BC%98%E5%85%88%E7%BA%A7%E5%92%8C%E8%BF%9B%E7%A8%8B%E5%BB%B6%E8%BF%9F%E6%97%B6%E9%97%B4%E6%94%B9%E5%8F%98%E5%90%8E%E7%9A%84%E6%89%A7%E8%A1%8C%E6%83%85%E5%86%B5.png

`‹prev`_   `next›`_

.. _`Step 03`: https://github.com/yyu/osfs03
.. _`‹prev`: https://github.com/yyu/osfs05
.. _`next›`: https://github.com/yyu/osfs07
