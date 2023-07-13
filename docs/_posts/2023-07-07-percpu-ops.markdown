---
layout: post
title:  "Per-CPU operations"
date:   2023-07-07 18:25:01 -0500
categories: Linux kernel 
---


In multi-core processors there are usually certain operations for which software needs to create and maintain variables or data structures in a 1-per-core relation, in my experience, I have seen this for the enabling of the VMX operation in the x86-64 architecture but I'm sure that there are many other examples in this arch as well as in others. The OS is the software component that typically manages the low level hardware resources and architectural features, so this kind of per-cpu operations are something that the OS/kernel deals with. In the case of the linux kernel there is a group of very useful APIs to express the per-cpu operations very efficiently and quite easily. 

Let's start with a simple example. Let's say that you need to use variable, an int for example, but there must be one instance of it for each core present in the CPU. If you want to keep your code portable you would need to inspect your cpu at runtime and find out how many cores it has, then allocate some dynamic memory to store your array of int variables. You don't need to implement those steps manually since the Linux kernel supports this in a very simple and elegant way.  

{% highlight ruby %}
DEFINE_PER_CPU(<var type>, <name>) 

//i.e.
DEFINE_PER_CPU(int, foo_per_core)
unsigned int cpu_index; 
...
...

for_each_possible_cpu(cpu_index)
	per_cpu(foo_per_core, cpu_index) = 5;
//or

for_each_possible_cpu(cpu_index)
	x = per_cpu(foo_per_core, cpu_index);

//
//<linux kernel src>/include/linux/percpu-defs.h


{% endhighlight %}



How the Linux kernel implements this mechanism is a deep topic, where the compiler also takes part, I will not explore that here but this [blog post][0xax-website] explains it in very good detail. 


The Linux kernel also extends this functionality to be able to run functions on specific cores in the system. This is typically useful in some cases where hardware requires that a particular asm instruction or sequence is executed per-core basis, for enabling a feature or managing data structures that are independent for each core.  

{% highlight ruby %}
//i.e.
DEFINE_PER_CPU(int, foo_per_core)
static int x = 0;
...
...

void functionX(void *ptr) {
	int y;

	this_cpu_write(foo_per_core, x);
	// or
	y = this_cpu_read(foo_per_core);
}


void functionY(void) {
	on_each_cpu(functionX, NULL , 1)
}

//
//<linux kernel src>/include/linux/smp.h



{% endhighlight %}



[0xax-website]: https://0xax.gitbooks.io/linux-insides/content/Concepts/linux-cpu-1.html
