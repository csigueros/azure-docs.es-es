---
title: Conexión a una cuenta de almacenamiento segura desde un área de trabajo de Azure Synapse
description: En este artículo se explica cómo conectarse a una cuenta de almacenamiento segura desde un área de trabajo de Azure Synapse.
author: ashinMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 02/10/2021
ms.author: seshin
ms.reviewer: jrasnick
ms.openlocfilehash: 11127453c67a41dd4b5f8677d02a10f749f516f9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745042"
---
# <a name="connect-to-a-secure-azure-storage-account-from-your-synapse-workspace"></a>Conexión a una cuenta de almacenamiento de Azure segura desde un área de trabajo de Synapse

En este artículo se explica cómo conectarse a una cuenta de almacenamiento de Azure segura desde un área de trabajo de Azure Synapse. Puede vincular una cuenta de almacenamiento de Azure a su área de trabajo de Synapse al crear el área de trabajo. Puede vincular más cuentas de almacenamiento después de crear el área de trabajo.


## <a name="secured-azure-storage-accounts"></a>Cuentas de almacenamiento de Azure protegidas
Azure Storage proporciona un modelo de seguridad por capas que permite proteger y controlar el acceso a las cuentas de almacenamiento. Puede configurar reglas de firewall de IP para que el tráfico desde intervalos de direcciones IP públicas seleccionados tenga acceso a su cuenta de almacenamiento. Además, puede configurar reglas de red para que el tráfico desde redes virtuales seleccionadas tenga acceso a su cuenta de almacenamiento. Puede combinar reglas de firewall de IP que permitan el acceso desde intervalos de direcciones IP seleccionados, y reglas de red que concedan acceso desde redes virtuales seleccionadas en la misma cuenta de almacenamiento. Estas reglas se aplican al punto de conexión público de una cuenta de almacenamiento. No necesita ninguna regla de acceso para permitir el tráfico desde puntos de conexión privados administrados creados en el área de trabajo hacia una cuenta de almacenamiento. Las reglas de firewall de almacenamiento se pueden aplicar a cuentas de almacenamiento existentes, o a nuevas cuentas de almacenamiento al crearlas. Puede obtener más información sobre la protección de cuentas de almacenamiento [aquí](../../storage/common/storage-network-security.md).

## <a name="synapse-workspaces-and-virtual-networks"></a>Áreas de trabajo de Synapse y redes virtuales
Al crear un área de trabajo de Synapse, puede elegir habilitar una red virtual administrada para asociarla. Si no habilita la red virtual administrada para el área de trabajo al crearla, el área de trabajo se encuentra en una red virtual compartida junto con otras áreas de trabajo de Synapse que no tienen asociada una red virtual administrada. Si ha habilitado la red virtual administrada al crear el área de trabajo, el área de trabajo está asociada a una red virtual dedicada administrada por Azure Synapse. Estas redes virtuales no se crean en la suscripción del cliente. Por lo tanto, no se podrá conceder al tráfico desde estas redes virtuales acceso a la cuenta de almacenamiento protegida mediante las reglas de red descritas anteriormente.  

## <a name="access-a-secured-storage-account"></a>Acceso a una cuenta de almacenamiento protegida
Synapse funciona desde redes que no se pueden incluir en las reglas de red. Debe hacer lo siguiente para permitir el acceso desde el área de trabajo a la cuenta de almacenamiento segura.

* Cree un área de trabajo de Azure Synapse con una red virtual administrada asociada a ella y, a partir de esta, cree puntos de conexión privados administrados a la cuenta de almacenamiento segura.
* Conceda a su área de trabajo de Azure Synapse acceso a su cuenta de almacenamiento segura como servicio de Azure de confianza. Como servicio de confianza, Azure Synapse usará una autenticación sólida para conectarse a su cuenta de almacenamiento de forma segura.   

### <a name="create-a-synapse-workspace-with-a-managed-virtual-network-and-create-managed-private-endpoints-to-your-storage-account"></a>Creación de un área de trabajo de Synapse con una red virtual administrada y creación de puntos de conexión privados administrados a la cuenta de almacenamiento
Puede seguir [estos pasos](./synapse-workspace-managed-vnet.md) para crear un área de trabajo de Synapse que tenga asociada una red virtual administrada. Una vez que se cree el área de trabajo con una red virtual administrada asociada, puede crear un punto de conexión privado administrado para la cuenta de almacenamiento segura siguiendo los pasos que se indican [aquí](./how-to-create-managed-private-endpoints.md). 

### <a name="grant-your-azure-synapse-workspace-access-to-your-secure-storage-account-as-a-trusted-azure-service"></a>Otorgamiento al área de trabajo de Azure Synapse acceso a la cuenta de almacenamiento segura como servicio de Azure de confianza
Las funcionalidades analíticas, como el grupo de SQL dedicado y el grupo de SQL sin servidor, usan una infraestructura multiinquilino que no se implementa en la red virtual administrada. Para que el tráfico de estas funcionalidades acceda a la cuenta de almacenamiento protegida, debe configurar el acceso a la cuenta de almacenamiento en función de la identidad administrada asignada por el sistema del área de trabajo siguiendo los pasos a continuación.

En Azure Portal, vaya a la cuenta de almacenamiento protegida. En el panel de navegación izquierdo, seleccione **Redes**. En la sección **Resource instances** (Instancias de recursos), seleccione *Microsoft.Synapse/workspaces* como **Tipo de recurso** y escriba el nombre del área de trabajo como **Nombre de instancia**. Seleccione **Guardar**.

![Configuración de red de la cuenta de almacenamiento.](./media/connect-to-a-secure-storage-account/secured-storage-access.png)

Ahora debería poder acceder a la cuenta de almacenamiento protegida desde el área de trabajo.


## <a name="next-steps"></a>Pasos siguientes

Más información sobre la [red virtual del área de trabajo administrada](./synapse-workspace-managed-vnet.md).

Más información sobre los [puntos de conexión privados administrados](./synapse-workspace-managed-private-endpoints.md).