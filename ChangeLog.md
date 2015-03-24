#summary Release ChangeLog

# Ostinato Change Log #

## 2014-07-07 version 0.6 ##
  * Rearchitected/refactored the protocol builder framework to remove GUI dependency from Drone
  * Added Python bindings for scripting support
  * Multiple clients can now simultaneously connect to the same Drone instance ([issue 22](https://code.google.com/p/ostinato/issues/detail?id=22))
  * Clients now need to check for version compatibility with Drone at startup
  * Bugfixes
    * [Issue 107](https://code.google.com/p/ostinato/issues/detail?id=107) - IGMPv3/MLDv2 Query missing Group Address Field
    * Start/Stop Transmit/Capture RPCs should return only after the invoked operation is complete
    * Port names in the port stats window are incorrect

## 2012-08-01 version 0.5.1 ##
  * Link state and improved statistics support for Linux, BSD and Mac OSX
  * Bugfixes
    * Fixed long inter-packet delay bug in interleaved mode due to which stream transmission could not be stopped  ([Issue 60](https://code.google.com/p/ostinato/issues/detail?id=60), [Issue 72](https://code.google.com/p/ostinato/issues/detail?id=72), [Issue 74](https://code.google.com/p/ostinato/issues/detail?id=74))
    * Fixed long inter-packet delay on some Win32 platforms ([Issue 66](https://code.google.com/p/ostinato/issues/detail?id=66))
    * Fixed crash at init when pcap wasn't able to open an interface ([Issue 64](https://code.google.com/p/ostinato/issues/detail?id=64))
    * Fixed incorrect TCP checksum when it is overridden ([Issue 58](https://code.google.com/p/ostinato/issues/detail?id=58))

## 2011-10-25 version 0.5 ##
  * Improved maximum traffic generation rates (pps); Linux - upto 25x; Windows - upto 60x
  * Reduced time taken to "apply" changes to stream(s)
  * Added support for "Pkts/Sec" and "Bytes/Sec" rate stats on Linux
  * Added support for transmit rates less than 1 pps
  * Added support for nanosecond resolution while calculating inter-packet gaps
  * Added support for average/aggregate port rate
  * Added a new "Interleaved Streams" transmission mode
  * Integrated newer version of QHexEdit widget - HexDump protocol edit now supports copy-paste and undo-redo
  * Major bugfix: (Windows Only) Tx Pkts were looped back to Rx on same port

## 2011-05-10 version 0.4.1 ##
  * Bugfixes
    * Fixed "Save As PCAP" for Linux distros where file was being saved in Ostinato format rather than PCAP
    * Fixed compilation problem with protobuf 2.4.0 and above

## 2011-04-19 version 0.4 ##
  * PCAP/PDML file import/export
  * Jumbo Frames support upto 16K (needs Hardware support)
  * Auto-reconnect for portgroups
  * Usability enhancements
  * Bugfixes
    * Fixed drone crash when NIC doesn't support promiscuous mode
    * Fixed ostinato crash when opening a file saved by a newer version
    * Others

## 2010-11-11 version 0.3 ##
  * Added IGMP (all versions v1, v2, v3)
  * Added MLD (all versions v1, v2)
  * Added HexDump (user specified content)
  * Several bugfixes

## 2010-08-12 version 0.2 ##
  * Save/Restore streams to/from disk

## 2010-06-29 version 0.1.1 ##
  * New platforms
    * BSD
    * Mac OS X
  * Protocols
    * Added IPv6
    * Added IP tunneling (IP-in-IP) - 6over4, 4over6, 6over6, 4over4
    * Added Text protocol - useful for HTTP, SIP, NNTP, RTSP etc.
    * Added ICMPv6
    * Preflight check for truncated frames
  * Build Framework
    * make install support
    * Qt Creator and other IDE support
  * Other
    * Improved GUI handling in the Port Stats Filter Dialog
  * Documentation
    * Improved Protocol Builder documentation (class AbstractProtocol)
  * Bugfixes
    * Invalid TCP/UDP checksums caused by invalid pseudo-IP checksum
    * Assert failure when closing Ostinato with the port list expanded
    * Crash in Port Stats Filter Dialog while removing selected rows
    * Fixes for 64bit compilation and Qt4.6

## 2010-04-11 version 0.1 ##
  * First public release