

# Quickstart #

_NOTE_: You need admin privileges to run Ostinato

  1. At startup note that the main workspace is divided into 3 main sections - the ports list, streams list and statistics window. ![http://wiki.ostinato.googlecode.com/hg/images/mainWinSections.png](http://wiki.ostinato.googlecode.com/hg/images/mainWinSections.png)
  1. You should see a port group entry for "127.0.0.1" in the ports list with a "green" (connected) status
  1. Expand the port group and you should see all the ports on your local system (if you don't see any ports, check [FAQ - Port group has no interfaces](FAQ#Q._Port_group_has_no_interfaces.md))
  1. Select a port in the ports list
  1. In the Stream List pane, right click and create a new stream
  1. Select the newly created stream and right click to edit it (or alternatively double-click on the stream icon to edit)
  1. In the just opened Stream Configuration Dialog, select the protocols, fill in the protocol fields, configure no of packets, burst parameters and rates. Click OK after you finish.
  1. Click the "Apply" Button in the Stream List pane **(IMPORTANT)**
  1. In the Statistics window, select the same port (select the whole column by clicking on the port heading) for which you configured the stream **(IMPORTANT)**
  1. Click the "Start Transmit" button

Here's a screencast showing the above steps -

<a href='http://www.youtube.com/watch?feature=player_embedded&v=On64lQYEFlY' target='_blank'><img src='http://img.youtube.com/vi/On64lQYEFlY/0.jpg' width='640' height=385 /></a>

# Client-Server Architecture #
Ostinato has a client server architecture. The client (ostinato) runs on a computer and connects to one or more computers running the server (drone). The client can then access and control all ports of all connected servers. The server can be connected to and thus shared by multiple clients at a time. The client and servers can run on different Operating Systems - e.g. you can a Windows client connected to both a Windows Server and a Linux Server.

The client and server can also run on the same computer - this is the default mode. A local server is represented by the loopback IP address 127.0.0.1

**IMPORTANT**: The client sends stream configuration information to the server only when you click the "Apply" button. So if you add/delete/edit streams but forget to click "Apply", your changes won't be reflected when you start the transmit

# Ports List #
![http://wiki.ostinato.googlecode.com/hg/images/portsList.png](http://wiki.ostinato.googlecode.com/hg/images/portsList.png)

The ports list shows all the ports that you can control. Ports are grouped into Port Groups. A Port Group is just a computer or device (local or remote) running the server component (drone).

The port group status icons are as follows -
| ![http://ostinato.googlecode.com/hg/client/icons/bullet_red.png](http://ostinato.googlecode.com/hg/client/icons/bullet_red.png) | The client is not connected to the port group |
|:--------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------|
| ![http://ostinato.googlecode.com/hg/client/icons/bullet_orange.png](http://ostinato.googlecode.com/hg/client/icons/bullet_orange.png) | The client is trying to connect to the port group |
| ![http://ostinato.googlecode.com/hg/client/icons/bullet_green.png](http://ostinato.googlecode.com/hg/client/icons/bullet_green.png) | The client is connected to the port group |
| ![http://ostinato.googlecode.com/hg/client/icons/bullet_error.png](http://ostinato.googlecode.com/hg/client/icons/bullet_error.png) | The client faced some error connecting to the port group |

The port status icons are as follows -
| ![http://ostinato.googlecode.com/hg/client/icons/bullet_white.png](http://ostinato.googlecode.com/hg/client/icons/bullet_white.png) | The port current link status is unknown |
|:------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------|
| ![http://ostinato.googlecode.com/hg/client/icons/bullet_red.png](http://ostinato.googlecode.com/hg/client/icons/bullet_red.png) | The port link status is down |
| ![http://ostinato.googlecode.com/hg/client/icons/bullet_green.png](http://ostinato.googlecode.com/hg/client/icons/bullet_green.png) | The port link status is up |

NOTE: If port is administratively disabled, it may not be listed - this is a Pcap/WinPcap limitation. If no ports are listed - check that drone (the server component) is running with administrative privileges. For more troubleshooting tips, check the [FAQ](FAQ.md)

## Actions ##
| **Icon** | **Action** | **Description** |
|:---------|:-----------|:----------------|
| ![http://ostinato.googlecode.com/hg/client/icons/portgroup_add.png](http://ostinato.googlecode.com/hg/client/icons/portgroup_add.png) | New Port Group | Adds a new remote computer to the list and connects to it. You need to provide the IP address and optionally the port number - hostnames will _not_ work |
| ![http://ostinato.googlecode.com/hg/client/icons/portgroup_delete.png](http://ostinato.googlecode.com/hg/client/icons/portgroup_delete.png) | Delete Port Group | Deletes a remote computer from the list |
| ![http://ostinato.googlecode.com/hg/client/icons/portgroup_connect.png](http://ostinato.googlecode.com/hg/client/icons/portgroup_connect.png) | Connect Port Group | Attempts to reconnect to a disconnected remote computer |
| ![http://ostinato.googlecode.com/hg/client/icons/portgroup_disconnect.png](http://ostinato.googlecode.com/hg/client/icons/portgroup_disconnect.png) | Disconnect Port Group | Disconnects from a remote computer. The remote computer is not removed from the list. You can connect to it again |
|  | Exclusive Port Control | See [Exclusive Port Control](#Exclusive_Port_Control.md)|
|  | Port Configuration | Configure [Port properties](#Port_Configuration.md) |

## Controlling multiple computers ##
  1. On the remote computer that you want to control, run drone (the server side component)
  1. In the Ostinato client, goto File | New Port Group and enter the IP address
  1. The remote computer should appear as a new Port Group in the Ports List

You can connect to any number of remote computers

## Port Configuration ##
Currently the following port properties are available -

### Transmit Mode ###
**Sequential Streams**: Streams are sent one after the other in a sequential fashion. All packets of one stream are sent before the next stream.

_Example_: There are 2 streams - TCP and UDP, the TCP stream configured to send 100 packets at the rate of 10 packets/sec and the UDP stream configured to send 500 packets at the rate of 5 packets/sec. In _sequential streams_ transmit mode, 100 TCP packets will be sent first at the rate of 10 packets/sec, followed by 500 UDP packets at the rate of 5 packets/sec.

**Interleaved Streams**: Streams are interleaved based on their packet/burst rate. Packets of all streams are sent together in an interleaved fashion. This is a "continuous" mode - you cannot configure the number of packets/bursts to be sent, you can only configure the packet/burst rates.

_Example_: There are 2 streams - TCP and UDP, the TCP stream configured to send at the rate of 10 packets/sec and the UDP stream configured to send at the rate of 5 packets/sec. In _interleaved streams_ transmit mode, 15 packets will be sent per second, of which 10 packets will be TCP and 5 will be UDP.

### Exclusive Port Control ###
Exclusive Port Control for Controlled Environment Testing is an "experimental" feature

To prevent the OS from sending packets on a port that you are using, you can take exclusive control of the port. To do so, select the port in the ports list, goto File | Exclusive Control (alternatively, right-click and use the same option from the context menu).

If exclusive control was granted the port's icon in the ports list is decorated with a ringed border as shown below

![http://wiki.ostinato.googlecode.com/hg/images/portExcl.png](http://wiki.ostinato.googlecode.com/hg/images/portExcl.png)

NOTE: This feature is currently available only on Windows and not on other platforms. For Windows, this feature is implemented by using an external command line application - [bindconfig](http://code.google.com/p/bindconfig)

# Creating, editing, duplicating and deleting streams #
To create a stream, first select the appropriate port in the ports list and then goto File | New Stream (alternatively use the right-click context menu).

To insert a stream between two existing streams, select the bottom stream and then create a new stream - the new stream will be created before the selected stream.

To name the stream, double-click inside the 'name' cell and type a suitable name. The stream can be enabled or disabled using the checkbox. You can configure the flow of packets across all the streams using the 'Goto' column. By default after sending one stream, Ostinato proceeds to the next. You can alternatively configure it to stop or go back to the first stream.

![http://wiki.ostinato.googlecode.com/hg/images/streamsList.png](http://wiki.ostinato.googlecode.com/hg/images/streamsList.png)

To configure more details about a stream including protocols, packet lengths, rates etc, see [Stream Configuration](#Stream_Configuration.md)

Starting version 0.7, to duplicate one or more streams, select the stream(s) that you wish to duplicate and then goto File | Duplicate Stream (alternatively use the right-click context menu) - you will be prompted to enter the number of copies you wish to create.

To delete a stream, select the stream(s) and then goto File | Delete Stream (alternatively use the right-click context menu)

**IMPORTANT**: The client sends stream configuration information to the server only when you click the "Apply" button. So if you add/delete/edit streams but forget to click "Apply", your changes won't be reflected when you start the transmit

# Opening and Saving Stream Files #
To open a stream file, first select the appropriate port in the ports list and then goto File | Open Streams (alternatively use the right-click context menu).

To save the streams configured on a port to a file, first select the appropriate port in the ports list and then goto File | Save Streams (alternatively use the right-click context menu).

Ostinato has its own native [file format](FileFormat.md). Starting version  0.4, you can import/export PCAP and PDML files. Starting version 0.7, you can export the configured streams as a python script for use with [python-ostinato](PythonScripting.md) - however, you cannot import a python script into the GUI.

# Stream Configuration #
To configure a stream's properties select the stream and goto File | Edit Stream (or alternatively use the right-click context menu). You can even double click the stream icon in the stream list to edit the stream.

Whichever method you employ to edit the stream, you will now see the Stream Configuration Dialog Box. This dialog box has four tabbed pages

![http://wiki.ostinato.googlecode.com/hg/images/scdTabBar.png](http://wiki.ostinato.googlecode.com/hg/images/scdTabBar.png)

## Protocol Selection ##
### Frame Length ###
![http://wiki.ostinato.googlecode.com/hg/images/scdFrameLen.png](http://wiki.ostinato.googlecode.com/hg/images/scdFrameLen.png)

On this page, you can configure the frame length. You can set the frame length to be a fixed value or use any of the other modes viz. incrementing, decrementing or random. For the non-fixed modes, you can configure a minimum and maximum value between which the frame length will vary.

NOTE: For the non-fixed modes, you should configure the stream to send more than 1 packet otherwise you may not see any variation in the frame lengths.

NOTE: The frame length values specified includes the 4 byte FCS.

### Protocols (Simple Mode) ###
![http://wiki.ostinato.googlecode.com/hg/images/scdProtoSimple.png](http://wiki.ostinato.googlecode.com/hg/images/scdProtoSimple.png)

For most usual cases, you can configure the protocols in your packet by clicking on the appropriate radio buttons as desired. Only standard combinations of the various protocols are allowed here (e.g. you can't have have TCP after ARP).

If you need a non standard combination, click on [advanced mode](#Protocols_(Advanced_Mode).md). Other cases when you need advanced mode are  -
  * More than 2 VLAN tags
  * If you need to use the "UserScript" protocol

NOTE: Each protocol level group here has a "Other" choice - this is not available for user selection but is used when a non-standard combination is selected using the advanced mode

### Protocols (Advanced Mode) ###
![http://wiki.ostinato.googlecode.com/hg/images/scdProtoAdv.png](http://wiki.ostinato.googlecode.com/hg/images/scdProtoAdv.png)

In this mode, you can stack the protocols that you need in any order that you want (unlike simple mode no checks are performed and you can stack TCP following ARP). You can also stack multiple protocols of the same type (e.g. IP over IP)

The left pane shows all the available protocols. The right pane shows the protocols that you select for your packet in the order that you want

Use the following toolbar buttons to configure the protocols required and their order -
| ![http://ostinato.googlecode.com/hg/client/icons/arrow_right.png](http://ostinato.googlecode.com/hg/client/icons/arrow_right.png) | Adds the protocol(s) selected in the left pane to the right pane |
|:----------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------|
| ![http://ostinato.googlecode.com/hg/client/icons/arrow_up.png](http://ostinato.googlecode.com/hg/client/icons/arrow_up.png) | Moves the protocol selected in the right pane up in order |
| ![http://ostinato.googlecode.com/hg/client/icons/arrow_down.png](http://ostinato.googlecode.com/hg/client/icons/arrow_down.png) | Moves the protocol selected in the right pane down in order |
| ![http://ostinato.googlecode.com/hg/client/icons/delete.png](http://ostinato.googlecode.com/hg/client/icons/delete.png) | Removes the protocol selected in the right pane from the selected list of protocols |

## Protocol Data ##
On this page you can configure the fields for each protocol that you selected in the [Protocol Selection page](#Protocol_Selection.md)

![http://wiki.ostinato.googlecode.com/hg/images/scdProtoData.png](http://wiki.ostinato.googlecode.com/hg/images/scdProtoData.png)

Click on the protocol name button to open up the configuration widget for the particular protocol and fill in values as desired.

NOTE: Currently "Protocol Id" fields for the implemented protocols cannot be modified (e.g. if you have Ethernet followed by IP, the Ethertype will be set to 0x0800 - you cannot change it)

## Stream Control ##
![http://wiki.ostinato.googlecode.com/hg/images/scdRates.png](http://wiki.ostinato.googlecode.com/hg/images/scdRates.png)

On this page you can configure whether you send packets or bursts.

If you send packets, you can configure the number of packets to send and the pkts/sec rate at which to send the packets.

If you send bursts, you can configure the number of bursts to send and the bursts/sec rate at which to send the bursts; additionally you can configure the burst size in terms of packets per burst (all packets in a burst are sent back to back without any delay between them)

You can also configure the stream transmission order here. After this stream, you can either goto the next stream in the stream list order, or goto stream 0 (i.e. go back to the first stream in the stream list), or stop transmission (even if there are subsequent streams in the stream list)

**NOTE**: Depending upon the [Transmit Mode](#Transmit_Mode.md), some fields may not be available

**IMPORTANT**: Ostinato tries its best to send the stream at the rate you have requested - but cannot guarantee it. The rates and accuracy achievable for a port depends on the computer running the server component (drone). The computer's inherent processing capability and the current load on the computer are big factors that may affect the rates. See the [FAQ "top-speed"](FAQ#Q._What_is_the_maximum_transmit_rate_that_Ostinato_supports?.md) question for achieving maximum transmit rate.

## Packet View ##
![http://wiki.ostinato.googlecode.com/hg/images/scdPktView.png](http://wiki.ostinato.googlecode.com/hg/images/scdPktView.png)

On this page you can view the packet that you configured in the traditional fashion - tree view and hex dump.

**Unimplemented**
  * If you have configured multiple packets and a varying packet field, you can only view the first of those packets
  * You can click on any item in the tree view to highlight the corresponding bytes in the hexdump pane, but not vice-versa

# Average/Aggregate Port Rates #
The average/aggregate port rate for all the active streams combined is displayed. Changing the average/aggregate port rate will change the rate for all active streams. The delta of the old rate and new rate is distributed amongst all the active streams such that the ratio of rates between the streams remain the same.

# Statistics Window #
The most important thing about using this window is that **ALL** the toolbar buttons (except `Clear All Stats`) operate on the port(s) selected in the statistics window itself (not the port selected in the ports list). If no ports are selected, no operation will be performed.

A port in the statistics window is deemed to be "selected" only when the **full column** is selected - you can do that by clicking on the "heading row" of the port. See the image below for the right way to select a port -

![http://wiki.ostinato.googlecode.com/hg/images/statsWinSelectPort.png](http://wiki.ostinato.googlecode.com/hg/images/statsWinSelectPort.png)

## Actions ##
| **Icon** | **Action** | **Description** |
|:---------|:-----------|:----------------|
| ![http://ostinato.googlecode.com/hg/client/icons/control_play.png](http://ostinato.googlecode.com/hg/client/icons/control_play.png) | Start Transmit | Starts packet transmit on selected port(s) |
| ![http://ostinato.googlecode.com/hg/client/icons/control_stop.png](http://ostinato.googlecode.com/hg/client/icons/control_stop.png) | Stop Transmit | Stops packet transmit on selected port(s) |
| ![http://ostinato.googlecode.com/hg/client/icons/portstats_clear.png](http://ostinato.googlecode.com/hg/client/icons/portstats_clear.png) | Clear Stats | Clears statistics on selected port(s) |
| ![http://ostinato.googlecode.com/hg/client/icons/portstats_clear_all.png](http://ostinato.googlecode.com/hg/client/icons/portstats_clear_all.png) | Clear All Stats | Clears statistics on all ports |
| ![http://ostinato.googlecode.com/hg/client/icons/sound_none.png](http://ostinato.googlecode.com/hg/client/icons/sound_none.png) | Start Capture | Starts packet capture on selected port(s) |
| ![http://ostinato.googlecode.com/hg/client/icons/sound_mute.png](http://ostinato.googlecode.com/hg/client/icons/sound_mute.png) | Stop Capture | Stops packet capture on selected port(s)|
| ![http://ostinato.googlecode.com/hg/client/icons/portstats_filter.png](http://ostinato.googlecode.com/hg/client/icons/portstats_filter.png) | Configure View | Select which port(s) to display in which order in the Statistics Window |