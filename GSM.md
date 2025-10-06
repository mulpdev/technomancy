https://github.com/axilirator/plmn-research/blob/master/layer23-lapdm-gsmtap.pcap 
- filter on ldapm
- LDAPm data is inside UDP data -> GSM TAP Header

https://www.cloudshark.org/captures/e6909dc0862f?filter=frame%20and%20eth%20and%20ip%20and%20sctp%20and%20m3ua%20and%20sccp%20and%20ranap%20and%20gsm_a.dtap%20and%20gsm_a.rp

- UPP RANAP.pcapng

https://wiki.wireshark.org/uploads/__moin_import__/attachments/SampleCaptures/gsm-r.uus1.pcap (BSSAP)


https://github.com/BurntSushi/ripgrep

https://www.etsi.org/deliver/etsi_gts/04/0405/03.01.05_60/gsmts_0405sv030105p.pdf
https://www.etsi.org/deliver/etsi_gts/04/0406/03.09.00_60/gsmts_0406sv030900p.pdf

https://code.visualstudio.com/shortcuts/keyboard-shortcuts-windows.pdf


# Protocol Stack
'Um' interface
- Named after ISDN U interface
	- m - mobile
- 184 bit control frames, 260 bit vocoder frames sent over 148 bit burst per timeslot
- Conceptually is OSI layers 1-3

04.11 and 03.40
## 1 Physical - Dm
`Dm` - term describing all various GSM signalling channels
- typically SDCCH or SACCH

## 2 Data Link
GSM TS 04.05 04.06

Protocol: `LAPDm` - Link Access Procedures on the `Dm` channel
- GPRS MAY use Logical link control (LLC, GSM 04.64)

Termination point for `BTS` entity
- `BS/BTS` - Base station / Base Transceiver Station 
### 3 Network
GSM 04.07, 04.08 

Consists of 3 sublayers, in order:
`RR` - Radio Resources sublayer
`MM` - Mobility Management sublayer
`CM` - Connection Management sublayer

Protocol: `SM-CP` - Short Message Control Protocol

Terminates in the `MSC` entity along with layer 4
- `MSC` Mobile Switching Center
![[attachments/gsm_04.07_protocol_heirarchy.png]]

## 4 Relay (Protocol Dependent)
GSM 04.11 - SMS protocol

`SM-RL` Short Message Relay Layer

Protocol: `SM-RP` Short Message Relay Protocol

Terminates in the `MSC` entity along with layer 3
- `MSC` Mobile Switching Center

## 5 Transfer (Protocol Dependent)
GSM 03.40 

`SM-RL` Short Message Transport Layer

Protocol: `SM-TP` Short Message Transfer Protocol
- Uses `TPDU` - Transfer Protocol Data Units

Terminates in the `SMSC`
- `SMSC` Short Message Service Center

Between `MSC` and `SMSC` the TPDUs are carried as a parameter of a `MAP` package
- `MAP` - Mobile Application Part (MAP) package

## 6 Application
?

# LAPDm Protocol
GSM TS 04.05
GSM TS 04.06

`LAPDm` - Link Access Procedures on the Dm channel
	- `Dm` = term describing all various GSM signalling channels
`MS-BSS` - Mobile Station to Base Station System
`N201` - Parameter N201 takes a fixed value for each type of channel, see 04.06 5.8.3 and determines the maximum number of octets in the information field of a frame
`SAP` - Service Access Point (transition to L3)
`SAPI` - Service Access Point Identifier
## Fields
### Address Field
- 1 to N octets. For apps on control channels it's 1 octet.
- Identifies SAP for which command frame is intended and SAP transmitting a response frame
```
bit        8        7        6        5        4        3        2        1
---------------------------------------------------------------------------------
Addr     | spare |  LPD             | SAPI                    | C/R    |  EA |
Addr     | 0     |  0        0/1    | 0111 or 0000            | C/R    |  EA |
```

`EA` - Address extension. Will be set to 0 until final address octet, where it's a 1
`C/R` - Command / Response frame 
- `MS` sends commands as 1, responses as 0
- `BS` is opposite. commands are 0, responses are 1
`SAPI` - Priority 
- 0- Highest, 3-Lowest 04.05 4.2.2
- 0-CC, MM, RR signalling, 3-SMS 04.06 3.3.3
`LPD` - Link Protocol Discriminator, set to 00 (or 01 for SMSBC)
`Spare` - Unused ?

### Control Field 
- One octet
- Identifies type of frame (command/response) and contains sequence numbers
- 3 formats
	- `I format` Information
	- `S format` Supervisory
	- `U format` Unnumbered
- Data
	- `N(S)` - Transmitter send sequence number
	- `N(R)` - Transmitter recv sequence number
	- function bits
		- `S` - supervisory function bit
		- `U` - Unnumbered function bit
		- See 04.06 Table 4 for for how commands make use of the `S` and `U` bits
	- `P/F` Poll bit when set in a command. Final bit when set in a response
	

```
bit        8        7        6        5        4        3        2        1
---------------------------------------------------------------------------------
I format | N(R)                  |    P    |   N(S)                     | 0 | 
---------------------------------------------------------------------------------
S format | N(R)                  |    P/F  |   S        S    |   0        1 | 
---------------------------------------------------------------------------------
U format | U        U        U   |    P/F  |   U        U    |   1        1 | 
```

### Length Indicator Field 
- 04.06 3.6 1 to N octets. For apps on control channels it's 1 octet.
- `E/L` - Extended Length/ Set to 0 if more data, set to 1 on final octet
- `M` - More Data. Is this only a part of a segmented (aka fragmented) L3 message?
	- 1 this is a segment of a L3 message
	- 0 depends
		- a complete message if previous M was 0
		- Last segment if previous M was 1
- `L` Number of octets in information field of UI, SABM, UA, or I frames
	- 0 - `N201` inclusive
	- 0 = Not information field

```
bit        8        7        6        5        4        3        2        1
---------------------------------------------------------------------------------
LIF        | L                                                 | M       | E/L |
```


### Information Field 
Length is LIF above. Max number of octets in IF (N201) is defined in 5.8.3. 
If less than `N201` length
- `00101011` 
- `MS` may use `11111111`

## Formats
### Format A
```
Used on DCCHs for frames where there is no information field
```
![[attachments/gsm_formatA.png]]

### Format B
```
Userd on DCCHs for frames containing an information field 
```
![[attachments/gsm_formatB.png]]

### Format Abis/Bbis
```
formats Abis and Bbis are equivalent to formats A and B but are only used on channels only supporting unacknowledged mode of operation SAPI=0 (BCCH, PCH, AGCH). The format Abis may be used when there is no information to be sent on the channel.
```


#### Abis
![[attachments/gsm_FormatAbis.png]]

#### Bbis
![[attachments/gsm_formatBbis.png]]


### Format C
```
Transmission of random access signals. Described in 04.06 Annex A (missing in v3.9 pdf)
```



## Example LAPDm Message 
PCAP layer23-lapdm-gsmtap.pcap No. 11 after TAP

Frame B info 
```
		 128       64       32       16        8        4        2        1
bit        8        7        6        5        4        3        2        1
---------------------------------------------------------------------------------
Address    | spare |  LPD              | SAPI                    | C/R  | EA=1 |
---------------------------------------------------------------------------------
Control I  | N(R)                  |    P    |   N(S)                    | 0 |
---------------------------------------------------------------------------------
LIF        | L                                                 | M       | E/L |

Byte Stream: 013f29 

# Layer 2 LAPDm
Address = 0x01 = 0b00000001
 - EA = 1, Final octet
 - C/R = 0 and direction is BS->MS so it's a command

Control = 0x3f = 0b00111111
 - Unnumbered format
 - Poll bit is True
 - UUU = 001, UU = 11, command is SABM

LIF     = 0x29 = 0b00101001
 - E/L set, no more data
 - M not set, (segment in No 10 was 0) so it's the last segment
 - length of 10 for L3 data (CP-Data)
```

## Example SMS Message
UPP RANAP.pcapng No 95
```

Byte Stream: 09014f || 0101079109359210000000430 || 40c9109356904341400004130726150758036d43a1a0dd29d502077393f2f83c461791af43ecbcbee76b90d3a97e5e575d9cd2ecb41ecf49c5e9ea741617d984d4e03

# CP-Data
PD = 0x09 = 0b00001001
TI Flag - 0, Alloc by sender
TID - 0
PD - SMS (0x9)

MTI = 0x01, CP-DATA (also CP-User Data IEI? 04.11 Fig 8.2)

Length = 0x4f

# RP-Data
MTI = 0x1, RP-Data (Network to MS)
Msg Ref = 0x1

Originator = 079109359210000
Length = 0x7
Next octet = 0x91 = 0b10011011
Ext = 0x1,
Type of Number = 0x01, International
Number Plan = 0x1 = ISDN/Telephony
Number = 905329010000

Destination = 0
No length, does not exist

Data 67 more bytes

# GSM SMS TDPU 
```