title: Docker, The new hotness and why you want it
author:
  name: Matthew Nicholson
  twitter: sjoeboo
  url: http://github.com/sjoeboo
output: index.html
controls: true
theme: jdan/cleaver-retro

--
# Docker
## The new hotness and why you want it.
--
### Who am I?
--
### Hi, I'm Matt Nicholson
* 3rd week in HMS RC
* Broad Institute + FAS RC "alum"
* Devops Sysadmin
--
### Docker, what IS it?
* A container runtime engine
* AKA, a bundle of all the things your application needs
* A package, but WAY better.
--
### A History of shipping
* Ships carry many types if $THINGS
* All shapes and sizes, wet, dry, etc
* How to pack them all, efficently?
* Days were spent planning and loading ships
--
### History of shipping part 2
* Enter Container ships!
* More importantly, containers!
* Standard Unit, shape and size.
* holds $things. Who cares what.
* Ships carry containers. only containers. many containers.
--
### So why not software?
--
### How nice? Very.
* Pick a (linux) OS.
* Install/run docker.
* Run containers.
* Done.
--
* Your OS, becomes very generic.
* Your application becomes self contained, very portable.
* Want a RHEL specific app but run Ubuntu? Container!
* Local Development!
--
### More uses
* Clean, fast to create build environments!
* Package data with code for reproducible jobs/runtimes
--
### Docker vs Vms
* Good: Don't run the full OS, no hardware to virtualize
* Good: Fast. No bootup/init.
* Bad(ish): No real init system (typically). Runs 1 process.
* Bad: Currently requires root access/privs to launch. User daemon+containers coming soon (kernel changes)
--
### Tech details
* Container really is a filesystem layout/image
* Containers are made of multiple layers, all piled up.
* Relies on the linux kernel namespace functions.
* Images = Filesystem layers
* Containers = Running/runable instances of a mash of images.
--
### Docker Hub
* public+private container registry/hosting.
* Many official containers (apache, redis, ruby, nginx, etc)
* Think github for containers!
--
### DOCKERFILE: Basics
* FROM centos:7
  * Would base container on CentOS 7, pulling from a registry (default: docekr hub)
  * All layers get cached. Other containers based on this only pull needed parts.
* RUN <some command>
  * Runs #whatever_you_want. (run during build!)
--
### DOCKERFILE: More basics
  * EXPOSE <port>
    * Sets up container to have a port exposed to the container host
  * VOLUME </path>
    * Creates mount point for data to be shared with hose at runtime.
  * CMD <command>
    * Defines command to run at runtime (not used in build!)
--
### Example Dockerfile
```
FROM sjoeboo/baseimage:latest
MAINTAINER mattthew.a.nicholson@gmail.com
EXPOSE 80
EXPOSE 443
RUN ln -s /opt/puppetlabs/bin/puppet /usr/local/bin/puppet
RUN mkdir /puppet/
ADD Puppetfile /puppet/Puppetfile
ADD default.pp /puppet/default.pp
ADD files/ /puppet/files
ADD templates/ puppet/templates
ADD hiera.yaml /puppet/hiera.yaml
ADD hiera/  /puppet/hiera/
WORKDIR /puppet/
RUN /usr/local/bin/librarian-puppet install --verbose
RUN /opt/puppetlabs/bin/puppet apply --modulepath=/puppet/modules/ --hiera_config=/puppet/hiera.yaml -v /puppet/default.pp
CMD /usr/bin/supervisord -n -c /etc/supervisord.conf
```
--
### Advanced features
* Link containers
* docker-compose to bring up multiple containers together
* Cluster/schedulers (Meos, Docker Swarm, etc)
--
### Live demo (ugh oh!)
--
### Tips
* Keep it clean:
  * Each "RUN" is a layer. smush things together when you can. Clean up at the end of each RUN!
* Supervisord is your friend
  * run supervisord. have it run your $things, be it 1, or many.
  * it can restart things 0->forever times. keeps containers from dying.
--
### Tips pt.2
  * Use Version control.
  * Use your config management to BUILD your containers, and to run them, but not IN them.
