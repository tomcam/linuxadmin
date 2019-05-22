# Linux admin cheatsheet

This is just meant for me because I don't know where to find a summary like this
anywhere else. If you know a better cheatsheet or have some suggestions, feel 
free to contact me at tomcampbell@gmail.com.

## Maintenance on CentOS

### View installed packages with yum list

See what yum packages are installed.
It will probably be a long list.

```
# Get a list of all installed yum packages.
sudo yum list
```

### Search for a particular package with yum list installed and grep

Suppose you want to know if `nvim` is installed.
You can use grep:

```
# Get a list of installed packages, pipe it
# through grep, and see if `nvim` is installed.
sudo yum list installed | grep nvim
```

### Determine if a yum package exists with yum list

Suppose you want to know if a yum package named `nvim` is
available at all. Just append it to `yum list`:

```
# Find out if there's a yum package for
# the nvim editor.
sudo yum list nvim
```

### Daily/every few days maintenance on CentOS: yum update

Every day or three use `sudo yum update` to see what
updates are necessary.

```
# See what updates are available.
sudo yum check-update
# Install patches and updates
sudo yum update
```

### Higher impact maintenance on CentOS: yum upgrade

When you can afford a little more downtime run `yum upgrade`:

```
# Install upgrades
sudo yum upgrades
```

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

The key sequence `Alt+Right` (`⌘+Right` on MacOS) switches you to the next terminal, 
and of course `Alt+Left`/`⌘+Left` to the previous session.
