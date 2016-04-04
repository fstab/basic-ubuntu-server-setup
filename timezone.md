How to Configure the Timezone on Ubuntu Servers
-----------------------------------------------

The timezone on Ubuntu servers is configured in `/etc/timezone`.
In order to change this setting, Ubuntu provides an interactive
timezone configuration tool that can be started with:

```sh
sudo dpkg-reconfigure tzdata
```

However, for automated configuration, it is also possible to configure the
timezone in non-interactive mode:

```bash
echo "Europe/Berlin" > /etc/timezone
dpkg-reconfigure -f noninteractive tzdata
```

Previous: [Update all packages].
Next: [Set up a firewall].

[Update all packages]: update.md<br/>
[Set up a firewall]: firewall.md
