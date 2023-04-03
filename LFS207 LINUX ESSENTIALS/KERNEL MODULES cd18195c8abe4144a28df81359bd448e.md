# KERNEL MODULES

Created: April 3, 2023 1:54 PM
Reviewed: No

# **Chapter Overview**

The Linux kernel makes extensive use of modules, which contain important software that can be loaded and unloaded as needed after the system starts. Many modules incorporate device drivers to control hardware either inside the system or attached peripherally. Other modules can control network protocols, support different filesystem types and many other purposes. Parameters can be specified when loading modules to control their behavior. The end result is great flexibility and agility in responding to changing conditions and needs.

# L**earning Objectives**

By the end of this chapter, you should be able to:

- List the advantages of utilizing kernel modules
- Use **insmod**, **rmmod** and **modprobe** to load and unload kernel modules
- Use **modinfo** to find out information about kernel modules
- Learn how to configure modules

# **Kernel Modules**

The Linux kernel makes extensive use of modules, which contain important software that can be dynamically loaded and unloaded as needed after the system starts.

Many modules incorporate device drivers to control hardware either inside the system or attached peripherally. Other modules can control network protocols, support different filesystem types and many other purposes.

Kernel modules are located in **/lib/modules/$(uname -r)** and can be compiled for specific kernel versions.

Parameters can be specified when loading modules to control their behavior. The end result is great flexibility and agility in responding to changing conditions and needs.

Kernel remains a *monolithic* one, not a microkernel.

# **Listing Modules in Use with lsmod**

Most kernel features can be configured as modules, even if they are almost always likely to be used. This flexibility also aids in development of new features as system reboots are almost never needed to test during development and debugging.

While other operating systems have used module-like methods, Linux uses it far more than any other operating system.

While the module is loaded, you can always see its status with the **lsmod** command, as in the screenshot below.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/x3isdpo0266p-lsmod.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/x3isdpo0266p-lsmod.png)

**Screenshot of the lsmod command and its output**

# **Module Utilities**

There are a number of utility programs that are used with kernel modules:

- **lsmod**List loaded modules.
- **insmod**Directly load modules.
- **rmmod**Directly remove modules.
- **modprobe**Load or unload modules, using a pre-built module database with dependency and location information.
- **depmod**Rebuild the module dependency database.
- **modinfo**Display information about a module.

Use the **modprobe** command to load a module:

**`$ modprobe e1000e`**

Use the **modprobe -r** command to unload or remove a module:

**`$ modprobe -r e1000e`**

**modprobe** requires a module dependency database be updated. Use the **depmod** command to generate or update the file **/lib/modules/$(uname -r)/modules.dep**.

Use the **insmod** command to directly load a module (requires fully qualified module name):

**`$ insmod /lib/modules/$(uname -r)/kernel/drivers/net/ethernet/intel/e1000e.ko`**

Use the **rmmod** command to directly remove a module:

**`$ rmmod e1000e`**

Use the **lsmod** command to list the loaded modules:

**`$ lsmod`**

Use the **modinfo** command to display information about a module (including parameters):

**`$ modinfo e1000e`**

# **Some Considerations with Modules**

Modules are usually loaded with **modprobe**, not **insmod**.

Modules loaded with non-acceptable open source licenses mark the kernel as tainted.

There are some important things to keep in mind when loading and unloading modules:

- It is impossible to unload a module being used by one or more other modules, which one can ascertain from the **lsmod** listing.
- It is impossible to unload a module that is being used by one or more processes, which can also be seen from the **lsmod** listing. However, there are modules which do not keep track of this reference count, such as network device driver modules, as it would make it too difficult to temporarily replace a module without shutting down and restarting much of the whole network stack.
- When a module is loaded with **modprobe**, the system will automatically load any other modules that need to be loaded first.
- When a module is unloaded with **modprobe -r**, the system will automatically unload any other modules being used by the module, if they are not being simultaneously used by any other loaded modules.

# **modinfo Example**

**modinfo** can be used to find out information about kernel modules (whether or not they are currently loaded). You can see an example in the screenshot here, which displays information about version, file name, which hardware devices the device driver module can handle, and what parameters can be supplied on loading.

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/jasgfwv5feoa-modinfo.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/jasgfwv5feoa-modinfo.png)

**Screenshot of the modinfo command and its output**

Much information about modules can also be seen in the **/sys** pseudo-filesystem directory tree; in our example, you would look under **/sys/module/sg** and some, if not all parameters, can be read and/or written under **/sys/module/sg/parameters**. We will show how to set them next.

Many modules can be loaded while specifying parameter values, such as in this command:

**`$ sudo /sbin/insmod <pathto>/e1000e.ko debug=2 copybreak=256`**

or, for a module already in the proper system location, it is easier achieved with the following command:

**`$ sudo /sbin/modprobe e1000e debug=2 copybreak=256`**

# **/etc/modprobe.d**

All files in the **/etc/modprobe.d** subdirectory tree which end with the **.conf** extension are scanned when modules are loaded and unloaded using **modprobe**. Files in the **/etc/modprobe.d** directory control some parameters that come into play when loading with **modprobe**. These parameters include module name aliases and automatically supplied options. You can also blacklist specific modules to avoid them being loaded.

Settings apply to modules as they are loaded or unloaded, and configurations can be changed as needs change.

The format of files in **/etc/modprobe.d** is simple: one command per line, with blank lines and lines starting with **#** ignored (useful for adding comments). A backslash at the end of a line causes it to continue on the next line, which makes the file a bit neater.