---
title: Sincronización de la configuración de red para Azure App Service
titleSuffix: Azure SQL Managed Instance
description: En este artículo se describe cómo sincronizar la configuración de red para el plan de hospedaje de Azure App Service con Instancia administrada de Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: mathoma, bonova
ms.date: 12/13/2018
ms.openlocfilehash: d1421b84c9156862f25c6dcf35b70b42dd8f2862
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130239373"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>Sincronización de la configuración de red para el plan de hospedaje de Azure App Service con Instancia administrada de Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Puede suceder que, aunque haya [integrado la aplicación con una red de Azure Virtual Network](../../app-service/overview-vnet-integration.md), no pueda establecer una conexión con Instancia administrada de SQL. Este problema se puede resolver actualizando o sincronizando la configuración de red para el plan de servicio. 

## <a name="sync-network-configuration"></a>Sincronización de la configuración de red 

Para ello, sigue estos pasos:  

1. Vaya al plan de App Service de las aplicaciones web.

   ![Captura de pantalla de Plan de App Service](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. Seleccione **Redes** y, después, **Haga clic aquí para administrar**.

   ![Captura de pantalla de administración de un plan de servicio](./media/azure-app-sync-network-configuration/manage-plan.png)

3. Seleccione **Red virtual** y haga clic en **Sincronizar red**.

   ![Captura de pantalla de una sincronización de red](./media/azure-app-sync-network-configuration/sync.png)

4. Espere hasta que se realice la sincronización.
  
   ![Captura de pantalla de una sincronización completada](./media/azure-app-sync-network-configuration/sync-done.png)

Ya está preparado para intentar volver a establecer la conexión con Instancia administrada de SQL.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre cómo configurar la red virtual para Instancia administrada de SQL, vea [Arquitectura de conectividad de la red virtual de Instancia administrada de SQL](connectivity-architecture-overview.md) y [Configuración de una red virtual existente](vnet-existing-add-subnet.md).