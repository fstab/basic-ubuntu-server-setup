How to Configure a Simple Firewall on an Ubuntu Server
======================================================

There's a lot to say about `iptables` firewalls.
However, as this tutorial is just an example, we will keep it very short.

First, drop everything except for SSH and PING:

```bash
iptables -F
iptables -I INPUT 1 -i lo -j ACCEPT
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -p tcp --dport ssh -j ACCEPT
iptables -A INPUT -p icmp -j ACCEPT
iptables -P INPUT DROP
```

Second, save the current firewall configuration to `/etc/iptables.rules`:

```bash
iptables-save > /etc/iptables.rules
```

Third, create a restore script in `/etc/network/if-pre-up.d/iptablesload`
to make sure the rules are restored after restarting the network:

```bash
cat <<EOF > /etc/network/if-pre-up.d/iptablesload
#!/bin/bash

iptables-restore < /etc/iptables.rules
exit 0
EOF

# Make restore script executable.

chmod 755 /etc/network/if-pre-up.d/iptablesload
```

Previous: [Configure the timezone]
Next: [Add a non-root user].

[Configure the timezone]: timezone.md<br/>
[Add a non-root user]: non-root-user-with-ssh-public-key.md
