# 110.1. Perform security administration tasks

## **110.1 Perform security administration tasks**

**Weight:** 3

**Description:** Candidates should know how to review system configuration to ensure host security in accordance with local security policies.

**Key Knowledge Areas:**

* Audit a system to find files with the suid/sgid bit set
* Set or change user passwords and password aging information
* Being able to use nmap and netstat to discover open ports on a system
* Set up limits on user logins, processes and memory usage
* Determine which users have logged in to the system or are currently logged in
* Basic sudo configuration and usage

**Terms and Utilities:**

* find
* passwd
* fuser
* lsof
* nmap
* chage
* netstat
* sudo
* /etc/sudoers
* su
* usermod
* ulimit
* who, w, last

In this lesson we just take a look at basic security audits. First we review several commands we have learned from the  security perspective and then get introduced to some other new commands.

### find suid/guid 

We have learned about suid/guid  when we talked about managing file permissions and owner ship, as a quick review see table bellow:

| access mode |  **on file** | **on directory** |
| :--- | :--- | :--- |
| **SUID** | executes with permissions of file owner | nothing |
| **GUID** | executes with the permissions of group | new files have group membership of directory |
| **Sticky Bit** | nothing | only owner can delete files |

There are some  security concern while using suid/guid  such as, what will happen if a destructive program has suid/guid permission set on it? 

look for open ports

netstat 

nmap  

examine sudo configuration 

