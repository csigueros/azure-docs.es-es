---
title: Puntos de conexión privados administrados
description: En este artículo se explican los puntos de conexión privados administrados en Azure Synapse Analytics.
author: ashinMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 01/12/2020
ms.author: seshin
ms.reviewer: jrasnick
ms.openlocfilehash: 0be33ec2e9815585bfbd810be00cb12a001bd26f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729707"
---
# <a name="synapse-managed-private-endpoints"></a>Puntos de conexión privados administrados de Synapse

En este artículo se explican los puntos de conexión privados administrados en Azure Synapse Analytics.

## <a name="managed-private-endpoints"></a>Puntos de conexión privados administrados

Los puntos de conexión privados administrados son puntos de conexión privados creados en una red virtual administrada asociada al área de trabajo de Azure Synapse. Los puntos de conexión privados administrados establecen un vínculo privado a los recursos de Azure. Azure Synapse administra estos puntos de conexión privados en su nombre. Puede crear puntos de conexión privados administrados desde el área de trabajo de Azure Synapse para acceder a los servicios de Azure (por ejemplo, Azure Storage o Azure Cosmos DB) y a los servicios de asociados o clientes hospedados en Azure.

Al usar puntos de conexión privados administrados, el tráfico entre el área de trabajo de Azure Synapse y otros recursos de Azure atraviesa completamente la red troncal de Microsoft. Los puntos de conexión privados administrados protegen contra la filtración de datos. Un punto de conexión privado administrado emplea la dirección IP privada de la red virtual administrada para, de forma efectiva, incorporar a la red virtual el servicio de Azure con el que se comunica el área de trabajo de Azure Synapse. Los puntos de conexión privados administrados se asignan a recursos específicos de Azure, no a todo el servicio. Los clientes pueden limitar la conectividad a un recurso específico aprobado por su organización. 

Más información sobre [vínculos privados y puntos de conexión privados](../../private-link/index.yml).

>[!IMPORTANT]
>Los puntos de conexión privados administrados solo se admiten en áreas de trabajo de Azure Synapse con una red virtual de área de trabajo administrada.

>[!NOTE]
>Al crear un área de trabajo de Azure Synapse, puede elegir asociarle una red virtual administrada. Si decide hacerlo, puede también elegir limitar el tráfico saliente del área de trabajo a solo los destinos aprobados. Para estos destinos, debe crear puntos de conexión privados administrados. 


Una conexión de punto de conexión privado se crea en un estado "pendiente" cuando se crea un punto de conexión privado administrado en Azure Synapse. Se inicia un flujo de trabajo de aprobación. El propietario del recurso de vínculo privado es responsable de aprobar o rechazar la conexión. Si el propietario aprueba la conexión, se establece el vínculo privado. Sin embargo, si el propietario no aprueba la conexión, no se establecerá el vínculo privado. En cualquier caso, el punto de conexión privado administrado se actualizará con el estado de la conexión. Solo se puede usar un punto de conexión privado administrado con un estado aprobado para enviar tráfico al recurso de vínculo privado que está vinculado al punto de conexión privado administrado.

## <a name="managed-private-endpoints-for-dedicated-sql-pool-and-serverless-sql-pool"></a>Puntos de conexión privados administrados para el grupo de SQL dedicado y el grupo de SQL sin servidor

El grupo de SQL dedicado y el grupo de SQL sin servidor representan funciones de análisis del área de trabajo de Azure Synapse. Estas funcionalidades usan una infraestructura de varios inquilinos que no está implementada en la [red virtual de área de trabajo administrada](./synapse-workspace-managed-vnet.md).

Cuando se crea un área de trabajo, Azure Synapse crea en ella dos puntos de conexión privados administrados, uno para el grupo de SQL dedicado y otro para el grupo de SQL sin servidor. 

Estos dos puntos de conexión privados administrados aparecen en Synapse Studio. Seleccione **Administrar** en el panel de navegación izquierdo y, luego, elija **Punto de conexión privados administrados** para verlos en Studio.

El punto de conexión privado administrado cuyo destino es un grupo de SQL se llama *synapse-ws-sql--\<workspacename\>* y el que tiene como destino el grupo de SQL sin servidor se llama *synapse-ws-sqlOnDemand--\<workspacename\>* .

![Puntos de conexión privados administrados para el grupo de SQL dedicado y el grupo de SQL sin servidor](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Estos dos puntos de conexión privados administrados se crean automáticamente al crear el área de trabajo de Azure Synapse. No se le cobra por ellos.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, avance al artículo [Creación de puntos de conexión privados administrados al origen de datos](./how-to-create-managed-private-endpoints.md).