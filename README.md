#  Asus ProArt X870E-CREATOR WIFI and NVidia / Mellanox Connect X 8

With BIOS 2103, release date 03/09/2026 system requires around 5 minutes to boot when card is installed in PCIE 5.0 x16 slot closest (physically) to CPU. During this time led VGA is active on motherboard 

When installed in next slot machine boots normally but second slot has only PCIE 5.0 x8.

Mellanox card model: C8240 PN: 900-9X81Q-00CN-ST0

Firmware: 40.48.1000
PSID: MT_0000001222

lspci:
```
lspci |grep Mellanox
01:00.0 Ethernet controller: Mellanox Technologies CX8 Family [ConnectX-8]
01:00.1 Ethernet controller: Mellanox Technologies CX8 Family [ConnectX-8]
```

## Updated BIOS to 2503, Release Date: 09/18/2026

No improvements

## Updated firmware to 40.50.1002 

No improvements 

### Disabled PXE on network card entirely

Pre change configuration:
```
sudo mlxconfig -d 0000:01:00.0 query | grep -i EXP_ROM


        EXP_ROM_UEFI_ARM_ENABLE                         True(1)                          
        EXP_ROM_UEFI_x86_ENABLE                         True(1)                          
        EXP_ROM_PXE_ENABLE                              True(1)
```

Configuration change:
```
sudo mlxconfig -d 0000:01:00.0 set \
  EXP_ROM_PXE_ENABLE=0 \
  EXP_ROM_UEFI_x86_ENABLE=0 \
  EXP_ROM_UEFI_ARM_ENABLE=0
```

Result: normal fast boot :) 

# Necron Sodium ION DTR3N UPS Linux

Apparently it uses Megatec controller and can be enabled for /etc/nut/ups.conf this way:

```
[netcron]
    driver = nutdrv_qx
    port = /dev/ttyUSB1
    protocol = megatec
    novendor
    norating
    desc = "Necron UPS"
```

Then:
```
sudo /usr/libexec/nut/nutdrv_qx -a netcron -DDD
```

Full output:
```
sudo /usr/libexec/nut/nutdrv_qx -a netcron -DDD
Network UPS Tools 2.8.4 release - Generic Q* USB/Serial driver 0.45
USB communication driver (libusb 1.0) 0.50
   0.000000	[D1] upsdrv_makevartable...
   0.000012	[D1] Using USB implementation: libusb-1.0.29 (API: 0x0100010B)
   0.000045	[D3] do_global_args: var='maxretry' val='3'
   0.000079	[D3] main_arg: var='driver' val='nutdrv_qx'
   0.000081	[D3] main_arg: var='port' val='/dev/ttyUSB1'
   0.000084	[D3] main_arg: var='protocol' val='megatec'
   0.000087	[D3] main_arg: var='novendor' val='<null>'
   0.000090	[D3] main_arg: var='norating' val='<null>'
   0.000093	[D3] main_arg: var='desc' val='Necron UPS'
   0.000095	[D1] Network UPS Tools version 2.8.4 release, 64-bit build for x86_64, built with gcc (Ubuntu 15.2.0-7ubuntu1) 15.2.0 and configured with flags: --build=x86_64-linux-gnu --prefix=/usr --includedir=${prefix}/include --mandir=${prefix}/share/man --infodir=${prefix}/share/info --sysconfdir=/etc --localstatedir=/var --disable-option-checking --disable-silent-rules --libdir=${prefix}/lib/x86_64-linux-gnu --runstatedir=/run --disable-maintainer-mode --disable-dependency-tracking --prefix=/usr --sysconfdir=/etc/nut --includedir=/usr/include --mandir=/usr/share/man --libdir=${prefix}/lib/x86_64-linux-gnu --libexecdir=/usr/libexec --with-ssl --with-nss --with-cgi --with-dev --enable-static --with-statepath=/run/nut --with-altpidpath=/run/nut --with-drvpath=/usr/libexec/nut --with-cgipath=/usr/lib/cgi-bin/nut --with-htmlpath=/usr/share/nut/www --with-pidpath=/run/nut --datadir=/usr/share/nut --with-pkgconfig-dir=/usr/lib/x86_64-linux-gnu/pkgconfig --with-user=nut --with-group=nut --with-udev-dir=/usr/lib/udev --with-systemdsystemunitdir=/usr/lib/systemd/system --with-systemdshutdowndir=/usr/lib/systemd/system-shutdown --with-systemdtmpfilesdir=/usr/lib/tmpfiles.d --with-python=python3 --with-python3=/usr/bin/python3 --with-libsystemd --with-doc=man,html-single,html-chunked,pdf
   0.000104	[D1] debug level is '3'
   0.000524	[D1] Succeeded to become_user(nut): now UID=114 GID=118
   0.000533	[D1] Signalling UPS [netcron]: driver.exit (quietly, no fuss if no driver is running or responding)
   0.000537	Can't open /run/nut/nutdrv_qx-netcron: No such file or directory
   0.000539	[D1] Request for other driver to exit returned code -1
   0.000540	[D1] Socket dialog with the other driver instance (may be absent) failed: No such file or directory
   0.000543	[D1] upsdrv_initups...
   0.124632	[D2] Skipping protocol Voltronic 0.12
   0.124655	[D2] Skipping protocol Voltronic-Axpert 0.01
   0.124656	[D2] Skipping protocol Voltronic-QS 0.10
   0.124658	[D2] Skipping protocol Voltronic-QS-Hex 0.11
   0.124659	[D2] Skipping protocol Mustek 0.08
   0.124660	[D2] Skipping protocol Megatec/old 0.08
   0.124661	[D2] Skipping protocol BestUPS 0.08
   0.124662	[D2] Skipping protocol Mecer 0.09
   0.124749	[D3] send: 'Q1'
   0.385626	[D3] read: '(222.1 000.0 220.1 011 49.9 2.50 50.0 00000001'
   0.385658	Using protocol: Megatec 0.08
   0.385661	[D2] blazer_initups: skipping input.voltage.nominal
   0.385662	[D2] blazer_initups: skipping input.current.nominal
   0.385663	[D2] blazer_initups: skipping battery.voltage.nominal
   0.385664	[D2] blazer_initups: skipping input.frequency.nominal
   0.385667	[D2] blazer_initups: skipping device.mfr
   0.385668	[D2] blazer_initups: skipping ups.serial
   0.385669	[D2] blazer_initups: skipping device.model
   0.385670	[D2] blazer_initups: skipping battery.runtime
   0.385672	[D2] blazer_initups: skipping ups.firmware
   0.385680	[D1] upsdrv_initinfo...
   0.385765	[D3] send: 'Q1'
   0.635634	[D3] read: '(222.6 000.0 219.8 011 50.0 2.50 50.0 00000001'
   0.635706	Can't autodetect number of battery packs [-1/2.50]
   0.635709	Battery runtime will not be calculated (runtimecal not set)
   0.635713	[D1] upsdrv_updateinfo...
   0.635715	[D1] Quick update...
   0.635787	[D3] send: 'Q1'
   0.885628	[D3] read: '(221.8 000.0 220.1 011 50.0 2.50 50.0 00000001'
   0.885715	Listening on socket /run/nut/nutdrv_qx-netcron
   0.885718	[D2] dstate_init: sock /run/nut/nutdrv_qx-netcron open on fd 5
   0.885722	Running as foreground process, not saving a PID file
   0.885726	[D1] Driver initialization completed, beginning regular infinite loop
   0.885728	upsnotify: notify about state NOTIFY_STATE_READY_WITH_PID with libsystemd: was requested, but not running as a service unit now, will not spam more about it
   0.885730	[D1] On systems without service units, consider `export NUT_QUIET_INIT_UPSNOTIFY=true`
   0.885732	upsnotify: failed to notify about state NOTIFY_STATE_READY_WITH_PID: no notification tech defined, will not spam more about it
   0.885734	upsnotify: logged the systemd watchdog situation once, will not spam more about it
   0.885736	[D1] upsdrv_updateinfo...
   0.885737	[D1] Quick update...
   0.885808	[D3] send: 'Q1'
   1.135649	[D3] read: '(221.3 000.0 219.8 011 49.9 2.50 50.0 00000001'
   2.887544	[D1] upsdrv_updateinfo...
   2.887586	[D1] Quick update...
   2.887674	[D3] send: 'Q1'
   3.135596	[D3] read: '(221.9 000.0 220.0 011 50.0 2.50 50.0 00000001'
   4.889692	[D1] upsdrv_updateinfo...
   4.889715	[D1] Quick update...
   4.889795	[D3] send: 'Q1'
   5.135696	[D3] read: '(221.7 000.0 220.6 011 49.9 2.50 50.0 00000001'
   6.891471	[D1] upsdrv_updateinfo...
   6.891487	[D1] Quick update...
   6.891558	[D3] send: 'Q1'
   7.135188	[D3] read: '(222.8 000.0 219.9 011 50.0 2.50 50.0 00000001'
   8.893275	[D1] upsdrv_updateinfo...
   8.893297	[D1] Quick update...
   8.893376	[D3] send: 'Q1'
   9.135273	[D3] read: '(223.3 000.0 219.7 011 50.0 2.50 50.0 00000001'
  10.548817	sock_connect: enabling asynchronous mode (auto)
  10.548837	[D3] sock_connect: new connection on fd 6
  10.548849	[D1] sock_arg: socket 6 requested NOBROADCAST mode
  10.548854	[D2] send_to_one: sending PONG
  10.548882	[D3] sock_arg: TRACKING = 2DDC5036-F665-4D29-BE4C-0374D091D263
  10.548885	[D2] entering main_instcmd(driver.exit, (null)) for [netcron] on socket 6
  10.548889	[D1] set_exit_flag: raising exit flag due to signal -2
  10.548892	[D2] send_to_one: sending TRACKING 2DDC5036-F665-4D29-BE4C-0374D091D263 0
  10.548897	Signal -2: exiting
  10.548904	[D1] upsdrv_cleanup...
  10.550801	[D3] sock_disconnect: disconnecting socket 6

```
