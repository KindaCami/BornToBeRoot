*This project has been created as part of the **42 curriculum by cfrancis***

# *Description*
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
Debian: Uses the .deb format and the apt package manager. It is known for having one of the largest and easiest-to-use repositories.
**Rocky Linux:** Uses the .rpm format and the **dnf** package manager (successor to yum). It is designed for server environments where package traceability and security are critical.
### Security (MAC - Mandatory Access Control)
This is the most important differences beetwen Rocky and Debian:
Debian uses AppArmor: It's based on file paths. It's simpler to configure but less granular.
Rocky Linux uses SELinux: It relies on security labels for files and processes. It's much more powerful and difficult to configure, it's the gold standard in enterprise security.

### VirtualBox, UTM, Virt-Manager

| Feature | Oracle VirtualBox | UTM (Apple Silicon / macOS) | virt-manager (KVM/QEMU) |
| :--- | :--- | :--- | :--- |
| **Backend Motor** | Oracle's own hypervisor | QEMU / Apple Virtualization | **KVM (Kernel-based VM)** |
| **Performance** | Good on x86, but resource-heavy | Excellent on Mac M1/M2/M3 | **Native on Linux (maximum speed)** |
| **Architecture** | Primarily x86_64 | Emulates multiple architectures | Highly flexible (x86, ARM, etc.) |
| **Use at 42** | Historical standard | Standard for newer Mac Silicon | Professional solution for Linux hosts |


### Pros and Cons:
**Debian:** 
+ Lightweight, highly compatible, great community, easy to set up.
+ Sometimes it has very old software versions in its "Stable" branch.

**Rocky Linux:**
+ Business stability (10 years of support), extreme security (SELinux), ideal for production.
+ High learning curve, stricter network configuration and security, hardware requirements (x86-64-v3).

 
# *Instructions*
1. Signature: With the VM powered off, generate the SHA1 hash of the .qcow2 file: sha1sum <file>.qcow2.
2. SSH Connection: ssh cfrancis42@l27.0.0.1 -p 4242
3. Monitoring: The monitoring.sh script runs every 10 minutes via Systemd Timer. To stop it: sudo systemctl stop monitoring.timer or Ctrl+C
4. vsftpd connection: ftp -p 127.0.0.1 2121

# Resources:
+ *OS*: [link](https://docs.rockylinux.org/10/es/guides/)
+ *Management*: [link](https://docs.rockylinux.org/10/es/books/admin_guide/06-users/)
+ *How to write a READme.md:* [link](https://commonmark.org/help/)
+ AI usages:
  1. Explanation of how to create bridges (30000 to 30002) so that the vsftpd service would work.
  2. WordPress password recovery
  3. Localhost:8080 problems connections debuggin
