
# NEW
https://github.com/griffina/SnmpSharpNet/blob/master/AsnType.cs
https://opensource.apple.com/source/net_snmp/net_snmp-10/net-snmp/include/net-snmp/library/snmp_impl.h.auto.html

# ASN.1 
## ASN.1 types
https://www.oss.com/asn1/resources/asn1-made-simple/asn1-quick-reference.html

![[TechnomancyOG/snmp/Pasted image 20230929122114.png]]

## ASN.1 BER (Specifically TLV TAG)
https://www.oss.com/asn1/resources/asn1-made-simple/asn1-quick-reference/basic-encoding-rules.html

![[TechnomancyOG/snmp/types_sm.png]]

# SNMP Types
## Historic / See also
RFC 1155, Sections 
3.2.1 - Primitive (ASN.1) data types
3.2.2 - Sequence (ASN.1) data types
3.2.3.* - (SNMP) Application defined types

RFC 1442, Sections
3.2 - Mappings for UNIVERSAL and APPLICATION (see below)

## RFC 2578
Structure of Management Information Version 2 (SMIv2)

### Section 2.*
The available options mappings look like:
```
SimpleSyntax ::=
    CHOICE { ...SNIP... }
OR
ApplicationSyntax ::=
    CHOICE { ...SNIP... }
```

Actual assignment mappings look like:
```
Integer32 ::=
              [UNIVERSAL 2]
OR
IpAddress ::=
              [APPLICATION 0]
```

Note the comment about SEQUENCE (changed from history?)
```
ObjectSyntax ::=
    CHOICE {
        simple
            SimpleSyntax,

          -- note that SEQUENCEs for conceptual tables and
          -- rows are not mentioned here...

        application-wide
            ApplicationSyntax
    }
```


### Section 3.2 AND 7.1.*
```
Finally, if any of the ASN.1 named types and macros defined in this document, specifically:
        Counter32, Counter64, Gauge32, Integer32, IpAddress, MODULE-
        IDENTITY, NOTIFICATION-TYPE, Opaque, OBJECT-TYPE, OBJECT-
        IDENTITY, TimeTicks, Unsigned32,
```




# Type TAG

## Bits 7-5
7-6 CLASS
5 Simple or Constructed

|  Bits 7,6 = CLASS    |  Bit 5 = Simple or Constructed |
|-----:|-----:|
| Universal (00) = 0x0     |      |
| Application (01) =  0x40    |      | 
| Context Specific (10) = 0x80     |
| Private (11) 0xC0      |      |
|      | Simple (0) = 0x0     | 
|      | Constructed (1) 0x20     |   

## Bits 4-0 (Type)
### UNIVERSAL
```
SimpleSyntax ::=
    CHOICE { ...SNIP... }
```

|  Type    |  Bits and Value | Note |
|-----:|-----:| -----:|
| Integer/32      | (00010) = 0x2     |
| Octet String      | (00100) = 0x4     |
| OID     |  (00110) = 0x6    |

Ignored

|  Type    |  Bits and Value | Note |
|-----:|-----:| -----:|
| Bool | (00001) = 0x1      |      |
| Bit String      |  (00011) = 0x3    |
| Null     | (00101) = 0x5     |
| Floating Point       |  (01001) = 0x9  |
| Sequence  | (01010) = 0xA     |
| IA5String (ASCII) | (10110) = 0x16   |



### APPLICATION TYPES for SNMP
```
ApplicationSyntax ::=
    CHOICE { ...SNIP... }
```

|  Application Data Type | Type bits | Note |
|:-----|:-----|:-----| :----- |
| IpAddress| (00000) 0x0 | OCTET STRING of length 4  |
| Counter32| (00001) 0x1 | |
| Gauge32  | (00010) 0x2 | |
| Unsigned32 | (00010) 0x2 | "indistinguishable from Gauge32"|
| TimeTicks  | (00010) 0x3 |
| Opaque     | (00010) 0x4 | "for backward-compatibility only" |
| Counter64  | (00110) 0x6 |      |
