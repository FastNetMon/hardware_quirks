# Mellanox CX 8 direct connect cable 

We're using Mellanox Connect X 8 card C8240 PN: 900-9X81Q-00CN-ST0 on Epyc SP5 platform which supports only PCIE 5.0. This card in 400G*2 configuration requires x32 lanes in PCIE v5 mode. To address this issue Mellanox offers special MCIU cables and addon PCIe cards NVIDIA 930-9XAX6-0025-000 

After we plugged everything to our server it was not activated automatically as it's required to do additional actions

```
sudo mlxconfig -d 0000:01:00.0 q | grep -E 'PCI_BUS(00|10)_(WIDTH|SPEED|HIERARCHY_TYPE)'
        PCI_BUS00_HIERARCHY_TYPE                        PCIE_ENDPOINT(0)
        PCI_BUS00_SPEED                                 PCI_GEN_6(5)
        PCI_BUS00_WIDTH                                 PCI_X16(5)
        PCI_BUS10_HIERARCHY_TYPE                        PCIE_ENDPOINT(0)
        PCI_BUS10_SPEED                                 PCI_GEN_1(0)
        PCI_BUS10_WIDTH                                 PCI_INACTIVE(0)
```

This output means that additional x16 is not active

To enable additional x16 I've applied following commands:
```
sudo mlxconfig -d 0000:01:00.0 set \
  PCI_BUS00_WIDTH=5 \
  PCI_BUS00_SPEED=4 \
  PCI_BUS00_HIERARCHY_TYPE=0 \
  PCI_BUS10_WIDTH=5 \
  PCI_BUS10_SPEED=4 \
  PCI_BUS10_HIERARCHY_TYPE=0
```

It showed me following output:
```
Device #1:
----------

Device type:        ConnectX8           
Name:               900-9X81Q-00CN-ST0_Ax
Description:        NVIDIA ConnectX-8 C8240 HHHL SuperNIC; 400GbE (default) / 400Gb/s IB; Dual-port QSFP112; PCIe 6 x16 with x16 PCIe Socket Direct Extension option; Crypto Enabled; Secure Boot Enabled
Device:             0000:01:00.0        

Configurations:                                         Next Boot                         New                              
        PCI_BUS00_WIDTH                                 PCI_X16(5)                        PCI_X16(5)                       
        PCI_BUS00_SPEED                                 PCI_GEN_6(5)                      PCI_GEN_5(4)                     
        PCI_BUS00_HIERARCHY_TYPE                        PCIE_ENDPOINT(0)                  PCIE_ENDPOINT(0)                 
        PCI_BUS10_WIDTH                                 PCI_INACTIVE(0)                   PCI_X16(5)                       
        PCI_BUS10_SPEED                                 PCI_GEN_1(0)                      PCI_GEN_5(4)                     
        PCI_BUS10_HIERARCHY_TYPE                        PCIE_ENDPOINT(0)                  PCIE_ENDPOINT(0)                 

 Apply new Configuration? (y/n) [n] : y
```

Then you have to reboot server (or ever shutdown entire and then start) and check again, in my case output was following:
```
sudo mlxconfig -d 0000:01:00.0 q | grep -E 'PCI_BUS(00|10)_(WIDTH|SPEED|HIERARCHY_TYPE)'


        PCI_BUS00_HIERARCHY_TYPE                        PCIE_ENDPOINT(0)
        PCI_BUS00_SPEED                                 PCI_GEN_5(4)
        PCI_BUS00_WIDTH                                 PCI_X16(5)
        PCI_BUS10_HIERARCHY_TYPE                        PCIE_ENDPOINT(0)
        PCI_BUS10_SPEED                                 PCI_GEN_5(4)
        PCI_BUS10_WIDTH                                 PCI_X16(5)
```

After  making these changes our Connet-X 8 addon card appeared as additional device in lspci output:
```
lspci |grep mellanox -i
01:00.0 Ethernet controller: Mellanox Technologies CX8 Family [ConnectX-8]
01:00.1 Ethernet controller: Mellanox Technologies CX8 Family [ConnectX-8]
02:00.0 Ethernet controller: Mellanox Technologies CX8 Family [ConnectX-8]
02:00.1 Ethernet controller: Mellanox Technologies CX8 Family [ConnectX-8]
```

Then we do one more check that each device (card itself and addon card) operate on PCIe x16 speed:
```
sudo mlxlink -d 0000:01:00.0 --port_type PCIE

PCIe Operational (Enabled) Info
-------------------------------
Depth, pcie index, node            : 0, 0, 0
Link Speed Active (Enabled)        : 32G-Gen 5 (32G-Gen 5)
Link Width Active (Enabled)        : 16X (16X)

PCIe Operational (Enabled) Info
-------------------------------
Depth, pcie index, node            : 0, 1, 0
Link Speed Active (Enabled)        : 32G-Gen 5 (32G-Gen 5)
Link Width Active (Enabled)        : 16X (16X)

pavel@epyc-sp5:~$ sudo mlxlink -d 0000:02:00.0 --port_type PCIE

PCIe Operational (Enabled) Info
-------------------------------
Depth, pcie index, node            : 0, 0, 0
Link Speed Active (Enabled)        : 32G-Gen 5 (32G-Gen 5)
Link Width Active (Enabled)        : 16X (16X)

PCIe Operational (Enabled) Info
-------------------------------
Depth, pcie index, node            : 0, 1, 0
Link Speed Active (Enabled)        : 32G-Gen 5 (32G-Gen 5)
Link Width Active (Enabled)        : 16X (16X)
```

Please note that Linux kernel will stop complaining about limited PCIe bandwidth in dmesg and it's OK:
```
[    3.196346] mlx5_core 0000:01:00.0: 504.112 Gb/s available PCIe bandwidth (32.0 GT/s PCIe x16 link)
[    4.712605] mlx5_core 0000:01:00.1: 504.112 Gb/s available PCIe bandwidth (32.0 GT/s PCIe x16 link)
[    6.100348] mlx5_core 0000:02:00.0: 504.112 Gb/s available PCIe bandwidth (32.0 GT/s PCIe x16 link)
[    6.666601] mlx5_core 0000:02:00.1: 504.112 Gb/s available PCIe bandwidth (32.0 GT/s PCIe x16 link)
```

You can compare it with CX 8 without direct connect cable:
```
sudo dmesg|grep 'pcie band' -i
[    1.114800] pci 0000:41:00.0: 504.112 Gb/s available PCIe bandwidth, limited by 32.0 GT/s PCIe x16 link at 0000:40:01.1 (capable of 1024.000 Gb/s with 64.0 GT/s PCIe x16 link)
[    7.166070] mlx5_core 0000:41:00.0: 504.112 Gb/s available PCIe bandwidth, limited by 32.0 GT/s PCIe x16 link at 0000:40:01.1 (capable of 1024.000 Gb/s with 64.0 GT/s PCIe x16 link)
[    8.564249] mlx5_core 0000:41:00.1: 504.112 Gb/s available PCIe bandwidth, limited by 32.0 GT/s PCIe x16 link at 0000:40:01.1 (capable of 1024.000 Gb/s with 64.0 GT/s PCIe x16 link)
```
```
