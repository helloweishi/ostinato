#summary Ostinato Scripting using Python
#labels Featured



Starting from version 0.6, Ostinato provides python bindings to enable scripting support.

# Requirements #
  * Python 2.7+ (but not Python 3)
  * protobuf 2.3+

# Installation #
The Ostinato python bindings are available in the python-ostinato distribution. The easiest way to install it is to use pip -
```
$ pip install python-ostinato
```
_If you already have an older version installed (including any rc/beta release), add `--upgrade` to the above command to upgrade._

NOTE: The distribution is called python-ostinato. The package that it provides is called ostinato.

You can do a quick check if your installation is successful by running the example module included in the distribution. First make sure drone is running and then run the below command -
```
$ python -m ostinato.example
```

The example expects the following topology -
```
 +-------+          +-------+
 |       |Tx--->----|       |
 | Drone |          |  DUT  |
 |       |Rx---<----|       |
 +-------+          +-------+
```
Drone has 2 ports connected to DUT. Packets sent on the Tx port are expected to be received back on the Rx port. **An easy way to simulate the above topology is to select the loopback port as both Tx and Rx ports**

When prompted by the module, provide the IP of drone and select the Tx/Rx ports (or accept the defaults chosen by the module).

We will go through this example in the next section.

# Tutorial #
We start with importing the required modules and classes -
```
import os
import time

from ostinato.core import ost_pb, DroneProxy
from ostinato.protocols.mac_pb2 import mac
from ostinato.protocols.ip4_pb2 import ip4, Ip4
```

Setup some variables and create a `DroneProxy` object that will act as a proxy to communicate with drone -
```
host_name = '127.0.0.1'
tx_port_number = 0
rx_port_number = 0
drone = DroneProxy(host_name)
```

Before we can instruct drone to do anything, we need to connect to drone. Once connected, we can invoke operations on drone. We query drone for the list of ports and then retrieve configuration for all the ports
```
    drone.connect()

    port_id_list = drone.getPortIdList()
    port_config_list = drone.getPortConfig(port_id_list)

    print('Port List')
    print('---------')
    for port in port_config_list.port:
        print('%d.%s (%s)' % (port.port_id.id, port.name, port.description))

```

Several of the `DroneProxy`'s methods, including the ones for transmit and capture, take a list of ports to operate on, so we set these up next.
```
    tx_port = ost_pb.PortIdList()
    tx_port.port_id.add().id = tx_port_number;

    rx_port = ost_pb.PortIdList()
    rx_port.port_id.add().id = rx_port_number;
```

Configuring a stream on a port is a two-step process - add + configure.

First we assign a stream id and then add it -
```
    stream_id = ost_pb.StreamIdList()
    stream_id.port_id.CopyFrom(tx_port.port_id[0])
    stream_id.stream_id.add().id = 1
    drone.addStream(stream_id)
```

This just adds a 'default' stream. We then configure the stream using the same stream id as what we assigned earlier -
```
    stream_cfg = ost_pb.StreamConfigList()
    stream_cfg.port_id.CopyFrom(tx_port.port_id[0])
    s = stream_cfg.stream.add()
    s.stream_id.id = stream_id.stream_id[0].id
    s.core.is_enabled = True
    s.control.num_packets = 5
```

NOTE: By default when you add a stream, it is disabled. You need to set `Stream.core.is_enabled` to True to enable it.

After configuring basic stream parameters such as number of packets, we configure the protocols in the stream. Adding a protocol to a stream is a 3-step process. First we add a protocol with protocol.add(), then we specify the specific protocol to use by assigning protocol\_id.id to one of the various `ost_pb.Protocol.kXXXFieldNumber`, and finally we configure that protocol's fields - the protocol's fields are accessed using the `Protocol.Extensions[XXX]` dictionary. After setting up the stream parameters, we call `modifyStream` to configure the default stream installed by `addStream()` with the values given here -
```
    # setup stream protocols as mac:eth2:ip4:udp:payload
    p = s.protocol.add()
    p.protocol_id.id = ost_pb.Protocol.kMacFieldNumber
    p.Extensions[mac].dst_mac = 0x001122334455
    p.Extensions[mac].src_mac = 0x00aabbccddee

    p = s.protocol.add()
    p.protocol_id.id = ost_pb.Protocol.kEth2FieldNumber

    p = s.protocol.add()
    p.protocol_id.id = ost_pb.Protocol.kIp4FieldNumber
    # reduce typing by creating a shorter reference to p.Extensions[ip4]
    ip = p.Extensions[ip4]
    ip.src_ip = 0x01020304
    ip.dst_ip = 0x05060708
    ip.dst_ip_mode = Ip4.e_im_inc_host

    s.protocol.add().protocol_id.id = ost_pb.Protocol.kUdpFieldNumber
    s.protocol.add().protocol_id.id = ost_pb.Protocol.kPayloadFieldNumber

    drone.modifyStream(stream_cfg)
```

Before we start transmitting the stream, we clear the port statistics -
```
    drone.clearStats(tx_port)
    drone.clearStats(rx_port)
```

Now we can start transmitting the stream that we configured. Since we want to capture the transmitted packets when they appear on the rx port we ensure that we start capture before we start transmit and do the reverse while stopping.
```
    drone.startCapture(rx_port)
    drone.startTransmit(tx_port)

    # wait for transmit to finish
    time.sleep(7)

    drone.stopTransmit(tx_port)
    drone.stopCapture(rx_port)
```

We verify the transmit and receive by retreiving statistics
```
    tx_stats = drone.getStats(tx_port)
    rx_stats = drone.getStats(rx_port)
```

We retrieve the captured packets, save it in a temporary pcap file and use tshark to dump them
```
    buff = drone.getCaptureBuffer(rx_port.port_id[0])
    drone.saveCaptureBuffer(buff, 'capture.pcap')
    os.system('tshark -r capture.pcap')
    os.remove('capture.pcap')
```

Finally, we cleanup by deleting the stream that we configured and disconnect from drone
```
    drone.deleteStream(stream_id)
    drone.disconnect()
```

Download the full [example.py](http://ostinato.googlecode.com/hg/binding/example.py)

# API Reference #

_"Use the Source, Luke!"_

At this time, detailed reference documentation for the DroneProxy class and other protocol classes is a TODO item. Till such time as this becomes available, here are a few pointers to get you started -

  * Look at the `*.proto` files in the common directory
  * The basic API is in [protocol.proto](https://code.google.com/p/ostinato/source/browse/common/protocol.proto)
  * All the RPCs listed under 'service OstService' in [protocol.proto](https://code.google.com/p/ostinato/source/browse/common/protocol.proto) are available as methods of DroneProxy
  * In addition, DroneProxy has one convenience method - saveCaptureBuffer(buf, filename); buf is what the getCaptureBuffer() method returns which is saved as a PCAP file with the provided filename
  * Each protocol (Mac, Ip4, Ip6, Udp etc.) has its own .proto file
  * You can learn about proto files from the [protobuf documentation](https://developers.google.com/protocol-buffers/docs/overview). At a minimum, you should read
    * [Language Guide](https://developers.google.com/protocol-buffers/docs/proto)
    * [Generated Python Code](https://developers.google.com/protocol-buffers/docs/reference/python-generated)
  * The above documentation talks about generating .py files from the .proto files. The python-ostinato package already contains these generated .py files, so you don't need to worry about that

# FAQ #
## Q. I configured a stream and called startTransmit(), but no packets are being transmitted. Why? ##
A. Check that Stream.core.is\_enabled is set to True

## Q. I configured multiple streams and called startTransmit(), but streams are being transmitted in a random order. Why? ##
A. In case of multiple streams, streams are ordered by the Stream.core.ordinal field (not by order of streams nor by stream id). Make sure you set the ordinal field in each stream correctly as per the required order.

## Q. Ostinato python bindings don't work with Python 3 ##
A. At this time Ostinato python bindings are compatible with Python 2 only and not with Python 3. The primary blocker for supporting Python 3 is the lack of official support of Python 3 in ProtoBuf. We are aware that there are third-party packages available, but our plan is to wait for Google to provide official support.

## Q. How do I find the version of the python bindings I'm using? ##
A. Use -
```
import ostinato
print ostinato.__version__
print ostinato.__revision__
```

## Q. Does the python package include any debugging features? ##
Yes. Add the following at the start of your script -
```
import logging
logging.basicConfig(level=logging.DEBUG)
```

You can set the logging level for the ostinato module independently from your own script's logging level -
```
ostinato.__log__.setLevel(<level>)
```