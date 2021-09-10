---
title: Escritura de código de autenticación de aplicación
titleSuffix: Azure Digital Twins
description: Vea cómo escribir código de autenticación en una aplicación cliente
author: baanders
ms.author: baanders
ms.date: 8/26/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a1b2465f29ae659f3e255a4843a2abd5f9ab75b2
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224853"
---
# <a name="write-client-app-authentication-code"></a>Escritura de código de autenticación de aplicación cliente

Después de [configurar una instancia y la autenticación de Azure Digital Twins](how-to-set-up-instance-portal.md), puede crear una aplicación cliente para usarla para interactuar con la instancia. Una vez configurado un proyecto cliente de inicio, necesitará **escribir código en esa aplicación cliente para autenticarla** en la instancia de Azure Digital Twins.

Azure Digital Twins realiza la autenticación mediante [tokens de seguridad de Azure AD basados en OAUTH 2.0](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). Para autenticar el SDK, deberá obtener un token de portador con los permisos correctos para Azure Digital Twins y pasarlo junto con las llamadas de API. 

En este artículo se describe cómo obtener credenciales mediante la biblioteca de cliente `Azure.Identity`. Aunque en este artículo se muestran ejemplos de código en C#, como los que escribiría para el [SDK de .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true), puede usar una versión de `Azure.Identity` independientemente del SDK que utilice. (Para obtener más información sobre los SDK disponibles para Azure Digital Twins, vea [API y SDK de Azure Digital Twins](concepts-apis-sdks.md)).

## <a name="prerequisites"></a>Requisitos previos

En primer lugar, complete los pasos de configuración que se especifican en [Configuración de una instancia y autenticación](how-to-set-up-instance-portal.md). Esto garantiza que tiene una instancia de Azure Digital Twins y que el usuario tiene permisos de acceso. Después de toda esta configuración, está listo para escribir código de aplicación cliente.

Para continuar, necesita un proyecto de aplicación cliente en el que escribir el código. Si aún no tiene un proyecto de aplicación cliente configurado, cree un proyecto básico en el lenguaje que prefiera para usarlo con este tutorial.

## <a name="authenticate-using-azureidentity-library"></a>Autenticación con una biblioteca Azure.Identity

`Azure.Identity` es una biblioteca de cliente que proporciona varios métodos para obtener credenciales que puede usar para obtener un token de portador y autenticarse con el SDK. Aunque en este artículo se proporcionan ejemplos en C# , puede ver `Azure.Identity` para varios idiomas, incluido...

* [.NET (C#)](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)
* [Java](/java/api/overview/azure/identity-readme?view=azure-java-stable&preserve-view=true)
* [JavaScript](/javascript/api/overview/azure/identity-readme?view=azure-node-latest&preserve-view=true)
* [Python](/python/api/overview/azure/identity-readme?view=azure-python&preserve-view=true)

Tres métodos comunes de obtención de credenciales en `Azure.Identity` son:

* [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) proporciona un flujo de autenticación `TokenCredential` predeterminado para las aplicaciones que se implementarán en Azure y es **la opción recomendada para el desarrollo local**. También se puede habilitar para probar los otros dos métodos recomendados en este artículo: contiene `ManagedIdentityCredential` y puede tener acceso a `InteractiveBrowserCredential` con una variable de configuración.
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) funciona bien en los casos en los que necesita [identidades administradas (MSI)](../active-directory/managed-identities-azure-resources/overview.md) y es un buen candidato para trabajar con Azure Functions y realizar implementaciones en servicios de Azure.
* [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) está diseñado para aplicaciones interactivas y se puede usar para crear un cliente de SDK autenticado.

En el resto de este artículo se muestra cómo usarlos con el [SDK de .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).

### <a name="add-azureidentity-to-your-net-project"></a>Agregación de Azure.Identity a un proyecto de .NET

Para configurar su proyecto de .NET de tal modo que se autentique con `Azure.Identity`, complete los siguientes pasos:

1. Incluya el paquete de SDK `Azure.DigitalTwins.Core` y el paquete `Azure.Identity` en el proyecto. En función de las herramientas que elija, puede incluir los paquetes con el administrador de paquetes de Visual Studio o con la herramienta de línea de comandos `dotnet`. 

1. Agregue las siguientes instrucciones using al código del proyecto:

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="Azure_Digital_Twins_dependencies":::

A continuación, agregue código para obtener credenciales mediante uno de los métodos de `Azure.Identity`. En las siguientes secciones se proporciona más información sobre el uso de cada una de ellas.

### <a name="defaultazurecredential-method"></a>Método DefaultAzureCredential

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) proporciona un flujo de autenticación `TokenCredential` predeterminado para las aplicaciones que se implementarán en Azure y es **la opción recomendada para el desarrollo local**.

Para usar las credenciales predeterminadas de Azure, necesitará la dirección URL de la instancia de Azure Digital Twins ([instrucciones para encontrarla](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

A continuación se muestra un ejemplo de código para agregar `DefaultAzureCredential` al proyecto:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_full":::

#### <a name="set-up-local-azure-credentials"></a>Configuración de credenciales locales de Azure

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>Método ManagedIdentityCredential

El método [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) funciona muy bien en los casos en los que necesita [identidades administradas (MSI)](../active-directory/managed-identities-azure-resources/overview.md), por ejemplo, al [autenticar con Azure Functions](#authenticate-azure-functions).

Esto significa que puede usar `ManagedIdentityCredential` en el mismo proyecto que `DefaultAzureCredential` o `InteractiveBrowserCredential` para autenticar otra parte del proyecto.

Para usar las credenciales predeterminadas de Azure, necesitará la dirección URL de la instancia de Azure Digital Twins ([instrucciones para encontrarla](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)). También puede que necesite un [registro de aplicación](./how-to-create-app-registration-portal.md) y el [Id. de aplicación (cliente)](./how-to-create-app-registration-portal.md#collect-client-id-and-tenant-id).

En una función de Azure, puede usar las credenciales de identidad administrada de la siguiente manera:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="ManagedIdentityCredential":::

### <a name="interactivebrowsercredential-method"></a>Método InteractiveBrowserCredential

El método [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) está pensado para aplicaciones interactivas y abrirá un explorador web para la autenticación. Puede utilizarlo en lugar de `DefaultAzureCredential` en los casos en los que necesite una autenticación interactiva.

Para usar las credenciales interactivas del explorador, necesitará un **registro de la aplicación** que tenga permisos para las API de Azure Digital Twins. Si desea conocer los pasos para configurar este registro de aplicación, consulte [Creación de un registro de aplicación](./how-to-create-app-registration-portal.md). Una vez configurado el registro de la aplicación, necesitará...
* el [id. de la aplicación (cliente) del registro de la aplicación;](./how-to-create-app-registration-portal.md#collect-client-id-and-tenant-id)
* el [id. del directorio (inquilino) del registro de la aplicación;](./how-to-create-app-registration-portal.md#collect-client-id-and-tenant-id)
* [la dirección URL de la instancia de Azure Digital Twins](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

Este es un ejemplo del código para crear un cliente de SDK autenticado mediante `InteractiveBrowserCredential`.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="InteractiveBrowserCredential":::

>[!NOTE]
> Aunque puede especificar el identificador de cliente, el identificador de inquilino y la dirección URL de la instancia directamente en el código, como se muestra arriba, se recomienda que, en su lugar, el código obtenga estos valores de un archivo de configuración o una variable de entorno.

## <a name="authenticate-azure-functions"></a>Autenticación con Azure Functions

Esta sección contiene algunas de las opciones de configuración importantes en el contexto de autenticación con Azure Functions. Primero, conocerá variables de nivel de clase recomendadas y código de autenticación con el que la función podrá acceder a Azure Digital Twins. A continuación, leerá algunos pasos de configuración finales que se deben completar con relación a la función después de publicar su código en Azure. 

### <a name="write-application-code"></a>Escritura de código de aplicación

Al escribir la función de Azure, considere la posibilidad de agregar estas variables y este código a la función:

* **Código para leer la URL del servicio Azure Digital Twins como una variable de entorno o un valor de configuración** Se recomienda leer la dirección URL del servicio en una [configuración de aplicación o variable de entorno](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal), en lugar de codificarla de forma rígida en la función. En una función de Azure, ese código para leer la variable de entorno podría tener el siguiente aspecto: 

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

    Más adelante, después de publicar la función, creará y establecerá el valor de la variable de entorno para que este código lea. Para obtener instrucciones sobre cómo hacerlo, vaya directamente a [Configuración de la aplicación](#configure-application-settings).

* **Una variable estática para contener una instancia de HttpClient.** HttpClient es relativamente caro de crear, por lo que es probable que le interese crearlo una vez con el código de autenticación, con el fin de evitar hacerlo para cada invocación de función.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* **Credenciales de identidad administrada.** Cree una credencial de identidad administrada para que la use la función con el fin de acceder a Azure Digital Twins.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

    Más adelante, después de publicar la función, se asegurará de que la identidad de la función tenga permiso para acceder a API de Azure Digital Twins. Para obtener instrucciones sobre cómo hacerlo, vaya directamente a [Asignación de un rol de acceso](#assign-an-access-role).    

* **Una variable local _DigitalTwinsClient_.** Agregue una variable dentro de la función para que contenga la instancia del cliente de Azure Digital Twins. *No* haga que esta variable sea estática dentro de la clase.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* **Una comprobación de NULL para _adtInstanceUrl_.** Agregue una comprobación de NULL y, a continuación, encapsule la lógica de la función en un bloque try/catch para detectar cualquier excepción.

Una vez agregados a una función, el código de la función podría ser parecido al siguiente ejemplo.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Cuando haya terminado con el código de función, lo cual incluye agregar autenticación y la lógica de la función, [publique la aplicación en Azure](../azure-functions/functions-develop-vs.md#publish-to-azure).

### <a name="configure-published-app"></a>Configuración de la aplicación publicada

Por último, complete los siguientes pasos de configuración con relación a una función de Azure publicada, para asegurarse de que esta pueda acceder a su instancia de Azure Digital Twins.

[!INCLUDE [digital-twins-configure-function-app-cli.md](../../includes/digital-twins-configure-function-app-cli.md)]

## <a name="authenticate-across-tenants"></a>Autenticación entre inquilinos

Azure Digital Twins es un servicio que solo admite un [inquilino de Azure Active Directory (Azure AD):](../active-directory/develop/quickstart-create-new-tenant.md) el inquilino principal de la suscripción donde se encuentra la instancia de Azure Digital Twins.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

Si necesita acceder a la instancia de Azure Digital Twins mediante una entidad de servicio o una cuenta de usuario que pertenezca a un inquilino distinto de la instancia, puede hacer que cada identidad federada de otro inquilino solicite un **token** del inquilino "principal" de la instancia de Azure Digital Twins. 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

También puede especificar el inquilino principal en las opciones de credenciales en el código. 

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="other-credential-methods"></a>Otros métodos de credenciales

Si los escenarios de autenticación resaltados anteriores no cubren las necesidades de la aplicación, puede explorar otros tipos de autenticación que se ofrecen en la [plataforma de Microsoft Identity](../active-directory/develop/v2-overview.md#getting-started). La documentación de esta plataforma abarca escenarios de autenticación adicionales organizados por tipo de aplicación.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre cómo funciona la seguridad en Azure Digital Twins:
* [Seguridad para las soluciones de Azure Digital Twins](concepts-security.md)

O bien, ahora que la autenticación está configurada, continúe con la creación y administración de modelos en la instancia:
* [Administración de modelos DTDL](how-to-manage-model.md)