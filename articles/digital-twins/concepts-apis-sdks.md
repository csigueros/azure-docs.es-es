---
title: API y SDK de Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Descubra las opciones de SDK y API de Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 04/30/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 6a69e1eeeb1861f7d2a14e1a96c959c18a090682
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438751"
---
# <a name="azure-digital-twins-apis-and-sdks"></a>API y SDK de Azure Digital Twins

Azure Digital Twins incluye las **API de plano de control** y las **API de plano de datos** para administrar su instancia y sus elementos. 
* Las API de plano de control son las API de [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md) y cubren las operaciones de administración de recursos como la creación y eliminación de la instancia. 
* Las API de plano de datos son las API de Azure Digital Twins y se usan para operaciones de administración de datos, como la administración de modelos, gemelos y el grafo.

En este artículo se proporciona información general sobre las API disponibles y los métodos para interactuar con ellas. Puede usar las API REST directamente con las instancias de Swagger asociadas (mediante una herramienta como [Postman](how-to-use-postman.md)) o mediante un SDK.

## <a name="overview-control-plane-apis"></a>Información general: API de plano de control

Las API del plano de control son las API de [ARM](../azure-resource-manager/management/overview.md) que se usan para administrar la instancia de Azure Digital Twins como un todo, por lo que cubren operaciones como la creación o eliminación de toda la instancia. También usará estas API para crear y eliminar puntos de conexión.

La versión más reciente de la API de plano de control es _**2020-12-01**_.

Para usar las API de plano de control:
* Para llamar a las API directamente, haga referencia a la carpeta de Swagger más reciente en la [repositorio de Swagger del plano de control](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable). Esta carpeta también incluye una carpeta de ejemplos que muestran el uso.
* Actualmente puede acceder a los SDK para las API de control en...
  - [.NET (C#)](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([referencia [generada automáticamente]](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true)) ([origen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [Java](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins) ([referencia [generada automáticamente]](/java/api/overview/azure/digitaltwins?view=azure-java-stable&preserve-view=true)) ([origen](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins))
  - [JavaScript](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([código fuente](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [Python](https://pypi.org/project/azure-mgmt-digitaltwins/) ([código fuente](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [Go](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt) ([código fuente](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt))

También puede ejercitar las API de plano de control interactuando con Azure Digital Twins mediante [Azure Portal](https://portal.azure.com) y la [CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true).

## <a name="overview-data-plane-apis"></a>Información general: API de plano de datos

Las API de plano de datos son las API de Azure Digital Twins que se usan para administrar los elementos de la instancia de Azure Digital Twins. Incluyen operaciones como la creación de rutas, la carga de modelos, la creación de relaciones y la administración de gemelos, y se pueden dividir ampliamente en las categorías siguientes:
* **DigitalTwinModels**: la categoría DigitalTwinModels contiene las API para administrar los [modelos](concepts-models.md) en una instancia de Azure Digital Twins. Las actividades de administración incluyen la carga, validación, recuperación y eliminación de modelos creados en DTDL.
* **DigitalTwins**: la categoría DigitalTwins contiene las API que permiten a los desarrolladores crear, modificar y eliminar [gemelos digitales](concepts-twins-graph.md) y sus relaciones en una instancia de Azure Digital Twins.
* **Query**: la categoría Query permite a los desarrolladores [buscar conjuntos de gemelos digitales en el grafo de gemelos](how-to-query-graph.md) entre las relaciones.
* **Event Routes**: la categoría Event Routes contiene las API para [enrutar datos](concepts-route-events.md), en el sistema y a los servicios de bajada.

La versión más actual de la API de plano de datos es _**2020-10-31**_.

Para usar las API de plano de datos:
* Para llamar directamente a las API...
   - haga referencia a la carpeta de Swagger más reciente en el [repositorio de Swagger del plano de datos](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Esta carpeta también incluye una carpeta de ejemplos que muestran el uso. 
   - consulte la [documentación de referencia de la API](/rest/api/azure-digitaltwins/).
* Puede usar el **SDK de .NET (C#)** . Para usar el SDK de .NET...
   - puede ver y agregar el paquete desde NuGet: [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - puede ver la [documentación de referencia del SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).
   - puede encontrar el código fuente del SDK, incluida una carpeta de ejemplos, en GitHub: [Biblioteca de cliente de Digital Twins de Azure IoT para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - puede ver información detallada y ejemplos de uso si continúa con la sección [SDK de .NET (C#) (plano de datos)](#net-c-sdk-data-plane) de este artículo.
* Puede usar el **SDK de Java**. Para usar el SDK de Java…
   - puede ver e instalar el paquete desde Maven: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - puede ver la [documentación de referencia del SDK](/java/api/overview/azure/digitaltwins/client?view=azure-java-stable&preserve-view=true)
   - puede encontrar el código fuente del SDK en GitHub: [Biblioteca cliente de Digital Twins de Azure IoT para Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Puede usar el **SDK de JavaScript**. Para usar el SDK de JavaScript...
   - puede ver e instalar el paquete desde npm: [Biblioteca cliente de Azure Digital Twins Core para JavaScript](https://www.npmjs.com/package/@azure/digital-twins-core).
   - puede ver la [documentación de referencia del SDK](/javascript/api/@azure/digital-twins-core/?view=azure-node-latest&preserve-view=true).
   - puede encontrar el código fuente del SDK en GitHub: [Biblioteca cliente de Azure Digital Twins Core para JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core).
* Puede usar el **SDK para Python**. Para usar el SDK para Python...
   - puede ver e instalar el paquete desde PyPi: [Biblioteca cliente de Azure Digital Twins Core para Python](https://pypi.org/project/azure-digitaltwins-core/).
   - puede ver la [documentación de referencia del SDK](/python/api/azure-digitaltwins-core/azure.digitaltwins.core?view=azure-python&preserve-view=true).
   - puede encontrar el código fuente del SDK en GitHub: [Biblioteca cliente de Azure Digital Twins Core para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)

También puede probar las API de plano de fecha interactuando con Azure Digital Twins a través de la [CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true).

## <a name="net-c-sdk-data-plane"></a>SDK de .NET (C#) (plano de datos)

El SDK de .NET (C#) para Azure Digital Twins forma parte del SDK de Azure para .NET. Es de código abierto y se basa en las API del plano de datos de Azure Digital Twins.

> [!NOTE]
> Para más información sobre el diseño del SDK, consulte los [principios de diseño generales para los SDK de Azure](https://azure.github.io/azure-sdk/general_introduction.html) y las [directrices de diseño de .NET](https://azure.github.io/azure-sdk/dotnet_introduction.html) específicas.

Para usar el SDK, incluya el paquete NuGet **Azure.DigitalTwins.Core** con el proyecto. También necesitará la versión más reciente del paquete **Azure.Identity**. En Visual Studio, puede agregar estos paquetes con el administrador de paquetes NuGet (al que se accede a través de *Herramientas > Administrador de paquetes NuGet > Administrar paquetes NuGet para la solución*). También puede usar la herramienta de línea de comandos de .NET con los comandos que se encuentran en los siguientes vínculos de paquetes NuGet para agregarlos al proyecto:
* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core): el paquete del [SDK de Azure Digital Twins para .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity): la biblioteca que proporciona herramientas para facilitar la autenticación en Azure.

Para un tutorial detallado sobre el uso de las API en la práctica, consulte [Programación de una aplicación cliente](tutorial-code.md). 

### <a name="serialization-helpers"></a>Asistentes de serialización

Las aplicaciones auxiliares de serialización son funciones auxiliares disponibles en el SDK para crear o deserializar rápidamente datos gemelos para el acceso a información básica. Dado que los métodos principales del SDK devuelven los datos gemelos como JSON de forma predeterminada, puede resultar útil usar estas clases auxiliares para desglosar aún más los datos gemelos.

Las clases de asistente disponibles son:
* `BasicDigitalTwin`: representa genéricamente los datos principales de un gemelo digital.
* `BasicDigitalTwinComponent`: representa genéricamente un componente en las propiedades `Contents` de un elemento `BasicDigitalTwin`.
* `BasicRelationship`: representa genéricamente los datos principales de una relación.
* `DigitalTwinsJsonPropertyName`: contiene las constantes de cadena que se usan en la serialización y deserialización JSON para tipos de gemelos digitales personalizados.

## <a name="general-apisdk-usage-notes"></a>Notas generales sobre el uso de la API o del SDK

> [!NOTE]
> Tenga en cuenta que Azure Digital Twins no admite actualmente el **uso compartido de recursos entre orígenes (CORS)** . Para más información sobre las estrategias de impacto y resolución, consulte la sección [Uso compartido de recursos entre orígenes (CORS)](concepts-security.md#cross-origin-resource-sharing-cors)  de *Conceptos: Seguridad para las soluciones de Azure Digital Twins*.

En la lista siguiente se proporcionan más detalles y directrices generales para usar las API y los SDK.

* Puede usar una herramienta de prueba de REST de HTTP como Postman para realizar llamadas directas a las API de Azure Digital Twins. Para más información sobre este proceso, consulte [Realización de solicitudes de API con Postman](how-to-use-postman.md).
* Para usar el SDK, cree una instancia de la clase `DigitalTwinsClient`. El constructor requiere credenciales que se pueden obtener con diferentes tipos de métodos de autenticación en el paquete de `Azure.Identity`. Para más información sobre `Azure.Identity`, consulte la [documentación del espacio de nombres](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true). 
* Es posible que encuentre útil `InteractiveBrowserCredential` al comenzar, pero hay muchas otras opciones, incluidas las credenciales para la [identidad administrada](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true), que probablemente va a usar para autenticar las [funciones de Azure configuradas con MSI](../app-service/overview-managed-identity.md?tabs=dotnet) en Azure Digital Twins. Para más información sobre `InteractiveBrowserCredential`, consulte la [documentación de la clase](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true).
* Las solicitudes a las API de Azure Digital Twins requieren un usuario o una entidad de servicio que forme parte del mismo inquilino de [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) donde existe la instancia de Azure Digital Twins. Para evitar exámenes malintencionados de los puntos de conexión de Azure Digital Twins, las solicitudes con tokens de acceso desde fuera del inquilino de origen recibirán un mensaje de error "404 No se ha encontrado el subdominio". Este error se devolverá *incluso si* al usuario o a la entidad de servicio se le ha concedido un rol de propietario de datos o de lector de datos de Azure Digital Twins mediante la colaboración de [Azure AD B2B](../active-directory/external-identities/what-is-b2b.md). Para información sobre cómo lograr el acceso a través de varios inquilinos, consulte [Escritura de código de autenticación de aplicación](how-to-authenticate-client.md#authenticate-across-tenants).
* Todas las llamadas de API de servicio se exponen como funciones miembro en la clase `DigitalTwinsClient`.
* Todas las funciones de servicio se encuentran en versiones sincrónicas y asincrónicas.
* Todas las funciones de servicio inician una excepción en cualquier estado de devolución de 400 o superior. Asegúrese de ajustar las llamadas en una sección `try` y de capturar al menos `RequestFailedExceptions`. Para más información sobre este tipo de excepción, consulte su [documentación de referencia](/dotnet/api/azure.requestfailedexception?view=azure-dotnet&preserve-view=true).
* La mayoría de los métodos de servicio devuelven `Response<T>` o (`Task<Response<T>>` para las llamadas asincrónicas), donde `T` es la clase de objeto de devolución para la llamada de servicio. La clase [Responde](/dotnet/api/azure.response-1?view=azure-dotnet&preserve-view=true) encapsula la devolución del servicio y presenta los valores devueltos en su campo `Value`.  
* Los métodos de servicio con los resultados paginados devuelven `Pageable<T>` o `AsyncPageable<T>` como resultados. Para más información sobre la clase `Pageable<T>`, vea su [documentación de referencia](/dotnet/api/azure.pageable-1?view=azure-dotnet&preserve-view=true); para más información sobre `AsyncPageable<T>`, vea su [documentación de referencia](/dotnet/api/azure.asyncpageable-1?view=azure-dotnet&preserve-view=true).
* Puede recorrer en iteración los resultados paginados mediante un bucle `await foreach`. Para más información sobre este proceso, consulte la [documentación pertinente](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8).
* El SDK subyacente es `Azure.Core`. Consulte la [documentación del espacio de nombres de Azure](/dotnet/api/azure?view=azure-dotnet&preserve-view=true) como referencia sobre los tipos y la infraestructura del SDK.


Los métodos de servicio devuelven objetos fuertemente tipados siempre que sea posible. Sin embargo, dado que Azure Digital Twins se basa en los modelos configurados de forma personalizada por el usuario en tiempo de ejecución (a través de los modelos DTDL cargados en el servicio), muchas API de servicio toman y devuelven datos gemelos en formato JSON.

## <a name="monitor-api-metrics"></a>Supervisión de las métricas de API

Las métricas de API, como las solicitudes, la latencia y la tasa de errores, se pueden ver en [Azure Portal](https://portal.azure.com/). 

En la página principal del portal, busque la instancia de Azure Digital Twins para ver sus detalles. Seleccione la opción **Métricas** en el menú de la instancia de Azure Digital Twins para abrir la página *Métricas*.

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Captura de pantalla que muestra la página de métricas de Azure Digital Twins":::

Desde aquí, puede ver las métricas de la instancia y crear vistas personalizadas.

## <a name="next-steps"></a>Pasos siguientes

Vea cómo se hacen solicitudes directas a las API mediante Postman:
* [Realización de solicitudes de API con Postman](how-to-use-postman.md)

O bien, practique el uso del SDK de .NET mediante la creación de una aplicación cliente con este tutorial:
* [Programación de una aplicación cliente](tutorial-code.md)
