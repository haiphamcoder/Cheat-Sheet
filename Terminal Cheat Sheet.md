# 🖥️ Terminal Cheat Sheet

## 🚀 Essential Terminal Commands

It all starts with the basics! There's a great deal of Linux commands, so it can be difficult to keep track of them all. Here's a list of useful commands, broken down by category.

---

### 📂 File Management

#### 🔍 Viewing Files and Directories

| Command | Effect |
| :--- | :--- |
| `ls` | List files in the current directory |
| `ls -l` | Long listing format (permissions, owner, size) |
| `ls -a` | Show hidden files, those starting with "." |
| `ls -lh` | Human-readable sizes |
| `tree` | View directory tree (may need to install) |

#### 📖 Reading File Content

| Command | Effect |
| :--- | :--- |
| `cat file.txt` | Print the whole file content to terminal |
| `less file.txt` | View file with pagination (use arrows to scroll, `q` to quit) |
| `head -n 10 file.txt` | Print the first 10 lines of the file |
| `tail -n 10 file.txt` | Print the last 10 lines of the file |
| `tail -f /var/log/syslog` | Follow a file in real-time (essential for reading logs) |

#### 🧭 Navigation

| Command | Effect |
| :--- | :--- |
| `cd /path/to/dir` | Change to a directory |
| `cd ..` | Go up one directory. |
| `cd ~` | Go to the home directory |
| `pwd` | Print current directory |

#### 🆕 Creating Files and Directories

| Command | Effect |
| :--- | :--- |
| `touch file.txt` | Create a new empty file |
| `mkdir new_folder` | Create a new directory |
| `mkdir -p parent/child` | Create nested directories |

#### 👯 Copying Files and Directories

| Command | Effect |
| :--- | :--- |
| `cp file1.txt file2.txt` | Copy file |
| `cp -r dir1/ dir2/` | Copy directory recursively |
| `cp -i file1.txt backup.txt` | Prompt before overwrite |

#### 🚚 Moving & Renaming

| Command | Effect |
| :--- | :--- |
| `mv file.txt /new/location/` | Move file to new location |
| `mv oldname.txt newname.txt` | Rename file |
| `mv -i file.txt /new/location/` | Prompt before overwrite |

#### 🗑️ Deleting Files and Directories

> [!WARNING]
> **Extremely dangerous!** `rm -rf /` or typing a space by mistake like `rm -rf / path` can destroy your entire system. Always double-check your path, preferably use absolute paths, or `ls` first to verify.

| Command | Effect |
| :--- | :--- |
| `rm file.txt` | Delete file |
| `rm -i file.txt` | Confirm before deleting |
| `rm -r folder/` | Recursively delete directory |
| `rm -rf folder/` | Force delete without prompt |

#### 🕵️ Finding Files

| Command | Effect |
| :--- | :--- |
| `find . -name "*.log"` | Find all .log files |
| `find /path/to/search -type f -size +1M` | Find files over 1MB |
| `find /path/to/search -type f -mmin -10` | Find files modified less than 10 minutes ago |
| `find /path/to/search -type f -mtime -1` | Find files modified less than 1 day ago |
| `find /path/to/search -type d` | Find directories instead of files |

#### 🔐 File Permissions and Ownership

| Command | Effect |
| :--- | :--- |
| `chmod +r file.txt` | Allow all users to read the file |
| `chmod -r file.txt` | Prevent any user from reading the file |
| `chmod +w file.txt` | Allow all users to make changes to the file |
| `chmod -w file.txt` | Prevent any user from making changes to the file |
| `chmod +x script.sh` | Allow all users to execute the file |
| `chmod -x script.sh` | Prevent any users from executing the file |
| `chmod u+r file.txt` | Allow the owner to read the file |
| `chmod u-r file.txt` | Prevent the owner from reading the file |
| `chmod u+w file.txt` | Allow the owner to make changes to the file |
| `chmod u-w file.txt` | Prevent the owner from making changes to the file |
| `chmod u+x script.sh` | Allow the owner to execute the file |
| `chmod u-x script.sh` | Prevent the owner from executing the file |
| `chmod g+r file.txt` | Allow the group to read the file |
| `chmod g-r file.txt` | Prevent the group from reading the file |
| `chmod g+w file.txt` | Allow the group to make changes to the file |
| `chmod g-w file.txt` | Prevent the group from making changes to the file |
| `chmod g+x script.sh` | Allow the group to execute the file |
| `chmod g-x script.sh` | Prevent the group from executing the file |
| `chmod o+r file.txt` | Allow others to read the file |
| `chmod o-r file.txt` | Prevent others from reading the file |
| `chmod o+w file.txt` | Allow others to make changes to the file |
| `chmod o-w file.txt` | Prevent others from making changes to the file |
| `chmod o+x script.sh` | Allow others to execute the file |
| `chmod o-x script.sh` | Prevent others from executing the file |
| `chmod 755 file.txt` | Set specific permissions (see table, below) |
| `chown user:group file.txt` | Change ownership |
| `chown -R user:group /path/to/dir` | Change ownership recursively (⚠️ Dangerous on root `/`, `/etc`, or `/var`) |

#### 📊 File Permission Table

> [!CAUTION]
> **Never use `chmod 777` in Production** to fix "Permission Denied" errors. It's a severe security vulnerability. Use `755` for directories, `644` for files, and ensure proper ownership with `chown`. Also, SSH private keys (e.g., `id_rsa`) MUST be `600`.

| Permission | Effect |
| :--- | :--- |
| 777 | Allow all users to read, write, and execute |
| 755 | Full permissions for owner; read and execute for group and others |
| 700 | Full permissions for owner; no permissions for group or others |
| 666 | Read and write for owner, group, and others |
| 644 | Read and write for owner; read-only for group and others |
| 600 | Read and write for owner; no permissions for group and others |
| 555 | Read and execute for owner, group, and others |
| 440 | Read-only for owner and group; no permissions for others |
| 400 | Read-only for owner; no permissions for group and others |
| 711 | Full permissions for owner; execute-only for group and others |

#### 📦 Archiving & Compression

| Command | Effect |
| :--- | :--- |
| `tar -cvf archive.tar folder/` | Create archive with the contents of folder/ |
| `tar -xvf archive.tar` | Extract archive |
| `tar -czvf archive.tar.gz folder/` | Create compressed archive |
| `tar -xzvf archive.tar.gz` | Extract compressed archive |

#### 🔎 Search & Text Processing

| Command | Effect |
| :--- | :--- |
| `grep "error" file.txt` | Search for "error" in file |
| `grep -i "error" file.txt` | Case-insensitive search |
| `grep -R "error" /path/` | Recursively search for "error" in directory |
| `awk '{print $1}' file.txt` | Print the first column/word of each line |
| `cut -d: -f1 /etc/passwd` | Cut and print the first field using `:` as delimiter |
| `sort file.txt` | Sort lines in a file alphabetically |
| `uniq -c file.txt` | Count unique lines (often used with `sort`) |
| `wc -l file.txt` | Count the number of lines in a file |
| `cat config.json \| jq .` | Pretty-print JSON files |
| `jq '.keyName' file.json` | Extract a specific key from JSON |
| `sed 's/foo/bar/' file.txt` | Replace first occurrence of "foo" with "bar" per line |
| `sed 's/foo/bar/g' file.txt` | Replace all occurrences of "foo" with "bar" per line |
| `sed -i 's/foo/bar/g' file.txt` | Same as above, but edits the file in place |
| `sed -n '3p' file.txt` | Print only the 3rd line of the file |
| `sed -n '5,10p' file.txt` | Print lines 5 through 10 |
| `sed '/^#/d' file.txt` | Delete all lines starting with # (comments) |
| `sed '/^$/d' file.txt` | Delete all blank lines |
| `sed '1d' file.txt` | Delete the first line of the file |
| `sed 's/[0-9]//g' file.txt` | Remove all digits from the file |
| `sed 's/.*/[&]/' file.txt` | Wrap each line in square brackets |

---

### 👥 User & Group Management

| Command | Effect |
| :--- | :--- |
| `sudo useradd -m username` | Create a new user with a home directory |
| `sudo userdel -r username` | Remove a user and their home directory |
| `sudo usermod -aG groupname username` | Add a user to a supplementary group |
| `sudo passwd username` | Change a user's password |
| `sudo groupadd groupname` | Create a new group |
| `sudo delgroup username groupname` | Remove a user from a group |
| `groups username` | Show groups a user belongs to |
| `who` | See who is currently logged in |
| `last` | Show last logins |
| `sudo visudo` | Safely edit the `/etc/sudoers` file |

---

### 🛠️ Package Management

#### 🍎 apt

| Command | Effect |
| :--- | :--- |
| `sudo apt update` | Update the package list from repositories |
| `sudo apt upgrade` | Upgrade all installed packages to the latest version |
| `sudo apt dist-upgrade` | Upgrade packages |
| `sudo apt install package-name` | Install a specific package |
| `sudo apt install package1 package2` | Install multiple packages at once |
| `sudo apt remove package-name` | Uninstall a package (keeping config files) |
| `sudo apt purge package-name` | Uninstall a package and its config files |
| `sudo apt autoremove` | Remove unused dependencies |
| `sudo apt search keyword` | Search for packages matching a keyword |
| `apt show package-name` | Display details about a package |
| `sudo apt list --upgradable` | Show packages that can be upgraded |
| `sudo apt clean` | Clear downloaded .deb package files from cache |
| `sudo apt edit-sources` | Edit the APT sources list using the default editor |

#### 🎩 dnf

| Command | Effect |
| :--- | :--- |
| `sudo dnf check-update` | Check for available package updates |
| `sudo dnf update` | Update all packages |
| `sudo dnf upgrade` | Alias for update (preferred in newer versions) |
| `sudo dnf install package-name` | Install a specific package |
| `sudo dnf install pkg1 pkg2` | Install multiple packages at once |
| `sudo dnf remove package-name` | Uninstall a package |
| `sudo dnf autoremove` | Remove unused dependencies |
| `dnf search keyword` | Search for packages by keyword |
| `dnf info package-name` | Show detailed info about a package |
| `sudo dnf list --installed` | List all installed packages |
| `sudo dnf list --available` | List all available packages |
| `sudo dnf clean all` | Clear package cache and metadata |
| `sudo dnf group list` | Show available software groups |
| `sudo dnf group install "Group Name"` | Install a software group |

#### 🦎 zypper

| Command | Effect |
| :--- | :--- |
| `sudo zypper refresh` | Refresh all enabled repositories |
| `sudo zypper update` | Update all installed packages |
| `sudo zypper up` | Alias for update |
| `sudo zypper patch` | Apply official patches (recommended for SUSE systems) |
| `sudo zypper install package-name` | Install a specific package |
| `sudo zypper install pkg1 pkg2` | Install multiple packages |
| `sudo zypper remove package-name` | Uninstall a package |
| `sudo zypper se keyword` | Search for packages matching a keyword |
| `zypper info package-name` | Show detailed information about a package |
| `sudo zypper list-updates` | List available updates |
| `sudo zypper ps` | List processes using deleted libraries |
| `sudo zypper clean --all` | Clear metadata and cache |
| `zypper lr` | List all configured repositories |
| `sudo zypper ar URL repo-alias` | Add a new repository |
| `sudo zypper rr repo-alias` | Remove a repository |
| `sudo zypper dup` | Perform a distribution upgrade |

---

### ⚙️ System Management

#### 🖥️ Hardware & Vital Stats

| Command | Effect |
| :--- | :--- |
| `lsblk` | List all block devices (disks/partitions) |
| `lscpu` | Show detailed CPU architecture information |
| `lsusb` | List USB devices |
| `lspci` | List PCI devices |
| `lsmem` | Show available system memory summary |
| `dmidecode -t system` | Show system hardware info from BIOS |
| `sensors` | Show hardware temperatures/fan speeds |

#### ℹ️ Viewing System Information

| Command | Effect |
| :--- | :--- |
| `hostname` | View system host name |
| `hostnamectl` | View system information |
| `uname -r` | View kernel version |
| `uname -m` | View architecture |

#### 📈 Resource Monitoring

| Command | Effect |
| :--- | :--- |
| `df` | Show available disk space |
| `df -h` | Show available disk space (human readable) |
| `df -i` | Show available inodes (crucial if disk is full but `df -h` shows space) |
| `du` | View disk usage for current working directory |
| `du -h` | View disk usage (human readable) |
| `du -sh folder/` | View disk usage of a particular directory |
| `free` | Check the amount of free system memory |
| `free -m` | Check the amount of free system memory (MB) |
| `top` | Open the "top" process manager |
| `htop` | Open the "htop" process manager (prettier) |

#### � Managing Processes

> [!TIP]
> Always prefer `kill -15` (SIGTERM) first to allow processes to exit gracefully (close DB connections, flush logs). Only use `kill -9` (SIGKILL) if the process is unresponsive.

| Command | Effect |
| :--- | :--- |
| `ps` | Show processes for the current shell session |
| `ps -e` | Show all processes |
| `ps -ef` | Show all processes in full-format listing |
| `ps aux` | Show detailed info for all processes (BSD-style) |
| `ps -u username` | Show processes for a specific user |
| `ps -p 1234` | Show info about process with PID 1234 |
| `ps -ef \| grep nginx` | Find all processes related to "nginx" |
| `ps --sort=-%mem \| head` | Show top memory-consuming processes |
| `ps --sort=-%cpu \| head` | Show top CPU-consuming processes |
| `kill PID` | Send default SIGTERM to the process with given PID |
| `kill -15 PID` | Gracefully stop a process (SIGTERM) |
| `kill -9 PID` | Forcefully kill a process using SIGKILL |
| `kill -HUP PID` | Restart a process by sending SIGHUP |
| `kill -l` | List all available signals |
| `pkill processname` | Kill a process by its name |
| `pkill -9 processname` | Forcefully kill all processes with that name |
| `killall processname` | Kill all processes matching the exact name |
| `xkill` | Graphically click on a window to kill it |

#### 🛠️ Managing Services

> Note: Replace `service` with the name of the service you're working with.

| Command | Effect |
| :--- | :--- |
| `systemctl status service` | Show status of the service |
| `systemctl start service` | Start the service |
| `systemctl stop service` | Stop the service |
| `systemctl restart service` | Restart the service |
| `systemctl reload service` | Reload without stopping |
| `systemctl enable service` | Enable to start on boot |
| `systemctl disable service` | Disable from starting on boot |
| `systemctl is-active service` | Check if currently active |
| `systemctl is-enabled service` | Check if enabled for boot |
| `systemctl list-units --type=service` | List all active services |
| `systemctl list-unit-files` | List all unit files and their state |
| `systemctl daemon-reload` | Reload systemd manager configuration |

#### 🧹 Formatting filesystems

| Command | Effect |
| :--- | :--- |
| `mkfs.ext4 /dev/sdX1` | Format partition as ext4 |
| `mkfs.xfs /dev/sdX1` | Format partition as XFS |
| `mkfs.vfat /dev/sdX1` | Format partition as FAT32 |
| `mkfs.ntfs /dev/sdX1` | Format partition as NTFS (requires `ntfs-3g`) |
| `mkfs.btrfs /dev/sdX1` | Format partition as Btrfs |
| `mkfs -t ext4 /dev/sdX1` | Specify filesystem type using `-t` |
| `mkfs.ext4 -L "DATA" /dev/sdX1` | Format ext4 with a volume label |
| `mkfs.ext4 -m 0 /dev/sdX1` | Set reserved block percentage to 0% |
| `mkfs -t ext4 -c /dev/sdX1` | Check for bad blocks before formatting |

#### 🔌 Mounting filesystems

| Command | Effect |
| :--- | :--- |
| `mount` | Show all currently mounted filesystems |
| `mount /dev/sdX1 /mnt` | Mount a device to the /mnt directory |
| `mount -t ext4 /dev/sdX1 /mnt` | Mount a partition as ext4 explicitly |
| `mount -o ro /dev/sdX1 /mnt` | Mount the partition as read-only |
| `mount -o loop image.iso /mnt` | Mount an ISO file as a loop device |
| `mount -o uid=1000,gid=1000 /dev/sdX1 /mnt` | Mount with specific user/group ownership |
| `mount -a` | Mount all filesystems from `/etc/fstab` |
| `umount /mnt` | Unmount a mounted filesystem |
| `umount /dev/sdX1` | Unmount by device |
| `umount -l /mnt` | Lazy unmount (force unmount, use sparingly) |
| `mount -t nfs server:/share /mnt/nfs` | Mount an NFS share |
| `mount -t cifs //server/share /mnt/smb -o user=username` | Mount a Samba share with authentication |

#### 🏗️ Advanced Storage (LVM)

| Command | Effect |
| :--- | :--- |
| `pvs` / `pvdisplay` | Display Physical Volume information |
| `vgs` / `vgdisplay` | Display Volume Group information |
| `lvs` / `lvdisplay` | Display Logical Volume information |
| `sudo vgextend vg_name /dev/sdX` | Add a physical disk to a Volume Group |
| `sudo lvextend -L +10G /dev/vg/lv` | Increase size of a Logical Volume by 10GB |
| `sudo resize2fs /dev/vg/lv` | Resize the filesystem to fill the volume (ext4) |
| `sudo xfs_growfs /mnt/path` | Resize XFS filesystem to fill the volume |
| `sudo lvcreate -L 20G -n lv_name vg_name` | Create a new Logical Volume |

---

### 🌐 Networking

#### 🏷️ IP Addressing and Routing

| Command | Effect |
| :--- | :--- |
| `ip a` | Show all IP addresses (alias for `ip addr`) |
| `ip addr show` | Show IP address details for all interfaces |
| `ip addr show eth0` | Show IP details for a specific interface |
| `ip link show` | Show all network interfaces |
| `ip link set eth0 up` | Enable the interface eth0 |
| `ip link set eth0 down` | Disable the interface eth0 |
| `ip route` | Display current routing table |
| `ip route add default via 192.168.1.1` | Set the default gateway |
| `ip route add 10.0.0.0/24 via 192.168.1.1` | Add a route to a specific network |
| `ip -s link` | Show interface statistics |
| `ip neigh` | Show ARP table (neighbor cache) |
| `ip link set eth0 mtu 1400` | Change the MTU of the interface |

#### 🚪 Inspecting open ports

> Note: It's a good idea to check this regularly, to ensure nothing is open that shouldn't be.

| Command | Effect |
| :--- | :--- |
| `ss` | Show summary of socket connections |
| `ss -t` | Show only TCP connections |
| `ss -u` | Show only UDP connections |
| `ss -l` | Show listening sockets only |
| `ss -tuln` | Show all listening TCP/UDP ports in numeric form |
| `ss -s` | Display summary statistics |
| `ss -p` | Show process using each socket |
| `ss -tnp` | Show TCP sockets with process info |
| `ss -a` | Show all sockets (listening and non-listening) |
| `ss -o state established '( dport = :ssh )'` | Show established SSH connections |
| `ss -H` | Suppress header line in output |

#### 🔎 DNS Querying

> Note: Although `nslookup` is said to be deprecated, that's no longer true. Both `nslookup` and `dig` are useful tools.

##### ⛏️ Using `dig`

| Command | Effect |
| :--- | :--- |
| `dig example.com` | Perform a basic DNS lookup for A record |
| `dig example.com A` | Explicitly query for the A record |
| `dig example.com AAAA` | Query for the IPv6 address (AAAA record) |
| `dig example.com MX` | Query mail exchange (MX) records |
| `dig example.com NS` | Query name server (NS) records |
| `dig example.com TXT` | Query for TXT records (SPF, DKIM info) |
| `dig @8.8.8.8 example.com` | Use a specific DNS server (e.g., Google DNS) |
| `dig +short example.com` | Output only the result (useful for scripts) |
| `dig +noall +answer example.com` | Show only the answer section |
| `dig -x 8.8.8.8` | Perform a reverse DNS lookup |
| `dig +trace example.com` | Trace DNS path from root to authoritative server |

##### 🔍 Using `nslookup`

| Command | Effect |
| :--- | :--- |
| `nslookup example.com` | Basic A record lookup for a domain |
| `nslookup` (then type `example.com`) | Interactive mode query |
| `nslookup -type=mx example.com` | Lookup mail exchange (MX) records |
| `nslookup -type=ns example.com` | Lookup name server (NS) records |
| `nslookup -type=txt example.com` | Lookup TXT records |
| `nslookup -type=soa example.com` | Get the Start of Authority (SOA) record |
| `nslookup -type=aaaa example.com` | Lookup IPv6 address (AAAA record) |
| `nslookup -port=53 example.com` | Query using a specific port |
| `nslookup example.com 8.8.8.8` | Query a specific DNS server |
| `nslookup -debug example.com` | Enable debug mode for detailed output |
| `nslookup -type=any example.com` | Request all available DNS record types |
| `nslookup -type=ptr 8.8.8.8` | Reverse DNS lookup (PTR record) |
| `nslookup` (then `server 8.8.8.8`) | Change default DNS server in interactive mode |
| `nslookup` (then `set timeout=10`) | Set query timeout in interactive mode |
| `nslookup` (then `set retry=5`) | Set number of retries in interactive mode |

#### 🔗 Connecting to Remote Systems

| Command | Effect |
| :--- | :--- |
| `ssh user@host` | Connect to a remote host as a user |
| `ssh -p 2222 user@host` | Connect using a non-default port |
| `ssh -i ~/.ssh/id_rsa user@host` | Connect using a specific private key |
| `ssh user@host 'ls -la'` | Run a single command remotely |
| `ssh -L 8080:localhost:80 user@host` | Forward local port 8080 to remote port 80 |
| `ssh -R 9090:localhost:22 user@host` | Allow remote host to access your local SSH |
| `ssh -N -f -L 3306:localhost:3306 user@host` | Create a background tunnel |
| `ssh-copy-id user@host` | Copy local SSH public key for passwordless login |
| `ssh -T git@github.com` | Test SSH authentication without opening a shell |

#### 📡 Copying/Transferring Files

##### 🛡️ Using `scp`

| Command | Effect |
| :--- | :--- |
| `scp file.txt user@remote:/home/user/` | Copy local to remote |
| `scp user@remote:/home/user/file.txt .` | Copy remote to local |
| `scp -r folder/ user@remote:/home/user/` | Copy directory recursively |
| `scp -P 2222 file.txt user@remote:/home/user/` | Use a specific port |
| `scp -i ~/.ssh/id_rsa file.txt user@remote:/home/user/` | Use an identity file (OpenSSH Key) |

##### 🔄 Using `rsync`

| Command | Effect |
| :--- | :--- |
| `rsync -avh --dry-run /source/ /destination/` | Dry run (preview what will happen) |
| `rsync -avh /source/ /destination/` | Sync a local directory to another local location |
| `rsync -avh /local/dir/ user@remote:/remote/dir/` | Sync a local directory to a remote server |
| `rsync -avh user@remote:/remote/dir/ /local/dir/` | Sync from remote server to local machine |
| `rsync -avh --delete /source/ /destination/` | Include deletion (mirror sync) |
| `rsync -avh --progress largefile.iso user@remote:/backup/` | Show progress for large files |
| `rsync -avh -e "ssh -p 2222" /source/ user@remote:/dest/` | Use a specific SSH port |
| `rsync -avh --exclude "*.log" /source/ /destination/` | Exclude certain files or directories |

---

### 📜 Logs

#### 📁 Common `/var/log` Files and Their Purpose

| Log File | Purpose |
| :--- | :--- |
| `/var/log/syslog` | General system log (Debian/Ubuntu) |
| `/var/log/messages` | General system log (RHEL/CentOS/Fedora) |
| `/var/log/dmesg` | Kernel ring buffer; hardware messages during boot |
| `/var/log/auth.log` | Authentication log (Debian/Ubuntu) |
| `/var/log/secure` | Authentication log (RHEL/CentOS/Fedora) |
| `/var/log/kern.log` | Kernel log messages |
| `/var/log/boot.log` | Boot process messages |
| `/var/log/faillog` | Failed login attempts |
| `/var/log/lastlog` | Last login information for users |
| `/var/log/wtmp` | Binary log of logins/logouts (view via `last`) |
| `/var/log/btmp` | Failed login attempts (view via `lastb`) |
| `/var/log/apt/history.log` | Package installation history (Debian) |
| `/var/log/yum.log` | Yum package manager log (RHEL/CentOS/Fedora) |
| `/var/log/httpd/access_log` | Web server access log (Apache on RHEL) |
| `/var/log/httpd/error_log` | Web server error log (Apache on RHEL) |
| `/var/log/nginx/access.log` | Nginx access log |
| `/var/log/nginx/error.log` | Nginx error log |
| `/var/log/Xorg.0.log` | X server startup log |

#### 📰 Modern Log Management (`journalctl`)

| Command | Effect |
| :--- | :--- |
| `journalctl -xe` | View latest logs with explanations |
| `journalctl -u service` | View logs for a specific systemd service |
| `journalctl -f` | Follow logs in real-time |
| `journalctl -p err` | Show only error-level messages and higher |
| `journalctl --since "1 hour ago"` | Show logs from the last hour |
| `journalctl -k` | Show kernel messages (similar to `dmesg`) |
| `journalctl --disk-usage` | Check total size of journal logs |
| `sudo journalctl --vacuum-time=7d` | Delete logs older than 7 days |

---

### 🩺 Troubleshooting & Identification

| Command | Effect |
| :--- | :--- |
| `nc -vz host 3306` | Check if a specific TCP port is open (e.g., Database) |
| `curl -v telnet://host:port` | Alternative to check if a port is open |
| `lsof -i :80` | Show which process is using port 80 |
| `lsof -u username` | List all files opened by a specific user |
| `strace -p PID` | Trace system calls made by a running process |
| `tcpdump -nn -i eth0` | Capture network packets on eth0 |
| `nmap -sn 192.168.1.0/24` | Scan local network for active hosts |
| `mtr google.com` | Combined traceroute and ping for network diagnostics |
| `iotop` | Monitor disk I/O usage by process |
| `netstat -plnt` | Legacy tool to view listening ports (use `ss` instead) |

---

### 🛡️ Security Hardening

#### 💡 General Tips

- Install updates as soon as they're released.
- Enable automatic updates (and consider live-patching).
- Harden OpenSSH (see table below).
- Regularly audit open connections.
- Use two-factor authentication on everything.
- Consider a password manager (such as Bitwarden).
- Check open ports with the `ss` command regularly.
- Lock the root account.
- Schedule shutdowns for unused servers after hours.
- Regularly audit cloud bills to avoid surprises.
- **Regularly test your backups!** Backups that aren't tested can't be trusted.
- Audit backups regularly for critical data.
- Audit users and permissions; remove unused accounts.
- Use LVM (Logical Volume Management) for flexible storage.
- Back up each LVM volume specifically.
- Use automation systems like **Ansible** to avoid human error.
- **Note:** Pro Linux admins memorize only the commands they use often and keep notes for the rest.

#### 🔒 Common OpenSSH Hardening Settings

| Recommended Setting | Description |
| :--- | :--- |
| `PermitRootLogin no` | Disables root login via SSH |
| `PasswordAuthentication no` | Enforces key-based authentication |
| `Protocol 2` | Ensures only SSH protocol version 2 is used |
| `MaxAuthTries 3` | Limits authentication attempts per connection |
| `LoginGraceTime 30` | Reduces time before unauthenticated session drop |
| `AllowUsers user1 user2` | Restrict SSH access to specified users |
| `AllowGroups sshusers` | Restrict SSH access to specific group members |
| `ClientAliveInterval 300` | Sends keep-alive messages every 300 seconds |
| `ClientAliveCountMax 2` | Disconnects after 2 missed keep-alives |
| `X11Forwarding no` | Disables X11 forwarding |
| `UseDNS no` | Skips reverse DNS lookup for faster login |
| `LogLevel VERBOSE` | Increases log detail for monitoring |
| `Banner /etc/issue.net` | Displays a legal warning before login |

> Note: Restart the `ssh` (Debian/Ubuntu) or `sshd` (other distros) service to apply changes.

---

### 🧱 Firewalls

#### 🛡️ Working with Firewalls (UFW)

| Command | Effect |
| :--- | :--- |
| `sudo ufw status` | Display status and rules |
| `sudo ufw enable` | Enable firewall |
| `sudo ufw disable` | Disable firewall |
| `sudo ufw default deny incoming` | Deny all incoming by default |
| `sudo ufw default allow outgoing` | Allow all outgoing by default |
| `sudo ufw allow 22` | Allow incoming SSH (port 22) |
| `sudo ufw allow 80,443/tcp` | Allow HTTP and HTTPS traffic |
| `sudo ufw allow from 192.168.1.0/24` | Allow traffic from a specific subnet |
| `sudo ufw allow from 10.0.0.5 to any port 22` | Allow SSH only from a specific IP |
| `sudo ufw delete allow 22` | Remove allow rule for port 22 |
| `sudo ufw deny 25` | Block port 25 (SMTP) |
| `sudo ufw limit ssh` | Throttle SSH to prevent brute-force |
| `sudo ufw reload` | Reload firewall rules |

#### 🔥 Working with Firewalls (firewall-cmd)

| Command | Effect |
| :--- | :--- |
| `sudo firewall-cmd --state` | Check if firewalld is running |
| `sudo firewall-cmd --reload` | Reload configuration |
| `sudo firewall-cmd --get-active-zones` | Show active zones and interfaces |
| `sudo firewall-cmd --get-services` | List all predefined services |
| `sudo firewall-cmd --zone=public --list-all` | Show all rules in "public" zone |
| `sudo firewall-cmd --zone=public --add-service=http` | Temporarily allow HTTP |
| `sudo firewall-cmd --zone=public --add-port=8080/tcp` | Temporarily allow TCP port 8080 |
| `sudo firewall-cmd --zone=public --remove-service=ssh` | Temporarily block SSH |
| `sudo firewall-cmd --permanent --add-service=https` | Permanently allow HTTPS |
| `sudo firewall-cmd --permanent --remove-port=21/tcp` | Permanently block FTP |
| `sudo firewall-cmd --runtime-to-permanent` | Save current rules to permanent |
| `sudo firewall-cmd --zone=internal --change-interface=eth1` | Assign interface to internal zone |

---

### ⌨️ Shell Shortcuts & Job Control

#### Bash Keyboard Shortcuts

| Shortcut | Effect |
| :--- | :--- |
| `Ctrl + A` | Move cursor to the start of the line |
| `Ctrl + E` | Move cursor to the end of the line |
| `Ctrl + U` | Clear the line from the cursor to the start |
| `Ctrl + K` | Clear the line from the cursor to the end |
| `Ctrl + R` | Search command history |
| `Ctrl + L` | Clear the screen (same as `clear`) |
| `Ctrl + C` | Interrupt/Terminate current foreground process |
| `Ctrl + Z` | Suspend/Pause current foreground process |

#### Job Control

| Command | Effect |
| :--- | :--- |
| `&` | Run command in the background (e.g., `cmd &`) |
| `jobs` | List all background jobs |
| `fg %1` | Bring job #1 to the foreground |
| `bg %1` | Resume suspended job #1 in the background |
| `nohup cmd &` | Keep command running after logout |

#### 🍱 Terminal Multiplexing basics (`tmux`)

| Shortcut | Effect |
| :--- | :--- |
| `tmux` | Start a new tmux session |
| `Ctrl + B`, then `D` | Detach from the session |
| `tmux ls` | List active sessions |
| `tmux attach -t 0` | Attach to session #0 |
| `Ctrl + B`, then `C` | Create a new window |
| `Ctrl + B`, then `N` | Move to next window |

---

Add these to your `~/.bashrc` and restart your shell:

```bash
# View the top 5/10 CPU-consuming processes
alias cpu5='ps auxf | sort -nr -k 3 | head -5'
alias cpu10='ps auxf | sort -nr -k 3 | head -10'

# View the top 5/10 memory-consuming processes
alias mem5='ps auxf | sort -nr -k 4 | head -5'
alias mem10='ps auxf | sort -nr -k 4 | head -10'

# View your public IP anytime
alias extip='curl icanhazip.com'

# View a local weather report
alias weather='curl wttr.in'
```

---

### 🪄 Helpful Tricks & Tweaks

#### 👀 Monitor commands continuously

```bash
watch -n 1 free -m
```

#### 📖 Quick command examples

Too Long Didn't Read manual pages (`man`). Install `tldr` first.

```bash
tldr tar
```

#### ⚡ Re-run most recent command with `sudo`

```bash
sudo !!
```

#### 📝 View system info on login

Add to `~/.bashrc`:

```bash
hostnamectl
```

#### ✨ Automatic `ls` while changing directories

Add this function to your `~/.bashrc`:

```bash
function cd() {
    new_directory="$*";
    if [ $# -eq 0 ]; then
        new_directory=${HOME};
    fi;
    builtin cd "${new_directory}" && /bin/ls -lhF --time-style=long-iso --color=auto --ignore=lost+found
}
```

---

### 🚀 Modern CLI Alternatives

Rust and Go have brought us some amazing, much faster, and prettier replacements for classic Unix tools. Consider installing these!

| Classic Command | Modern Alternative | Why it's better |
| :--- | :--- | :--- |
| `cat` | `bat` | Syntax highlighting, line numbers, git integration. |
| `grep` | `rg` (ripgrep) | Insanely fast, ignores `.gitignore` and hidden files by default. |
| `find` | `fd` | Shorter syntax, colored output, faster. |
| `du -sh` | `ncdu` / `dust` | Interactive & visual disk usage inspector. |
| `top` / `htop` | `btop` / `glances` | Gorgeous, detailed dashboards with mouse support. |
| `cd` | `zoxide` | Smart navigation, remembers your most used directories (`z folder`). |
