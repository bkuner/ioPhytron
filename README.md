# ioPhytron

EPICS drivers for phyMotion IO-cards [Phytron GmbH](https://www.phytron.eu/)

## Ioc shell driver Commands

Create port for a single card. 

- cardNr: card number in chassis, read from left to right
- timeout in ms
- Init commands is a list of phyLogic commands

`phytronCreateIoCtrl( "IP_PORT", "CARD_PORT", cardNr, timeout, "init;by;phyLogic;commands")`

Show all installed cards. use CARD_PORT is one of the created card_ports.

`phyreport(CARD_PORT)`

Send arbitrary commands to the phyMotion device

`phycmd("list;of;commands")`

## Reading EPICS records: ai, longin, bi, mbbi

The card number m is defined by the phytronCreateIoCtrl cardNr parameter, ADDR is the channel number

- PORT: CARD_PORT, means card numer in the chassis count from left to right
- ADDR: In- Output channel number n=1,2..
- REASON: the reason defines the used phyLogic Command: m=CardNr, n=Channel
  - DIN: n=0: "EGmR" read all channels of card m, n>0 "EZm.nR" read a single bit
  - DOUT: n=0: "AGmR" card m: readback state of all channels, n>0 "AZm.nSz" read state of a single bit
  - AIN: n>0 "ADm.nR" read analog channel
  - AOUT "DAm.n" Readback channel
  
`record($(REC),"$(PV)") {
    field(DTYP,"$(DTYP=asynInt32)")
    field(INP,"@asyn($(PORT),$(ADDR))$(REASON)")
}`

## Writing EPICS records: ao, longout, bo, mbbo

The card number m is defined by the phytronCreateIoCtrl cardNr parameter, ADDR is the channel number

- PORT: CARD_PORT, means card numer in the chassis count from left to right
- ADDR: In- Output channel number n=1,2..
- REASON: the reason defines the used phyLogic Command: m=CardNr, n=Channel
  - DOUT: n=0: "AGmSvalue" Set all channels of card m, n>0 "AZm.nSz" set a single bit to 
  - AOUT "DAm.n=value" Set channel
  
`record($(REC),"$(PV)") {
    field(DTYP,"$(DTYP=asynInt32)")
    field(INP,"@asyn($(PORT),$(ADDR))$(REASON)")
}`

## Command interface by stringout, stringin

`record(stringout,"$(DEVN):setCmd") {
    field(DESC,"$(DEVN) Command")
    field(DTYP,"asynOctetWrite")
    field(OUT, "@asyn($(PORT),0)CMD")
    field(FLNK,"$(DEVN):rdCmd")
}
record(stringin,"$(DEVN):rdCmd") {
    field(DESC,"cmd response")
    field(DTYP,"asynOctetRead")
    field(INP,"@asyn($(PORT),0)CMD")
    field(FLNK,"$(DEVN):rdIntCmd")
}
record(longin,"$(DEVN):rdIntCmd") {
    field(DESC,"cmd response as int")
    field(INP,"$(DEVN):rdCmd")
}`


