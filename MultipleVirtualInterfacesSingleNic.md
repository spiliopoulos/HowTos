# This is how you setup multiple virtual network interfaces in a single NIC

We will use something called macvlan. Essentially we create sub-interfaces
that connect to a single physical interface.

The sub-interfaces will have their own MAC address, a random if none is
provided during setup

```
# ip link add {NAME_OF_VIRTUAL_INTERFACE} link {PHYSICAL_INTERFACE} address {MAC_ADDRESS_TO_USE} type macvlan
```

Optionally you can specify one of the 4 modes of macvlan that control the
communication between different macvlan interfaces

* vepa  
   This mode expects a VEPA/802.1Qbg capable switch at the other end of
   the interface. The switch on the other end acts as a reflective relay, As
   such we forward all traffic out to the switch even if it is destined for us
   and rely on the switch at the other end to send it back (aka 'Hairpin' mode).
   Useful when there are network level policies being enforced by the switches
   (eg DHCP filtering).
* private
   Filter all incoming packets so that no MAC VLAN bound to an interface can
   communicate with each other (drop all packets ingressing overthe interface
   that have a source MAC address that matches one of the MAC VLAN interfaces).
* passthru
   Just pass the packet to the network. The default behavior of a L2
   switch is to not send a packet back down the port it egressed from to prevent
   loops forming. This has the effect of making a MAC VLAN in this mode with a
   standard switch behave as if it was in Private mode and prevent cross MAC
   VLAN communication
* bridge
   Provide a pseudo bridge to allow MAC VLANs bound to the same
   interface to communicate without the packet being sent out the network
   interface.

After that you need to configure the new interfaces with static ip addresses
or use a dhcp client for them.

```
# dhcpcd {NAME_OF_VIRTUAL_INTERFACE}
```
