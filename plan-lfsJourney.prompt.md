## Plan: LFS First Steps

Use the official systemd development book, revision r13.1-21-systemd (published October 5, 2026), and prepare an isolated VM-backed build. The first milestone is a verified build environment, a dedicated LFS filesystem, and the LFS user/environment ready to begin Chapter 5. Keep all host storage and boot configuration untouched.

**Steps**
1. **Confirm host and resource budget.** Identify the exact Ubuntu release (the user said 24.06; verify, since this may mean 24.04), architecture, kernel, CPU count, RAM, free disk, virtualization support, and available hypervisor. Compare with the systemd book's Chapter 2 requirements (four CPU cores and 8 GB RAM recommended; minimum tool versions plus GCC <= 16.2.0 and Binutils <= 2.47 are the tested ceiling). Set VM memory to no more than one-third of host RAM and choose CPU allocation from the available cores.
2. **Choose an isolated storage topology before provisioning.** Decide whether the stated 50 GB is the LFS target virtual disk or the total VM storage budget. Prefer an Ubuntu builder VM with its own OS disk and a separate 50 GB LFS target disk if host storage permits; otherwise calculate a single-disk layout that still leaves adequate room for the builder OS and at least the book's 15 GB minimum (30 GB is a reasonable root partition). Record UEFI/BIOS mode. Do not attach or modify the physical Ubuntu host disk.
3. **Provision and verify the VM.** Install/use a supported hypervisor (KVM/libvirt is a natural first choice on Ubuntu when available; use an existing supported hypervisor if already configured). Create the isolated builder VM and LFS target disk, take a clean snapshot, and verify the guest boots, networking works, and the target disk is the intended virtual device before any filesystem creation.
4. **Run host preflight.** Follow the book's Chapter 2 host requirements. Run the repository's `version-check.sh` from a fresh temporary working directory, not the repository: the script creates and removes `a.out` in its current directory. Compare all reported versions and aliases against the systemd edition, including the upper tested compiler/linker versions, and resolve any failure before continuing.
5. **Prepare the LFS filesystem and packages.** Following the same systemd book revision, create and mount the filesystem on the verified virtual disk, set `LFS` and umask 022, review current errata/security advisories, download the Chapter 3 package set, and verify archive checksums. Keep notes of device identity, mountpoint, book revision, and commands/results.
6. **Finish the first preparation milestone.** Complete the book's Chapter 4 layout, dedicated unprivileged `lfs` user, ownership, and environment setup. Verify the LFS user sees the correct `$LFS`, umask, PATH, and writable build directories; take a VM snapshot before starting the Chapter 5 cross-toolchain.

**Relevant files**
- `/home/zyph42/Desktop/Github/debian_clone/version-check.sh` — existing host preflight checker; run from a temporary directory because it removes `a.out` from its working directory.
- Official systemd book, revision r13.1-21-systemd: https://www.linuxfromscratch.org/lfs/view/systemd/
- Official systemd book Chapter 2 host requirements: https://www.linuxfromscratch.org/lfs/view/systemd/chapter02/hostreqs.html
- Official systemd book Chapters 2-4 cover host preparation, packages, and final preparation.

**Verification**
1. Confirm `/etc/os-release`, `uname -m`, `uname -r`, `nproc`, `free -h`, host free disk, hypervisor availability, and the exact VM disk topology.
2. Run `version-check.sh` from a temporary directory and resolve every failing item against the r13.1-21-systemd host requirements.
3. In the guest, verify the intended virtual disk by stable device identity and capacity before formatting; verify mount options, `$LFS`, umask, user ownership, and writable directories before Chapter 5.
4. Confirm the downloaded package set matches the selected book revision and the published checksums before compiling.

**Decisions**
- Follow the systemd edition, not the default SysVinit edition. The repo checker identifies LFS 13.1-systemd; the official live systemd development book is r13.1-21-systemd.
- Use a disposable VM; never experiment on the Ubuntu host's boot disk.
- 50 GB is a reasonable LFS target allocation, but whether that is separate from the VM's builder OS disk must be settled using the host's measured free space.
- No repository changes are planned for these initial steps; only consider updating the checker or adding notes if a specific need appears.
