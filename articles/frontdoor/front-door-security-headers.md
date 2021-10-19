---
title: 'Tutorial: Adición de encabezados de seguridad con el motor de reglas: Azure Front Door'
description: En este tutorial se enseña a configurar un encabezado de seguridad a través del motor de reglas en Azure Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 48f70bd0cd71ffc517d7955f645c579f1ffa2b48
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714859"
---
# <a name="tutorial-add-security-headers-with-rules-engine"></a>Tutorial: Agregar encabezados de seguridad con el motor de reglas

En este tutorial se indica cómo implementar encabezados de seguridad para evitar vulnerabilidades basadas en el explorador como HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy o X-Frame-Options. Los atributos basados en seguridad también se pueden definir con cookies.

En el ejemplo siguiente se muestra cómo agregar un encabezado Content-Security-Policy a todas las solicitudes entrantes que coinciden con la ruta de acceso definida en la ruta a la que está asociada la configuración del motor de reglas. En este caso, solo se permiten la ejecución de scripts de nuestro sitio de confianza, **https://apiphany.portal.azure-api.net** , en nuestra aplicación.

En este tutorial, aprenderá a:
> [!div class="checklist"]
> - Configurar una directiva de seguridad de contenido en el motor de reglas.

## <a name="prerequisites"></a>Prerrequisitos

* Para poder completar los pasos de este tutorial, primero debe crear una instancia de Front Door. Para más información, consulte [Inicio rápido: Cree una instancia de Front Door](quickstart-create-front-door.md).
* Si esta es la primera vez que usa la característica del motor de reglas, consulte [Configuración de un motor de reglas](front-door-tutorial-rules-engine.md).

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Adición de un encabezado Content-Security-Policy en Azure Portal

1. Haga clic en **Agregar** para agregar una regla nueva. Proporcione un nombre para la regla y haga clic en **Agregar una acción** > **Encabezado de respuesta**.

1. Establezca Operador en **Anexar** para agregar este encabezado como respuesta a todas las solicitudes entrantes para esta ruta.

1. Agregue el nombre del encabezado: **Content-Security-Policy** y defina los valores que debe aceptar este encabezado. En este escenario, hemos elegido *"script-src 'self' https://apiphany.portal.azure-api.net".*

   > [!NOTE]
   > Los valores del encabezado están limitados a 640 caracteres.

1. Una vez que haya agregado todas las reglas deseadas a su configuración, no olvide ir a su ruta preferida y asociar la configuración del motor de reglas a la regla de ruta. Este paso es necesario para habilitar la regla para que funcione. 

![ejemplo del portal](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> En este escenario, no hemos agregado [condiciones de coincidencia](front-door-rules-engine-match-conditions.md) a la regla. Esta regla se aplicará a todas las solicitudes entrantes que coincidan con la ruta de acceso definida en la regla de ruta. Si desea que solo se aplique a un subconjunto de esas solicitudes, asegúrese de agregar las **condiciones de coincidencia** específicas a esta regla.

## <a name="clean-up-resources"></a>Limpieza de recursos

En los pasos anteriores, configuró encabezados de seguridad con el motor de reglas. Si ya no desea la regla, puede quitarla haciendo clic en Eliminar regla.

:::image type="content" source="./media/front-door-rules-engine/rules-engine-delete-rule.png" alt-text="Eliminar regla":::

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo configurar un firewall de aplicaciones web para Front Door, continúe con el siguiente tutorial.

> [!div class="nextstepaction"]
> [Firewall de aplicaciones web y Front Door](front-door-waf.md)
