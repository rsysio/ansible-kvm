# ansible-kvm
ansible install kvm

[notes](http://notes.rsys.io/post/151509090518/my-kvm-setup)

[github](https://github.com/rsysio/ansible-kvm)

## Goal

Install and configure KVM.

## Requirements

* Ubuntu 16.04

## tasks

    install.yml

#### packages

* *virtinst* - installs `virt-install` command to create VMs
* *libosinfo-bin* - this is to list `--os-variant` options for `virt-install`
* *genisoimage* - to generate iso images with user-data and meta-data (can do cloud-init)
* *libvirt-dev* - i need this to interact with libvirt from [go](https://github.com/rgbkrk/libvirt-go)

network:

    network.yml
    dnsmasq.yml
    iptables.yml

The network config is trying to capture the steps from [jamielinux.com](https://jamielinux.com/docs/libvirt-networking-handbook/custom-nat-based-network.html) 

    download.yml

This will download an Ubuntu image to be used as a template.


# Create VM

## Create user-data

Create the files `meta-data` and `user-data` with the following

    VM_NAME=myVM

    SSH_KEY=$(cat ~/.ssh/id_rsa.pub)
    cat  meta-data
    instance-id: ${VM_NAME}
    local-hostname: ${VM_NAME}
    public-keys:
      - ${SSH_KEY}
    EOT

    cat  user-data
    #cloud-config
    packages:
      - language-pack-en
    hostname: ${VM_NAME}
    fqdn: ${VM_NAME}.rsys.io
    manage_etc_hosts: true
    EOT

Create user-data iso image

    genisoimage -o config.iso -V cidata -r -J meta-data user-data

## Images

To check the image info:

    qemu-img info xenial.img

Copy and resize the image:

    cp xenial.img ~/.vms/myVM.qcow2

Resize image

    qemu-img resize ~/.vms/myVM.qcow2 +20

## Create VM

    virt-install \
    	--name myVM \
    	--vcpus=2 \
    	--ram 2048 \
     	--accelerate \
    	--network bridge:virbr10,mac=52:54:00:b7:bc:b9 \
    	--disk path=~/.vms/myVM.qcow2,format=qcow2 \
    	--import \
    	--disk path=config.iso,device=cdrom \
     	--os-type=linux \
    	--os-variant=ubuntu16.04 \
    	--nographics \
    	--noautoconsole

## Check

Check your VM running

    virsh list --all

Check dnsmasq resevations

    cat /var/lib/libvirt/dnsmasq/virbr10.leases

At this point I can ssh into the VM
I can also do `virsh console myVM` to see the console output
