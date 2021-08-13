---
title: archivo de inclusión
description: archivo de inclusión
services: backup
author: v-amallick
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 06/04/2021
ms.author: v-amallick
ms.custom: include file
ms.openlocfilehash: 36a45be18e5614371e3e29dc2907f5c25ba772b9
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952968"
---
El agente MARS requiere acceso a los puntos de conexión de servicio de Azure Active Directory, Azure Storage y Azure Backup. Para obtener los intervalos IP públicos, consulte el [archivo JSON](https://www.microsoft.com/en-us/download/confirmation.aspx?id=56519&preserveview=true). Permita el acceso a las IP correspondientes a Azure Backup (AzureBackup), Azure Storage(Storage) y Azure Active Directory(AzureActiveDirectory). Asimismo, en función de la versión de Windows, las comprobaciones de conectividad de red del sistema operativo necesitarán acceso a `www.msftconnecttest.com` o `www.msftncsi.com`.

Si la máquina tiene acceso limitado a Internet, asegúrese de que la configuración de firewall, proxy y red permita el acceso a los siguientes FQDNS y direcciones IP públicas.

### <a name="url-and-ip-access"></a>Acceso a direcciones URL e IP

**FQDNs**

- `www.msftncsi.com`
- `www.msftconnecttest.com`
- *.microsoft.com
- *.windowsazure.com
- *.microsoftonline.com
- \* .windows.net

**Direcciones IP**

- 20.190.128.0/18
- 40.126.0.0/18

Si es un cliente de la Administración Pública de Estados Unidos, asegúrese de que tiene acceso a las siguientes direcciones URL:

- `www.msftncsi.com`
- *.microsoft.com
- *.windowsazure.us
- *.microsoftonline.us
- \* .windows.net
- *.usgovcloudapi.net

El acceso a todas las direcciones URL y direcciones IP enumeradas anteriormente usa el protocolo HTTPS en el puerto 443.

Cuando se realiza una copia de seguridad de archivos y carpetas de VM de Azure con el agente de MARS, también debe configurar la red virtual de Azure para permitir el acceso. Si emplea grupos de seguridad de red (NSG), use la etiqueta de servicio de AzureBackup para permitir el acceso de salida a Azure Backup. Además de la etiqueta de Azure Backup, también debe permitir la conectividad para la autenticación y la transferencia de datos mediante la creación de [reglas de NSG](../articles/virtual-network/network-security-groups-overview.md#service-tags) similares para Azure AD (AzureActiveDirectory) y Azure Storage (Storage).

Para crear una regla para la etiqueta de Azure Backup, siga estos pasos:

1. En **Todos los servicios**, vaya a **Grupos de seguridad de red** y seleccione el grupo de seguridad de red.
1. En **Configuración**, seleccione **Reglas de seguridad de salida**.
1. Seleccione **Agregar**.
1. Escriba todos los detalles necesarios para crear una nueva regla, tal como se explica en [Configuración de reglas de seguridad](../articles/virtual-network/manage-network-security-group.md#security-rule-settings).<br>Asegúrese de que las opciones se establecen tal como se indica a continuación:
   - **Destino** se establece en _Etiqueta de servicio_.
   - La **etiqueta de servicio de destino** se establece en _AzureBackup_.
1. Seleccione **Agregar** para guardar la regla de seguridad de salida recién creada.

Puede crear reglas de seguridad de salida de NSG para Azure Storage y Azure AD de forma similar. Para obtener más información acerca de las etiquetas del grupo de seguridad de red, consulte el artículo sobre [Etiquetas del servicio de red virtual](../articles/virtual-network/service-tags-overview.md).

### <a name="azure-expressroute-support"></a>Compatibilidad con Azure ExpressRoute

Puede realizar una copia de seguridad de los datos mediante Azure ExpressRoute mediante el emparejamiento público (disponible para circuitos antiguos). No se admite la copia de seguridad de emparejamiento de Microsoft a través del emparejamiento privado.

Para usar el emparejamiento público, asegúrese de que los siguientes dominios y direcciones tienen acceso HTTPS en el puerto 443 para:

- `www.msftncsi.com`
- `www.msftconnecttest.com`
- *.microsoft.com
- *.windowsazure.com
- *.microsoftonline.com
- \* .windows.net

**Direcciones IP**
- 20.190.128.0/18
- 40.126.0.0/18

Para usar el emparejamiento de Microsoft, seleccione los siguientes servicios, regiones y los valores de comunidad correspondientes:
- Azure Active Directory (12076:5060)
- Región de Azure (según la ubicación del almacén de Recovery Services)
- Azure Storage (según la ubicación del almacén de Recovery Services)

Obtenga más información sobre los [requisitos de enrutamiento de ExpressRoute](../articles/expressroute/expressroute-routing.md#bgp).

>[!NOTE]
>El emparejamiento público está en desuso para circuitos nuevos.


### <a name="private-endpoint-support"></a>Compatibilidad con el punto de conexión privado

Ahora puede usar puntos de conexión privados para realizar copias de seguridad de los datos de los servidores al almacén de Recovery Services. Como no se puede acceder a Azure AD a través de puntos de conexión privados, debe permitir las direcciones IP y los FQDN necesarios para obtener acceso saliente a Azure AD por separado.

Al usar el agente de MARS para realizar una copia de seguridad de los recursos locales, asegúrese de que la red local (que contiene los recursos de los que se va a realizar la copia de seguridad) esté emparejada con la red virtual de Azure que contiene un punto de conexión privado para el almacén. Después, puede continuar con la instalación del agente de MARS y configurar la copia de seguridad. Sin embargo, debe asegurarse de que toda la comunicación para la copia de seguridad se produzca solo a través de la red emparejada.

Si quita los puntos de conexión privados del almacén después de haber registrado un agente de MARS, deberá volver a registrar el contenedor con el almacén. No es necesario detener la protección de los mismos.
Obtenga más información sobre los [puntos de conexión privados para Azure Backup](../articles/backup/private-endpoints.md).

### <a name="throttling-support"></a>Limitaciones de compatibilidad

**Característica** | **Detalles**
--- | ---
Control del ancho de banda | Compatible. En el agente de MARS, use **Cambiar propiedades** para ajustar el ancho de banda.
Limitación de la red | No está disponible para las máquinas de copia de seguridad que ejecutan Windows Server 2008 R2, Windows Server 2008 SP2 o Windows 7.
