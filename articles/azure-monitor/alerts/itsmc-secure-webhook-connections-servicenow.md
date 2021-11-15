---
title: 'Conector de Administración de servicios de TI: exportación segura en Azure Monitor: configuración con ServiceNow'
description: En este artículo se muestra cómo conectar los productos o servicios de ITSM con ServiceNow en Exportación segura de Azure Monitor.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 6b65c0bfb3781a5f78c92f533e126c640347f06d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455271"
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
   * [Roma](https://docs.servicenow.com/bundle/rome-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [Quebec](https://docs.servicenow.com/bundle/quebec-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [París](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/concept/azure-integration.html)
