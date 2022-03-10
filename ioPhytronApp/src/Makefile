TOP=../..

include $(TOP)/configure/CONFIG
#----------------------------------------
# Build the IOC application


LIBRARY_IOC += phytronIo

DBD += phytronIo.dbd

# The following are compiled and added to the support library
phytronIo_SRCS += phytronIoCtrl.cpp

INC += phytronIoCtrl.h

phytronIo_LIBS += asyn
phytronIo_LIBS += $(EPICS_BASE_IOC_LIBS)

# Test with PHYIOC
PROD_IOC = PHYIOC
PHYIOC_SRCS += PHYIOC_registerRecordDeviceDriver.cpp SoftIocMain.cpp

#PHYIOC_LIBS += motor phytronSupport
PHYIOC_LIBS += asyn 
PHYIOC_LIBS += phytronIo
PHYIOC_LIBS += $(EPICS_BASE_IOC_LIBS)

PHYIOC_DBD += base.dbd asyn.dbd
PHYIOC_DBD += drvAsynIPPort.dbd phytronIo.dbd
#PHYIOC_DBD += motorSupport.dbd  phytronSupport.dbd

DBD += PHYIOC.dbd

#===========================

include $(TOP)/configure/RULES

