I am working on a project where we run users’ applications in a rootless container, and we need to use overlayfs to mount each application’s filesystem. Because mounting is a privileged operation, we have to perform it inside a new user namespace.

How to use overlayfs

First, create the directories that overlayfs will use:



Thanks for reading! Subscribe for free to receive new posts and support my work.

$ cd /home/user

$ mkdir -p temp/lower
$ mkdir -p temp/upper
$ mkdir -p temp/work
$ mkdir -p temp/merged


Next, mount the overlay filesystem as root:

$ cd /home/user/temp
$ sudo mount -t overlay overlay \
    -o lowerdir=/home/user/temp/lower,upperdir=/home/user/temp/upper,workdir=/home/user/temp/work \
    merged


You can verify that the overlay mount is active:

$ mount
...
overlay on /home/user/temp/merged type overlay (rw,relatime,lowerdir=/home/user/temp/lower,upperdir=/home/user/temp/upper,workdir=/home/user/temp/work)


Testing the mount





Confirm that the merged directory is initially empty:

$ ls -la merged/
total 8
drwxrwxr-x 1 user user 4096 Nov 16 14:01 .
drwxrwxr-x 6 user user 4096 Nov 16 14:01 ..






Create files in the lower and upper directories:

$ touch /home/user/temp/lower/a
$ touch /home/user/temp/upper/b


Now check that these files appear in the merged view:

$ ls -la merged/
total 8
drwxrwxr-x 1 user user 4096 Nov 16 14:04 .
drwxrwxr-x 6 user user 4096 Nov 16 14:01 ..
-rw-rw-r-- 0 user user    0 Nov 16 14:02 a
-rw-rw-r-- 1 user user    0 Nov 16 14:04 b


Finally, unmount the overlay:

$ sudo umount merged


Using overlayfs without root (rootless)

Running mount as an unprivileged user will fail:

$ mount -t overlay overlay -o lowerdir=/home/user/temp/lower,upperdir=/home/user/temp/upper,workdir=/home/user/temp/work merged
mount: /home/user/temp/merged: must be superuser to use mount.


However, we can create a new user namespace, where we map our real UID/GID to root inside that namespace, allowing us to perform mounts without host-level root privileges.

Terminal 1: Create the namespace





Check your user ID:

$ id
uid=1004(user) gid=1004(user) groups=1004(user),27(sudo),100(users)






Create a new user namespace:

$ unshare -Um sh






Print the process ID of the shell inside the namespace:

$ echo $$
1243


Terminal 2: Set up UID/GID mappings





Run:

$ newuidmap 1243 0 1004 1
$ newgidmap 1243 0 1004 1


(Here, 1243 is the PID from Terminal 1.)

Back to Terminal 1





After running newuidmap, verify that the UID is now mapped to root:

$ id
uid=0(root) gid=65534(nogroup) groups=65534(nogroup)






After running newgidmap, verify that both UID and GID are mapped to root:

$ id
uid=0(root) gid=0(root) groups=0(root),65534(nogroup)


Now we can run the mount command as root inside the namespace:

$ mount -t overlay overlay \
    -o lowerdir=/home/user/temp/lower,upperdir=/home/user/temp/upper,workdir=/home/user/temp/work \
    merged


And that’s it — it works!

This approach can also be implemented programmatically in virtually any programming language. By forking the main process and configuring a new user namespace for the child process, the child can safely run with root privileges inside the namespace while remaining unprivileged on the host. Languages such as C, Rust, Go, Python, and others can invoke unshare, configure UID/GID mappings, and perform the mount operation entirely in code. This makes it possible to automate rootless overlayfs setups in container runtimes, sandboxing tools, or any application that needs to perform privileged filesystem operations without requiring real root access.



Thanks for reading! Subscribe for free to receive new posts and support my work.

