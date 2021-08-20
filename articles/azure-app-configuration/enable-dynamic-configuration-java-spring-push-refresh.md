---
title: 'Tutorial: Uso de la configuración dinámica mediante una actualización de inserción en una instancia única de la aplicación de Java Spring'
titleSuffix: Azure App Configuration
description: En este tutorial aprenderá a actualizar dinámicamente los datos de configuración de una aplicación de Java Spring mediante una actualización de inserción.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: mametcal
ms.openlocfilehash: 9f9f5bff0cc0c1e70178cab1e1b0e29ffbd8fc90
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114479709"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-java-spring-app"></a>Tutorial: Uso de la configuración dinámica mediante una actualización de inserción en una aplicación de Java Spring

La biblioteca cliente de Java Spring de App Configuration admite la actualización de la configuración a petición sin provocar el reinicio de una aplicación. Se puede configurar una aplicación para que detecte cambios en App Configuration mediante uno de los dos métodos siguientes o ambos.

- Modelo de sondeo: este es el comportamiento predeterminado, que usa el sondeo para detectar los cambios en la configuración. Una vez que el valor almacenado en caché de una configuración expira, la siguiente llamada a `AppConfigurationRefresh` o `refreshConfigurations` envía una solicitud al servidor para comprobar si la configuración ha cambiado y extrae la configuración actualizada si es necesario.

- Modo de inserción: este modo utiliza [eventos de App Configuration](./concept-app-configuration-event.md) para detectar los cambios en la configuración. Una vez que App Configuration está configurado para enviar eventos de cambio de valor de clave a Event Grid con un [webhook](/azure/event-grid/handler-event-hubs), la aplicación puede usar estos eventos para optimizar el número total de solicitudes necesarias para mantener la configuración actualizada.

Este tutorial le muestra cómo puede implementar las actualizaciones de configuración dinámica en el código mediante una actualización de inserción. Se basa en la aplicación que se introdujo en los inicios rápidos. Antes de continuar, termine [Creación de una aplicación de Java Spring con Azure App Configuration](./quickstart-java-spring-app.md).

Para realizar los pasos de este tutorial, puede usar cualquier editor de código. [Visual Studio Code](https://code.visualstudio.com/) es una excelente opción que está disponible en las plataformas Windows, macOS y Linux.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar una suscripción para enviar eventos de cambio de configuración desde App Configuration a un webhook.
> * Implementar una aplicación de Spring Boot en App Service.
> * Configurar la aplicación de Java Spring para actualizar su configuración en respuesta a los cambios en App Configuration.
> * Consuma en la aplicación la configuración más reciente.

## <a name="prerequisites"></a>Prerrequisitos

- Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/)
- Un [kit de desarrollo de Java (JDK)](/java/azure/jdk) admitido, versión 8.
- [Apache Maven](https://maven.apache.org/download.cgi), versión 3.0 o posterior.
- Un almacén de Azure App Configuration existente.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="setup-push-refresh"></a>Configuración de la actualización de inserción

1. Abra *pom.xml* y actualice el archivo con las dependencias siguientes.

   ```xml
           <dependency>
               <groupId>com.azure.spring</groupId>
               <artifactId>azure-spring-cloud-appconfiguration-config-web</artifactId>
               <version>2.0.0</version>
           </dependency>
   
           <!-- Adds the Ability to Push Refresh -->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-actuator</artifactId>
           </dependency>
   ```

1. Configure la [implementación de Maven en App Service](/azure/app-service/quickstart-java?tabs=javase) de manera que la aplicación se pueda implementar en Azure App Service mediante Maven.

   ```console
   mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
   ```

1. Abra bootstrap.properties y configure la actuación de inserción de Azure App Configuration y Azure Service Bus

   ```properties
   # Azure App Configuration Properties
   spring.cloud.azure.appconfiguration.stores[0].connection-string= ${AppConfigurationConnectionString}
   spring.cloud.azure.appconfiguration.stores[0].monitoring.enabled= true
   spring.cloud.azure.appconfiguration.stores[0].monitoring.cacheExpiration= 30d
   spring.cloud.azure.appconfiguration.stores[0].monitoring.triggers[0].key= sentinel
   spring.cloud.azure.appconfiguration.stores[0].monitoring.push-notification.primary-token.name= myToken
   spring.cloud.azure.appconfiguration.stores[0].monitoring.push-notification.primary-token.secret= myTokenSecret
   
   management.endpoints.web.exposure.include= "appconfiguration-refresh"
   ```

Se agrega un retraso aleatorio antes de que el valor almacenado en caché se marque como modificado para reducir la posible limitación. El retraso máximo predeterminado antes de que el valor en caché se marque como modificado es de 30 segundos.

> [!NOTE]
> El nombre del token principal debe almacenarse en App Configuration como clave y, para mayor seguridad, el secreto del token principal, como referencia de Key Vault en App Configuration.

## <a name="build-and-run-the-app-locally"></a>Compilación y ejecución de la aplicación en un entorno local

Al crease los webhooks de Event Grid deben validarse. Puede realizar la validación siguiendo esta [guía](/azure/event-grid/webhook-event-delivery)o iniciando la aplicación con la biblioteca web de Spring de Azure App Configuration ya configurada, lo cual registrará la aplicación automáticamente. Para usar una suscripción a eventos, siga los pasos de las dos secciones siguientes.

1. Establezca la variable de entorno en la cadena de conexión de la instancia de App Configuration:

    #### <a name="windows-command-prompt"></a>[Símbolo del sistema de Windows](#tab/cmd)

    ```cmd
    setx AppConfigurationConnectionString <connection-string-of-your-app-configuration-store>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AppConfigurationConnectionString = <connection-string-of-your-app-configuration-store>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AppConfigurationConnectionString = <connection-string-of-your-app-configuration-store>
    ```

1. Ejecute el siguiente comando para compilar la aplicación de consola:

   ```shell
    mvn package
   ```

1. Una vez que la compilación se haya realizado correctamente, ejecute el siguiente comando para ejecutar la aplicación localmente:

    ```shell
    mvn spring-boot:deploy
    ```

## <a name="set-up-an-event-subscription"></a>Configuración de una suscripción a eventos

1. Abra el recurso de App Configuration en Azure Portal y, a continuación, haga clic en `+ Event Subscription` (+ Suscripción de eventos) en el panel `Events` (Eventos).

    :::image type="content" source="./media/events-pane.png" alt-text="El panel de eventos tiene una opción para crear nuevas suscripciones." :::

1. Escriba un nombre para los campos `Event Subscription` (Suscripción de eventos) y `System Topic` (Tema del sistema). De forma predeterminada, se establecen los tipos de eventos clave-valor modificados y eliminados; pero se puede cambiar además del uso de la pestaña Filtros para elegir los motivos exactos por los que se envía un evento de inserción.

    :::image type="content" source="./media/create-event-subscription.png" alt-text="Los eventos requieren un nombre, un tema y filtros." :::

1. Seleccione `Endpoint Type` como `Web Hook` y `Select an endpoint`.

    :::image type="content" source="./media/event-subscription-webhook-endpoint.png" alt-text="Al seleccionar Punto de conexión, se crea una hoja para escribir el URI del punto de conexión." :::

1. El punto de conexión es el URI de la aplicación + "/actuator/appconfiguration-refresh?{nombre-del-token}={secreto-del-token}". Por ejemplo, `https://my-azure-webapp.azurewebsites.net/actuator/appconfiguration-refresh?myToken=myTokenSecret`.

1. Haga clic en `Create` (Crear) para crear la suscripción de eventos. Cuando se selecciona `Create`, se envía una solicitud de registro para el webhook a la aplicación. Esto lo recibe la biblioteca cliente de Azure App Configuration, lo comprueba y devuelve una respuesta válida.

1. Haga clic en `Event Subscriptions` (Suscripciones de eventos) en el panel `Events` (Eventos) para validar que la suscripción se ha creado correctamente.

    :::image type="content" source="./media/event-subscription-view-webhook.png" alt-text="El webhook se muestra en una tabla en la parte inferior de la página." :::

> [!NOTE]
> Al suscribirse a los cambios de configuración, se pueden usar uno o varios filtros para reducir el número de eventos que se envían a la aplicación. Se pueden configurar como [filtros de suscripción de Event Grid](/azure/event-grid/event-filtering.md) o [filtros de suscripción de Service Bus](/azure/service-bus-messaging/topic-filters.md). Por ejemplo, un filtro de suscripción se puede utilizar para suscribirse solo a eventos de cambios en una clave que empieza por una cadena específica.

## <a name="verify-and-test-application"></a>Comprobación y prueba de la aplicación

1. Una vez que se está ejecutando la aplicación, puede usar *curl* para probarla, por ejemplo:

   ```cmd
   curl -X GET http://localhost:8080
   ```

1. Abra **Azure Portal** y vaya al recurso de App Configuration asociado a la aplicación. Seleccione **Explorador de configuración** en **Operaciones** y actualice los valores de las claves siguientes:

    | Clave | Value |
    |---|---|
    | application/config.message | Hello: actualizado |

1. Actualice la página del explorador para ver el nuevo mensaje que se muestra.

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha habilitado la aplicación de Java para actualizar dinámicamente la configuración desde App Configuration. Para obtener información sobre cómo usar una identidad administrada de Azure para simplificar el acceso a App Configuration, vaya al siguiente tutorial.

> [!div class="nextstepaction"]
> [Integración de identidades administradas](./howto-integrate-azure-managed-service-identity.md)
