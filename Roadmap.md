#summary Feature roadmap and wishlist

**This document is a Work-in-Progress**

If you would like to propose a new feature, please discuss on the mailing list `ostinato at googlegroups dot com` or file a new issue in the issue tracker.

# Planned Releases #
## ~~Release 0.6~~ (DONE) ##
~~Target Date: May/June 2014~~ (Released on July 7, 2014)
  * ~~Rearchitect/Refactor to remove GUI dependency from Drone ([issue 34](https://code.google.com/p/ostinato/issues/detail?id=34))~~
  * ~~CLI/Scripting/Automation Support ([issue 11](https://code.google.com/p/ostinato/issues/detail?id=11))~~
  * ~~IGMPv3 Query missing Group Address Field ([issue 107](https://code.google.com/p/ostinato/issues/detail?id=107))~~
  * ~~Multiple clients connecting simultaneously to a drone ([issue 22](https://code.google.com/p/ostinato/issues/detail?id=22))~~
  * ~~Error Statistics and Link State ([issue 28](https://code.google.com/p/ostinato/issues/detail?id=28))~~

## ~~Release 0.5~~ (DONE) ##
~~Target Date: August 2011~~ (Released on Oct 25, 2011)
  * ~~Inter-mix streams ([issue 12](https://code.google.com/p/ostinato/issues/detail?id=12))~~
  * ~~Nano-second granularity ([issue 30](https://code.google.com/p/ostinato/issues/detail?id=30))~~
  * ~~Improve maximum send rate (pps/bps)~~ ([issue 33](https://code.google.com/p/ostinato/issues/detail?id=33), [issue 39](https://code.google.com/p/ostinato/issues/detail?id=39))
  * ~~Change algo to reduce time taken for "apply"~~ ([issue 35](https://code.google.com/p/ostinato/issues/detail?id=35))
  * ~~Configure packet rate for port instead of per stream ([issue 41](https://code.google.com/p/ostinato/issues/detail?id=41))~~
  * ~~Inter-packet gap in seconds ([issue 42](https://code.google.com/p/ostinato/issues/detail?id=42))~~
  * ~~Windows only: Transmit traffic is also counted in received statistics([issue 46](https://code.google.com/p/ostinato/issues/detail?id=46))~~
  * ~~New version of [QHexEdit](http://code.google.com/p/qhexedit2) widget supporting copy-paste~~

## ~~Release 0.4~~ (DONE) ##
  * ~~Target Date: April 2011~~ (Released on Apr 19, 2011)
  * ~~User Interface related~~
    * ~~Main Window and Stream Configuration Dialog - save/restore geometry (size)~~
    * ~~Auto reconnect to port group/drone~~
    * ~~If port group has zero ports, offer troubleshooting tips to the user~~
  * ~~Pcap/Pdml file format support~~
  * ~~Jumbo frames support ([issue 27](https://code.google.com/p/ostinato/issues/detail?id=27))~~

## ~~Release 0.3~~ (DONE) ##
  * ~~Target Date: Nov 2010~~ (Released on Nov 11, 2010)
  * ~~Protocols~~
    * ~~Custom Hexdump~~
    * ~~IGMP~~
    * ~~MLD~~

## ~~Release 0.2~~ (DONE) ##
  * ~~Target Date: July 2010~~ (Released on August 12, 2010)
  * ~~Protocols~~
    * ~~IPv6~~
    * ~~IP Tunneling aka IP-in-IP (4over4, 6over6, 4over6, 6over6)~~
    * ~~ICMPv6~~
    * ~~Text Protocol~~
  * ~~Save/Restore streams to/from disk~~

## ~~Release 0.1~~ (DONE) ##
  * ~~Target Date: April 2010~~ (Released on April 11, 2010)
  * ~~First public release~~

# Near Future #
  * Protocols
    * DHCP
    * IPv4 Options
    * IPv6 Extension Headers
    * TCP Options ([issue 7](https://code.google.com/p/ostinato/issues/detail?id=7))
    * User Defined Fields ([issue 31](https://code.google.com/p/ostinato/issues/detail?id=31))
  * Goto _specified_ stream (not just the first stream) ([issue 13](https://code.google.com/p/ostinato/issues/detail?id=13))
  * ~~_Link Status_ for non-Windows platforms~~ (DONE)
  * _Exclusive Port Control_ for non-Windows platforms ([issue 36](https://code.google.com/p/ostinato/issues/detail?id=36))
  * ~~_Rate Stats_ for BSD/OSX~~ (DONE)
  * _Random Burst Rate/Size_ Transmission Mode ([issue 32](https://code.google.com/p/ostinato/issues/detail?id=32))
  * ~~Allow multiple Ostinato clients to connect to one drone server ([issue 22](https://code.google.com/p/ostinato/issues/detail?id=22))~~ (DONE)
  * Plugin architecture for drone to support non-pcap NIC interface - useful for PF\_RING/DNA, accelerated/specialized NICs like Napatech, Endace, Turbocap etc.
  * ~~"vDrone" - drone packaged as a virtual appliance~~ (DONE)
  * Refactoring
    * Cleaner separation of client side domain/model/view objects
    * Privilege separation of drone for a better security architecture
    * ~~Remove drone dependency on GUI/X ([issue 34](https://code.google.com/p/ostinato/issues/detail?id=34))~~ (DONE)
  * More accurate timing on Mac OS X

# Distant Future #
  * State-full protocols/Connection Oriented operation
    * TCP
    * Neighbour Resolution ([issue 24](https://code.google.com/p/ostinato/issues/detail?id=24), [issue 29](https://code.google.com/p/ostinato/issues/detail?id=29))