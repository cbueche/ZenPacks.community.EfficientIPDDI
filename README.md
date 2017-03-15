# Efficient iP DDI monitoring

## Introduction

This is a Zenpack for [Efficient iP DDI](http://www.efficientip.com/solutions/smart-ddi/) monitoring. This Zenpack is developed using the [ZenPack SDK](https://www.zenoss.com/product/zenpacks/zenpacklib) on Zenoss 4.x and might as well run under Zenoss 5.x.

## Supported devices

I only had models SDS-550, SDS-1100 and SDS-2200 with OS version 6.x to test my zenpack. Feel free to try it with other models, and report if it works or break. I'm happy to assist you to get it working with other models.

# Features

- hardware monitoring:
    - tbd
- OS monitoring:
    - tbd
- application monitoring:
    - tbd

# Release notes

- xx.yy.2017 : 1.0.0 : initial version

# Installation

## Pre-requisites : Python packages

The installation of the required PyYAML should happen automatically. If not, use this :

```
easy_install PyYAML
```

## Device class

- create device class `FIXME`

## Zenpack

- install the Zenpack:

```
zenpack --install ZenPacks.community.EfficientIPDDI
zopectl restart; zenhub restart
```

- a full `zenoss restart` is probably better.

## set the Python class of the existing devices

This is a one-time operation that is needed for devices that were present before the Zenpack installation (the devices added after installation get the correct Python class automatically). The symptom to decide if you need this: `WARNING zen.ApplyDataMap: no relationship:XXX found on:YYY` in zenhub.log.

*Warning*: this loops must be repeated until no device get moved anymore. Not sure why, maybe some glitch of Zenoss 4.x.

`zendmd`

```
for d in dmd.Devices.FIXME.getSubDevicesGen():
    devname = d.getId()
    print('checking %s' % devname)
    if d.__class__.__name__ != 'FIXMEDevice':
        dmd.Devices.FIXME.moveDevices('/FIXME', devname)
        commit()
        print('class of %s set to FIXMEDevice' % devname)

```

## Post-installation

### MIB load

If you want to automatically map the device models and the SNMP traps, you need to load two MIB's:

```
cd MIB
cp FIXME-*.mib $ZENHOME/share/mibs/site
cd $ZENHOME/share/mibs/site
zenmib run -v 10
zentrap restart
```

The go to Zenoss / advanced / MIBs. See if MIBs are available.

### modeler plugins for /FIXME

The plugins are automatically assigned during the Zenpack installation:

- zenoss.snmp.NewDeviceMap
- zenoss.snmp.DeviceMap
- zenoss.snmp.InterfaceMap
- zenoss.snmp.InterfaceAliasMap
- zenoss.snmp.RouteMap
- ... FIXME

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

## filesystem mapping

In the VM, /tmp/vagrant is mapped to the Zenpack source, you can use these commands to install it:

    zenpack --link --install ZenPacks.community.EfficientIPDDI
    zopectl restart; zenhub restart

# Known issues

- n/a

# To-do

- n/a

# Resources

- [ZenPack SDK](https://www.zenoss.com/product/zenpacks/zenpacklib)
