# Platform Caveats

## Cisco IOS

* Cisco IOSv release 15.x does not support unnumbered interfaces. Use Cisco CSR 1000v.
* Cisco CSR 1000v does not support interface MTU lower than 1500 bytes or IP MTU higher than 1500 bytes.

## Cisco Nexus OS

* Nexus OS release 9.3 runs in 6 GB of RAM (*netsim-tools* system default).
* Nexus OS release 10.1 requires 8 GB of RAM and will fail with a cryptic message claiming it's running on unsupported hardware when it doesn't have enough memory.
* Nexus OS release 10.2 requires at least 10 GB of RAM and crashes when ran as an 8 GB VM.
* To change the default amount of memory used by a **nxos** device, set the **defaults.devices.nxos.memory** parameter (in MB)

## Cumulus Linux in ContainerLab

* *containerlab* could run Cumulus Linux as a container or as a micro-VM with *firecracker* (default, requires KVM). To run Cumulus VX as a pure container, add **runtime: docker** parameter to node data.
* *netsim-tools* uses Cumulus VX containers created by Michael Kashin and downloaded from his Docker Hub account. Once Nvidia releases an official container image, change the container name with **defaults.devices.cumulus.clab.image** parameter (or by editing the `topology-defaults.yml` file included with *netsim-tools*).
* The Cumulus VX 4.4.0 Vagrant box for VirtualBox is broken. *netsim-tools* is using Cumulus VX 4.3.0 with *virtualbox* virtualization provider.

## Fortinet FortiOS

* *FortiOS* VM images by default have a 15 day evaluation license. The VM has [limited capabilities](https://docs.fortinet.com/document/fortigate-private-cloud/6.0.0/fortigate-vm-on-kvm/504166/fortigate-vm-virtual-appliance-evaluation-license) without a license file. It will work for 15 days from first boot, at which point you must install a license file or recreate the vagrant box completely from scratch.
* Ansible automation of FortiOS requires the installation of the [FortiOS Ansible Collection 2.1.3 or greater](https://galaxy.ansible.com/fortinet/fortios) and a FortiOS version > 6.0.

## FRR

* *containerlab* FRR containers run FRR release 7.5.0 -- the latest release that survives FRR daemon restart during the initial configuration process.
* *netsim-tools* don't support FRR running in a Linux VM. Use Cumulus Linux instead.

## Generic Linux

*Generic Linux device* is a Linux VM running Ubuntu 20.04 or an Alpine/Python container. To use any other Linux distribution, add **image** attribute with the name of Vagrant box or Docker container to the node data[^1]; the only requirements are working Python environment (to support Ansible playbooks used in **netlab initial** command) and the presence of **ip** command used in initial device configuration.

[^1]: You can also set the **defaults.devices.linux._provider_.image** attribute to change the Vagrant box for all Linux hosts in your lab.

### Host Routing

Generic Linux device is an IP host that does not support IP forwarding or IP routing protocols. It uses static routes set up as follows:

* IPv4 default route points to Vagrant management interface (set by Vagrant/DHCP).
* IPv6 default route points to whichever adjacent device is sending IPv6 Route Advertisement messages (default Linux behavior).
* IPv4 static routes for all IPv4 address pools defined in lab topology point to the first neighbor on the first non-management interface.

**Corollary:** Linux devices SHOULD have a single P2P link to an adjacent network device. If you encounter problems using any other lab topology, please submit a Pull Request fixing it instead of complaining ;)

### LLDP

* LLDP on Generic Linux is started in Ubuntu VMs but not in Alpine containers.

## Mikrotik CHR RouterOS

* LLDP on Mikrotik CHR RouterOS is enabled on all the interfaces.

## Nokia SR Linux
* Only supported on top of *Containerlab*
* Requires the latest Ansible Galaxy collection 'nokia.grpc' and its dependencies to be installed, from the git repo:
```
ansible-galaxy collection install git+https://github.com/nokia/ansible-networking-collections.git#/grpc/
python3 -m pip install grpcio protobuf
```

## Nokia SR OS
* Only supported on top of *Containerlab*, using VRNetlab (VM running inside container)
* Requires the latest Ansible Galaxy collection 'nokia.grpc' and its dependencies to be installed, from the git repo:
```
ansible-galaxy collection install git+https://github.com/nokia/ansible-networking-collections.git#/grpc/
python3 -m pip install grpcio protobuf
```
* OpenConfig support depends on a [pending PR](https://github.com/nokia/ansible-networking-collections/pull/21)
