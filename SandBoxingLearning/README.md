what is sandboxing : Is a security technique that is used to secure system from being affected by application vulnerabilities by creating a restricted and controlled  application's running environment and isolating and limiting the program from accessing the filesystem calls and other resources outside of the application running environment.


THE FEATURES THAT SANDBOX INTERFACE CONTAINS.

Sandbox Interfaces
Sandbox interfaces are system and hardware resources whose access permission is to be restricted in a sandbox solution. With a filtering mechanism, the sandbox sits between these resources outside and the application inside, thereby allowing or disallowing access behaviors according to a set of pre-defined or customized policies. The design of the sandbox policy follows the least-privilege and minimized access permission principle. Usually, the customizable policy makes it possible users, application administrators, or developers to adjust the policy afterward according to the requirements in the actual scenarios.

This section lists the commonly existing interfaces managed by the sandboxes on the GNU/Linux platform and the general practices to control and restrict the program from accessing them.

Filesystem Access
The filesystem access, including directories and files, should be restricted to prevent the application from reading or writing to files containing critical and sensitive system information. By writing to sensitive files like executable or configuration files, the attacker can inject malicious code or the backdoor and may followingly obtain further privileges or even root permission. In addition, some of these sensitive files, such as /etc/machine-id 4, can be used to track the user and threaten the user’s privacy.

The restriction of filesystem access can be implemented by blocking the application directly, as most MAC tools do, or creating a virtual filesystem environment where only necessary directories or files have been mapped.

System Call
System calls are kernel interfaces exposed to the userspace called by the application to execute critical functions, including memory managing, file system operating, process controlling, and other kernel-level tasks. There are more than 400 system calls for kernel version 5.14 on all system architectures 5 . Usually, an application only uses a small set of system calls. It is necessary to limit the access of the system calls to avoid the unused ones being misused by potential vulnerabilities.

Seccomp is a security mechanism implemented in the kernel to restrict the application process from accessing unnecessary system calls. Only the very basic system calls such as read(), write(), _exit(), and sigreturn() are allowed by defaut. Seccomp-bpf is an extension of Seccomp, which provides a configurable filter design based on the Berkeley Packet Filter (BPF) rule. Seccomp and Seccomp-bpf have been integrated and utilized by many sandbox solutions on the GNU/Linux platform.

Network
A sandbox solution should be able to put the network access in the control based on the application status or pre-defined policies. On the GNU/Linux platform, firewall tools such as iptables and nftables can set rules based on the process ID or user ID. For example, iptables carries out the -m owner option with --pid-owner, --uid-owner, and --gid-owner in the rule configuration. A higher-level sandbox solution can work similarly to an application firewall on the network restriction.

Another viable approach is to restrict network access through network namespace, which provides a way to create virtual networks for the separate applications, thereby easily controlling their network accessing behaviors.

Device
Controlling the access permissions to the devices by the application, such as hard drive, microphone, and digital camera, is a critical sandboxing interface considering the user’s security and privacy. On GNU/Linux, considering all devices except the network devices and the video adapter present as device files under /dev/, the access to the devices could be controlled by setting the permission of the device file in terms of reading, writing, and executing for specified users and groups. During the system loading process, the default permissions of the devices are set by udev rules. Moreover, the cgroups feature 6 in the Linux kernel is also a viable tool to control and isolate the application processes from accessing some devices. It is used by many container solutions.

Process
As one of the purposes of the application sandbox, the process in a sandbox should be restricted to access or see other processes outside the box.

Inter-Process Communication (IPC) is an often-used mechanism by the operating systems to deliver and synchronize the information between different processes. The GNU/Linux operating system could create boundaries between different groups of processes using IPC Namespace 7. Also, PID Namespace 8 isolates process IDs. Each PID namespace has a dedicated environment, so two processes in different PID namespaces may have the same process ID number. In addition, cgroups is another powerful tool in isolating the application processes.

As one of the commonly used IPC mechanisms, D-Bus is widely used for desktop applications. D-Bus is designed as a bus system that provides inter-process communication and process lifecycle management 9, and it has been available on many often-used applications, such as Gnome Nautilus, Network Manager, Dolphin, and KMail 10. Therefore, as a sandbox solution, it is necessary to manage the access permission to D-Bus, which can be done by editing the configurations of the <policy> section in the file /usr/share/dbus-1/session.conf 11 or modifying D-Bus service files under the directory /usr/share/dbus-1/services/ 12.

Window System
By design, the GUI applications running on the X Window system are able to obtain the input of other applications without any restriction. It opens a risk that a keylogger running behind may secretly steal sensitive information typed by users.

Lauching different applications in separated X Window sessions can mitigate this issue but it brings further complexity to the sandbox configuration. A native solution is to apply the window systems based on the Wayland protocol 13, which provides the isolation between GUI applications by design 14. However, as a prerequisite, the desktop environment should support the Wayland protocol as well as the applications running on it.

Major desktop environments, including Gnome 15 and KDE 16, have integrated their native Wayland support as the default delivery option. XFCE’s Wayland support is still working in progress 17. On Gnome, most of the commonly used native applications have been covered under the Wayland, such as Gnome Terminal, Evince, Nautilus, etc. Also, LibreOffice and web browsers, such as Mozilla Firefox and Chromium, have already implemented native Wayland support.

Audio
The audio service on the GNU/Linux platform, namely PulseAudio, follows a user-centric security model, so it does not provide any isolation mechanisms 18, which poses a security concern as applications can do things like snooping on another application’s audio content, accessing the microphone and recording the sound input, loading or unloading server modules, snooping on the audio server’s shared memory pool, and so on.

As an alternative to PulseAudio, Pipwire addresses these problems by designing an access control mechanism in the product 19. It also officially supports the Flatpak applications 20 in which the sandboxing solution is available. So far, most of the major GNU/Linux distributions have migrated their sound system to Pipewire.



