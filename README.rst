===========
Staticstack
===========
by Jonathan Wong (Dynavisor) 04/21/2015

Staticstack is an Openstack configuration and installation tool based on Devstack primarily focused on running Openstack on FreeBSD.

===================
Goal of Staticstack
===================
The goal of Staticstack is to maintain compatibility with Devstack while allowing users to configure and install Openstack on a machine quickly. Unlike Devstack, the intended audience of Staticstack are cloud administrators who want to setup Openstack quickly on a smaller number of machines. Therefore the main emphasis of Staticstack is to create reproducible versions of Openstack without the stack being updated on a constant basis, which is the intended purpose of Devstack. This doesn't necessarily mean that Staticstack can't be up-to-date. It merely means that users of Staticstack want to retain more control over the particular version or state of any particular project and dependency of Openstack.

Staticstack also includes extensions to Devstack to install Openstack on FreeBSD. Many of the extensions are based on contributions from Semihalf. Staticstack extensions for FreeBSD try to retain the "FreeBSD" conventions for installing libraries and such when possible. Generally speaking, Staticstack works more or less the same on FreeBSD as it does on Linux.

========================
Operation of Staticstack
========================

Fundamentally Staticstack will is basically the same as Devstack. However one must install the Openstack prerequisities before installing Openstack itself. This includes the non-Openstack packages (apache, mysql, etc...) and the relevant python libraries.

To install Openstack using Staticstack:

1. Go to the *installation* subdirectory, and follow the pre-installation instructions for each particular distro.
2. Create a *localrc* file. An example is named *localrc.freebsd.example* in the root directory. Remember to configure passwords, enable disable services, set the proper hostname and HOST_IP, and also the proper network interfaces.
3. Run *stack.sh* in the root directory.
4. In the case of errors, run *unstack.sh* and kill python/python2.7 processes. Rerun *stack.sh* afterwards.

============================
Status of Openstack Services
============================
Nova, Glance, Keystone, and Horizon services should work properly. Currently there is no FreeBSD support for Neutron. The Swift service runs, but has not been thoroughly tested on FreeBSD, and Cinder/ZFS on FreeBSD developement is being worked on at the moment.
