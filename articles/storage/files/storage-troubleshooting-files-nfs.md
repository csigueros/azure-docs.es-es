---
title: 'Solución de problemas de recursos compartidos de archivos NFS de Azure: Azure Files'
description: Solución de problemas de recursos compartidos de archivos NFS de Azure
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: fe9450310e3f8774b31557fd7c045f05ed9b56b1
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518363"
---
# <a name="troubleshoot-azure-nfs-file-share-problems"></a>Solución de problemas de recursos compartidos de archivos NFS de Azure

En este artículo se enumeran algunos problemas habituales y conocidos relacionados con los recursos compartidos de archivos NFS de Azure. Se proporcionan las posibles causas y soluciones alternativas cuando se producen estos problemas.

## <a name="applies-to"></a>Se aplica a
| Tipo de recurso compartido de archivos | SMB | NFS |
|-|:-:|:-:|
| Recursos compartidos de archivos Estándar (GPv2), LRS/ZRS | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Estándar (GPv2), GRS/GZRS | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Premium (FileStorage), LRS/ZRS | ![No](../media/icons/no-icon.png) | ![Sí](../media/icons/yes-icon.png) |

## <a name="chgrp-filename-failed-invalid-argument-22"></a>Error en chgrp "nombre de archivo": argumento no válido (22)

### <a name="cause-1-idmapping-is-not-disabled"></a>Causa 1: idmapping no está deshabilitado
Azure Files no permite UID/GID alfanuméricos. Por lo tanto, idmapping debe estar deshabilitado. 

### <a name="cause-2-idmapping-was-disabled-but-got-re-enabled-after-encountering-bad-filedir-name"></a>Causa 2: idmapping estaba deshabilitado, pero se volvió a habilitar tras encontrar un nombre de archivo o de directorio incorrecto
Incluso si idmapping se ha deshabilitado correctamente, la configuración para deshabilitar idmapping se invalida en algunos casos. Por ejemplo, cuando Azure Files encuentra un nombre de archivo incorrecto, devuelve un error. Tras ver este código de error concreto, el cliente de Linux de NFS v4.1 decide volver a habilitar idmapping y las solicitudes futuras se envían de nuevo con UID/GID alfanuméricos. Para obtener una lista de los caracteres no admitidos en Azure Files, consulte este [artículo](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata). El carácter de los dos puntos es uno de los caracteres no admitidos. 

### <a name="workaround"></a>Solución alternativa
Compruebe que idmapping está deshabilitado y que nada lo vuelve a habilitar; luego, haga lo siguiente:

- Desmontaje del recurso compartido
- Deshabilite idmapping con # echo Y > /sys/module/nfs/parameters/nfs4_disable_idmapping.
- Vuelva a montar el recurso compartido.
- Si ejecuta rsync, hágalo con el argumento "—numeric-ids" desde un directorio que no tenga un nombre de directorio o archivo incorrecto.

## <a name="unable-to-create-an-nfs-share"></a>No se puede crear un recurso compartido de NFS

### <a name="cause-1-unsupported-storage-account-settings"></a>Causa 1: configuración de cuenta de almacenamiento no admitida

NFS solo está disponible en las cuentas de almacenamiento con la siguiente configuración:

- Nivel: Prémium
- Tipo de cuenta: FileStorage
- Regiones: [lista de las regiones admitidas](./storage-files-how-to-create-nfs-shares.md?tabs=azure-portal#regional-availability)

#### <a name="solution"></a>Solución

Siga las instrucciones del artículo: [Procedimiento para crear un recurso compartido de NFS](storage-files-how-to-create-nfs-shares.md).

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>No se puede conectar a un recurso compartido de archivos NFS de Azure ni montarlo

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>Causa 1: La solicitud se origina desde un cliente en una red o una IP que no son de confianza

A diferencia de SMB, la autenticación de NFS no se basa en el usuario. La autenticación de un recurso compartido se basa en la configuración de la regla de seguridad de red. Por eso, para tener la certeza de que solo se establecen conexiones seguras con su recurso compartido de NFS, debe usar el punto de conexión de servicio o puntos de conexión privados. Para acceder a recursos compartidos desde un entorno local, además de los puntos de conexión privados, debe configurar una VPN o ExpressRoute. Se han omitido las direcciones IP agregadas a la lista de permitidos de la cuenta de almacenamiento para el firewall. Debe usar uno de los siguientes métodos para configurar el acceso a un recurso compartido de NFS:


- [Punto de conexión de servicio](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - Se accede a través del punto de conexión público.
    - Solo está disponible en la misma región.
    - El emparejamiento de VNet no proporcionará acceso al recurso compartido.
    - Cada red virtual o subred debe agregarse individualmente a la lista de permitidos.
    - En el caso del acceso local, se pueden usar puntos de conexión de servicio con ExpressRoute y VPN de punto a sitio y de sitio a sitio, pero se recomienda usar un punto de conexión privado, ya que es más seguro.

En el diagrama siguiente se muestra la conectividad mediante puntos de conexión públicos.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="Diagrama de conectividad mediante puntos de conexión públicos" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [Punto de conexión privado](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - El acceso es más seguro que con el punto de conexión de servicio.
    - El acceso al recurso compartido de NFS a través de un vínculo privado está disponible desde y hacia la región de Azure de la cuenta de almacenamiento (entre regiones o local).
    - El emparejamiento de red virtual con redes virtuales hospedadas en el punto de conexión privado proporciona acceso al recurso compartido de NFS a los clientes en las redes virtuales emparejadas.
    - Se pueden usar puntos de conexión privados con ExpressRoute y VPN de punto a sitio y de sitio a sitio.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="Diagrama de conectividad mediante puntos de conexión privados" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>Causa 2: La opción Se requiere transferencia segura está habilitada

Todavía no se admite el cifrado doble de los recursos compartidos de NFS. Azure proporciona una capa de cifrado para todos los datos en tránsito entre los centros de datos de Azure mediante MACSec. Solo se puede acceder a los recursos compartidos de NFS desde redes virtuales de confianza y a través de túneles VPN. No hay ningún cifrado de capa de transporte adicional disponible en los recursos compartidos de NFS.

#### <a name="solution"></a>Solución

Deshabilite Se requiere transferencia segura en la hoja Configuración de la cuenta de almacenamiento.

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/disable-secure-transfer.png" alt-text="Captura de pantalla de la hoja Configuración de la cuenta de almacenamiento, en la que se deshabilita la opción Se requiere transferencia segura":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>Causa 3: el paquete nfs-common no está instalado
Antes de ejecutar el comando de montaje, instale el paquete mediante la ejecución del comando específico de la distribución que se muestra a continuación.

Para comprobar si el paquete NFS está instalado, ejecute: `rpm qa | grep nfs-utils`.

#### <a name="solution"></a>Solución

Si el paquete no está instalado, instálelo en la distribución.

##### <a name="ubuntu-or-debian"></a>Ubuntu o Debian

```
sudo apt update
sudo apt install nfs-common
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora, Red Hat Enterprise Linux 8+ y CentOS 8+

Use el administrador de paquetes dnf: `sudo dnf install nfs-utils`.

En las versiones anteriores de Red Hat Enterprise Linux y CentOS, use el administrador de paquetes yum: `sudo yum install nfs-common`.

##### <a name="opensuse"></a>openSUSE

Use el administrador de paquetes zypper: `sudo zypper install-nfscommon`.

### <a name="cause-4-firewall-blocking-port-2049"></a>Causa 4: El firewall bloquea el puerto 2049

El protocolo NFS se comunica con su servidor a través del puerto 2049; asegúrese de que este puerto está abierto para la cuenta de almacenamiento (el servidor NFS).

#### <a name="solution"></a>Solución

Compruebe que el puerto 2049 está abierto en el cliente mediante la ejecución del siguiente comando: `telnet <storageaccountnamehere>.file.core.windows.net 2049`. Si el puerto no está abierto, ábralo.

## <a name="ls-hangs-for-large-directory-enumeration-on-some-kernels"></a>ls no responde con la enumeración de directorios grandes en algunos kernels

### <a name="cause-a-bug-was-introduced-in-linux-kernel-v511-and-was-fixed-in-v5125"></a>Causa: en el kernel de Linux v5.11 se introdujo un error, que se corrigió en v5.12.5.  
Algunas versiones de kernel tienen un error que hace que las listas de directorios provoquen una secuencia READDIR infinita. Los directorios muy pequeños donde todas las entradas se pueden enviar en una sola llamada no tienen este problema.
El error se introdujo en el kernel de Linux v5.11 y se corrigió en v5.12.5. Por lo tanto, todas las versiones intermedias tienen el error. Se sabe que RHEL 8.4 tiene esta versión del kernel.

#### <a name="workaround-downgrading-or-upgrading-the-kernel"></a>Solución alternativa: cambiar a una versión anterior o posterior del kernel
Cambie a una versión anterior o posterior del kernel para resolver el problema.

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.
Si sigue necesitando ayuda, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
