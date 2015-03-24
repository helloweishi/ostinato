

# Introduction #

For unimplemented protocols (or implemented protocols not meeting your specific needs) you can write a script to simulate it.

Scripts are written in `QtScript` (which is very similar to `JavaScript` - both based on ECMA Script)

# Documentation #
Two objects are available to the script - `protocol` and `Protocol` (note the difference in case). The script uses these two objects to define the protocol.

To define your protocol, you need to define the following properties of the `protocol` object -

NOTE: Some of the functions take an `index` param. `index` is incremented by 1 for every packet at runtime - if you would like to vary your protocol size/contents at runtime you can use it, otherwise just ignore it

| **Property** | **Required?** | **Default Value** | **Description** |
|:-------------|:--------------|:------------------|:----------------|
| `protocolFrameSize(index)` | Mandatory | -- | function returning the size of the protocol header in bytes |
| `protocolFrameValue(index)` | Mandatory | -- | function returning an array containing the protocol header bytes; the size of the array should be same as what `protocolFrameSize()` returns;  |
| `name` | Optional | "" (Empty String) | String identifying the protocol |
| `protocolId(type)` | Optional | 0 | function returning the protocol id of your protocol - type is one of `ProtocolIdLlc`, `ProtocolIdEth`, `ProtocolIdIp` or `ProtocolIdTcpUdp`; if your protocol immediately follows a LLC/Eth/IP/TCP/UDP Header, they will use the value that you return to fill-in their `ProtocolId` fields, if you don't define this function or don't provide a value, 0 will be used |
| `protocolFrameCksum(index, type)` | Optional | Calculated checksum as per the CksumType | function returning the cksum of type for the protocol header; type is one of `CksumIp`, `CksumIpPseudo`, `CksumTcpUdp` |
| `protocolFrameValueVariable` | Optional | False | boolean indicating whether the protocol contents vary at run time with every packet; default value is false - set to true if required |
| `protocolFrameSizeVariable` | Optional | False | boolean indicating whether the protocol header size varies at run time with every packet; default value is false - set to true if required |
| `protocolFrameVariableCount` | Optional | 1 | integer specifying the minimum number of frames required to vary its fields/size before the values are repeated again; default value is 1 indicating the protocol does not vary its fields with every packet |

The `protocol` object provides some predefined functions that you can use in your functions -
| `payloadProtocolId(type)` | returns the protocol id of the subsequent protocol - use when your protocol has a "Protocol Id" field that you need to fill-in based on what protocol follows yours |
|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `protocolFrameOffset(index)` | returns the byte offset within the frame from which your protocol will start |
| `protocolFramePayloadSize(index)` | returns the cumulative size of all subsequent protocols and the payload - use if your protocol has a "length" or "payloadLength" field that you need to fill-in |
| `isProtocolFramePayloadValueVariable` | returns a boolean indicating if the protocols that appear as payload to this protocol have varying fields; if your protocol has some fields dependent on the payload, you can use this function to determine if your protocol may need to vary its fields accordingly |
| `isProtocolFramePayloadSizeVariable` | returns a boolean indicating if the protocols that appear as payload to this protocol have a varying size; if your protocol has some fields dependent on the payload size, you can use this function to determine if your protocol may need to vary its fields accordingly |
| `protocolFramePayloadVariableCount` | returns the minimum number of frames required by the payload protocols to vary their fields; if your protocol has some fields dependent on the payload, you can use this function to determine your protocol's `protocolFrameVariableCount` |
| `protocolFrameHeaderCksum(index, cksumType)` | returns the cumulative checksum of all protocol headers that appear before your protocol - useful for protocols like TCP/UDP which need a Pseudo-Ip Checksum of the preceding IP protocol; if `cksumType` is not specified, it defaults to `CksumIp` |
| `protocolFramePayloadCksum(index, cksumType)` | returns the cumulative checksum of all protocol headers and payloads that appear after your protocol- use if your protocol has a "checksum" field that needs the checksum of the payload also; if `cksumType` is not specified, it defaults to `CksumIp` |

The `Protocol` object is a Read-Only object providing some convenient enums as properties -
```
ProtocolIdLlc
ProtocolIdEth
ProtocolIdIp
ProtocolIdTcpUdp
```
```
CksumIp
CksumIpPseudo
CksumTcpUdp
```

# Examples #
## ICMP ##
Here`s an example implementing [ICMP](http://en.wikipedia.org/wiki/Internet_Control_Message_Protocol) -
```
protocol.protocolFrameSize = function() { return 8; }
protocol.protocolFrameValue = function(index)
{
    var type = 8; // Echo Request
    var code = 0;
    var id  = 0x9127; // example value
    var seq = 0x1234; // example value
    var sum = (type << 8 | code) + id + seq + (0xFFFF & ~protocol.protocolFramePayloadCksum());

    var pfv = new Array(8);

    pfv[0] = type;
    pfv[1] = code;

    while(sum>>16)
        sum = (sum & 0xFFFF) + (sum >> 16);

    sum = ~sum;
    pfv[2] = sum >> 8;
    pfv[3] = sum & 0xFF;

    pfv[4] = id >> 8;
    pfv[5] = id & 0xFF;

    pfv[6] = seq >> 8;
    pfv[7] = seq & 0xFF;

    return pfv;
}
protocol.protocolId = function() { return 0x1; }
```

## IPv6 ##
Here`s a slightly more involved example for [IPv6](http://en.wikipedia.org/wiki/IPv6_packet) (a protocol that is not implemented as of this writing)
```
protocol.name = "IPv6"

protocol.protocolFrameSize = function() { return 40; }

protocol.protocolId = function(id_type)
{
    if (id_type == Protocol.ProtocolIdEth)
        return 0x86dd;
    
    if (id_type == Protocol.ProtocolIdIp)
        return 0x29;

    return 0;
}


protocol.protocolFrameValue = function(index)
{
    var len;
    var pfv = new Array(40);

    // ip version = 6
    pfv[0] = 0x60;

    // payload length
    len = protocol.protocolFramePayloadSize(index); 
    pfv[4] = len >> 8;
    pfv[5] = len & 0xFF;

    // Fill-in other fields as required

    // NextHeader
    pfv[6] = protocol.payloadProtocolId(Protocol.ProtocolIdIp); 
    pfv[7] = 64; // HopLimit

    return pfv;
}

protocol.protocolFrameCksum = function(index, type)
{
    var sum = 0;

    if (type == Protocol.CksumIpPseudo) {
        sum += protocol.protocolFramePayloadSize(index);
        sum += protocol.payloadProtocolId(Protocol.ProtocolIdIp);
    }
    return ~sum;
}
```

## VxLAN ##
[Inficone](http://www.inficone.com/technology/crafting-vxlan-packets-using-ostinato/917/) has a blog post about writing a userscript for VxLAN