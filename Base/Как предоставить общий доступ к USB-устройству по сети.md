#USB 

Совместное использование USB-устройств по сети с помощью утилиты usbip.

# Предварительные заметки

Следующий пример протестирован на Ubuntu 21.10 с использованием ядра 5.13.0-27-generic

```
$ lsb_release -a

No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 21.10
Release:	21.10
Codename:	impish

$ uname -a

Linux itsecforu 5.13.0-27-generic #29-Ubuntu SMP Wed Jan 12 17:36:47 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux

```

# Общие шаги

Установите пакет linux-tools-common, поскольку он содержит утилиту usbip.

```
$ sudo apt install linux-tools-common
```

Определите, все ли зависимости у вас установлены.

```
$ usbip version

WARNING: usbip not found for kernel 5.13.0-27

  You may need to install the following packages for this specific kernel:
    linux-tools-5.13.0-27-generic
    linux-cloud-tools-5.13.0-27-generic

  You may also want to install one of the following packages to keep up to date:
    linux-tools-generic
    linux-cloud-tools-generic
```

Установите недостающие зависимости.

```
$ sudo apt install linux-tools-generic linux-cloud-tools-generic
```

Исправьте отсутствующий список идентификаторов USB.

```
$ sudo mkdir  /usr/share/hwdata

$ sudo ln -s /usr/share/misc/usb.ids /usr/share/hwdata/usb.ids
```

Убедитесь, что необходимые модули будут загружаться при загрузке.

```
$ cat <<EOF | sudo tee /etc/modules-load.d/usbip.conf
usbip_core
usbip_host
vhci_hcd
EOF
```

Перезапустите службу systemd-modules-load, чтобы загрузить эти модули без перезагрузки.

```
$ sudo systemctl restart systemd-modules-load.service
```

Убедитесь, что утилита usbip может быть запущена.

```
$ usbip version

usbip (usbip-utils 2.0)
```

# Настройка сервера

Создайте службу systemd для демона сервера USB/IP.

```
$ cat <<EOF | sudo tee /etc/systemd/system/usbipd.service
[Unit]
Description=USB/IP server daemon
After=network.target


[Service]
Type=simple
Restart=always
ExecStart=/usr/bin/usbipd

[Install]
WantedBy=multi-user.target
EOF
```

Создайте шаблон службы  для привязки целей.

```
$ cat <<EOF | sudo tee /etc/systemd/system/usbip-bind@.service
[Unit]
Description=USB/IP server 
After=network.target usbipd.service
Requires=usbipd.service

[Service]
Type=simple
ExecStart=/usr/bin/usbip bind --busid %i
ExecStop=/usr/bin/usbip unbind --busid %i
RemainAfterExit=yes
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF
```

Перезагрузите конфигурацию менеджера systemd.

```
$ sudo systemctl daemon-reload
```

Демон сервера USB/IP по умолчанию отключен.

```
$ systemctl status usbipd.service 

○ usbipd.service - USB/IP server daemon
     Loaded: loaded (/etc/systemd/system/usbipd.service; disabled; vendor preset: enabled)
     Active: inactive (dead)
```

Включите службу демона сервера USB/IP.

```
$ sudo systemctl enable --now usbipd.service 

Created symlink /etc/systemd/system/multi-user.target.wants/usbipd.service → /etc/systemd/system/usbipd.service
```

Проверьте состояние демона сервера USB/IP.

```
$ systemctl status usbipd.service 

● usbipd.service - USB/IP server daemon
     Loaded: loaded (/etc/systemd/system/usbipd.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2022-01-23 14:49:02 CET; 1min 12s ago
   Main PID: 2281034 (usbipd)
      Tasks: 1 (limit: 18955)
     Memory: 332.0K
        CPU: 5ms
     CGroup: /system.slice/usbipd.service
             └─2281034 /usr/lib/linux-tools/5.13.0-25-generic/usbipd

sty 23 14:49:02 desktop systemd[1]: Started USB/IP server daemon.
sty 23 14:49:02 desktop usbipd[2281034]: usbipd: info: starting usbipd (usbip-utils 2.0)
sty 23 14:49:02 desktop usbipd[2281034]: usbipd: info: listening on 0.0.0.0:3240
sty 23 14:49:02 desktop usbipd[2281034]: usbipd: info: listening on :::3240
```

Отображение устройств USB.

```
$ lsusb 

Bus 006 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 005 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 002: ID 8087:8000 Intel Corp. Integrated Rate Matching Hub
Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 001 Device 006: ID 1050:0407 Yubico.com Yubikey 4/5 OTP+U2F+CCID
Bus 001 Device 002: ID 8087:8008 Intel Corp. Integrated Rate Matching Hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 004 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```

Отображение экспортируемых USB-устройств.

```
$ usbip list --local

 - busid 1-1.6 (1050:0407)
   Yubico.com : Yubikey 4/5 OTP+U2F+CCID (1050:0407)
```

Отображение экспортируемых USB-устройств с использованием удобного для машины формата.

```
$ usbip list --parsable --local

busid=1-1.6#usbid=1050:0407#
```

Привязка устройства с помощью службы шаблонов systemd.

```
$ sudo systemctl enable --now usbip-bind@1-1.6

Created symlink /etc/systemd/system/multi-user.target.wants/usbip-bind@1-1.6.service → /etc/systemd/system/usbip-bind@.service.
```

Проверим состояние службы

```
$ sudo systemctl status  usbip-bind@1-1.6

● usbip-bind@1-1.6.service - USB/IP server
     Loaded: loaded (/etc/systemd/system/usbip-bind@.service; enabled; vendor preset: enabled)
     Active: active (exited) since Sun 2022-01-23 14:55:46 CET; 13s ago
    Process: 2282127 ExecStart=/usr/bin/usbip bind --busid 1-1.6 (code=exited, status=0/SUCCESS)
   Main PID: 2282127 (code=exited, status=0/SUCCESS)
        CPU: 4ms

sty 23 14:55:46 desktop systemd[1]: Started USB/IP server.
sty 23 14:55:46 desktop usbip[2282127]: usbip: info: bind device on busid 1-1.6: complete
```

Отображение экспортированных USB-устройств.

```
$ usbip list --remote=localhost

Exportable USB devices
======================
 - localhost
      1-1.6: Yubico.com : Yubikey 4/5 OTP+U2F+CCID (1050:0407)
           : /sys/devices/pci0000:00/0000:00:1a.0/usb1/1-1/1-1.6
           : (Defined at Interface level) (00/00/00)
```

# Настройка клиента

Отображение USB-устройств.

```
$ lsusb 

Bus 004 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 007: ID 0bda:0129 Realtek Semiconductor Corp. RTS5129 Card Reader Controller
Bus 001 Device 008: ID 05e3:0608 Genesys Logic, Inc. Hub
Bus 001 Device 006: ID 05e3:0608 Genesys Logic, Inc. Hub
Bus 001 Device 010: ID 046d:c52b Logitech, Inc. Unifying Receiver
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```

Отображение USB-устройств, экспортированных на удаленный сервер.

```
$ usbip list --remote dell

Exportable USB devices
======================
 - dell
      1-1.6: Yubico.com : Yubikey 4/5 OTP+U2F+CCID (1050:0407)
           : /sys/devices/pci0000:00/0000:00:1a.0/usb1/1-1/1-1.6
           : (Defined at Interface level) (00/00/00)
```

Прикрепите конкретное устройство.

```
$ sudo usbip attach --remote dell --busid 1-1.6
```

Отображение подключенных устройств.

```
$ sudo usbip port

Imported USB devices
====================
Port 00:  at Full Speed(12Mbps)
       Yubico.com : Yubikey 4/5 OTP+U2F+CCID (1050:0407)
       3-1 -> usbip://dell:3240/1-1.6
           -> remote bus/dev 001/006
```

Отображение устройств USB.

```
$ lsusb 

Bus 004 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
**Bus 003 Device 002: ID 1050:0407 Yubico.com Yubikey 4/5 OTP+U2F+CCID**
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 007: ID 0bda:0129 Realtek Semiconductor Corp. RTS5129 Card Reader Controller
Bus 001 Device 008: ID 05e3:0608 Genesys Logic, Inc. Hub
Bus 001 Device 006: ID 05e3:0608 Genesys Logic, Inc. Hub
Bus 001 Device 010: ID 046d:c52b Logitech, Inc. Unifying Receiver
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```

> Пользуйтесь устройством свободно.

Отсоедините устройство после использования.

```
$ sudo usbip detach --port 00

usbip: info: Port 0 is now detached!
```

Создайте шаблон службы

```
$ cat <<EOF | sudo tee /etc/systemd/system/usbip-attach@.service
[Unit]
Description=USB/IP bind service 
After=network.target
Requires=usbipd.service

[Service]
Type=simple
ExecStart=/usr/bin/usbip bind --busid %i
ExecStop=/usr/bin/usbip unbind --busid %i
RemainAfterExit=yes
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF
```

Перезагрузите конфигурацию менеджера systemd.

```
$ sudo systemctl daemon-reload
```

# Дополнительные примечания

-   Используйте firewall и создайте белый список на сервере для защиты порта демона USB/IP сервера в локальной сети/vpn.
-   Шаблон systemd для клиента может быть создан с помощью специального файла окружения в качестве параметра, внутри которого вы можете определить удаленный сервер и идентификатор шины USB.
-   На стороне клиента требуется модуль vhci_hcd.