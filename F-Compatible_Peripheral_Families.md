# Appendix F. Compatible Peripheral Families

The Z280 MPU supports two different types of bus interface: the Z80-Bus and the Z-BUS. Families of peripheral devices are available for both types of component interconnect buses.

The Z80 Bus configurations of the Z280 MPU have two compatible peripheral families: the Z8400 and Z8000/Z8500 peripheral families (Tables F-1 and F-2). The Z8400 family of devices were originally designed to support the Z80-Bus. The Z8000 series of peripherals are designed for systems employing multiplexed address/data buses, and are also easily interfaced to Z80-Bus Z8000 MPU systems. The Z8500 peripheral family is identical to the Z8000 family, except the devices are configured to interface to non-multiplexed buses: the Z8500 series devices can be used in Z280 MPU systems where the address/data bus is de-multiplexed external to the processor.

The Z-BUS versions of the Z280 MPU are supported by the Z8000/Z8500 peripheral family (Table F-2). These devices interface directly to the Z-BUS.

Refer to the Zilog Components Data Book for further information regarding these peripheral families.

<br/>

Part Number | Description
|-|-|
Z8410 | DMA Direct Memory Access Controller
Z8420 | PIO Parallel Input/Output Controller
Z8430 | CTC Counter/Timer Circuit
Z8440/1/2 | SIO Serial Input/Output Controller
Z8470 | DART Dual Asynchronous Receiver/Ttansmitter

_Table F-1. Z8400 Peripheral Family_

<br/>

Part Number | Description
|-|-|
Z8016/Z8516 | D7C Direct Memory Access Transfer Controller
Z8030/Z8530 | SGC Serial Communications Controller
Z8036/Z8536 | CIO Counter/Timer and Parallel I/O Unit
Z8038 | Z-FIO FIFO Input/Output Interface Unit
Z8060 | Z-FIFO Buffer Unit and Z-FIO Expander
Z8065 | BEP Burst Error Processor
Z8068 | Z-DCP Data Ciphering Processor
Z8090/Z8590 | UPC Universal Peripheral Controller (ROM-based)
Z8094/Z8594 | UPC Universal Peripheral Controller (RAM-based)

_Table F-2. Z8000/Z8500 Peripheral Family_