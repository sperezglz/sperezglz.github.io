---
layout: post
title:  "Virtual Firmware loading"
date:   2024-01-16 21:46:00 -0500
categories: firmware virtualization 
---


Just like a real (computer) machine typically requires some type of firmware, like 'bios' to boot, some virtual machines, or VMs, also require a form of virtual firmware to run. It is a bit strange to think that virtual machines would require a firmware, which in physical machines is used to initialize io's, memory, and other clearly physical hardware. But indeed, this is how some VMs work in the cloud, I don't know if in all instances this is the case, but I know this is the case for VMs that run in 'secure' hardware environments. Without going too deep into the topic, in recent years, various companies have introduced the concept of Trusted Execution Environment ([TEE][tee-wikipedia]), which is even defined as a set of industry standards. In summary, trusted execution environment, defines an operating mode in some computer chips, in which software runs with resources isolated and protected from other software components, which would be considered untrusted. The goal is that software runs with higher levels of security protections, and has a lower risk of being compromised by bad actors.

Coming back to the 'virtual firmware', it is used to initialize some internal data and metadata structures or resources that the Guest OS needs to be able to run, and in some cases, also run security assurance processes. Virtual firmware does not really need to initialize any hardware directly, because at that point that has already been done by physical machine firmware, and all those resources are abstracted by the OS and/or the hypervisor. Nevertheless, virtual firmware follows some of the same protocols and internal organization as regular firmware uses, this is an interesting area to zoom in.

The virtual firmware for TEE VMs and containers is organized following [the td-shim specification][td-shim-spec]. Inspecting an example binary we can identify some of the metadata that the VMM uses to load and map the firmware's sections. With the `hexdump` utility, we can take a look at the internals of the binary. According to the td-shim spec the offset inside the binary where to find the metadata structure is `0x20` before the file end. 


![image1](/assets/images/img1-fw.png)
*Fig. 1 - Metadata offset found at (file_size - 0x20).*

The offset where to find the metadata is `0xcaf010`, the metadata descriptor can be easily located with the signature string, `TDVF`.
![image2](/assets/images/img2-fw.png)
*Fig. 2 - Metadata descriptor structure starts with the signature string TDVF.*

With the use of a simple application, we can display the properties of the different sections in the binary. These properties inform the VMM on how to build the memory map of the VM for the FW to run, and at what offsets to find the parts of the binary to be loaded in memory.

![image3](/assets/images/img3-fw.png)
*Fig. 3 - Output of a small application that displays the sections in the binary. Details [here][td-shim-spec].*


[tee-wikipedia]: https://en.wikipedia.org/wiki/Trusted_execution_environment
[td-shim-spec]: https://github.com/confidential-containers/td-shim/blob/main/doc/tdshim_spec.md



