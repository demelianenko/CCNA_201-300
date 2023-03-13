# Router on a stick config

The configuration of router on a stick would consist of one or more trunk ports and multiple access ports

## The switch

On every access port on a switch set the ports to access port 
you can use the range command to do it faster

```
interface range fastethernet 0/3-10
switch portmode access
switch acess vlan [vlan-id]
```

This should be preformed on each switch port that will be an access port and will be connected to a end point (host)
