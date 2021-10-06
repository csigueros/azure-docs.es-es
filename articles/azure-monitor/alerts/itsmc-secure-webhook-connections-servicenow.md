---
title: 'Conector de Administración de servicios de TI: exportación segura en Azure Monitor: configuración con ServiceNow'
description: En este artículo se muestra cómo conectar los productos o servicios de ITSM con ServiceNow en Exportación segura de Azure Monitor.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 20cfd57ea759138ff3e266632eb87b9f90669934
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129426480"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Conexión de ServiceNow a Azure Monitor

En las secciones siguientes se proporcionan detalles sobre cómo conectar el producto ServiceNow y Exportación segura en Azure.

## <a name="prerequisites"></a>Requisitos previos

Asegúrese de que se cumplen los requisitos previos siguientes:

* Azure AD está registrado.
* Tiene la versión compatible de ServiceNow Event Management, o ITOM (versión Nueva York o posterior).
* [Aplicación](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ac4c9c57dbb1d090561b186c1396191a/1.3.1?referer=%2Fstore%2Fsearch%3Flistingtype%3Dallintegrations%25253Bancillary_app%25253Bcertified_apps%25253Bcontent%25253Bindustry_solution%25253Boem%25253Butility%26q%3DEvent%2520Management%2520Connectors&sl=sh) instalada en la instancia de ServiceNow.

## <a name="configure-the-servicenow-connection"></a>Configuración de la conexión de ServiceNow

1. Use el vínculo https://(nombre de instancia).service-now.com/api/sn_em_connector/em/inbound_event?source=azuremonitor del URI de la definición de exportación segura.

2. Siga las instrucciones correspondientes a la versión:
   * [Quebec](https://docs.servicenow.com/bundle/quebec-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [París](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/concept/azure-integration.html)
