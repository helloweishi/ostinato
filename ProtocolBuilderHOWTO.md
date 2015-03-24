#summary Writing a Protocol Builder

Ostinato provides a framework to write protocol builders. Typical effort required to add a new protocol using the provided framework is around 2-3 days; your mileage may vary depending on the protocol and your familiarity with the Ostinato protocol builder framework.

If you intend to contribute your changes back to the project (**you should!**), please read ContributingCodeToOstinato.

You will need to have some familiarity with [Qt](http://qt-project.org/) and [Protocol Buffers](http://code.google.com/p/protobuf/). Even if you are not familiar with the above, copy-paste from an existing protocol's source code may take you forward quite a bit - but if you get stuck, you will have to go dig into the `Qt/ProtoBuf` documentation.

# The Protocol Parts #
A protocol is broken up into 3 classes
  * The core protocol
  * The configuration widget for the protocol
  * The pdml to ostinato decoder (for importing PCAP files)

## Core Protocol ##
The core protocol inherits from the [AbstractProtocol](http://code.google.com/p/ostinato/source/browse/common/abstractprotocol.h) class. The `AbstractProtocol` class defines the interface for a protocol and also provides sensible default implementation for several methods. The fields of a protocol are defined in [protocol buffers message format](https://developers.google.com/protocol-buffers/docs/proto) in a .proto file. The protoc compiler then generates a C++ class from this .proto file. This generated class is encapsulated by the core protocol. The core protocol defines and exports an enum for each field and provides get/set accessor functions for the field data. For each field, different attributes can be requested such as field name, field value, field value as text or field encoded for transmission. Read the `AbstractProtocol`'s doxygen [documentation](http://wiki.ostinato.googlecode.com/hg/html/class_abstract_protocol.html) for more information.

## Protocol Configuration Widget ##
The configuration widget for the protocol inherits from the [AbstractProtocolConfigForm](http://code.google.com/p/ostinato/source/browse/common/abstractprotocolconfig.h) class. The `AbstractProtocolConfigForm` class defines the interface for a protocol widget. A protocol widget provides two key methods - `loadWidget()` which uses the core protocol's !fieldData() method to retrieve field values to populate the widget and `storeWidget()` which uses the core protocol's !setFieldData() method to transfer the user configured field values from the widget to the core protocol. Read the `AbstractProtocolConfigForm`'s doxygen [documentation](http://wiki.ostinato.googlecode.com/hg/html/class_abstract_protocol_config_form.html)

## Pdml Helper Protocol ##
The pdml protocol's purpose is to decode the protocol fields when importing a PCAP file. Befre we look at what the pdml protocol does, a brief note on how Ostinato imports PCAP files.

A PCAP file contains raw packets as they are sent on the wire alongwith some minimal meta-data such as the time stamp and packet length. Each packet from the PCAP file needs to 'decoded' to identify the protocols and protocol fields contained within - this is what [Wireshark](http://www.wireshark.org/) does when you open a PCAP file with it. To avoid reinventing the wheel, we take advantage of this capability. We use tshark (terminal-version of wireshark) to decode the PCAP file and generate a PDML file. [PDML](http://www.nbee.org/doku.php?id=netpdl:pdml_specification) is a XML representation of the decoded packet, protocol and protocol fields. We parse the XML to populate Ostinato streams/protocols in protobuf format.

A packet in a PDML schema looks like this -
```
<packet>
	<proto>
		<field/>
		<field/>
		<field/>
	</proto>
</packet>
```

The PdmlReader class is responsible for parsing the PDML and as it encounters each protocol, it checks to see if there is any PdmlProtocol registered that matches the protocol's name attribute. If it finds one, it creates an instance of that PdmlProtocol to help decode the protocol's fields and populate the protobuf protocol.

Here's a sample UDP PDML -

```
  <proto name="udp" showname="User Datagram Protocol, Src Port: http (80), Dst Port: http (80)" size="8" pos="34">
    <field name="udp.srcport" showname="Source port: http (80)" size="2" pos="34" show="80" value="0050"/>
    <field name="udp.dstport" showname="Destination port: http (80)" size="2" pos="36" show="80" value="0050"/>
    <field name="udp.port" showname="Source or Destination Port: 80" hide="yes" size="2" pos="34" show="80" value="0050"/>
    <field name="udp.port" showname="Source or Destination Port: 80" hide="yes" size="2" pos="36" show="80" value="0050"/>
    <field name="udp.length" showname="Length: 26" size="2" pos="38" show="26" value="001a"/>
    <field name="udp.checksum_coverage" showname="Checksum coverage: 26" hide="yes" size="0" pos="38" show="26"/>
    <field name="udp.checksum" showname="Checksum: 0xef06 [correct]" size="2" pos="40" show="0xef06" value="ef06">
      <field name="udp.checksum_good" showname="Good Checksum: True" size="2" pos="40" show="1" value="ef06"/>
      <field name="udp.checksum_bad" showname="Bad Checksum: False" size="2" pos="40" show="0" value="ef06"/>
    </field>
  </proto>
```

A PdmlProtocol subclass should register itself with PdmlReader using the proto name attribute as the key. For most of the fields in the protocol, it will be a direct one-to-one mapping between the field name and the protobuf field - the subclass specifies this mapping in its constructor by populating the inherited protected member fieldMap_. For all these "known" fields, the base class will automatically populate the protobuf. For any "unknown" fields, the !unknownFieldHandler() method is invoked where the subclass can do any special magic as required to handle that field. The !preProtocolHandler() and !postProtocolHandler() are invoked at the start and end of a protocol's handling and the subclass may provide implementations of these virtual methods if required._

If you are a Wireshark user, the protocol and field 'name' attributes are same as what you use in Wireshark's display filters.

Before implementing a PdmlProtocol subclass, it is highly instructive and will make it much easier if you do the following -
  * Procure a PCAP containing your protocol, generate the PDML and study it.
    * Wireshark wiki has some [sample captures](http://wiki.wireshark.org/SampleCaptures). You can search the internet for others as well.
    * To generate the PDML from PCAP, you can run the command - ` tshark -r <pcap-file> -Tpdml `
  * Look at the Wireshark [display filter reference](http://www.wireshark.org/docs/dfref/) for your protocol

Some tips related to PDML handling -
  * The same field may be present under multiple names in the PDML. Choose the field name which is unique in such a case
  * Some fields may be meta fields such as if a checksum is good or bad - they are not fields within the packet as such. Such fields can be ignored
  * Fields which are not available as a display filter will have no 'name' attribute, so will be classified as "unknown". In such cases, check to see if there are other attributes such as 'show', 'showname' etc. that you can use to identify the field. e.g. there is no filter defined for TCP Ack Number - so we use the 'show' attribute to identify it.
  * For fields not supported by Ostinato, use a HexDump protocol to hold the requisite number of bytes. See how PdmlTcpProtocol maps tcp.options into a hexdump
  * Use the pre/post protocol handler methods to populate any protobuf meta-fields.

## Recommendation ##
It is instructive to read through the implementation of an already supported protocol for all 3 classes. You can choose which supported protocol to read through based on its similarities with the protocol you are implementing (e.g. to implement SCTP you can look at the TCP implementation) or based on the fields of your protocol (e.g. checksum fields or bit fields).

All 3 classes - core protocol, config and pdml also provide !createInstance() factory methods that are registered with their respective factories viz. ProtocolManager, ProtocolWidgetFactory and PdmlReader.

# Suggested Workflow #
A sample protocol is provided which can be modifed suitably to add support for a new protocol.

For a protocol called 'ABCD', do the following -
  1. Create a copy of all the `common/sample*.*` files and rename them as `common/abcd*.*`
  1. Open `abcd.ui` in 'Qt Designer' or 'Qt Creator' and design your protocol's configuration widget as required. Rename the form widget as 'Abcd' (note the Title Case)
  1. Edit `abcd.proto`
    * rename the 'Sample' message to 'Abcd' (note the Title Case)
    * add the protocol fields to the message (all protocol fields should be qualified as optional)
    * you may optionally define default values for one or more protocol fields if they make sense (you can rename and reuse the existing sample fields)
    * after defining your protocol's message, extend the 'Protocol' message by adding your 'Abcd' message as an optional nested message - choose a free field number
    * add the same number to enum k (part of 'Protocol' message) in `common/protocol.proto`
  1. Open `abcd.h` , `abcd.cpp`, `abcdconfig.h` and `abcdconfig.cpp` and do the following global search-replace -
    * replace 'Sample' with 'Abcd'
    * replace 'sample' with 'abcd'
    * replace 'SAMPLE' with 'ABCD'
> > (Note - preserve the case)
  1. Re-edit `abcd.h`
    * edit the ASCII diagram showing the packet format for your protocol
    * edit the `enum abcdfield` to add your protocol's frame fields and meta fields, if any, in that order
    * ensure that you retain the item `abcd_fieldCount` as the last item of the enum
  1. Edit `abcd.cpp`
    * follow the TODO instruction comments in the file to add protocol specific code
  1. Edit `common/protocolmanager.cpp` and add a registration for the abcd protocol in the ProtocolManager's constructor (you will also need to `#include abcd.h`)
  1. Add the `abcd.*` files to `ostproto.pro` (protocols are ordered from lower to higher layers - insert your protocol to retain the order)
  1. Edit `abcdconfig.h`
    * add any private methods or slots, if required
  1. Edit `abcdconfig.cpp`
    * follow the TODO instruction comments in the file to add protocol specific code
  1. Edit `common/protocolwidgetfactory.cpp` and add a registration for the abcd widget in the ProtocolWidgetFactory's constructor (you will also need to `#include abcdconfig.h`)
  1. Add the `abcdconfig.*` files to `ostprotogui.pro` (protocols are ordered from lower to higher layers - insert your protocol to retain the order)
  1. Edit `abcdpdml.h`
    * add any private members, if required
  1. Edit `abcdpdml.cpp`
    * follow the TODO instruction comments in the file to add protocol specific code
  1. Edit `common/pdmlreader.cpp` and add a registration for the abcd pdml class in the PdmlReaders's constructor (you will also need to `#include abcdpdml.h`)
  1. Add the `abcdpdml.cpp` file to `ostprotogui.pro` (protocols are ordered from lower to higher layers - insert your protocol to retain the order)
  1. Compile and Test your Protocol
  1. Send a merge request to the core team!

After testing the code, remove the TODO comments :-)