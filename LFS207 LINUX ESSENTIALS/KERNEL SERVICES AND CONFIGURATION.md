# KERNEL SERVICES AND CONFIGURATION

Created: March 30, 2023 1:40 PM
Reviewed: No

# **Kernel Overview**

Narrowly defined, Linux is only the kernel of the operating system, which includes many other components, such as libraries and applications that interact with the kernel.

The kernel is the essential central component that connects the hardware to the software and manages system resources, such as memory and CPU time allocation among competing applications and services. It handles all connected devices using device drivers, and makes the devices available for operating system use.

A system running only a kernel has rather limited functionality. It will be found only in dedicated and focused embedded devices.

Kernel responsibilities include:

- System initialization and boot up
- Process scheduling
- Memory management
- Controlling access to hardware
- I/O (Input/Output) between applications and storage devices
- Implementation of local and network filesystems
- Security control, both locally (such as filesystem permissions) and over the network
- Networking control

# **Kernel Boot Parameters**

There is a rather long list of available kernel parameters. Detailed documentation can be found:

- In the **kernel-parameters.txt** file in the kernel source
- Online, using [the kernel's command line parameters documentation](https://www.kernel.org/doc/Documentation/)
- On the system in the kernel documentation package provided by most distributions with a name like **kernel-doc** or **linux-doc**
- By typing **man bootparam**

Parameters can be specified either as a simple value given as an argument, or in the form of **param=value**, where the given value can be a string, integer, array of integers, etc., as explained in the documentation file.

**vmlinuz root=/dev/sda6 rhgb quiet crashkernel=512M**

Kernel options are placed at the end of the kernel line and are separated by spaces. An example of kernel boot parameters (all in one line):

**linux /boot/vmlinuz-5.19.0 root=/dev/sda5 ro crashkernel=512M quiet selinux=0**

Below you can see an explanation of some of the boot parameters:

- **root**: root filesystem (can be in the for of **root=UUID=...** or **root=/dev/sda5** or **root=LABEL=CentOS9**, etc.)
- **ro**: mounts root device read-only on boot
- **crashkernel=512M**: how much memory to set aside for kernel crashdumps through the **kdump** facility
- **quiet**: disables most log messages
- **selinux=0**: disables SELinux

By convention, there should be no intentionally hidden or secret parameters. They should all be explained in the documentation, and patches to the kernel source with new parameters should always include patches to the documentation file.

Note that Linux distributions often add parameters that are particular to that distribution, such as **rhgb** in the example above, which stands for Red Hat Graphical Boot.

# **Kernel Boot Parameters**

There is a rather long list of available kernel parameters. Detailed documentation can be found:

- In the **kernel-parameters.txt** file in the kernel source
- Online, using [the kernel's command line parameters documentation](https://www.kernel.org/doc/Documentation/)
- On the system in the kernel documentation package provided by most distributions with a name like **kernel-doc** or **linux-doc**
- By typing **man bootparam**

Parameters can be specified either as a simple value given as an argument, or in the form of **param=value**, where the given value can be a string, integer, array of integers, etc., as explained in the documentation file.

**`vmlinuz root=/dev/sda6 rhgb quiet crashkernel=512M`**

Kernel options are placed at the end of the kernel line and are separated by spaces. An example of kernel boot parameters (all in one line):

**`linux /boot/vmlinuz-5.19.0 root=/dev/sda5 ro crashkernel=512M quiet selinux=0`**

Below you can see an explanation of some of the boot parameters:

- **root**: root filesystem (can be in the for of **root=UUID=...** or **root=/dev/sda5** or **root=LABEL=CentOS9**, etc.)
- **ro**: mounts root device read-only on boot
- **crashkernel=512M**: how much memory to set aside for kernel crashdumps through the **kdump** facility
- **quiet**: disables most log messages
- **selinux=0**: disables SELinux

By convention, there should be no intentionally hidden or secret parameters. They should all be explained in the documentation, and patches to the kernel source with new parameters should always include patches to the documentation file.

Note that Linux distributions often add parameters that are particular to that distribution, such as **rhgb** in the example above, which stands for Red Hat Graphical Boot.

# **Kernel Command Line**

Various parameters are passed to the system at boot on the kernel command line. Normally, these are placed on the **linux** or **linux16** line in the GRUB configuration file.

Sample kernel command lines will depend on Linux distribution and version and might look like:

**`linux /boot/vmlinuz-4.15.0-58-generic \       root=UUID=5cec328b-bcd6-46d2-bcfa-8430257cd7a5 \       ro find_preseed=/preseed.cfg auto noprompt \       priority=critical locale=en_US quiet crashkernel=512M-:192M`**

or:

**`BOOT_IMAGE=/boot/vmlinuz-4.15.0-58-generic \     root=UUID=5cec328b-bcd6-46d2-bcfa-8430257cd7a5\     ro find_preseed=/preseed.cfg auto noprompt \     priority=critical locale=en_US quiet crashkernel=512M-:192M`**

or

**`linux /boot/vmlinuz-5.19.0 root=UUID=7ef4e747-afae-48e3-90b4-9be8be8d0258 ro quiet`**

or:

**`linuxefi /boot/vmlinuz-5.2.9 root=UUID=77461ee7-c34a-4c5f-b0bc-29f4feecc743 ro crashkernel=auto rhgb quiet crashkernel=384M-:128M`**

and would be found in the **grub.cfg** file in a subdirectory under **boot**, such as **/boot/grub**, or in a place like **/boot/efi/EFI/centos/grub.cfg**.

Everything after the **vmlinuz** file specified is an option. Any options not understood by the kernel will be passed to init (pid = 1), the first user process to be run on the system.

To see what command line a system was booted with, type the following command:

**`$ cat /proc/cmdline`**

Output:

**`BOOT_IMAGE=(hd0,msdos2)/boot/vmlinuz-5.19.0 root=UUID=7f7221b8-60d8-41b9-b643-dfcc80527c37 ro rhgb quiet crashkernel=512M`**

Fedora and RHEL/CentOS use the BLSFG (Boot Loader Specification Configuration) which alters where the kernel command line is set. You should now look in either **`/boot/grub2/grubenv`** for that information, or the files in **`/boot/loader/entries**.`

# **Boot Process Failures**

If the system fails to boot properly or fully, being familiar with what happens at each stage is important. Assuming you get through the BIOS stage, you may reach a state of:

**No bootloader screen:**

Check for GRUB misconfiguration or a corrupt boot sector. Bootloader re-install needed?

**Kernel fails to load:**

If the kernel panics during the boot process, it is most likely a misconfigured or corrupt kernel, or incorrect kernel boot parameters in the GRUB configuration file. If the kernel has booted successfully in the past, either it has corrupted, or bad parameters were supplied. Depending on which, you can re-install the kernel, or enter into the interactive GRUB menu at boot and use very minimal command line parameters and try to fix that way. Or you can try booting into a rescue image.

**Kernel loads but fails to mount the root filesystem:**

The main causes here are:

1. Misconfigured GRUB configuration file
2. Misconfigured **/etc/fstab**
3. No support for the root filesystem type built into the kernel or in the initramfs image

**Failure during the init process.**

There are many things that can go wrong once init starts; look closely at the messages that are displayed before things stop. If things were working before, with a different kernel, that is a big clue. Look out for corrupted filesystems, errors in startup scripts, etc. Try booting into a lower runlevel, such as 3 (no graphics) or 1 (single user mode).

# **sysctl**

The **sysctl** interface can be used to read and tune kernel parameters at run time. The screenshot shows you how the current values can be displayed by doing:

**$ sysctl -a**

![https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/j0hr175gqefk-sysctl.png](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/j0hr175gqefk-sysctl.png)

**Screenshot of the sysctl -a command and its output**

Each value corresponds to a particular pseudofile residing under **/proc/sys**, with directory slashes being replaced by dots. For example, the following two commands are equivalent:

**$ sudo sh -c 'echo 1 > /proc/sys/net/ipv4/ip_forward'$ sudo sysctl net.ipv4.ip_forward=1**

where the second form is used to set a value with the **sysctl** command line interface. Do not leave spaces around the **=** sign in this command. Note that in the first form, we cannot just use a simple **sudo** with **echo**; the command must be done in the complicated way shown, or executed as root.

If settings are placed in the **/etc/sysctl.conf** file (see **man sysctl.conf** for details), settings can be fixed at boot time.

Note that typing this command:

**$ sudo sysctl -p**

effectuates immediate digestion of the file, setting all parameters as found; this is also part of the boot process.

With the advent of systemd, things are a little more complicated. Vendors put their settings in files in the **/usr/lib/sysctl.d/** directory. These can be added to or supplanted by files placed in **/etc/sysctl.d**. However, the original file (**/etc/sysctl.conf**) is still supported, as is self-documented in that file.

**`sysctl`** es un comando en sistemas operativos tipo Unix, incluyendo Linux, que se utiliza para visualizar y modificar los parámetros del kernel en tiempo de ejecución. Los parámetros del kernel son valores que controlan el comportamiento y la configuración del kernel del sistema operativo. Al cambiar los parámetros del kernel, se pueden ajustar el comportamiento y el rendimiento del sistema para adaptarse a diferentes necesidades.

El comando **`sysctl`** se utiliza para leer y modificar estos parámetros del kernel. Los parámetros del kernel se representan en el sistema de archivos **`/proc/sys`** y se pueden acceder utilizando el comando **`sysctl`**. El comando **`sysctl`** acepta una gran cantidad de opciones y configuraciones, lo que permite a los usuarios modificar una amplia variedad de parámetros del kernel.

Algunos ejemplos de uso comunes de **`sysctl`** incluyen ajustar la configuración de red, establecer límites de recursos del sistema, configurar parámetros de seguridad y modificar la configuración de hardware.

Por ejemplo, para verificar la configuración actual de un parámetro del kernel, puede utilizar el siguiente comando:

```
rubyCopy code
$ sysctl net.ipv4.tcp_fin_timeout

```

Este comando muestra el valor actual del parámetro **`net.ipv4.tcp_fin_timeout`**, que determina cuánto tiempo se espera después de que se envía un paquete FIN antes de que se cierre una conexión TCP.

Para cambiar el valor de un parámetro, se puede utilizar el siguiente comando:

```
rubyCopy code
$ sysctl -w net.ipv4.tcp_fin_timeout=30

```

Este comando establece el valor del parámetro **`net.ipv4.tcp_fin_timeout`** en 30 segundos. Es importante tener en cuenta que algunos parámetros del kernel solo se pueden modificar por usuarios con permisos de superusuario, lo que requiere la ejecución de **`sysctl`** con permisos elevados.

Ambos comandos se utilizan para habilitar el reenvío de paquetes IP en un sistema Linux, lo que significa que se permitirá que los paquetes IP pasen a través del sistema desde una red a otra. Esto es útil cuando se necesita que un sistema actúe como un enrutador o un gateway entre dos o más redes.

El primer comando **`$ sudo sh -c 'echo 1 > /proc/sys/net/ipv4/ip_forward'`** utiliza el comando **`echo`** para escribir el valor **`1`** en el archivo **`/proc/sys/net/ipv4/ip_forward`**. Este archivo contiene un valor que controla si el sistema Linux reenvía o no paquetes IP. Al escribir un **`1`** en este archivo, se habilita el reenvío de paquetes IP en el sistema.

El segundo comando **`$ sudo sysctl net.ipv4.ip_forward=1`** establece el valor del parámetro **`net.ipv4.ip_forward`** a **`1`**. Este parámetro controla si el reenvío de paquetes IP está habilitado en el sistema. Al establecer el valor en **`1`**, se habilita el reenvío de paquetes IP.

Ambos comandos hacen lo mismo, habilitar el reenvío de paquetes IP, pero utilizan enfoques ligeramente diferentes para hacerlo. La ventaja del segundo comando es que es más fácil de recordar y se puede utilizar con otros parámetros del kernel también. El primer comando es más útil si necesita ejecutar una serie de comandos como un solo comando utilizando **`sh -c`**.

# 

This chapter described two ways of tuning kernel configurations. The kernel command line allows specification of start up options, while use of **sysctl** allows specification of runtime options. Select which of the following are runtime options:

- **A.** Enable IP forwarding
- **B.** Mount root device read-only
- **C.** Activate the root filesystem in the logical volume specified
- **D.** Maximum PID number
- **E.** Disable DM RAID detection
- **F.** Select system language
- **G.** Enter in single user mode