Docker Isn’t a Sandbox: How the docker Group Quietly Grants Root-Level Access

Most developers assume Docker containers are isolated little worlds—sealed boxes where processes can’t touch the host system. That assumption is dangerous.

If your user is in the docker group, you’re not “just a developer with access to containers.” You effectively have root-level control of the host. Full stop.







Thanks for reading! Subscribe for free to receive new posts and support my work.

Let’s walk through a real example that demonstrates why.

A Simple Experiment That Breaks the Illusion

Check you are logged in as a regular user:

$ whoami
user_A


Sure enough, this user can’t access /root:

$ ls /root
ls: cannot open directory ‘/root’: Permission denied


Everything looks normal… until you remember that user_A is part of the docker group.

And with Docker, that’s all you need.

Mounting the Host’s Root Directory Inside a Container

Watch what happens when we run a container with:

docker run -v /root:/workdir debian:trixie-slim ls -la /workdir/


Inside the container, /workdir is mapped to /root on the host.

Suddenly, the “unprivileged user” is browsing the host’s root account:

-rw------- 1 root root ... .profile
-rw-r--r-- 1 root root ... .bashrc
...


And we can read the contents, too:

docker run -v /root:/workdir debian:trixie-slim cat /workdir/.profile


At this point, the container has become a surgical tool for host-level privilege escalation.

There’s no exploit. No CVE. This is how Docker works by design.

Why This Happens

The Docker daemon (dockerd) runs as root.

Any user who can talk to the daemon—whether via Unix socket or TCP—can:





Mount any part of the host filesystem into a container



Launch a container that executes arbitrary commands as root



Modify host binaries



Add new users



Replace SSH keys



Or just chroot into the host and take over completely



This is why giving someone access to Docker is essentially the same as adding them to sudo.



Thanks for reading! Subscribe for free to receive new posts and support my work.
