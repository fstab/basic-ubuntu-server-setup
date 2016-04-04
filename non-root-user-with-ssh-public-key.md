Adding a Non Root User With SSH Public Key Access in Ubuntu 15.10
-----------------------------------------------------------------

In this last part of our example tutorial, we will add a non-root user that
will be able to log into the Ubuntu server with SSH public key authentication.

This text is intended as an example of our [Literate Shell Scripting] blog post,
see [README.md] for more.

Firstly, we assume that the username is set in an environment variable
`USER_NAME`, and the SSH public key is set in an environment
variable `SSH_KEY`:

```sh
export USER_NAME="fabian"
export SSH_KEY="ssh-rsa APUBLICSSHKEYISaLoNGStriNG..."
```

We can use the following commands to verify if the variables are set.
These commands should execute without producing an error message:

```bash
if [ -z "$USER_NAME" -o -z "$SSH_KEY" ] ; then
    echo "ERROR: \$USER_NAME and \$SSH_KEY must be set." >&2
    exit -1
fi
```

Of course, we need `ssh` installed (on most systems `ssh` is already installed,
but on some systems, like Ubuntu's official Docker image, it isn't):

```bash
apt-get install -y ssh
```

Create the non-root user without a password (the user cannot log in):

```bash
adduser --disabled-password --gecos \"\" $USER_NAME
```

Add the new user to the group sudo, so it can run `sudo` commands:

```bash
adduser $USER_NAME sudo
```

Force the new user to choose a new password on the first login:

```bash
chage -d 0 $USER_NAME
passwd -d $USER_NAME
```

Add the ssh public key to the user's `~/.ssh/authorized_keys` file:

```bash
mkdir /home/$USER_NAME/.ssh
echo "$SSH_KEY" >> /home/$USER_NAME/.ssh/authorized_keys
chown -R $USER_NAME.$USER_NAME /home/$USER_NAME/.ssh
chmod 700 /home/$USER_NAME/.ssh
```

Configure `sshd` to make sure only the non-root user can log-in and
only ssh keys are accepted. This will prevent `root` access via `ssh`,
and it will prevent brute force attempts to guess the login password:

```bash
cat <<EOF >> /etc/ssh/sshd_config

# Allow ssh login only for user $USER_NAME

AllowUsers $USER_NAME

# Force public key authentication

PasswordAuthentication no
EOF
```

The new user should now be able use `ssh` to log in, and it should be prompted
to change the password on the first login.

Previous: [Set up a firewall].

[README.md]: README.md
[Set up a firewall]: firewall.md
