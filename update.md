How to Update an Ubuntu Server
------------------------------

Ubuntu server images are snapshots created with a certain version of the
package repositories. Before using an image, it is always a good idea to
update the package index and upgrade all installed packages to the current
version.

Using `apt-get`, this is done as follows:

```bash
apt-get update
apt-get upgrade -y
```

Previous: [Overview]<br/>
Next: [Configure the timezone]

[Overview]: basic-ubuntu-server-setup.md
[Configure the timezone]: timezone.md
