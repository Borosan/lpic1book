# 104.6. Create and change hard and symbolic links

**Weight:** 2

**Description:** Candidates should be able to create and manage hard and symbolic links to a file.

**Key Knowledge Areas:**

* Create links
* Identify hard and/or soft links
* Copying versus linking files
* Use links to support system administration tasks

**Terms and Utilities:**

* ln
* ls



### Introducing links <a id="introducing-links"></a>

 On a storage device, a file or directory is stored in a collection of blocks. Information about a file is held in an _inode_, which records information such as the owner, when the file was last accessed, how large it is, whether it is a directory or not, and who can read from or write to it. 

 A _directory entry_ contains a name for a file or directory and a pointer to the inode where the information about the file or directory is stored.

![](.gitbook/assets/links-inodes.jpg)

> The inode number is unique within a particular filesystem.

```text
root@ubuntu16-1:~/sandbox# ls -1i
2228290 dir
2228289 file1
```

**Whats is link ?**  A link is simply an additional directory entry for a file or directory, allowing two or more names for the same thing.

#### Creating links

 A _hard link_ is a directory entry that points to an inode, while a _soft link_ or _symbolic link_ is a directory entry that points to an inode that provides the name of another directory entry.  Symbolic links are also called _symlinks_.

![](.gitbook/assets/link-links.jpg)

> hard links point to an inode, and inodes are only unique within a particular file system, hard links cannot cross file systems.

> You can create hard links only for files and not for directories. The exception is the special directory entries in a directory for the directory itself and for its parent \(. and ..\)

### Hard Links vs Soft Links

| hard link | soft link |
| :--- | :--- |
| have same inodes number. | have different inodes numbers. |
| can’t cross the file system boundaries | can cross the file system |
| can’t link directories | allows you to link between directories |
| Links have actual file contents | contains the path for original file and not the contents |
| if the original file is removed, the link will still show you the contents of the file | Removing soft link doesn't affect anything but when the original file is removed, the link becomes a 'dangling' link that points to nonexistent file. |
| permissions will be updated if we change the permissions of source file | permissions will not be updated |

#### Creating links

### ln

we can  use ln command to create both hard links and soft links

```text
### For  Hard Link 
ln  [original filename] [link name] 

### For  Soft link 
ln  -s [original filename] [link name] 
```

#### Hard Links

```text
root@ubuntu16-1:~/sandbox# ls -l
total 4
drwxr-xr-x 2 root root 4096 Jan 29 08:14 dir
-rw-r--r-- 1 root root    0 Jan 29 08:14 file1

root@ubuntu16-1:~/sandbox# ls -1i
2228290 dir
2228289 file1

root@ubuntu16-1:~/sandbox# ln file1 HardLink

root@ubuntu16-1:~/sandbox# ls -1i
2228290 dir
2228289 file1
2228291 file2
2228289 HardLink
```

ls -l command shows all the links with the link column showing the number of links:

```text
root@ubuntu16-1:~/sandbox# ls -l
total 4
drwxr-xr-x 2 root root 4096 Jan 29 08:14 dir
-rw-r--r-- 2 root root    0 Jan 29 08:14 file1
-rw-r--r-- 2 root root    0 Jan 29 08:14 HardLink
```

look at that"2" infront of file1, it was "1" before creating HardLink.

#### Soft Links

```text
root@ubuntu16-1:~/sandbox# touch file2

root@ubuntu16-1:~/sandbox# ls -l
total 4
drwxr-xr-x 2 root root 4096 Jan 29 08:14 dir
-rw-r--r-- 2 root root    0 Jan 29 08:14 file1
-rw-r--r-- 1 root root    0 Jan 29 09:05 file2
-rw-r--r-- 2 root root    0 Jan 29 08:14 HardLink

root@ubuntu16-1:~/sandbox# ls -li
total 4
2228290 drwxr-xr-x 2 root root 4096 Jan 29 08:14 dir
2228289 -rw-r--r-- 2 root root    0 Jan 29 08:14 file1
2228291 -rw-r--r-- 1 root root    0 Jan 29 09:05 file2
2228289 -rw-r--r-- 2 root root    0 Jan 29 08:14 HardLink

root@ubuntu16-1:~/sandbox# ln -s  file2 SoftLink

root@ubuntu16-1:~/sandbox# ls -1i
2228290 dir
2228289 file1
2228291 file2
2228289 HardLink
2228292 SoftLink
```

ls -l command shows all links with second column value 1 and the link points to the original file.

```text
root@ubuntu16-1:~/sandbox# ls -l
total 4
drwxr-xr-x 2 root root 4096 Jan 29 08:14 dir
-rw-r--r-- 2 root root    0 Jan 29 08:14 file1
-rw-r--r-- 1 root root    0 Jan 29 09:05 file2
-rw-r--r-- 2 root root    0 Jan 29 08:14 HardLink
lrwxrwxrwx 1 root root    5 Jan 29 09:06 SoftLink -> file2
```

.

.

.

{% hint style="danger" %}
**remove symbolic links**

  We can ****remove \(delete\) symbolic links  using the `rm`, `unlink`, and `find` commands. 

_To remove a symlink, you need to have writing permissions on the directory that contains the symlink. Otherwise, you will get “Operation not permitted” error._

**Remove Symbolic Links with `rm`**

 To delete a symlink, invoke the `rm` command followed by the symbolic link name as an argument: `rm symlink_name`

 With `rm` you can delete more than one symbolic links at once. To do that pass the names of the symlinks as arguments, separated by space: `rm symlink1 symlink2`  Try -i to get confirmed.

 If the name of the argument ends with `/`, the `rm` command assumes that the file is a directory. The error happens because, when used without the `-d` or `-r` option, `rm` cannot delete directories.

#### Remove Symbolic Links with `unlink`

The unlink command deletes a given file`!` Unlike rm, unlink accepts only a single argument.

To delete a symbolic link, run the unlink command followed by the symlink name as an argument: `unlink symlink_name`

Do not append the / trailing slash at the end of the symlink name because unlink cannot remove directories`.`

#### Find and Delete Broken Symbolic Links

To find all broken symbolic links under a given directory, run the following command: `find /path/to/directory -xtype l`

 Once you find the broken symlinks, you can either manually remove them with `rm` or `unlink` or use the `-delete` option of the `find` command`: find /path/to/directory -xtype l -delete`

#### Conclusion

To remove a symbolic link, use either the `rm` or `unlink` command followed by the name of the symlink as an argument. When removing a symbolic link that points to a directory do not append a trailing slash to the symlink name.
{% endhint %}

.

[https://developer.ibm.com/tutorials/l-lpic1-104-6/](https://developer.ibm.com/tutorials/l-lpic1-104-6/)

[http://bambamdeo-linux.blogspot.com/2014/11/soft-link-and-hard-link.html](http://bambamdeo-linux.blogspot.com/2014/11/soft-link-and-hard-link.html)

[https://www.ostechnix.com/explaining-soft-link-and-hard-link-in-linux-with-examples/](https://www.ostechnix.com/explaining-soft-link-and-hard-link-in-linux-with-examples/)

[https://linoxide.com/linux-how-to/difference-soft-link-hard-link/](https://linoxide.com/linux-how-to/difference-soft-link-hard-link/)

[https://www.geeksforgeeks.org/soft-hard-links-unixlinux/](https://www.geeksforgeeks.org/soft-hard-links-unixlinux/)

\`\`[`https://linuxize.com/post/how-to-remove-symbolic-links-in-linux/`](https://linuxize.com/post/how-to-remove-symbolic-links-in-linux/)\`\`

.

