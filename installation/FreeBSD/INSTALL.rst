=================================================
Installing Openstack on FreeBSD using Staticstack
=================================================
by Jonathan Wong (Dynavisor) 04/21/2015

This guide explains how to install Openstack after a fresh install of FreeBSD 10.1 64-bit on a machine with an Intel chipset.

=====================
Install pkg and  sudo
=====================

For simplicity we will be installing *pkg* so we can install other packages quickly, and most importantly install *sudo*. There are many ways to do this, but in our case, we want to tightly control the version of each of our packages so we chose to boostrap pkg with a particular version of the source code (pkg-1.3.8.tar.xz).

#. Log in as *root*
#. Place pkg-1.3.8.tar.xz in usr/ports/distfiles/
#. Install pkg from ports::

     cd /usr/ports/ports-mgmt/pkg
     make
     make install

#. Next we install sudo (sudo-1.8.13.txz) which we create beforehand using (pkg create sudo)::

     pkg add sudo-1.8.13.txz

==============================================================
Install Openstack package dependencies and python dependencies
==============================================================

#. Log in as $STACK_USER
#. Obtain the list of freebsd packages in (packages.txt). Install them into ports::

     sudo pkg add *.txz

#. Some packages like *bash* require a little extra work::
     sudo sh -c "printf 'fdesc /dev/fd fdescfs rw 0 0\n' >> /etc/fstab"
     sudo sh -c "printf 'proc /proc procfs rw 0 0\n' >> /etc/fstab"

     sudo mount -t fdescfs fdesc /dev/fd
     sudo mount -t procfs proc /proc
     
#. Next download the list of python packages from pip.txt (Refer to PIP Caching Section). Also download and place all of the necessary openstack repositories in /opt/stack. Necessary openstack repositories are:

   *cinder
   *django_openstack_auth
   *glance
   *heat
   *horizon
   *keystone
   *noVNC
   *nova
   *pbr
   *python-cinderclient
   *python-glanceclient
   *python-heatclient
   *python-keystoneclient
   *python-neutronclient
   *python-novaclient
   *python-openstackclient
   *requirements
   *swift
   *taskflow
   *tempest

#. Next install the python packages. This can be done in the following way (assuming your python cached packages are installed in ${HOME}/.cache/).::
     sudo pip install --no-index --find-links=file://${HOME}/.cache six==1.9.0 
     sudo pip install -e /opt/stack/pbr
     sudo pip install --no-index --find-links=file://${HOME}/.cache/ -r pip_freeze.txt
     sudo pip install -e /opt/stack/pbr
     sudo pip install --no-index --find-links=file://${HOME}/.cache pyOpenSSL==0.14

     sudo pip install -e /opt/stack/python-glanceclient
     sudo pip install -e /opt/stack/python-novaclient
     sudo pip install -e /opt/stack/python-neutronclient
     sudo pip install -e /opt/stack/python-openstackclient
     sudo pip install -e /opt/stack/glance

     sudo pip install -e /opt/stack/nova
     sudo pip install -e /opt/stack/keystone
     sudo pip install -e /opt/stack/heat

     sudo pip install --no-index --find-links=file://${HOME}/.cache oslo.config==1.9.3
     sudo pip install --no-index --find-links=file://${HOME}/.cache websockify==0.5.1

You may run across some error messages, but installing in the order above should ensure that the proper versions of all of the packages described in pip.txt are installed properly.

#. Next we need to modify rc.conf to enable FreeBSD services we use.::

    sudo sh -c "printf 'libvirtd_enable=\"YES\"\n' >> /etc/rc.conf"
    sudo sh -c "printf 'firewall_enable=\"YES\"\n' >> /etc/rc.conf"
    sudo sh -c "printf 'firewall_type=\"open\"\n' >> /etc/rc.conf"
    sudo sh -c "printf 'firewall_script=\"/etc/ipfw.rules\"\n' >> /etc/rc.conf"
    sudo sh -c "printf 'firewall_logging=\"YES\"\n' >> /etc/rc.conf"
    sudo sh -c "printf 'gateway_enable=\"YES\"\n' >> /etc/rc.conf"
    sudo sh -c "printf 'natd_enable=\"YES\"\n' >> /etc/rc.conf"
    sudo sh -c "printf 'natd_interface=\"bridge0\"\n' >> /etc/rc.conf"
    sudo sh -c "printf 'natd_flags=\"-dynamic -m\"\n' >> /etc/rc.conf"

#. Lastly we want to install a placeholder for firewall rules.::
     sudo cp ipfw.rules /etc/ipfw.rules

===================
Install Staticstack
===================

Follow the Staticstack installation instructions.

=======================
Post-Installation Setup
=======================
After Staticstack is finished installing Openstack properly we need to install our automated startup scripts, such that Openstack will start on boot.

#. All we need to do is copy over the provided rc scripts::
     sudo mv startup-scripts/rc.d/* /usr/local/etc/rc.d/
     sudo reboot

At this point Openstack should be installed properly.

===========
PIP Caching
===========
To build your own pip packages cache to roll out with Staticstack you can do the following::
  mkdir -p $HOME/.cache
  sudo pip install --download $HOME/.cache -r pip.txt
  
