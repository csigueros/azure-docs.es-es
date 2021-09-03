---
title: Protección de los entornos locales y de Azure mediante la eliminación de SMB 1 en Linux | Microsoft Docs
description: Azure Files admite SMB 3.x y SMB 2.1, versiones heredadas no seguras de SMB como SMB 1. Antes de conectarse a un recurso compartido de archivos de Azure, puede deshabilitar las versiones anteriores de SMB como SMB 1.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/19/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8f0e75ebf45839eb75f22a8fb46e76bdebec4688
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/15/2021
ms.locfileid: "112124788"
---
# <a name="remove-smb-1-on-linux"></a>Eliminación de SMB 1 en Linux
Muchas organizaciones y proveedores de servicios de Internet (ISP) bloquean el puerto que usa SMB para comunicarse, el puerto 445. Esta práctica procede de las instrucciones de seguridad sobre las versiones heredadas y en desuso del protocolo SMB. Aunque SMB 3.x es un protocolo seguro para Internet, las versiones anteriores de SMB, especialmente SMB 1, no lo son. SMB 1, también conocido como CIFS (Sistema de archivos de Internet común), se incluye en muchas distribuciones de Linux. 

SMB 1 es un protocolo obsoleto, ineficaz y no seguro. La buena noticia es que Azure Files no es compatible con SMB 1 y, a partir de la versión 4.18 del kernel de Linux, Linux hace posible deshabilitar SMB 1. Siempre [recomendamos encarecidamente](https://aka.ms/stopusingsmb1) deshabilitar SMB 1 en los clientes Linux antes de usar recursos compartidos de archivos SMB en producción.

## <a name="linux-distribution-status"></a>Estado de la distribución de Linux
A partir del kernel de Linux 4.18, el módulo de kernel de SMB, denominado `cifs` por motivos de herencia, expone un nuevo parámetro de módulo (a menudo conocido como *parm* de diversos documentos externos), denominado `disable_legacy_dialects`. Aunque se presentó en el kernel de Linux 4.18, algunos proveedores han trasladado este cambio a los kernels más antiguos que admiten. Para mayor comodidad, en la tabla siguiente se detalla la disponibilidad de este parámetro de módulo en distribuciones de Linux comunes.

| Distribución | Puede deshabilitar SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | No |
| Ubuntu 18.04 | Sí |
| Ubuntu 19.04+ | Sí |
| Debian 8-9 | No |
| Debian 10+ | Sí |
| Fedora 29+ | Sí |
| CentOS 7 | No | 
| CentOS 8+ | Sí |
| Red Hat Enterprise Linux 6.x-7.x | No |
| Red Hat Enterprise Linux 8+ | Sí |
| openSUSE Leap 15.0 | No |
| openSUSE Leap 15.1+ | Sí |
| openSUSE Tumbleweed | Sí |
| SUSE Linux Enterprise 11.x-12.x | No |
| SUSE Linux Enterprise 15 | No |
| SUSE Linux Enterprise 15.1 | No |

Puede comprobar si la distribución de Linux es compatible con el parámetro del módulo `disable_legacy_dialects` por medio del comando siguiente:

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Este comando debe generar el siguiente mensaje:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

## <a name="remove-smb-1"></a>Eliminación de SMB 1
Antes de deshabilitar SMB 1, confirme que el módulo SMB no está cargado actualmente en el sistema (esto sucede de manera automática si ha montado un recurso compartido de SMB). Puede hacerlo con el siguiente comando, que no debe generar nada si no se carga SMB:

```bash
lsmod | grep cifs
```

Para descargar el módulo, desmonte primero todos los recursos compartidos de SMB (con el comando `umount` como se describió anteriormente). Puede identificar todos los recursos compartidos de SMB montados en el sistema con el siguiente comando:

```bash
mount | grep cifs
```

Una vez que haya desmontado todos los recursos compartidos de archivos SMB, es seguro descargar el módulo. Para ello, puede usar el comando `modprobe` :

```bash
sudo modprobe -r cifs
```

Puede cargar manualmente el módulo con SMB 1 descargado mediante el comando `modprobe`:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Por último, puede comprobar que el módulo SMB se ha cargado con el parámetro, para ello, examine los parámetros cargados en `/sys/module/cifs/parameters`:

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Para deshabilitar de forma persistente SMB 1 en las distribuciones basadas en Ubuntu y Debian, debe crear un nuevo archivo (si aún no tiene opciones personalizadas para otros módulos) denominado `/etc/modprobe.d/local.conf` con la configuración. Para ello, use el siguiente comando:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

Para comprobar que esto ha funcionado, cargue el módulo SMB:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="next-steps"></a>Pasos siguientes
Consulte los vínculos siguientes para más información sobre Azure Files:

- [Planeamiento de una implementación de Azure Files](storage-files-planning.md)
- [Uso de Azure Files con Linux](storage-how-to-use-files-linux.md)
- [Solución de problemas](storage-troubleshoot-linux-file-connection-problems.md)