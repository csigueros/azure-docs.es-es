---
title: Uso de DNS dinámico para registrar nombres de host en Azure | Microsoft Docs
description: Obtenga información sobre cómo configurar DNS dinámico para registrar nombres de host en sus propios servidores DNS.
services: dns
documentationcenter: na
author: KumudD
manager: vitinnan
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: kumud
ms.openlocfilehash: 0f688e3ea61dc550571c8f6d3b7009ba2cf06ab4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746161"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Uso de DNS dinámico para registrar nombres de host en su propio servidor DNS

[Azure ofrece resolución de nombres](virtual-networks-name-resolution-for-vms-and-role-instances.md) para las máquinas virtuales (VM) y las instancias de rol. Sin embargo, cuando la resolución de nombres tiene que ir más allá de lo que el servicio DNS predeterminado de Azure ofrece, puede proporcionar sus propios servidores DNS. El uso de sus propios servidores DNS le ofrece la capacidad de personalizar su solución DNS para satisfacer sus propias necesidades específicas. Por ejemplo, puede que necesite acceder a los recursos locales a través del controlador de dominio de Active Directory.

Si los servidores DNS personalizados se hospedan como máquinas virtuales de Azure, puede reenviar consultas de nombre de host de la misma red virtual a Azure para resolver los nombres de host. Si no quiere usar esta opción, puede registrar los nombres de host de las máquinas virtuales en el servidor DNS usando para ello DNS dinámico (DDNS). Azure no tiene las credenciales para crear directamente los registros en los servidores DNS, por lo que a menudo se necesitan medidas alternativas. Aquí presentamos algunos escenarios comunes con alternativas:

## <a name="windows-clients"></a>Clientes Windows
Los clientes Windows no unidos a dominio intentan realizar actualizaciones de DDNS no seguras cuando arrancan o cuando su dirección IP cambia. El nombre DNS es el nombre de host más el sufijo DNS primario. Azure deja en blanco el sufijo DNS principal, pero puede establecerlo en la VM con la [interfaz de usuario](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794784(v=ws.10)) o mediante [PowerShell](/powershell/module/dnsclient/set-dnsclient).

Los clientes Windows unidos a dominio registran sus direcciones IP con el controlador de dominio mediante DDNS seguro. El proceso de unión a dominio establece el sufijo DNS primario en el cliente y crea y mantiene la relación de confianza.

## <a name="linux-clients"></a>Clientes Linux
Por lo general, los clientes Linux no se registran con el servidor DNS al iniciarse, dado que asumen que esto lo hace el servidor DHCP. Los servidores DHCP de Azure no tienen las credenciales para realizar registros en el servidor DNS. Puede usar una herramienta denominada `nsupdate`, que se incluye en el paquete Bind, para enviar las actualizaciones de DDNS. Dado que el protocolo DDNS está estandarizado, puede usar `nsupdate` aunque no use Bind en el servidor DNS.

Puede usar los enlaces que proporciona el cliente DHCP para crear y mantener la entrada del nombre de host en el servidor DNS. Durante el ciclo de DHCP, el cliente ejecuta los scripts que aparecen en */etc/dhcp/dhclient-exit-hooks.d/* . Puede usar los enlaces para registrar la nueva dirección IP mediante `nsupdate`. Por ejemplo:

```bash
#!/bin/sh
requireddomain=mydomain.local

# only execute on the primary nic
if [ "$interface" != "eth0" ]
then
    return
fi

# When you have a new IP, perform nsupdate
if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
then
   host=`hostname`
   nsupdatecmds=/var/tmp/nsupdatecmds
     echo "update delete $host.$requireddomain a" > $nsupdatecmds
     echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
     echo "send" >> $nsupdatecmds

     nsupdate $nsupdatecmds
fi
```

También puede usar el comando `nsupdate` para realizar actualizaciones de DDNS seguras. Por ejemplo, cuando usa un servidor Bind DNS, se genera un par de claves pública-privada (`http://linux.yyz.us/nsupdate/`). El servidor DNS está configurado (`http://linux.yyz.us/dns/ddns-server.html`) con la parte pública de la clave, de manera que se puede comprobar la firma en la solicitud. Para proporcionar el par de claves para `nsupdate`, use la opción `-k`. El objetivo es la firma de la solicitud de actualización DDNS.

Si usa un servidor DNS de Windows, puede usar la autenticación Kerberos con el parámetro `-g` en `nsupdate`, pero no está disponible en la versión de `nsupdate` para Windows. Para usar Kerberos, utilice `kinit` para cargar las credenciales. Por ejemplo, puede cargar las credenciales de un [archivo keytab](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html) y `nsupdate -g` extraer las credenciales de la memoria caché.

En caso de ser necesario, puede agregar un sufijo de búsqueda DNS a las máquinas virtuales. El sufijo DNS se especifica en el archivo */etc/resolv.conf* . La mayoría de las distribuciones de Linux administran automáticamente el contenido de este archivo, por lo que normalmente no se puede editar. Sin embargo, puede reemplazar el sufijo mediante el comando `supersede` del cliente DHCP. Para invalidar el sufijo, agregue la línea siguiente al archivo */etc/dhcp/dhclient.conf*:

```
supersede domain-name <required-dns-suffix>;
```