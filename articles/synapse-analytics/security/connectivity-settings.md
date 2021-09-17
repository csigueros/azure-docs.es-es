---
title: Configuración de la conectividad de Azure Synapse
description: En este artículo se muestra cómo establecer la configuración de conectividad en Azure Synapse Analytics.
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 08/05/2021
author: ashinMSFT
ms.author: seshin
ms.reviewer: jrasnick, wiassaf
ms.openlocfilehash: 58086bcbe321b7d50d6f5d0e41a9723dfbe512ed
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861656"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Configuración de conectividad de Azure Synapse Analytics

En este artículo se explicará la configuración de conectividad en Azure Synapse Analytics y cómo establecerla cuando corresponda.

## <a name="public-network-access"></a>Acceso a una red pública 

Puede usar la característica de acceso a la red pública para permitir la conectividad de red pública entrante en el área de trabajo de Azure Synapse. 

- Cuando el acceso a la red pública está **deshabilitado**, solo puede conectarse al área de trabajo mediante [puntos de conexión privados](synapse-workspace-managed-private-endpoints.md). 
- Cuando el acceso a la red pública está **habilitado**, también puede conectarse al área de trabajo desde redes públicas. Puede administrar esta característica durante la creación del área de trabajo y después de esta. 

> [!IMPORTANT]
> Esta característica solo está disponible para las áreas de trabajo de Azure Synapse asociadas con la [red virtual administrada de Azure Synapse Analytics](synapse-workspace-managed-vnet.md). Sin embargo, puede abrir las áreas de trabajo de Synapse a la red pública, independientemente de su asociación con la red virtual administrada. 

Al seleccionar la opción **Deshabilitar**, no se aplicará ninguna regla de firewall que pueda configurar. Además, las reglas de firewall aparecerán en gris en la configuración de Red del portal de Synapse. Las configuraciones de firewall se volverán a aplicar cuando vuelva a habilitar el acceso a la red pública. 

> [!TIP]
> Cuando lo vuelva a habilitar, espere unos momentos antes de editar las reglas de firewall.

### <a name="configure-public-network-access-when-you-create-your-workspace"></a>Configuración del acceso a la red pública al crear el área de trabajo

1.    Seleccione la pestaña **Redes** al crear el área de trabajo en [Azure Portal](https://aka.ms/azureportal).
2.    En Red virtual administrada, seleccione **Habilitar** para asociar el área de trabajo con la red virtual administrada y permitir el acceso a la red pública. 

       :::image type="content" source="./media/connectivity-settings/create-synapse-workspace-managed-virtual-network-1.png" alt-text="Creación de un área de trabajo de Synapse, pestaña de redes, configuración de red virtual administrada" lightbox="media/connectivity-settings/create-synapse-workspace-managed-virtual-network-1.png":::

3. En **Public network access** (Acceso de la red pública), seleccione **Deshabilitar** para denegar el acceso público al área de trabajo. Seleccione **Habilitar** si quiere permitir el acceso público al área de trabajo.

   :::image type="content" source="./media/connectivity-settings/create-synapse-workspace-public-network-access-2.png" alt-text="Creación de un área de trabajo de Synapse, pestaña de redes, configuración de acceso de red pública" lightbox="media/connectivity-settings/create-synapse-workspace-public-network-access-2.png"::: 

4.    Complete el resto del flujo de creación del área de trabajo.

### <a name="configure-public-network-access-after-you-create-your-workspace"></a>Configuración del acceso de red pública después de crear el área de trabajo

1.    Seleccione su área de trabajo de Synapse en [Azure Portal](https://aka.ms/azureportal).
2.    En el panel de navegación izquierdo, seleccione **Redes**.
3.    Seleccione **Deshabilitado** para denegar el acceso público al área de trabajo. Seleccione **Habilitado** si quiere permitir el acceso público al área de trabajo.

       :::image type="content" source="./media/connectivity-settings/synapse-workspace-networking-public-network-access-3.png" alt-text="Área de trabajo de Synapse existente, pestaña de redes, con el acceso de red pública habilitado" lightbox="media/connectivity-settings/synapse-workspace-networking-public-network-access-3.png"::: 

4.    Cuando se deshabilita, las **reglas de firewall** aparecen atenuadas para indicar que no están activas. Se conservarán las configuraciones de la regla de firewall. 

       :::image type="content" source="./media/connectivity-settings/synapse-workspace-networking-firewall-rules-4.png" alt-text="En un área de trabajo de Synapse existente, pestaña redes, la configuración de acceso de red pública está deshabilitada, atención a las reglas de firewall" lightbox="media/connectivity-settings/synapse-workspace-networking-firewall-rules-4.png"::: 
 
5.    Seleccione **Guardar** para guardar el cambio. Una notificación confirmará que la configuración de red se guardó correctamente.

## <a name="connection-policy"></a>Directiva de conexión
La directiva de conexión para Synapse SQL en Azure Synapse Analytics está establecida en *Valor predeterminado*. No es posible cambiarla en Azure Synapse Analytics. Más información [aquí](../../azure-sql/database/connectivity-architecture.md#connection-policy) sobre cómo afecta esto a las conexiones a Synapse SQL en Azure Synapse Analytics. 

## <a name="minimal-tls-version"></a>Versión mínima de TLS
Synapse SQL en Azure Synapse Analytics permite conexiones que utilizan todas las versiones de TLS. No se puede establecer la versión mínima de TLS para Synapse SQL en Azure Synapse Analytics.

## <a name="next-steps"></a>Pasos siguientes

 - Cree un [área de trabajo de Azure Synapse](./synapse-workspace-ip-firewall.md).