# Kubernetes: Disk Pressure

Disk pressure is a system state where the OS is under stress because disk resources are running out or are heavily constrained

* The disk is starting to become a bottleneck for normal operations
* The equivalent of high CPU or low memory pressure

<br>

# Common Causes

* Disk space is almost full
* Inodes are exhausted
* Disk I/O is saturated
* Too many writes are happening at once

<br>

# Troubleshoot Disk Pressure

* Check disk space using the `df- h` command
* Check inode usage using the `df -i` command
* Check for any disk pressure by describing a node from a master node within a Kubernetes cluster using the `kubectl describe node HOSTNAME | grep -i diskpressure` command