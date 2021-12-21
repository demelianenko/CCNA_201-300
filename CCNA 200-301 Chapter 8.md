# CCNA 200-301 Chapter 8

## Lan

All devices with in the same broadcast domain.

## Reasons to use vlan's

- reduces the amount of frames that that the CPU has to handle each broadcast frame 
- increases security as by minimizing the flood of packets that the devices doesn't  need to receive. broadcast, multicast, unknown unicast 
- improves security per vlan as you could use setting that fit each vlan.
- more flexible design of the network as a whole, restrict and secure per vlan.
- reduce the workload on sftp to single access switch.

## Multi switch vlans

When using vlans on 2 switches in order for the vlans to talk to each other you would need a trunking link between the switches.
and for the switches to know where to forward the frame to we would need vlan tagging and a trunking link.

### Vlan tagging 

the process of adding an extra header to the to the frame with an vlan id field.
It would add a 12 bits header with the vlan id.

**Normal vlan range 1-1005**
**Extended vlan range 1006-4094**

## 802.1Q

By default when trunk is made the switch will assign the native vlan by default to the trunk (vlan1)
and will not add headers to frames sent from vlan1. For this to work properly both switches need to agree on the native vlan.

## Forwarding data between vlans

For same vlan but different switch we will need trunk but to talk between different vlan we would need a router/layer 3 switch. as each vlan is its own "network".In order to forward packets between vlans we would need routing as vlan will make the switch to act like 2 or more physical switches.

## Vlan and vlan Trunking

### Configure a new vlan:

- enter config t and afterward:

  ```
  vlan "vlan-d"
  ```

- you can use the name command to name the vlan, otherwise by default the vlan name would be VLANZZZZ (where Z is the four digit vlan id)

  ```
  name "new vlan name"
  ```

- for each access interface do the following steps:

- ```
  interface "type interface number"
  ```

- ```
  switchport access vlan "id-number"
  ```

- ```
  switchport mode access
  ```

  a Shorter way is possible when creating a vlan. You could enter in to interface config and go straight in to switchport access vlan with out running the first 2 commands. the switch will see the vlan was not created and create it him self.

### Vlan Trunking protocol

#### Trunk administrative options when using switchport mode



| Command option    | Description                                                  |
| ----------------- | ------------------------------------------------------------ |
| Access            | Always act in access mode (nontrunk) port                    |
| trunk             | Always act in trunk port                                     |
| dynamic desirable | dynamic negotiations the active part taker that starts the negotiations |
| dynamic auto      | dynamic negotiations the passive part that waits for the negotiations to start |



How will the link behave with all trunk modes. this table shows 2 ends of a link.

| Administrative Mode | Access     | Dynamic Auto | Trunk      | Dynamic Desirable |
| ------------------- | ---------- | ------------ | ---------- | ----------------- |
| access              | access     | access       | do not use | access            |
| dynamic auto        | access     | access       | trunk      | trunk             |
| trunk               | do not use | trunk        | trunk      | trunk             |
| dynamic desirable   | access     | trunk        | trunk      | trunk             |

## Phones

Because a single cable is used for the phone and computer but is separated by vlans. the idea here because both devices would need to use  1 cable, and the port would need to be access but to allow headers to come in as well. 

- Data vlan: same idea as an access vlan on the access port to forward traffic to the computer.
- Voice vlan: vlan on the link to define the forwarding the phones traffic in the vlan typically would be tagged.

we would create the vlans and assign them both(data and phone) to the port we would use. make the switch in to access mode. 

the most important command is to add the magic command with the voice vlan id. the reset is just making a normal access port

```
switchport voice vlan "vlan-id"
```

the commands to make a port be access and trunk for phone would be:

```
vlan 10
vlan 11
interface range FastEtharent0/1 - 4
switch port mode access
switchport access vlan 10
switch port voice vlan 11
```

the interfaces would not show under trunk. they would act like normal access port but will send the headers for the 802.q encapsulation for the voice vlan. for the 2 in 1 link to work. when phone and computer use the same port.



## Troubleshooting

default steps to make sure always are right.

- Confirm that all Vlans are both defined and active
- Check allowed Vlans list on both ends of each trunk to ensure that all vlans intended to be used are included.
- Check for incorrect trunk configuration settings that result in one switch operating as a trunk, with the neighboring switch not operating as a trunk 
- Check the native vlan settings on both ends of the trunk to ensure settings match

### Verifying a vlan exists

- the vlan command can be used

  ```
  vlan "vlan-id"
  ```

- or we can use the show vlan command

  ```
  show vlan br
  ```

### Disable and enable vlans

- we can use the no shutdown command just like for interfaces

- ```
  no shutdown vlan "vlan-id"
  ```

- or goin in to the vlan and using the no shutdown command

  ```
  vlan 30
  no shutdown
  ```



### Trunking mismatch

the most common issue with trunk is when both ends are set to :

```
switchport mode dynamic auto
```

 in which both ends will wait for the other to start the negotiations.

we can check it using the command:

```
show interface switchport 
```

Another common problem is when 1 switch trunk and the other dose not, the one that dose not the switch with the access port configured will discard all packets with 802.q headers resulting in no data transfer between the switches.

you could check it using the command:

```
show interfaces trunk
show interfaces switchport
```

to check which vlans the trunk can forward we can use the command:

```
show interfaces "interface-id" trunk
```


to limit the vlans that can be forwarded in to the vlan

```
switchport interface allowed vlan 1-60
```



| List Position | Heading                  | Reasons                                                      |
| ------------- | ------------------------ | ------------------------------------------------------------ |
| First         | Vlans allowed            | Vlans 1-4094, minus those removed by switchport trunk allowed command |
| Second        | Vlans allowed and active | all the vlans in the first list minus all vlans that are not configured on the local switch |
| Third         | Vlans in spanning tree   | the second list minus vlans in a stp blocking state          |

To allow or dis allow vlans on the trunk

```
switchport trunk allowed vlan {add | all | except | remove} vlan
```

### Native vlan mismatch 

Because the switch wont add headers to the native vlan, if the 2 switch's have 2 different vlans configured for example Switch A has vlan 1 and Switch B has vlan 2 after sending the packet switch B will forward the frame to vlan 2 because it assumes its the native one. SO make sure the Native vlan matches on the switches 
