---
title: Archivo de inclusión
description: archivo de inclusión
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 09/01/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2584bbcebf01072df93b66248d9674cd69d7b35f
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123454248"
---
## <a name="create-a-namespace-in-the-azure-portal"></a>Creación de un espacio de nombres en Azure Portal
Para empezar a usar entidades de mensajería de Service Bus en Azure, primero hay que crear un espacio de nombres con un nombre que sea único en Azure. Un espacio de nombres proporciona un contenedor con un ámbito para el desvío de recursos de Service Bus en la aplicación.

Para crear un espacio de nombres:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com)
2. En el panel de navegación izquierdo del portal, seleccione sucesivamente **+ Crear un recurso**, **Integración** y **Service Bus**.

    :::image type="content" source="./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png" alt-text="Imagen que muestra la selección de Crear un recurso, Integración y, posteriormente, Service Bus en el menú.":::
3. En la pestaña **Datos básicos** de la página **Crear espacio de nombres**, siga estos pasos: 
    1. En **Suscripción**, elija la suscripción de Azure en la que se va a crear el espacio de nombres.
    1. En **Grupo de recursos**, elija un grupo de recursos existente en el que residirá el espacio de nombres o cree uno.      
    1. Escriba **nombre para el espacio de nombres**. El sistema realiza la comprobación automáticamente para ver si el nombre está disponible. Para obtener una lista de las reglas para asignar nombres a los espacios de nombres, consulte [Creación de API REST de espacio de nombres](/rest/api/servicebus/create-namespace).
    1. En **Ubicación**, elija la región en que se debería hospedar el espacio de nombres. 
    1. En **Plan de tarifa**, seleccione el plan de tarifa (Básico, Estándar o Prémium) del espacio de nombres. Si desea usar [temas y suscripciones](../service-bus-queues-topics-subscriptions.md#topics-and-subscriptions), elija Estándar o Premium. No se admiten temas o suscripciones en el plan de tarifas Básico. Si ha seleccionado el plan de tarifa **Prémium**, especifique el número de **unidades de mensajería**. El plan Premium proporciona aislamiento de recursos en el nivel de CPU y memoria para que cada carga de trabajo se ejecute de forma aislada. Este contenedor de recursos se llama unidad de mensajería. A cada espacio de nombres prémium se le asigna al menos una unidad de mensajería. Puede seleccionar 1, 2 o 4 unidades de mensajería para cada espacio de nombres Premium de Service Bus. Para más información, consulte [Mensajería prémium de Service Bus](../service-bus-premium-messaging.md).
    7. Seleccione **Revisar + crear**. El sistema crea ahora el espacio de nombres del servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras el sistema realiza el aprovisionamiento de los recursos para la cuenta.
   
        :::image type="content" source="./media/service-bus-create-namespace-portal/create-namespace.png" alt-text="Imagen que muestra la página Crear un espacio de nombres":::
    1. En la página **Revisar y crear**, examine la configuración y seleccione **Crear**. 
4. Seleccione **Ir al recurso** en la página de implementación. 

    :::image type="content" source="./media/service-bus-create-namespace-portal/deployment-alert.png" alt-text="Imagen que muestra la página implementación correcta con el vínculo Ir al recurso.":::
6. Verá la página principal del espacio de nombres de Service Bus. 

    :::image type="content" source="./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png" alt-text="Imagen que muestra la página principal del espacio de nombres de Service Bus creado." :::

## <a name="get-the-connection-string"></a>Obtención de la cadena de conexión 
La creación un nuevo espacio de nombres genera automáticamente una regla de firma de acceso compartido (SAS) inicial con un par asociado de claves principal y secundaria en el que ambas conceden control total sobre todos los aspectos del espacio de nombres. Para obtener información acerca de cómo crear reglas adicionales con derechos más restringidos para remitentes y destinatarios normales, consulte [Autenticación y autorización de Service Bus](../service-bus-authentication-and-authorization.md). Para copiar las claves principal y secundaria del espacio de nombres, siga estos pasos: 

1. Haga clic en **Todos los recursos** y, después, en el nombre del espacio de nombres recién creado.
2. En la ventana del espacio de nombres, haga clic en **Directivas de acceso compartido**.
3. En la pantalla **Directivas de acceso compartido**, haga clic en **RootManageSharedAccessKey**.
   
    :::image type="content" source="./media/service-bus-create-namespace-portal/connection-info.png" alt-text="La captura de pantalla muestra la ventana de directivas de acceso compartido con una de ellas resaltada.":::
4. En la ventana **Directiva: RootManageSharedAccessKey**, haga clic en el botón Copiar que hay junto a **Cadena de conexión principal** para copiar la cadena de conexión en el portapapeles para su uso posterior. Pegue este valor en el Bloc de notas o cualquier otra ubicación temporal.
   
    :::image type="content" source="./media/service-bus-create-namespace-portal/connection-string.png" alt-text="La captura de pantalla muestra una directiva de SAS llamada RootManageSharedAccessKey, que incluye claves y cadenas de conexión.":::
5. Repita el paso anterior, copie y pegue el valor de **clave principal** en una ubicación temporal para su uso posterior.

<!--Image references-->

