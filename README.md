
ABOUT
INSTALLATION
Prerequisites
Docker Images
Initial Configuration
Official Packages
Amazon Linux
CentOS
Debian
Fedora
RHEL
Ubuntu
Homebrew
Go
Node.js at npm
Startup and Shutdown
Community Repositories
Alpine Linux
Arch Linux
CentOS/RHEL SCLs
FreeBSD
Gentoo
NixOS/Nix
Remi’s RPM Repo
Source Code
Obtaining Sources
Installing Required Software
Configuring Sources
Configuring Modules
Building and Installing Unit
Startup
CONFIGURATION
HOWTO
TROUBLESHOOTING
CONTRIBUTION
Installation
Prerequisites

NGINX Unit compiles and runs on various Unix-like operating systems, including:

FreeBSD 10 or later
Linux 2.6 or later
macOS 10.6 or later
Solaris 11
It also supports most modern instruction set architectures, such as:

ARM
IA-32
PowerPC
MIPS
S390X
x86-64
App languages and platforms that Unit can run (including several versions of the same language):

Go 1.6 or later
Java 8 or later
Node.js 8.11 or later
PHP 5, 7
Perl 5.12 or later
Python 2.6, 2.7, 3
Ruby 2.0 or later
Docker Images

To install and run Unit from NGINX’s Docker image repository:

$ docker pull nginx/unit
$ docker run -d nginx/unit
Default image tag is :latest; it resolves into a -full configuration of the latest Unit version. Other tags:

Tag	Description
1.18.0-full	Modules for all supported languages.
1.18.0-minimal	No language modules.
1.18.0-<language>	A specific language module, for example 1.18.0-ruby2.3 or 1.18.0-python2.7.
We also publish these images as tarballs on our website:

$ curl -O https://packages.nginx.org/unit/docker/1.18.0/nginx-unit-1.18.0-full.tar.gz
$ curl -O https://packages.nginx.org/unit/docker/1.18.0/nginx-unit-1.18.0-full.tar.gz.sha512
$ sha512sum -c nginx-unit-1.18.0-full.tar.gz.sha512
      nginx-unit-1.18.0-full.tar.gz: OK
$ docker load < nginx-unit-1.18.0-full.tar.gz
Note

The control socket’s pathname is /var/run/control.unit.sock.
For further details, see the repository page and the official Howto.

Initial Configuration

Official images support initial container configuration, implemented with an ENTRYPOINT script.

First, the script checks the Unit state directory (/var/lib/unit/ in official images) of the container. If it’s empty, the script processes certain file types in the container’s /docker-entrypoint.d/ directory:

File Type	Purpose/Action
.pem	Certificate bundles are uploaded under respective names: cert.pem to certificates/cert.
.json	Configuration snippets are uploaded as to the config section of Unit’s configuration.
.sh	Shell scripts that run in the container after the .pem and .json files are uploaded.
Note

The script issues warnings about any other file types in the /docker-entrypoint.d/ directory. Also, your shell scripts must have execute permissions set.
This mechanism allows you to customize your containers at startup, reuse configurations, and automate your workflows, reducing manual effort. To use the feature, add COPY directives for certificate bundles, configuration fragments, and shell scripts to your Dockerfile derived from an official image:

FROM nginx/unit:1.18.0-minimal
COPY ./*.pem  /docker-entrypoint.d/
COPY ./*.json /docker-entrypoint.d/
COPY ./*.sh   /docker-entrypoint.d/
Note

Mind that running Unit even once populates its state directory; this prevents the script from executing, so this script-based initialization must occur before you run Unit within your derived image.
This feature comes in handy if you want to tie Unit to a certain app configuration for later use. For ad-hoc initialization, you can mount a directory with configuration files to a container at startup:

$ docker run -d --mount \
         type=bind,src=/path/to/config/files/,dst=/docker-entrypoint.d/ \
         nginx/unit:latest)
Official Packages

Installing a precompiled Unit binary package is best for most occasions; official binaries are available for:

Amazon Linux, Amazon Linux 2
CentOS 6, 7, 8
Debian 8, 9, 10
Fedora 28, 29, 30, 31
RHEL 6, 7, 8
Ubuntu 16.04, 18.04, 18.10, 19.04, 19.10, 20.04
These include core Unit executables, developer files, and support packages for individual languages.

We also maintain an official Homebrew tap for macOS users.

Note

Unit’s language module for Node.js is available at the npm registry.
Note

For details of packaging custom modules that install alongside the official Unit, see here.
Amazon Linux

Supported architectures: x86-64.

2.0 LTS
To configure Unit repository, create the following file named /etc/yum.repos.d/unit.repo:

[unit]
name=unit repo
baseurl=https://packages.nginx.org/unit/amzn2/$releasever/$basearch/
gpgcheck=0
enabled=1
Install Unit base package and other packages you would like to use:

# yum install unit
# yum install unit-devel unit-go unit-jsc8 unit-perl \
      unit-php unit-python27 unit-python37
AMI
Note

The control socket’s pathname is /var/run/unit/control.sock.
CentOS

8.x, 7.x
Supported architectures: x86-64.

To configure Unit repository, create the following file named /etc/yum.repos.d/unit.repo:

[unit]
name=unit repo
baseurl=https://packages.nginx.org/unit/centos/$releasever/$basearch/
gpgcheck=0
enabled=1
Install Unit base package and other packages you would like to use:

# yum install unit
# yum install unit-devel unit-go unit-jsc8 unit-jsc11 \
      unit-perl unit-php unit-python27 unit-python36
6.x
Note

The control socket’s pathname is /var/run/unit/control.sock.
Debian

Supported architectures: i386, x86-64.

10
Download NGINX’s signing key and add it to apt’s keyring:

# curl -sL https://nginx.org/keys/nginx_signing.key | apt-key add -
This eliminates the ‘packages cannot be authenticated’ warnings during installation.

To configure Unit repository, create the following file named /etc/apt/sources.list.d/unit.list:

deb https://packages.nginx.org/unit/debian/ buster unit
deb-src https://packages.nginx.org/unit/debian/ buster unit
Install Unit base package and other packages you would like to use:

# apt update
# apt install unit
# apt install unit-dev unit-go unit-jsc11 unit-perl \
      unit-php unit-python2.7 unit-python3.7 unit-ruby
9
8
