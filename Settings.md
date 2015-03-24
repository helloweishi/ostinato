# Ostinato #
TODO

# Drone #
Drone settings are specified in a .ini file. This file is read only once at startup and never written to by the application (i.e. it is expected to be user created and edited). It is located at one of the below locations (searched in order) -

On Unix and Mac OS X -
```
<path-to-drone-executable>/drone.ini
$HOME/.config/Ostinato/drone.ini 
$HOME/.config/Ostinato.ini 
/etc/xdg/Ostinato/drone.ini 
/etc/xdg/Ostinato.ini 
```

On Windows -
```
<path-to-drone-executable>/drone.ini
%APPDATA%\Ostinato\drone.ini 
%APPDATA%\Ostinato.ini 
%COMMON_APPDATA%\Ostinato\Ostinato.ini 
%COMMON_APPDATA%\Ostinato.ini 
```

The `%APPDATA%` path is usually `C:\Documents and Settings\User Name\Application Data`; the `%COMMON_APPDATA%` path is usually `C:\Documents and Settings\All Users\Application Data`.

The .ini file contents are case-sensitive and the format is -
```
[PortList]
Include=
Exclude=
```

## PortList ##
Use the PortList Include/Exclude list to filter the list of ports that drone manages. Both Include and Exclude are comma-separated glob patterns which match port names (in case of Windows, port description instead of port name is matched). For a port to pass the filter and appear on the port list managed by drone, it should be allowed by the Include list and not disallowed by the Exclude List. An empty Include list matches all ports (i.e. all ports are allowed). An empty Exclude list matches no ports (i.e. no ports are disallowed)

e.g. to filter out usbmon ports -
```
[PortList]
Include=
Exclude=usbmon*
```

or e.g. to have drone work on only eth ports and loopback ports but not on eth0 -
```
[PortList]
Include=eth*, lo*
Exclude=eth0
```