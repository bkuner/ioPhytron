# ioPhytron

EPICS drivers for phyMotion IO-cards [Phytron GmbH](https://www.phytron.eu/)

## Ioc shell driver Commands

Create port for a single card. 
  
- cardNr: Phytron IO commands address the first module,channel or bit by 1 not by 0!
  The card number in the chassis is read from left to right. First analog: cardNr=1, 
  first digital cardNr=1. The total amount of digital or analog cards may be found 
  by the commands 'IMAIO','IMDIO'
- timeout in ms
- Init commands is a list of phyLogic commands

`phytronCreateIoCtrl( "IP_PORT", "CARD_PORT", cardNr, timeout, "init;by;phyLogic;commands")`

Show all installed cards. CARD_PORT is one of the created card_ports.

`phyreport(CARD_PORT)`

Send arbitrary commands to the phyMotion device

`phycmd("list;of;commands")`

## Reading EPICS records: ai, longin, bi, mbbi

The card number m is defined by the phytronCreateIoCtrl cardNr parameter, ADDR is the channel number. The reasons AOUT,AIN will readback set values.

- PORT: CARD_PORT, as defined by phytronCreateIoCtrl()
- ADDR: In- Output channel number n=1,2..
- REASON: the reason defines the used phyLogic Command
  - DIN: n=0: "EGmR" read all channels of card m, n>0 "EZm.nR" read a single bit
  - DOUT: n=0: "AGmR" card m: readback state of all channels, n>0 "AZm.nSz" read state of a single bit
  - AIN: n>0 "ADm.nR" read analog channel
  - AOUT "DAm.n" Readback channel
  
```
record($(REC),"$(PV)") {
    field(DTYP,"$(DTYP=asynInt32)")
    field(INP,"@asyn($(PORT),$(ADDR))$(REASON)")
}
```

## Writing EPICS records: ao, longout, bo, mbbo

The card number m is defined by the phytronCreateIoCtrl cardNr parameter, ADDR is the channel number

- PORT: CARD_PORT, as defined by phytronCreateIoCtrl()
- ADDR: In- Output channel number n=1,2..
- REASON: the reason defines the used phyLogic Command
  - DOUT: n=0: "AGmSvalue" Set all channels of card m, n>0 "AZm.nSz" set a single bit to 
  - AOUT "DAm.n=value" Set channel
  
```
record($(REC),"$(PV)") {
    field(DTYP,"$(DTYP=asynInt32)")
    field(OUT,"@asyn($(PORT),$(ADDR))$(REASON)")
}
```

## Command interface by stringout, stringin to process arbitrary commands by EPICS.db 
The drivers stringin/out support will set the stringin to the answer of a command sent by
stringout. Either "ACK" for acknowledge, "NAK" for not acknowledge or the valid return data 
as string.

```
record(stringout,"$(DEVN):setCmd") {
    field(DESC,"$(DEVN) Command")
    field(DTYP,"asynOctetWrite")
    field(OUT, "@asyn($(CARD_PORT),0)CMD")
    field(FLNK,"$(DEVN):rdCmd")
}
record(stringin,"$(DEVN):rdCmd") {
    field(DESC,"cmd response")
    field(DTYP,"asynOctetRead")
    field(INP,"@asyn($(CARD_PORT),0)CMD")
    field(FLNK,"$(DEVN):rdIntCmd")
}
record(longin,"$(DEVN):rdIntCmd") {
    field(DESC,"cmd response as int")
    field(INP,"$(DEVN):rdCmd")
}
```
