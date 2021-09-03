---
title: Conexión a Synapse Studio mediante vínculos privados
description: En este artículo se explica cómo conectarse a Azure Synapse Studio mediante vínculos privados.
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 967d7462ebdbbeab9d0653914dc0640af453b7ee
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "112291896"
---
# <a name="connect-to-azure-synapse-studio-using-azure-private-link-hubs"></a>Conexión a Azure Synapse Studio con centros de Azure Private Link 

En este artículo se explica cómo se usan los vínculos privados de los centros de Private Link de Azure Synapse Analytics para conectarse de forma segura a Synapse Studio. 

## <a name="azure-private-link-hubs"></a>Centros de Azure Private Link 
Puede conectarse de forma segura a Azure Synapse Studio desde la red virtual de Azure mediante vínculos privados. Los centros de Private Link de Azure Synapse Analytics son recursos de Azure que funcionan como conectores entre la red protegida y la experiencia web de Synapse Studio. 

Para conectarse a Synapse Studio mediante vínculos privados se necesitan dos pasos. En primer lugar, debe crear un recurso de centros de vínculo privado. En segundo lugar, debe crear un punto de conexión privado entre la red virtual de Azure y este centro de vínculo privado. Después, puede usar puntos de conexión privados para comunicarse de forma segura con Synapse Studio. Los puntos de conexión privados se deben integrar con la solución DNS, ya sea con la solución local o con DNS privado de Azure. 

## <a name="azure-private-links-hubs-and-azure-synapse-studio"></a>Centros de Azure Private Link y Azure Synapse Studio
Puede usar un único recurso de centro de vínculo privado de Azure Synapse para conectarse de forma privada a todas las áreas de trabajo de Azure Synapse Analytics mediante Azure Synapse Studio. Las áreas de trabajo no tienen que estar en la misma región que el centro de vínculo privado de Azure Synapse. El recurso de centro de vínculo privado de Azure Synapse también se puede usar para las conexiones a las áreas de trabajo de Synapse en distintas suscripciones o inquilinos de Azure AD.

Para crear el centro de vínculo privado, busque *Centros de Private Link de Synapse* en Azure Portal y seleccione **Azure Synapse Analytics (centros de Private Link)** en Servicios. Para más información, siga los pasos de la guía para [conectarse a los recursos del área de trabajo desde una red restringida](./how-to-connect-to-workspace-from-restricted-network.md).

>[!NOTE]
>Los centros de Private Link están diseñados para cargar de forma segura el contenido estático de Synapse Studio mediante vínculos privados. Debe crear **puntos de conexión privados independientes** a los recursos a los que quiera conectarse en el área de trabajo, como grupos de SQL aprovisionados o dedicados, o grupos de Spark. 

## <a name="azure-private-links-hubs-and-azure-virtual-network"></a>Centros de Azure Private Link y Azure Virtual Network
Debe conectar su instancia de Azure Virtual Network al recurso de centro de vínculo privado de Synapse para proteger la conexión de un extremo a otro a Synapse Studio. Para ello, debe crear un punto de conexión privado entre la red virtual y el centro de vínculo privado que creó. Puede usar Azure Portal con el centro de vínculo privado e ir a la sección Punto de conexión privado. Seleccione "+ Punto de conexión privado" para crear un punto de conexión privado que se conecte a su centro de vínculo privado.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-private-endpoint.png" alt-text="Captura de pantalla que muestra la página de conexiones de punto de conexión privado.":::

Asegúrese de elegir el tipo de recurso "Microsoft. Synapse/privateLinkHubs" en la pestaña "Recurso".

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-resource-type.png" alt-text="Captura de pantalla que muestra la página &quot;Crear un punto de conexión privado&quot; con &quot;Tipo de recurso&quot; resaltado.":::

Al realizar la integración con la red virtual y la zona DNS privada, en la pestaña "Configuración", seleccione "privatelink.azuresynapse.net" para las zonas DNS privadas.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-dns-zones.png" alt-text="Creación de un punto de conexión privado a un centro de vínculo privado":::

## <a name="next-steps"></a>Pasos siguientes

[Conexión a los recursos de un área de trabajo desde una red restringida](./how-to-connect-to-workspace-from-restricted-network.md)

Más información sobre [Red virtual del área de trabajo administrada](./synapse-workspace-managed-vnet.md)

Más información sobre los [puntos de conexión privados administrados](./synapse-workspace-managed-private-endpoints.md)

[Creación de puntos de conexión privados administrados a los orígenes de datos](./how-to-create-managed-private-endpoints.md)

[Conexión al área de trabajo de Synapse mediante puntos de conexión privados](./how-to-connect-to-workspace-with-private-links.md)

