*This project has been created as part of the **42 curriculum by cfrancis***

# **Description**
This project is about System Administration, touching the most importants points for a begginer, like configuration, strict security rules,
managing partitions using encrypted LVM, and setting up web services (WordPress) and file transfer services (vsftpd).

Due to the technical specifications of this version, the following design decisions were made:

x86-64-v3 Architecture: Rocky Linux 10 requires a modern microarchitecture level (AVX2 instructions). Because of incompatibilities with certain versions of Oracle VirtualBox, virt-manager (QEMU/KVM) was chosen to ensure kernel stability.

SELinux Management: Unlike Debian, Rocky uses SELinux by default. The system is configured in Enforcing mode, and policies have been adapted using booleans to allow WordPress network traffic (httpd_can_network_connect) and full FTP access (ftpd_full_access).

Systemd Monitoring: Instead of cron, a Systemd Timer is used to run the monitoring.sh script every 10 minutes. This allows for better integration with journalctl logs.

## Debian vs Rocky

### Philosophy

I chose Rocky because I rather to learn about OS focus on bussiness, Debian is commonly used for personal purposes.

### Package management
**Debian:** Uses the .deb format and the apt package manager. It is known for having one of the largest and easiest-to-use repositories.

**Rocky Linux:** Uses the .rpm format and the **dnf** package manager (successor to yum). It is designed for server environments where package traceability and security are critical.

### **Security**

This is the most important differences beetwen Rocky and Debian:
Both use *MAC - Mandatory Access  Control*

+ **Debian** uses **AppArmor:** It's based on file paths. It's simpler to configure but less granular.
It creates application-specific security profiles, defined by their path, that strictly restrict what a program can do (files, network, capabilities), even for privileged users, functioning as a security layer beneath traditional permissions (DAC) to limit vulnerabilities.
It works by tagging processes and objects and applies administrator-defined, not user-defined, policies to deny access if it is not explicitly permitted:

1. *Application Profiles:* Associates a set of rules (profile) with each executable based on its path (e.g., /usr/bin/firefox).
These profiles define a "least privilege" setting, limiting access to specific resources (files, sockets, etc.).

2. *Modes:*
   1. **Enforcing (Strict):** Enforces rules and blocks violations, logging them.
   2. **Complain (Relaxed/Audit):** Only logs violations without blocking them; useful for creating profiles.
   3. **Audit:** Logs accepted and rejected system calls.

  
+ **Rocky Linux** uses **SELinux:** It relies on security labels for files and processes. It's much more powerful and difficult to configure, it's the gold standard in enterprise security.
How SELinux Works:
It's a security layer that enforces strict policies defined by the administrator, not the file owner, to control who (processes/users) can do what (read/write/execute) with what (files/ports/memory) on the system,
limiting the damage of exploits by denying access even if standard DAC permissions would allow it.
It works by assigning security tags to processes and objects and checking these tags against policy rules in the kernel to determine access:

1. *Tagging:* Assigns security tags to all processes (subjects) and resources (objects) such as files, directories, and ports.
2. *Security Context:* Each entity has a context (e.g., httpd_t for web processes, httpd_sys_content_t for web files).
3. *Policies (Rules):* Defines rules that dictate which tags can interact with others (e.g., can a process httpd_t access files tagged httpd_sys_content_t?).
4. *Kernel Enforcement (LSM):* Integrated into the Linux kernel through the Linux Security Modules (LSM), the kernel enforces these policies.
5. *Access Decision:* When a process requests access, the kernel consults the SELinux policy. If the policy prohibits access (even if normal UNIX permissions (DAC) would allow it), access is denied and logged.

6. *Modes:* It operates in the following modes:
   1. **Enforcing:** Enforces the policy and denies access.
   2. **Permissive:** Logs denials without blocking them, useful for testing policies.
   3. **Disabled:** Does not enforce policies.

Technical summary of the differences between the two most widely used Mandatory Access Control (MAC) systems in Linux.

| Feature | AppArmor | SELinux |
| :--- | :--- | :--- |
| **Control Method** | **Path-based:** Relies on file location (e.g., `/bin/bash`). | **Label-based:** Uses filesystem extended attributes (Labels). |
| **Configuration** | **Easy:** Human-readable profiles in plain text. | **Complex:** Requires policy compilers and security contexts. |
| **Flexibility** | **Low:** Moving a file breaks the profile. | **High:** Security context follows the file if moved or renamed. |
| **Granularity** | **Medium:** Focuses on isolating specific applications. | **Total:** Controls every object-subject interaction in the system. |
| **Advanced Security** | No Multi-Level Security (MLS) support. | **MLS/MCS Support:** Standard for military/government grade. |
| **Default Distros** | Ubuntu, Debian, openSUSE, SUSE Linux. | RHEL, Fedora, Rocky Linux, Android. |
| **Learning Curve** | Fast (Ideal for small/medium teams). | Steep (Requires specialized administration). |

-----------------------------------------------------------------------------------------------------------------------------------------------------


### **VirtualBox, UTM, Virt-Manager**

| Feature | Oracle VirtualBox | UTM (Apple Silicon / macOS) | virt-manager (KVM/QEMU) |
| :--- | :--- | :--- | :--- |
| **Backend Motor** | Oracle's own hypervisor | QEMU / Apple Virtualization | **KVM (Kernel-based VM)** |
| **Performance** | Good on x86, but resource-heavy | Excellent on Mac M1/M2/M3 | **Native on Linux (maximum speed)** |
| **Architecture** | Primarily x86_64 | Emulates multiple architectures | Highly flexible (x86, ARM, etc.) |
| **Use at 42** | Historical standard | Standard for newer Mac Silicon | Professional solution for Linux hosts |

### **UFW vs Firewalld**

+ UFW is simpler and more straightforward (Ubuntu/Debian) like this name says "uncomplicated firewall", ideal for users seeking ease of use.
+ Firewalld is more powerful and flexible with its zone system, perfect for enterprise environments that require more detailed and dynamic management (RHEL/Fedora/openSUSE).
The choice depends on complexity: UFW for speed and simplicity, firewalld for granular control and complex environments


### **Pros and Cons**
**Debian:** 
+ Lightweight, highly compatible, great community, easy to set up.
+ Sometimes it has very old software versions in its "Stable" branch.

**Rocky Linux:**
+ Business stability (10 years of support), extreme security (SELinux), ideal for production.
+ High learning curve, stricter network configuration and security, hardware requirements (x86-64-v3).

 
# **Instructions**
1. Signature: With the VM powered off, generate the SHA1 hash of the .qcow2 file: sha1sum <file>.qcow2.
2. SSH Connection: ssh cfrancis42@l27.0.0.1 -p 4242
3. Monitoring: The monitoring.sh script runs every 10 minutes via Systemd Timer. To stop it: sudo systemctl stop monitoring.timer or Ctrl+C
4. vsftpd connection: ftp -p 127.0.0.1 2121

# **Resources**
+ *OS*: [link](https://docs.rockylinux.org/10/es/guides/)
+ *Management*: [link](https://docs.rockylinux.org/10/es/books/admin_guide/06-users/)
+ *How to write a READme.md:* [link](https://commonmark.org/help/)
+ AI usages:
  1. Explanation of how to create bridges (30000 to 30002) so that the vsftpd service would work.
  2. WordPress password recovery
  3. Localhost:8080 problems connections debuggin
