---
title: Exportación de las API de Azure API Management a Microsoft Power Platform | Microsoft Docs
description: Obtenga información sobre cómo exportar una API de API Management como un conector personalizado a Power Apps y Power Automate en Microsoft Power Platform.
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: how-to
ms.date: 07/27/2021
ms.author: apimpm
ms.openlocfilehash: 6560995ba770e01c910c2b8fc923c9fcbad6f3d3
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178239"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>Exportación de API de Azure API Management a Power Platform 

Los desarrolladores civiles que usan [Power Platform](https://powerplatform.microsoft.com) a menudo necesitan acceder a las funcionalidades empresariales que desarrollan los desarrolladores profesionales y se implementan en Azure. [Azure API Management](https://aka.ms/apimrocks) permite a los desarrolladores profesionales publicar su servicio back-end como API y exportar fácilmente estas API a Power Platform ([Power Apps](/powerapps/powerapps-overview) y [Power Automate](/power-automate/getting-started)) como conectores personalizados para que los desarrolladores civiles los detecten y los usen. 

Este artículo le indica cómo crear en Azure Portal un conector de Power Platform personalizado a una API en API Management. Con esta funcionalidad, los desarrolladores civiles pueden usar Power Platform para crear y distribuir aplicaciones basadas en API internas y externas administradas por API Management.

## <a name="prerequisites"></a>Requisitos previos

+ Complete el siguiente inicio rápido: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md)
+ Asegúrese de que haya una API en la instancia de API Management que desea exportar a Power Platform.
+ Asegúrese de que tiene un [entorno](/powerapps/powerapps-overview#power-apps-for-admins) de Power Apps o Power Automate. 

## <a name="create-a-custom-connector-to-an-api"></a>Creación de un conector personalizado a una API

1. Vaya al servicio API Management en Azure Portal.
1. En el menú, en **API**, seleccione **Power Platform**.
1. Seleccione **Creación de un conector**
1. En la ventana **Creación de un conector**, haga lo siguiente:
    1. Seleccione una API para publicar en Power Platform.
    1. Seleccione un entorno de Power Platform donde publicar la API. 
    1. Introduzca un nombre para mostrar, que se usará como nombre del conector personalizado.  
    1. Opcionalmente, si la API está [protegida por un servidor OAuth 2.0](api-management-howto-protect-backend-with-aad.md), proporcione detalles que incluyan **id. de cliente**, **secreto de cliente**, **URL de autorización**, **URL del token** y **URL de actualización**.  
1. Seleccione **Crear**. 

    :::image type="content" source="media/export-api-power-platform/create-custom-connector.png" alt-text="Creación de un conector personalizado a la API en API Management":::

Una vez creado el conector, vaya al entorno de [Power Apps](https://make.powerapps.com) o de [Power Automate](https://flow.microsoft.com). Verá la API en **Datos > Conectores personalizados**.

:::image type="content" source="media/export-api-power-platform/custom-connector-power-app.png" alt-text="Conector personalizado en Power Platform":::

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre Power Platform](https://powerplatform.microsoft.com/)
* [Más información sobre la creación y el uso de conectores personalizados](/connectors/custom-connectors/)
* [Obtenga información sobre las tareas comunes en API Management siguiendo los tutoriales.](./import-and-publish.md)
