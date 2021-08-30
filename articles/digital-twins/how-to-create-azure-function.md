---
title: Configuración de una función de Azure para procesar datos
titleSuffix: Azure Digital Twins
description: Consulte cómo crear una función de Azure a la que se pueda acceder y que se desencadene mediante gemelos digitales.
author: baanders
ms.author: baanders
ms.date: 7/14/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: eaa26b4682dc7e984c0c84575d9e87b5255f6c99
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114386979"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Configuración de aplicaciones de funciones de Azure para procesar datos

Los gemelos digitales se pueden actualizar en función de los datos mediante [rutas de eventos](concepts-route-events.md) con recursos de proceso. Por ejemplo, una función que se haya realizado mediante [Azure Functions](../azure-functions/functions-overview.md) puede actualizar un gemelo digital en respuesta a:
* Datos de telemetría de dispositivos procedentes de Azure IoT Hub.
* Un cambio de propiedad u otros datos procedentes de otro gemelo digital dentro del grafo gemelo.

Este artículo se le mostrará cómo crear una función de Azure para su utilización con Azure Digital Twins. Para crear una función, deberá seguir estos pasos básicos:

1. Cree un proyecto de Azure Functions en Visual Studio.
2. Escriba una función que tenga un desencadenador de [Azure Event Grid](../event-grid/overview.md).
3. Agregue código de autenticación a la función para que pueda acceder a Azure Digital Twins.
4. Publique la aplicación de funciones en Azure.
5. Configure la [seguridad](concepts-security.md) para la aplicación de funciones.

## <a name="prerequisite-set-up-azure-digital-twins"></a>Requisito previo: configuración de Azure Digital Twins

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Creación de una aplicación de funciones en Visual Studio

Para instrucciones sobre cómo crear una aplicación de funciones mediante Visual Studio, consulte [Desarrollo de Azure Functions con Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

## <a name="write-a-function-that-has-an-event-grid-trigger"></a>Escritura de una función que tenga un desencadenador de Event Grid

Para escribir una función, agregue un SDK a la aplicación de funciones. La aplicación de funciones interactúa con Azure Digital Twins con el [SDK de Azure Digital Twins para .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 

Para usar el SDK, debe incluir los siguientes paquetes en el proyecto. Instale los paquetes mediante el administrador de paquetes NuGet de Visual Studio. O bien, agregue los paquetes mediante `dotnet` en una herramienta de línea de comandos.

* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure.Core](https://www.nuget.org/packages/Azure.Core/)

A continuación, en el Explorador de soluciones de Visual Studio, abra el archivo _.cs_ que incluye el código de ejemplo. Agregue las siguientes instrucciones `using` para los paquetes.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Incorporación de código de autenticación a la función

Ahora declare las variables de nivel de clase y agregue un código de autenticación que permitirá a la función acceder a Azure Digital Twins. Agregue las variables y el código a la función.

* **Código para leer la URL del servicio Azure Digital Twins como una variable de entorno.** Se recomienda leer la dirección URL del servicio en una variable de entorno, en lugar de codificarla de forma rígida en la función. Establecerá el valor de esta variable de entorno [más adelante en este artículo](#set-up-security-access-for-the-function-app). Para más información sobre las variables de entorno, consulte [Administración de la aplicación de funciones](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal).

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* **Una variable estática para contener una instancia de HttpClient.** HttpClient es relativamente costoso de crear, por lo que queremos evitar crearlo para cada invocación de función.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* **Credenciales de identidad administrada.**
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* **Una variable local _DigitalTwinsClient_.** Agregue una variable dentro de la función para que contenga la instancia del cliente de Azure Digital Twins. *No* haga que esta variable sea estática dentro de la clase.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* **Una comprobación de NULL para _adtInstanceUrl_.** Agregue una comprobación de NULL y, a continuación, encapsule la lógica de la función en un bloque try/catch para detectar cualquier excepción.

Después de estos cambios, el código de la función tendrá un aspecto similar al del siguiente ejemplo.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Ahora que la aplicación está escrita, puede publicarla en Azure.

## <a name="publish-the-function-app-to-azure"></a>Publicación de la aplicación de funciones en Azure

Para obtener instrucciones sobre cómo publicar una aplicación de funciones, vea [Desarrollo de Azure Functions con Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

### <a name="verify-the-publication-of-your-function"></a>Comprobación de la publicación de la función

1. Use sus credenciales para iniciar sesión en [Azure Portal](https://portal.azure.com/).
2. En el cuadro de búsqueda de la parte superior de la ventana, busque el nombre de la aplicación de funciones y selecciónelo.

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Captura de pantalla que muestra Azure Portal. En el campo de búsqueda, escriba el nombre de la aplicación de funciones." lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. En la página **Aplicación de funciones** que se abrirá, en el menú de la izquierda, elija **Funciones**. Si la función se ha publicado correctamente, su nombre aparecerá en la lista.

    > [!Note] 
    > Es posible que tenga que esperar unos minutos o actualizar la página un par de veces antes de que se muestre la función en la lista de funciones publicadas.

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Captura de pantalla que muestra las funciones publicadas en Azure Portal." lightbox="media/how-to-create-azure-function/view-published-functions.png":::

Para acceder a Azure Digital Twins, la aplicación de funciones necesita una identidad administrada por el sistema con permisos para acceder a la instancia de Azure Digital Twins. Va a realizar esa configuración a continuación.

## <a name="set-up-security-access-for-the-function-app"></a>Configuración del acceso de seguridad para la aplicación de funciones

[!INCLUDE [digital-twins-configure-function-app.md](../../includes/digital-twins-configure-function-app.md)]

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha configurado una aplicación de funciones de Azure para usarla con Azure Digital Twins. A continuación, consulte cómo crear una función básica para [ingerir datos de IoT Hub en Azure Digital Twins](how-to-ingest-iot-hub-data.md).
