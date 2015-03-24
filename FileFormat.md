**IMPOTANT NOTE:** Saving/Restoring streams from disk is a <font color='red'>hot out of the oven</font> feature. The file format is <font color='red'>not set in stone yet</font>. The below text is the _plan_ (_intent_, if you will) with respect to backward/forward compatibility - however, things may change!
<font color='red' size='3'><b>YOU HAVE BEEN WARNED!</b></font>


# Version Numbering #
Ostinato uses a 3-part file format version - _Major.Minor.Revision_

**NOTE**: The _file format version_ is independent of the Ostinato _software version_

| _Major_ | Incremented for radical change |
|:--------|:-------------------------------|
| _Minor_ | Incremented for modifications to existing protocols, fields etc. |
| _Revision_ | Incremented for enhancements such as new protocol additions or new fields for existing protocols |

As new features are added in Ostinato, including support for newer protocols, the _Revision_ is expected to change **very frequently**; the _Minor_ version is likely to change relatively less frequently; _Major_ is expected to change rarely, if at all.

# Forward and Backward compatibility #
Every Ostinato _software version_ will support a "native" version of the _file format version_ (typically this will be the most recent file format version as of the release of the software version).

Newer versions of Ostinato will be able to open files saved by older versions of Ostinato, only if the file format version change is in _minor_ or _revision_ numbers relative to the "native" file format version of the newer Ostinato

Older versions of Ostinato will be able to open files saved by newer versions of Ostinato, if the file format version change is in the _revision_ number only - however, the enhancements in the newer version (e.g. a new protocol) will not be available.

In other words,
  * Changes in major version are neither backward nor forward compatible
  * Changes in minor version are backward compatible but not forward compatible
  * Changes in revisions are backward compatible fully and forward compatible with limitations

# File Format Specification #
Ostinato files are in binary format using [Google Protocol Buffers encoding](http://code.google.com/apis/protocolbuffers/docs/encoding.html).

A filename for a Ostinato file does not have a special "extension" such as .ost (although a user may use one if he desires). For identification as an Ostinato file, a fixed size magic field is used.

An Ostinato file starts with a fixed size magic field, followed by variable sized content and ends with a fixed size checksum field.

The magic field is a fixed 12 bytes in size containing -
```
120aa7b7 4f535449 4e41544f
```

The checksum field is fixed 5 bytes long; the first byte is a "tag" `0x7d` while the remaining four bytes is the actual checksum. The checksum algorithm used is CRC32C

For further details on the variable sized content, look at [fileformat.proto](http://code.google.com/p/ostinato/source/browse/common/fileformat.proto)
