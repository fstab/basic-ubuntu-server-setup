How to Set Up a Basic Ubuntu 15.10 Server with SSH Access
---------------------------------------------------------

This tutorial is an example of _Literate Shell Scripting_, which is a way of
documenting shell scripts that was introduced in a blog post on [labs.consol.de].

The example tutorial shows some basic steps for setting up a new
Ubuntu 15.10 server with SSH access:

* [update.md]: Initial package update.
* [timezone.md]: Setting the timezone.
* [firewall.md]: Simple firewall setup.
* [non-root-user-with-ssh-public-key.md]: Create a new user who can login
  with an `ssh` public key.

The remainder of this page shows how to automatically execute the code snippets
from this tutorial using [Packer].

Configuring the Automated Build
-------------------------------

The example code in this tutorial can be executed using Packer.
Packer is a tool for creating machine images for multiple platforms, like
[Amazon Web Services], [Virtual Box], [Digital Ocean], [VMWare], [Docker], [etc.]
A Packer build is configured via a JSON file called _template_. The
template for this tutorial is [basic-ubuntu-server-setup.json].

In order to run the code snippets from this tutorial, we first need to
download the tutorial from GitHub:

```bash
git clone https://github.com/ConSol/basic-ubuntu-server-setup
```

The template file uses environment variables to learn the name
and the ssh public key of the user to be created. We need to export
these variables before running the build (replace _fabian_ with the
user name and _ssh-rsa XXX..._ with the ssh public key for the user):

```bash
export USER_NAME="fabian"
export SSH_KEY="ssh-rsa APUBLICSSHKEYISaLoNGStriNG..."
```

Last but not least, we need to install Packer.
Packer is a single executable, so installing Packer boils down to putting
the executable somewhere in the path. See Packer's [installation instructions]
for more information. Alternatively, on machines with the [Go] programming
language compiler installed, we can install Packer from source:

```bash
go get -u github.com/mitchellh/packer
```

How to Run the Build on Docker
---------------------------------

We can use Packer to create a [Docker] container from the code snippets in the
tutorial. The following command will create a file
`basic-ubuntu-server-setup.tar` containing the Docker container.

```bash
packer build -only docker basic-ubuntu-server-setup.json
```

We can now import the container file and create a new Docker image from it:

```bash
docker import - consol/basic-ubuntu-server-setup:latest < basic-ubuntu-server-setup.tar
```

Now we can the image. Docker images don't start services automatically,
so in order to test the ssh service, we need to explicitly pass
`service ssh start` as the command when starting the container.
Moreover, if we use `docker-machine` or `boot2docker`, we cannot access
the Docker container directly, but we must forward the port through the
Docker host. As the ssh port `22` on the Docker host is already in use,
we map the port to `2222`:

```bash
docker run -p 2222:22 -t -i consol/basic-ubuntu-server-setup /bin/bash -c 'service ssh start && exec /bin/bash'
```

Assuming the Docker host's address is `192.168.99.100` (on Linux it would be
`localhost` instead), we should now be able to start a new shell window
and connect to the Docker container:

```bash
ssh -p 2222 -l $USER_NAME 192.168.99.100
```

How to Run the Build on Digital Ocean
----------------------------------------

As an alternative to running this tutorial locally with Docker, we can
run it on [Digital Ocean], which is a simple, developer-friendly and
reasonably-priced cloud service.

In order to use Digital Ocean from the command line, we need an API token.
The API token can be retrieved from Digital Ocean's Web interface.
After logging into the Web interface and navigating to the _'API'_ tab, there
is a button labeled _'Generate New Token'_.

Packer reads the API token from the `DIGITALOCEAN_API_TOKEN` environment
variable, so we need to export that variable (replace the value `2d611...`
with your API token):

```bash
export DIGITALOCEAN_API_TOKEN=2d61116d42a12d860802fa9dfc06ed8aae3ff8187c923d7ac66b6e8823b2d456
```

To test if this works, we run an example API call using `curl`.
The following command should retrieve a list of available images from
Digital Ocean:

```bash
curl -X GET \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer ${DIGITALOCEAN_API_TOKEN}" \
     "https://api.digitalocean.com/v2/images?page=1&per_page=100"
```

If everything goes fine, we can run the code snippets from this tutorial on
Digital Ocean using Packer:

```bash
packer build -only digitalocean basic-ubuntu-server-setup.json
```

After the successful run, a new image with the result will be available
on Digital Ocean's web interface on the _'Images'_ tab. Clicking _'More'_ ->
_'Create Droplet'_ will run that image.

How to Run the Build on Other Platforms
-----------------------------------------

The `builders` configuration in `basic-ubuntu-server-setup.json` contain
Docker as an example of a local build, and Digital Ocean as an example
of a cloud service. However, it is perfectly possible to run the example an
any other platform supported by Packer.

Packer's [builders documentation] shows how to configure the `builders`
section for any supported platform.

Just as a side note: The [Virtual Box] configuration turns out to be quite
complex, because we need to automate the graphical installation process
after booting Ubuntu's ISO image. It is a good idea to get a working
configuration from [https://github.com/boxcutter/ubuntu] and start from there.

TL;DR
-----

This tutorial is an example of
_Literate Shell Scripting with Markdown and Packer_,
which is introduced here: [labs.consol.de]

[labs.consol.de]: https://labs.consol.de/packer/2016/04/04/literate-shell-scripting.html
[update.md]: update.md
[timezone.md]: timezone.md
[firewall.md]: firewall.md
[non-root-user-with-ssh-public-key.md]: non-root-user-with-ssh-public-key.md
[Packer]: https://www.packer.io
[Amazon Web Services]: http://aws.amazon.com
[Virtual Box]: https://www.virtualbox.org
[Digital Ocean]: https://www.digitalocean.com
[VMWare]: http://www.vmware.com
[Docker]: https://www.docker.com/
[etc.]: https://www.packer.io/docs/
[basic-ubuntu-server-setup.json]: basic-ubuntu-server-setup.json
[installation instructions]: https://www.packer.io/docs/installation.html
[Go]: https://golang.org/
[builders documentation]: https://www.packer.io/docs/templates/builders.html
[Virtual Box]: https://www.virtualbox.org/
[https://github.com/boxcutter/ubuntu]: https://github.com/boxcutter/ubuntu
