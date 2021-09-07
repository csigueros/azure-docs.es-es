---
title: SDK y API de REST para Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Obtenga más información acerca de los SDK de Azure Communication Services y las API de REST.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: cdc9ae8f89c13a528ff31855f731821b39e5a048
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742537"
---
# <a name="sdks-and-rest-apis"></a>SDK y API REST

Las funcionalidades de Azure Communication Services están organizadas conceptualmente en ocho áreas. La mayoría de las áreas tienen SDK de código abierto totalmente programados con las API de REST publicadas que puede usar directamente a través de Internet. Calling SDK usa interfaces de red propietarias y tiene formato de código cerrado.

En las tablas siguientes se resumen estas áreas y la disponibilidad de las API REST y las bibliotecas del SDK. Observe también si las API y los SDK están diseñados para clientes de usuario final o entornos de servicio de confianza. Los dispositivos de usuario final no deben acceder directamente a las API y SDK, como SMS, en entornos de confianza baja.

El desarrollo de aplicaciones de llamadas y chat basadas en web puede acelerarse con las [bibliotecas de interfaz de usuario de Azure Communication Services](https://azure.github.io/communication-ui-library). La biblioteca de interfaz de usuario proporciona componentes de interfaz de usuario preparados para la producción que puede colocar en sus aplicaciones.

## <a name="rest-apis"></a>API de REST
Las API de Communication Services están documentadas junto con otras API REST de Azure en [docs.microsoft.com](/rest/api/azure/). Esta documentación le indicará cómo estructurar los mensajes HTTP y ofrece instrucciones para el uso de Postman. La documentación de la interfaz de REST también se ofrece en formato Swagger en [GitHub](https://github.com/Azure/azure-rest-api-specs).


## <a name="sdks"></a>SDK
| Ensamblado | Protocolos| Entorno | Funcionalidades|
|--------|----------|---------|----------------------------------|
| Azure Resource Manager | [REST](/rest/api/communication/communicationservice)| Servicio| Aprovisiona y administra recursos de Communication Services.|
| Comunes | N/D | Cliente y servicio | Proporciona tipos base para otros SDK |
| Identidad | [REST](/rest/api/communication/communicationidentity) | Servicio| Administración de usuarios y tokens de acceso|
| Números de teléfono| [REST](/rest/api/communication/phonenumbers)| Servicio| Adquisición y administración de números de teléfono |
| SMS| [REST](/rest/api/communication/sms) | Servicio| Envía y recibe mensajes SMS.|
| Chat | [REST](/rest/api/communication/) con señalización propietaria | Cliente y servicio | Incorpora chat de texto en tiempo real a las aplicaciones. |
| Llamar| Transporte propietario | Cliente | Permite usar la voz, el vídeo, el uso compartido de pantalla y otras comunicaciones en tiempo real. |
| Servidor de llamadas | REST| Servicio| Permite hacer y administrar llamadas, reproducir audio y configurar la grabación. |
| Network Traversal| REST| Servicio| Permite acceder a servidores TURN para el transporte de datos de bajo nivel. |
| Biblioteca de interfaz de usuario | N/D | Cliente | Componentes de interfaz de usuario listos para producción para aplicaciones de chat y de llamadas |

### <a name="languages-and-publishing-locations"></a>Idiomas y ubicaciones de publicación

A continuación se detallan las ubicaciones de publicación para los paquetes de SDK individuales.

| Área | JavaScript | .NET | Python | Java SE | iOS | Android | Otros|
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | [npm](https://www.npmjs.com/package/@azure/arm-communication) | [NuGet](https://www.NuGet.org/packages/Azure.ResourceManager.Communication)| [PyPi](https://pypi.org/project/azure-mgmt-communication/)| [Maven](https://search.maven.org/search?q=azure-resourcemanager-communication)| -| -| [Go a través de GitHub](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| Comunes | [npm](https://www.npmjs.com/package/@azure/communication-common) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Common/)| N/D| [Maven](https://search.maven.org/search?q=a:azure-communication-common) | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)| [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common) | -|
| Identidad | [npm](https://www.npmjs.com/package/@azure/communication-identity) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Identity)| [PyPi](https://pypi.org/project/azure-communication-identity/)| [Maven](https://search.maven.org/search?q=a:azure-communication-identity) | -| -| -|
| Números de teléfono | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.PhoneNumbers)| [PyPi](https://pypi.org/project/azure-communication-phonenumbers/)| [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers) | -| -| -|
| Chat | [npm](https://www.npmjs.com/package/@azure/communication-chat)| [NuGet](https://www.NuGet.org/packages/Azure.Communication.Chat) | [PyPi](https://pypi.org/project/azure-communication-chat/) | [Maven](https://search.maven.org/search?q=a:azure-communication-chat) | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)| [Maven](https://search.maven.org/search?q=a:azure-communication-chat) | -|
| SMS| [npm](https://www.npmjs.com/package/@azure/communication-sms) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Sms)| [PyPi](https://pypi.org/project/azure-communication-sms/) | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms) | -| -| -|
| Llamar| [npm](https://www.npmjs.com/package/@azure/communication-calling) | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Calling) | -| - | [GitHub](https://github.com/Azure/Communication/releases) | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)| -|
|Automatización de llamadas||[NuGet](https://www.NuGet.org/packages/Azure.Communication.CallingServer/)||[Maven](https://search.maven.org/artifact/com.azure/azure-communication-callingserver)
|Network Traversal| [npm](https://www.npmjs.com/package/@azure/communication-network-traversal)|[NuGet](https://www.NuGet.org/packages/Azure.Communication.NetworkTraversal/)
| Biblioteca de interfaz de usuario| [npm](https://www.npmjs.com/package/@azure/communication-react) | - | - | - | - | - | [GitHub](https://github.com/Azure/communication-ui-library), [Storybook](https://azure.github.io/communication-ui-library/?path=/story/overview--page) |
| Documentación de referencia | [docs](https://azure.github.io/azure-sdk-for-js/communication.html) | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)| -| [docs](http://azure.github.io/azure-sdk-for-java/communication.html) | [docs](/objectivec/communication-services/calling/)| [docs](/java/api/com.azure.android.communication.calling)| -|

La asignación entre nombres de ensamblado de confianza y espacios de nombres es:

| Ensamblado | Espacios de nombres |
|------------------------|--------------------------------------|
| Azure Resource Manager | Azure.ResourceManager.Communication|
| Comunes | Azure.Communication.Common |
| Identidad | Azure.Communication.Identity |
| Números de teléfono| Azure.Communication.PhoneNumbers |
| SMS| Azure.Communication.SMS|
| Chat | Azure.Communication.Chat |
| Llamar| Azure.Communication.Calling|
| Servidor de llamadas | Azure.Communication.CallingServer|
| Network Traversal| Azure.Communication.NetworkTraversal |
| Biblioteca de interfaz de usuario | Azure.Communication.Calling|


## <a name="rest-api-throttles"></a>Limitaciones de la API de REST
Ciertas API de REST y los métodos de SDK correspondientes tienen límites que se deben tener en cuenta. Si se superan estos límites, se desencadenará una respuesta de error `429 - Too Many Requests`. Estos límites se pueden aumentar a través de [una solicitud al Soporte técnico de Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).

| API| Limitación|
|------------------------------------------------------------------------------------------------------------------------------|---------------------|
| [Todas las API de plan de número de teléfono de búsqueda](/rest/api/communication/phonenumbers) | 4 solicitudes/día|
| [Plan de número de teléfono de compra](/rest/api/communication/phonenumbers/purchasephonenumbers) | 1 compra al mes|
| [Envío de SMS](/rest/api/communication/sms/send) | 200 solicitudes por minuto |


## <a name="sdk-platform-support-details"></a>Detalles de compatibilidad de la plataforma de SDK

### <a name="ios-and-android"></a>iOS y Android

- Los SDK de iOS de Communication Services tienen como destino iOS versión 13+ y Xcode 11+.
- Los SDK de Java para Android se dirigen al nivel de API de Android 21+ y Android Studio 4.0+.

### <a name="net"></a>.NET

A excepción de las llamadas, los paquetes de Communication Services tienen como destino .NET Standard 2.0, que es compatible con las plataformas que se enumeran a continuación.

Compatibilidad mediante .NET Framework 4.6.1
- Windows 10, 8.1, 8 y 7
- Windows Server 2012 R2, 2012 y 2008 R2 SP1

Compatibilidad mediante .NET Core 2.0:
- Windows 10 (1607+), 7 SP1+, 8.1
- Windows Server 2008 R2 SP1 y versiones posteriores
- Max OS X 10.12+
- Varias versiones o distribuciones de Linux
- UWP 10.0.16299 (RS3) septiembre 2017
- Unity 2018.1
- Mono 5.4
- Xamarin iOS 10.14
- Xamarin Mac 3.8

El paquete de llamadas admite la compilación de aplicaciones para UWP con .NET Native o C++/WinRT en:
- Windows 10 10.0.17763
- Windows Server 2019 10.0.17763

## <a name="api-stability-expectations"></a>Expectativas de estabilidad de API

> [!IMPORTANT]
> En esta sección se proporcionan instrucciones sobre las API DE REST y los SDK marcados como **estables**. Las API marcadas como versión preliminar o beta se pueden cambiar o dejar de usar **sin previo aviso**.

En el futuro, es posible que retiremos las versiones de los SDK de Communication Services y que introduzcamos cambios importantes en nuestras API de REST y SDK publicados. Azure Communication Services *generalmente* sigue dos directivas de compatibilidad para retirar versiones de servicio:

- Se le notificará con una antelación de al menos tres años cuando sea necesario cambiar el código debido a un cambio en la interfaz de Communication Services. Todas las API de REST documentadas y las API de los SDK generalmente disfrutan de un período de al menos tres años de advertencia antes de que se retiren las interfaces.
- Se le notificará al menos un año antes de que tenga que actualizar los ensamblados de los SDK a la versión secundaria más reciente. Estas actualizaciones necesarias no deben requerir ningún cambio en el código porque están en la misma versión principal. El uso del SDK más reciente es sumamente importante para las bibliotecas de Calling y Chat que usan componentes en tiempo real que a menudo requieren actualizaciones de seguridad y rendimiento. Le recomendamos encarecidamente que mantenga actualizados todos los SDK de Communication Services.

### <a name="api-and-sdk-decommissioning-examples"></a>Ejemplos de retirada de API y SDK

**Ha integrado la versión 24 de la API REST de SMS en la aplicación. Publicaciones de Azure Communication Services v25.**

Recibirá una advertencia tres años antes de que estas API dejen de funcionar y sea obligatorio actualizarlas a la v25. Es posible que esta actualización requiera un cambio de código.

**Ha integrado la versión v2.02 de los SDK de llamadas en la aplicación. Publicaciones de Azure Communication Services v2.05.**

Es posible que se le pida que actualice a la versión v2.05 de los SDK de llamadas en un plazo de 12 meses a partir de la publicación de la v2.05. Debe ser un reemplazo sencillo del artefacto sin necesidad de un cambio de código porque la v2.05 está en la versión principal v2 y no tiene cambios importantes.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte la siguiente información general de los SDK:

- [Información general del SDK de llamada](../concepts/voice-video-calling/calling-sdk-features.md)
- [Información general del SDK de chat](../concepts/chat/sdk-features.md)
- [Información general del SDK de SMS](../concepts/telephony-sms/sdk-features.md)

Para empezar a trabajar con Azure Communication Services:

- [Cree un recurso de Azure Communication Services](../quickstarts/create-communication-resource.md).
- Genere [tokens de acceso de usuario](../quickstarts/access-tokens.md).
