# Efficient IP DDI monitoring

## Introduction

This is a Zenpack for [Efficient iP DDI](http://www.efficientip.com/solutions/smart-ddi/) monitoring. This Zenpack is developed using the [ZenPack SDK](https://www.zenoss.com/product/zenpacks/zenpacklib) on Zenoss 4.x and might as well run under Zenoss 5.x.

## Supported devices

I only had models SDS-550, SDS-1100 and SDS-2200 with OS version 6.x to test my zenpack. Feel free to try it with other models, and report if it works or break. I'm happy to assist you to get it working with other models.

# Features

- hardware monitoring: done by a distinct Zenpack (ZenPacks.community.iDRAC)
- OS monitoring:
    - CPU, RAM, swap
    - interface traffic
    - filesystem usage
- application monitoring:
    - DHCP statistics (ack, nack, offer, inform, decline, release, request, discover)
    - DNS statistics (queries, answers, requests, transfer, DNSSEC)
- service monitoring: done elsewhere

# Release notes

- 20.03.2017 : 1.0.0 : initial version

# Installation

## Pre-requisites : Python packages

The installation of the required PyYAML should happen automatically. If not, use this :

```
easy_install PyYAML
```

## Device class

- the zenpack will automatically create the device class `/Server/EfficientIP` if not present.

## Zenpack

- install the Zenpack:

```
zenpack --install ZenPacks.community.EfficientIPDDI
zopectl restart; zenhub restart
```

- a full `zenoss restart` is probably better.

## set the Python class of the existing devices

This is a one-time operation that might be needed for devices that were present before the Zenpack installation (the devices added after installation get the correct Python class automatically). The symptom to decide if you need this: `WARNING zen.ApplyDataMap: no relationship:XXX found on:YYY` in zenhub.log.

*Warning*: this loops must be repeated until no device get moved anymore. Not sure why, maybe some glitch of Zenoss 4.x.

`zendmd`

```
for d in dmd.Devices.Server.EfficientIP.getSubDevicesGen():
    devname = d.getId()
    print('checking %s' % devname)
    if d.__class__.__name__ != 'EfficientIPDDIDevice':
        dmd.Devices.Server.EfficientIP.moveDevices('/Server/EfficientIP', devname)
        commit()
        print('class of %s set to EfficientIPDDIDevice' % devname)

```

## Post-installation

The template binding must be done manually if it fails at installation time:

- go to /Server/EfficientIP
- Details
- Gear (lower left edge of browser window)
- Bind templates
  - Device (/server)
  - DHCPstats (/Server/EfficientIP)
  - DNSstats (/Server/EfficientIP)

# Development notes

## Vagrant development environment

Using Vagrant in Virtualbox, use `vagrant up` to create and start a Zenoss to further develop this Zenpack.

## ZenPack SDK

This Zenpack depends on the presence of the ZenPack SDK that can be found at the [ZenPack SDK site](https://www.zenoss.com/product/zenpacks/zenpacklib).

The development happened with version 2.0.4 of 21.2.2017

```
easy_install PyYAML

cd /tmp
wget http://wiki.zenoss.org/download/zenpacks/ZenPacks.zenoss.ZenPackLib/2.0.4/ZenPacks.zenoss.ZenPackLib-2.0.4.egg
zenpack --install ZenPacks.zenoss.ZenPackLib-2.0.4.egg
zenoss restart
zenpacklib --version
# brings up 2.0.3, maybe the version bump didn't happen when they released 2.04
```

Debug mode for zenpack installation:

`zenpack -v 10 --link --install ZenPacks.community.EfficientIPDDI`

Build a ZenPack egg from source:

- cd <ZENPACK_SOURCE>
- python setup.py bdist_egg

## filesystem mapping

In the VM, /tmp/vagrant is mapped to the Zenpack source, you can use these commands to install it:

    zenpack --link --install ZenPacks.community.EfficientIPDDI
    zopectl restart; zenhub restart

# Known issues

- the template auto-bind for /Server/EfficientIP is not automatic. Work-around: do it manually.

# To-do

- identify why the template binding for DNS and DHCP doesn't work.
  - Is this a bug fixed by a zenup ?
  - to bind manually, editing the `zDeviceTemplates` as line doesn't work, it's a list that needs to be edited using the `bind template` gear function.

# Resources

- [ZenPack SDK](https://www.zenoss.com/product/zenpacks/zenpacklib)
