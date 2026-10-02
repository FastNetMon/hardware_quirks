#  Asus ProArt X870E-CREATOR WIFI and NVidia / Mellanox Connect X 8

With BIOS 2103, release date 03/09/2026 system requires around 5 minutes to boot when card is installed in PCIE 5.0 x16 slot closest (physically) to CPU. During this time led VGA is active on motherboard 

When installed in next slot machine boots normally but second slot has only PCIE 5.0 x8.

Mellanox card model: C8240 PN: 900-9X81Q-00CN-ST0

Firmware: 40.48.1000

lspci:
```
lspci |grep Mellanox
01:00.0 Ethernet controller: Mellanox Technologies CX8 Family [ConnectX-8]
01:00.1 Ethernet controller: Mellanox Technologies CX8 Family [ConnectX-8]
```

## Updated BIOS to 2503, Release Date: 09/18/2026

No improvements

