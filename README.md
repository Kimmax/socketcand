socketcand
==========

Socketcand is a daemon that provides access to CAN interfaces on a machine via a network interface. The communication protocol uses a TCP/IP connection and a specific protocol to transfer CAN frames and control commands. The protocol specification can be found in ./doc/protocol.md.

Installation
------------

To build and run socketcand make sure you have the following tools installed:

* meson
* gcc or another C compiler
* a kernel that includes the SocketCAN modules
* the headers for your kernel version
* the libconfig with headers (libconfig-dev under debian based systems)
* the libsocketcan with headers (libsocketcan-dev under debian based systems) is a requirement to configure the interfaces from socketcand

Execute the following commands to configure, build, and install the software:

    $ meson setup -Dlibconfig=true --buildtype=release build
    $ meson compile -C build
    $ meson install -C build

Docker image
------------
Prebuild docker images are available.  
Note this image still needs a host kernel with SocketCAN modules.  
The CAN interface needs to be configured on the host and made available to the container using the `--network=host` option.  
  
Example usage:  
`docker run --rm --network=host -it ghcr.io/linux-can/socketcand:latest -v -i can0`

Usage
-----

     socketcand [-v | --verbose] [-i interfaces | --interfaces interfaces]
		[-p port | --port port] [-l interface | --listen interface]
		[-u name | --afuxname name] [-n | --no-beacon] [-d | --daemon]
		[-e error_mask | --error-mask error_mask]
		[-h | --help]

### Description of the options
* **-v** (activates verbose output to STDOUT)
* **-i interfaces** (comma separated list of CAN interfaces the daemon shall provide access to e.g. '-i can0,vcan1' - default: vcan0)
* **-p port** (changes the default port '29536' the daemon is listening at)
* **-l interface** (changes the default network interface the daemon will bind to - default: eth0)
* **-u name** (the AF_UNIX socket path - an abstract name is used when the leading '/' is missing. N.B. the AF_UNIX binding will supersede the port/interface settings)
* **-n** (deactivates the discovery beacon)
* **-e error_mask** (enable CAN error frames in raw mode providing an hexadecimal error mask, e.g: 0x1FFFFFFF)
* **-d** (set this flag if you want log to syslog instead of STDOUT)
* **-h** (prints this message)

Service discovery
-----------------

The daemon uses a simple UDP beacon mechanism for service discovery. A beacon containing the service name, type and address is sent to the broadcast address (port 42000) at minimum every 3 seconds. A client only has to listen for messages of this type to detect all SocketCAN daemons in the local network.

License
-------

The source code is released under either GPL-2.0-only or BSD-3-Clause licenses.
