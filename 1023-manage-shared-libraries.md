# 102.3. Manage shared libraries

## **102.3 Manage shared libraries**

**Weight:** 1

**Description:** Candidates should be able to determine the shared libraries that executable programs depend on and install them when necessary.

**Key Knowledge Areas:**

* Identify shared libraries
* Identify the typical locations of system libraries
* Load shared libraries

**Terms and Utilities:**

* ldd
* ldconfig
* /etc/ld.so.conf
* LD_LIBRARY_PATH

#### What is a library?

In programming, a library is an assortment of pre-compiled pieces of code that can be reused in a program. Libraries simplify life for programmers, in that they provide reusable functions, routines, classes, data structures and so on (written by a another programmer), which they can use in their programs.

![](.gitbook/assets/sharedlib-intro.jpg)

Linux supports two classes of libraries, namely:

* Static libraries – are bound to a program statically at compile time.
* Dynamic or shared libraries – are loaded when a program is launched and loaded into memory and binding occurs at run time.

**Static libraries vs Shared libraries**

* Each program using routines from a static library has a copy of them in the executable file. This wastes disk space and (if more than one such program is in use at the same time) RAM as well. Also, when a static library is updated, all the programs using it must be recompiled in order to take advantage of the new code. 
* When a program uses a shared library instead, the program binary does not include a copy of the code, but only a reference to the library. The run time loader, finds the library and loads it into memory at the same time as the program.** **One advantage of using dynamic library is that when a library is updated ( security issues ) all other programs take advantage of new code without recompiling.

{% hint style="info" %}
Dynamic or shared libraries can further be categorized into two groups ( beyond the scope of LPIC exam):

* **Dynamically linked libraries** – here a program is linked with the shared library and the kernel loads the library (in case it’s not in memory) upon execution.
* **Dynamically loaded libraries** – the program takes full control by calling functions with the library.
{% endhint %}

### Libraries locations in linux

Depending on library, Linux stores its libraries mainly in three locations:

* **/lib**
* **/usr/lib**
* **/usr/local/lib**

**/lib **: The /lib directory contains those **shared library images needed to boot** the system and run the commands in the root filesystem, ie. by binaries in /bin and /sbin.

```
root@ubuntu16-1:~# ls /lib
apparmor  ifupdown                              modules        udev
brltty    init                                  recovery-mode  ufw
cpp       klibc-k3La8MUnuzHQ0_kG8hokcGAC0PA.so  resolvconf     x86_64-linux-gnu
crda      linux-sound-base                      systemd        xtables
firmware  lsb                                   sysvinit
hdparm    modprobe.d                            terminfo
```

The /lib folder sister folders are /lib32 and /lib64.

**/usr/lib** :** All software libraries** are installed here. This **does not contain system default or kernel libraries**.

```
root@ubuntu16-1:~# ls /usr/lib/
```

**/usr/local/lib**: To place **extra system library files** here. These library files can be used by different applications.

```
root@ubuntu16-1:~# ls /usr/local/lib/
python2.7  python3.5
```

Also **/var/lib** Directory, holds **dynamic data libraries/files** like the rpm/dpkg database and game scores.

```
root@ubuntu16-1:~# ls /var/lib/
AccountsService      fwupd            nginx                    udisks2
acpi-support         gconf            nssdb                    update-manager
alsa                 ghostscript      os-prober                update-notifier
apparmor             git              pam                      update-rc.d
app-info             hp               plymouth                 upower
apt                  initramfs-tools  polkit-1                 urandom
aspell               initscripts      python                   ureadahead
avahi-autoipd        insserv          resolvconf               usb_modeswitch
binfmts              ispell           samba                    usbutils
bluetooth            libreoffice      sgml-base                vim
colord               lightdm          snapd                    vmware
dbus                 lightdm-data     snmp                     vmware-caf
dhcp                 locales          sudo                     whoopsie
dictionaries-common  logrotate        systemd                  xfonts
doc-base             man-db           ubiquity                 xkb
dpkg                 misc             ubuntu-drivers-common    xml-core
emacsen-common       mlocate          ubuntu-release-upgrader
fail2ban             NetworkManager   ucf
```

In this article we will discuss specifically about Shared Libraries.

### Shared Library Naming Conventions

Shared libraries are named in three ways:

* library name (a.k.a "soname")  
* filename  (a.k.a "real name")(absolute path to file which stores library code)
* "linker name"

**1)**Every shared library has a special name called the **"soname''**. The soname has the prefix "**lib**'', the name of the library, the phrase "**.so**'', followed by a period and a **version number** that is incremented whenever the interface changes . 

```
libreadline.so.6
```

A fully-qualified soname includes as a prefix the directory it's in.

```
/usr/lib/libreadline.so.6
```

On a working system a fully-qualified soname is simply a symbolic link to the shared library's "real name''.

**2)**Every shared library also has a **"real name''**, which is the filename containing the actual library code. The real name adds to the soname a period, a minor number, another period, and the release number. The last period and release number are optional.

```
libreadline.so.6.3
```

**3)**In addition, there's the name that the compiler uses when requesting a library, (I'll call it the **"linker name''**), which is simply the soname without any version number.

```
libreadline.so
```

Okey lets explain all in an example:

```
root@ubuntu16-1:/lib/x86_64-linux-gnu# ls -l | grep libread
lrwxrwxrwx 1 root root      18 Nov 26  2017 libreadline.so.6 -> libreadline.so.6.3
-rw-r--r-- 1 root root  282392 Feb  4  2016 libreadline.so.6.3
```

Thus, `/usr/lib/libreadline.so.6` is a fully-qualified soname, which is set to be a symbolic link to some realname like `/usr/lib/libreadline.so.6.3`. There should also be a linker name, `/usr/lib/libreadline.so` which could be a symbolic link referring to `/usr/lib/libreadline.so.6`.

### ldd

To **get a list of all shared library dependencies for a binary file**, you can use the ldd utility**. **For example lets see what shared libraries are required by ls command:

```
root@ubuntu16-1:~# which ls
/bin/ls
root@ubuntu16-1:~# ldd /bin/ls
    linux-vdso.so.1 =>  (0x00007ffe511d5000)
    libselinux.so.1 => /lib/x86_64-linux-gnu/libselinux.so.1 (0x00007f687d3b1000)
    libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f687cfe7000)
    libpcre.so.3 => /lib/x86_64-linux-gnu/libpcre.so.3 (0x00007f687cd77000)
    libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f687cb72000)
    /lib64/ld-linux-x86-64.so.2 (0x00005591d42e3000)
    libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f687c955000)
```

Try `ltrace ls` to get surprised! But how poor ls can find its required shared libraries?

### Locating Shared Libraries in Linux

A program can call a library using its library name or filename, and a library path stores directories where libraries can be found in the filesystem. But How it can find them? Lets show the structure in a picture:

![](.gitbook/assets/sharedlib-filestructure.jpg)

These three files helps programs to find their required shared libraries.

### ld.so.conf

ld.so.conf file tells the system where to look for library files, that is kind of an address book.

```
root@ubuntu16-1:~# cat /etc/ld.so.conf
include /etc/ld.so.conf.d/*.conf
```

Traditionally in the past, it included all the paths which libraries existed. Today more abstraction has been implemented and it just points to ld.so.conf.d directory. Simply it says go to the ld.so.conf.d directory and load any configuration files.

### /etc/ld.so.conf.d

ld.so.conf.d directory contains several configuration files for the kernel or for other third party applications.

```
root@ubuntu16-1:~# ls -la /etc/ld.so.conf.d/
total 32
drwxr-xr-x   2 root root  4096 Oct  7 00:37 .
drwxr-xr-x 142 root root 12288 Nov 11 00:36 ..
-rw-rw-r--   1 root root    38 Nov 24  2014 fakeroot-x86_64-linux-gnu.conf
-rw-r--r--   1 root root    44 Jan 27  2016 libc.conf
-rw-r--r--   1 root root   276 Oct  7 00:37 vmware-tools-libraries.conf
-rw-r--r--   1 root root    68 Apr 14  2016 x86_64-linux-gnu.conf
lrwxrwxrwx   1 root root    43 Nov 26  2017 x86_64-linux-gnu_EGL.conf -> /etc/alternatives/x86_64-linux-gnu_egl_conf
lrwxrwxrwx   1 root root    42 Nov 26  2017 x86_64-linux-gnu_GL.conf -> /etc/alternatives/x86_64-linux-gnu_gl_conf
```

Lets see what is inside configuration files :

```
root@ubuntu16-1:~# cat /etc/ld.so.conf.d/libc.conf 
# libc default configuration
/usr/local/lib
root@ubuntu16-1:~# cat /etc/ld.so.conf.d/x86_64-linux-gnu.conf 
# Multiarch support
/lib/x86_64-linux-gnu
/usr/lib/x86_64-linux-gnu
root@ubuntu16-1:~# cat /etc/ld.so.conf.d/vmware-tools-libraries.conf 
/usr/lib/vmware-tools/lib32/libvmGuestLib.so
/usr/lib/vmware-tools/lib64/libvmGuestLib.so
/usr/lib/vmware-tools/lib32/libvmGuestLibJava.so
/usr/lib/vmware-tools/lib64/libvmGuestLibJava.so
/usr/lib/vmware-tools/lib32/libDeployPkg.so
/usr/lib/vmware-tools/lib64/libDeployPkg.so
```

We can add a new configuration file or edit any of them in order to include a new path.

### ldconfig

Because shared libraries can exist in many different directories, searching through all of these directories when a program is launched would be greatly inefficient, which is one of the likely disadvantages of dynamic libraries. Therefore a mechanism of caching employed, performed by a the program ldconfig.

By default, ldconfig reads the content of /etc/ld.so.conf, creates the appropriate symbolic links in the dynamic link directories, and then writes a cache to /etc/ld.so.cache which is then easily used by other programs.

### ld.so.cache

This is a binary file, so that is not something we usually like to see :

```
root@ubuntu16-1:~# cat /etc/ld.so.cache 
ld.so-1.7.0�^^<^P^~^�^�^�^�^
                                       _<_F_f_s_�_�_�_�_``<`K`t`�`�`�`�`�`a#aJa\a�a�a�a�a�a
b4bFbrb�b�b�b�b�b$c7cdcuc�c�c�c�cd(dTdgd�d�d�d�de0e^ere�e�e�e�ef,fVfgf�f�f�f�f
                                           ggFgSgvg�g�g�g�g�g(h7h`hth�h�h�h�h"i9iji�i�i�i�ij>jRj�j�j�j�jk%kTklk�k�k�kl6lKlzl�l�l�lm"mXmpm�m�m�m�mn%nLn]n�n�n�n�n6oFopo�o�o�o�o�o,p=phpp�p�p�p    q:qQq�q�q�q�q�q�q&r3rZrmr�r�r�r�rssHsZs�s�s�s�s�stWtlt�t�tuuPucu�u�u�u�u
vv@vKvpv}v�v�v�v�vww<wMwxw�w�w�w�w�wx5xfxzx�x�x�x�x&y<yly�y�y�yzz>z\z�z�z�z�z {.{V{l{�{�{�{|N|a|�|�|�|�|*}?}n}�}�}�}
~"~T~f~�~�~�~�~�~
4Adr�����'�N�Z�����Ѐހ��P�m�����ց����J�W�~���������$�6�b�t�����؃��
�4�L�~�����ȅ����$�3�\�h�����Ćц���@�Z����������;�t������ڈ���(�N�i�����މ��
�:�G�n�~��������� �6�f�x�����Ҏ���)�Z�x���ď��ь��
<The out put has been truncuated>
```

use `string` command intead and it just shows human readable binary files, also `lconfig -p` prints cache.

This is very important especially when we have just installed new shared libraries or created our own, or created new library directories. We need to run ldconfig command to effect the changes.(The out put has been truncated):

```
root@ubuntu16-1:~# ldconfig -v
/sbin/ldconfig.real: Path `/lib/x86_64-linux-gnu' given more than once
/sbin/ldconfig.real: Path `/usr/lib/x86_64-linux-gnu' given more than once
/usr/lib/x86_64-linux-gnu/libfakeroot:
    libfakeroot-0.so -> libfakeroot-tcp.so
/usr/local/lib:
/usr/lib/vmware-tools/lib32/libvmGuestLib.so:
    libvmGuestLib.so -> libvmGuestLib.so
/usr/lib/vmware-tools/lib64/libvmGuestLib.so:
    libvmGuestLib.so -> libvmGuestLib.so
/usr/lib/vmware-tools/lib32/libvmGuestLibJava.so:
    libvmGuestLibJava.so -> libvmGuestLibJava.so
/usr/lib/vmware-tools/lib64/libvmGuestLibJava.so:
    libvmGuestLibJava.so -> libvmGuestLibJava.so
/usr/lib/vmware-tools/lib32/libDeployPkg.so:
    libDeployPkg.so -> libDeployPkg.so
/usr/lib/vmware-tools/lib64/libDeployPkg.so:
    libDeployPkg.so -> libDeployPkg.so
/lib/x86_64-linux-gnu:
    libsepol.so.1 -> libsepol.so.1
    libisc-export.so.160 -> libisc-export.so.160.0.0
    libdns-export.so.162 -> libdns-export.so.162.1.3
    libselinux.so.1 -> libselinux.so.1
    libuuid.so.1 -> libuuid.so.1.3.0
    libc.so.6 -> libc-2.23.so
    libnss_mdns6.so.2 -> libnss_mdns6.so.2
    libnss_files.so.2 -> libnss_files-2.23.so
    ...
/sbin/ldconfig.real: /lib/x86_64-linux-gnu/ld-2.23.so is the dynamic linker, ignoring

    ld-linux-x86-64.so.2 -> ld-2.23.so
    libnih-dbus.so.1 -> libnih-dbus.so.1.0.0
    libnss_mdns.so.2 -> libnss_mdns.so.2
    libatasmart.so.4 -> libatasmart.so.4.0.5
    libdbus-1.so.3 -> libdbus-1.so.3.14.6
    libpthread.so.0 -> libpthread-2.23.so
    libutil.so.1 -> libutil-2.23.so
    libz.so.1 -> libz.so.1.2.8
    ...
/usr/lib/x86_64-linux-gnu:
    libplc4.so -> libplc4.so
    libcdr-0.1.so.1 -> libcdr-0.1.so.1.0.2
    libmwaw-0.3.so.3 -> libmwaw-0.3.so.3.0.7
    libjbig.so.0 -> libjbig.so.0
    libnssutil3.so -> libnssutil3.so
    libOxideQtQuick.so.0 -> libOxideQtQuick.so.0
    libubsan.so.0 -> libubsan.so.0.0.0
    libgudev-1.0.so.0 -> libgudev-1.0.so.0.2.0
    libx265.so.79 -> libx265.so.79
    libnetfilter_conntrack.so.3 -> libnetfilter_conntrack.so.3.5.0
    libsamba-util.so.0 -> libsamba-util.so.0.0.1
    ...
d.so
    libtotem-plparser-mini.so.18 -> libtotem-plparser-mini.so.18.1.0
    libhcrypto.so.4 -> libhcrypto.so.4.1.0
    libxcb-shape.so.0 -> libxcb-shape.so.0.0.0
    libQt5PrintSupport.so.5 -> libQt5PrintSupport.so.5.5.1
    libdmapsharing-3.0.so.2 -> libdmapsharing-3.0.so.2.9.34
    libyaml-0.so.2 -> libyaml-0.so.2.0.4
    libfreerdp-core.so.1.1 -> libfreerdp-core.so.1.1.0
    libzeitgeist-1.0.so.1 -> libzeitgeist-1.0.so.1.1.4
    libHalf.so.12 -> libHalf.so.12.0.0
    libxcb-util.so.1 -> libxcb-util.so.1.0.0
2.4.so.1.3.0
    libhardware.so.2 -> libhardware.so.2.0.0
    libindicator3.so.7 -> libindicator3.so.7.0.0
    libcmis-0.5.so.5 -> libcmis-0.5.so.5.0.0
    libmpxwrappers.so.0 -> libmpxwrappers.so.0.0.0
    libkrb5.so.3 -> libkrb5.so.3.3
    libavdevice-ffmpeg.so.56 -> libavdevice-ffmpeg.so.56.4.100
    libndp.so.0 -> libndp.so.0.0.2
    libao.so.4 -> libao.so.4.0.0
    libsignon-plugins.so.1 -> libsignon-plugins.so.1.0.0
    liba52-0.7.4.so -> liba52-0.7.4.so
    ...
/usr/lib/x86_64-linux-gnu/mesa-egl:
    libEGL.so.1 -> libEGL.so.1.0.0
/usr/lib/x86_64-linux-gnu/mesa:
    libGL.so.1 -> libGL.so.1.2.0
/lib:
/usr/lib:
    libgdiplus.so.0 -> libgdiplus.so.0.0.0
    libMonoPosixHelper.so -> libMonoPosixHelper.so
    libMonoSupportW.so -> libMonoSupportW.so
    libnetpbm.so.10 -> libnetpbm.so.10.0
    libfsplib.so.0 -> libfsplib.so.0.0.0
```

ldconfig command has some options:

```
root@ubuntu16-1:~# ldconfig --help
Usage: ldconfig.real [OPTION...]
Configure Dynamic Linker Run Time Bindings.

  -c, --format=FORMAT        Format to use: new, old or compat (default)
  -C CACHE                   Use CACHE as cache file
  -f CONF                    Use CONF as configuration file
  -i, --ignore-aux-cache     Ignore auxiliary cache file
  -l                         Manually link individual libraries.
  -n                         Only process directories specified on the command
                             line.  Don't build cache.
  -N                         Don't build cache
  -p, --print-cache          Print cache
  -r ROOT                    Change to and use ROOT as root directory
  -v, --verbose              Generate verbose messages
  -X                         Don't update symbolic links
  -?, --help                 Give this help list
      --usage                Give a short usage message
  -V, --version              Print program version

Mandatory or optional arguments to long options are also mandatory or optional
for any corresponding short options.

For bug reporting instructions, please see:
<https://bugs.launchpad.net/ubuntu/+source/glibc/+bugs>.
```

After creating our shared library, we need to install it.

### Installing a Dynamic Library: <a href="f3ce" id="f3ce"></a>

There are several ways to install a dynamic library:

**1)**We can either move it into any of the standard directories mentioned above, and run the ldconfig command.

**2)**The method above sets the library path permanently. To set it temporarily, use the LD_LIBRARY_PATH environment variable on the command line.

## LD_LIBRARY_PATH

In Linux, the environment variable LD_LIBRARY_PATH is a colon-separated set of directories where libraries should be searched for first, before the standard set of directories; this is useful when debugging a new library or using a nonstandard library for special purposes.

```
root@ubuntu16-1:~# echo $LD_LIBRARY_PATH
```

The usual dynamic linker on Linux uses a cache to find its libraries, So there is no default value for LD_LIBRARY_PATH, default library lookup doesn’t need it at all. If LD_LIBRARY_PATH is defined, then it is used first, but doesn’t disable the other lookups (which also include a few default directories).

Whether it has a default value or not we can use blow command to add our new library path:

```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/your/custom/path/
```

Do not forget that these changes are not permanent and If you want to keep the changes permanent, then add above line in the shell initialization file**/etc/profile**(global) or**\~/.profile**(user specific), which will be discussed in next lessons.

That's all!

.

.

.

Sources:

[https://www.tecmint.com/understanding-shared-libraries-in-linux/](https://www.tecmint.com/understanding-shared-libraries-in-linux/)

[https://www.ibm.com/developerworks/linux/library/l-dynamic-libraries/](https://www.ibm.com/developerworks/linux/library/l-dynamic-libraries/)

[http://www.linuxforums.org/forum/newbie/151875-what-libraries.html](http://www.linuxforums.org/forum/newbie/151875-what-libraries.html)

[https://www.linuxnix.com/linux-directory-structure-lib-explained/](https://www.linuxnix.com/linux-directory-structure-lib-explained/)

[https://askubuntu.com/questions/17545/where-does-ubuntu-store-its-library-files](https://askubuntu.com/questions/17545/where-does-ubuntu-store-its-library-files)

[https://medium.com/@jhuang1012bn/static-library-and-shared-library-de6def6b1d3b](https://medium.com/@jhuang1012bn/static-library-and-shared-library-de6def6b1d3b)

[http://tldp.org/HOWTO/Program-Library-HOWTO/shared-libraries.html](http://tldp.org/HOWTO/Program-Library-HOWTO/shared-libraries.html)

[https://unix.stackexchange.com/questions/354295/what-is-the-default-value-of-ld-library-path](https://unix.stackexchange.com/questions/354295/what-is-the-default-value-of-ld-library-path)
