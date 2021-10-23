---
title: Creación de un laboratorio con un recurso compartido | Azure Lab Services
description: Obtenga información sobre cómo crear un laboratorio que requiera un recurso compartido entre los alumnos.
author: emaher
ms.topic: how-to
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 6436a157c91c7594cd328be53dfaf6e0856412dd
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180673"
---
# <a name="how-to-create-a-lab-with-a-shared-resource-in-azure-lab-services"></a>Creación de un laboratorio con un recurso compartido en Azure Lab Services

A veces, al crear un laboratorio educativo, puede haber algunos recursos que se deban compartir entre todos los alumnos del laboratorio.  Por ejemplo, si dispone de un servidor de licencias o SQL Server para una clase de base de datos.  En este artículo se describen los pasos para habilitar el recurso compartido para un laboratorio.  También hablaremos sobre cómo limitar el acceso a ese recurso compartido.

## <a name="architecture"></a>Architecture

Como se muestra en el diagrama siguiente, tendremos una cuenta de laboratorio con un laboratorio.  La cuenta de laboratorio tendrá la configuración de emparejamiento de VNet para que la red virtual del laboratorio esté conectada a la red del recurso compartido.  En el diagrama siguiente hay dos redes virtuales con intervalos IP que no se superponen.  Estos intervalos IP son solo de ejemplo.  Tenga en cuenta también que la red virtual del recurso compartido está en la misma suscripción que la cuenta de laboratorio.

![Lab Services con arquitectura de recurso compartido](./media/how-to-create-a-lab-with-shared-resource/shared-resource-architecture.png)

## <a name="setup-shared-resource"></a>Configuración del recurso compartido

La red virtual del recurso compartido debe crearse antes que el laboratorio.  Para más información sobre cómo crear una red virtual, consulte [Creación de una red virtual](../virtual-network/quick-create-portal.md).  Es importante planear los intervalos de red virtual para que no se superpongan con la dirección IP de los equipos del laboratorio.  Para más información sobre cómo planear la red, consulte el artículo [Planear redes virtuales](../virtual-network/virtual-network-vnet-plan-design-arm.md). En nuestro ejemplo, el recurso compartido se encuentra en una red virtual con el intervalo 10.2.0.0/16.  Si no lo ha hecho ya, [cree una subred](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) que contenga el recurso compartido.  En el ejemplo, usamos el intervalo de 10.2.0.0/24, pero el intervalo puede variar en función de las necesidades de la red.

El recurso compartido puede ser software que se ejecute en una máquina virtual o en un servicio proporcionado por Azure. El recurso compartido debe estar disponible a través de una dirección IP privada.  Al hacer que el recurso compartido esté disponible solo a través de una dirección IP privada, se limita el acceso a ese recurso compartido.

En el diagrama también se muestra un grupo de seguridad de red (NSG) que puede usarse para restringir el tráfico procedente de la máquina virtual de los alumnos.  Por ejemplo, puede escribir una regla de seguridad que indique que el tráfico procedente de las direcciones IP de las máquinas virtuales de los alumnos solo pueda acceder a un recurso compartido y nada más.  Para más información sobre cómo establecer reglas de seguridad, consulte [Administración de los grupos de seguridad de red](../virtual-network/manage-network-security-group.md#work-with-security-rules). Si desea restringir el acceso a un recurso compartido a un laboratorio específico, obtenga la dirección IP del laboratorio desde la [configuración de laboratorio de la cuenta de laboratorio](manage-labs.md#view-labs-in-a-lab-account) y establezca una regla de entrada para permitir el acceso solo desde esa dirección IP.  No olvide habilitar los puertos de 49152 a 65535 para esa dirección IP.  Opcionalmente, puede encontrar la dirección IP privada de las máquinas virtuales de los alumnos con la [página del grupo de máquinas virtuales](how-to-set-virtual-machine-passwords.md).

Si el recurso compartido es una máquina virtual de Azure que ejecuta el software necesario, es posible que tenga que modificar las reglas de firewall predeterminadas para la máquina virtual.

### <a name="tips-for-shared-resources---license-server"></a>Sugerencias para recursos compartidos: servidor de licencias
Uno de los recursos compartidos más comunes es un servidor de licencias; aquí encontrará algunas sugerencias sobre cómo configurar uno de ellos correctamente.
#### <a name="server-region"></a>Región del servidor
El servidor de licencias deberá estar conectado a la red virtual que está emparejada con el laboratorio, por lo que el servidor de licencias debe estar ubicado en la misma región que la cuenta de laboratorio.

#### <a name="static-private-ip-and-mac-address"></a>Dirección MAC e IP privada estática
De forma predeterminada, las máquinas virtuales tienen una dirección IP privada dinámica, por lo que [antes de configurar cualquier software debe establecer la dirección IP privada como estática](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). Esto configura la dirección IP privada y la dirección MAC para que sean estáticas.  

#### <a name="control-access"></a>Control de acceso
Controlar el acceso al servidor de licencias es un paso clave.  Una vez que la VM está configurada, el acceso seguirá siendo necesario para realizar trabajos de mantenimiento, solucionar problemas e instalar actualizaciones.  Estas son algunas formas de hacerlo.
- [Configurar el acceso Just-in-Time (JIT) en Azure Security Center.](../security-center/security-center-just-in-time.md?tabs=jit-config-asc%252cjit-request-asc)
- [Configurar un grupo de seguridad de red para restringir el acceso.](../virtual-network/network-security-groups-overview.md)
- [Configurar Bastion para permitir el acceso seguro al servidor de licencias.](https://azure.microsoft.com/services/azure-bastion/)

## <a name="lab-account"></a>Cuenta de laboratorio

Para usar un recurso compartido, la cuenta de laboratorio debe estar configurada para usar una [red virtual emparejada](how-to-connect-peer-virtual-network.md).  En este caso, se emparejará con la red virtual que contiene el recurso compartido.

>[!WARNING]
>El laboratorio de la clase se debe crear **después** de que la cuenta de laboratorio esté emparejada con la red virtual del recurso compartido.  
Máquina de plantilla

Una vez que la cuenta de laboratorio esté emparejada a la red virtual, la máquina de plantilla debería tener acceso al recurso compartido.  Es posible que tenga que actualizar las reglas de firewall, en función del recurso compartido al que se acceda.