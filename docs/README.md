# Linux admin cheatsheet

This is just meant for me because I don't know where to find a summary like this
anywhere else. If you know a better cheatsheet or have some suggestions, feel 
free to contact me at tomcampbell@gmail.com.

## Disk layout

### Individual drives and virtual drives

Linux is installed in a directory tree with `/` (forward slash, not backslash as in Windows.

**Subdirectories** are given names like `/dev` and `/usr`. 

**Disk drives** are given designations starting with `sd` followed by a letter. They liv in the `/dev` directory.
The first drive name defaults to `sda`, the next drive is named `sdb`, and so on. So you'd address them
as `/dev/sda`, `/dev/sdb`, etc.

**Disk partitions** are given numbers appended to the drive designation, 
so partition 1 on drive `/dev/sda` is addressed as `/dev/sda1`, partition 2 is addressed
as `/dev/sda2`, and so on.

## Permissions

### sudo and su

`sudo -s` (followed by a password) keeps you logged in as sudo.

## Command shell

### Multiple command shells

The key sequence `Alt+Right` (Or 
