---
title: Casos de uso de la biblioteca de interfaz de usuario
titleSuffix: An Azure Communication Services concept document
description: Aprenda sobre la Biblioteca de interfaz de usuario y cómo puede ayudar a crear experiencias de comunicación.
author: ddematheu2
manager: chrispalm
services: azure-communication-services
ms.author: dademath
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: dd338cd0f7d8e39f7f2d72445477991219e8d91c
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653150"
---
# <a name="ui-library-use-cases"></a>Casos de uso de la biblioteca de interfaz de usuario

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

> [!NOTE]
> Puede encontrar documentación detallada sobre la Biblioteca de interfaz de usuario en el [ libro de historias sobre la Biblioteca de interfaz de usuario ](https://azure.github.io/communication-ui-library). Ahí encontrará documentación conceptual adicional, inicios rápidos y ejemplos.


La Biblioteca de interfaz de usuario admite muchos casos de experiencias de llamada y chat.
Estas capacidades están disponibles por medio de componentes de interfaz de usuario y composiciones.
En el caso de las composiciones, estas capacidades se integran directamente y se exponen cuando la composición se integra en una aplicación.
En el caso de los componentes de interfaz de usuario, estas capacidades se exponen mediante una combinación de funcionalidad de interfaz de usuario y bibliotecas con estado subyacentes.
Para aprovechar al máximo estas capacidades, se recomienda usar los componentes de interfaz de usuario con las bibliotecas cliente de chat y llamada con estado.

## <a name="calling-use-cases"></a>Casos de uso de llamada

| Área                | Casos de uso                                              |
| ------------------- | ------------------------------------------------------ |
| Tipos de llamada          | Unirse a una reunión de Teams                                     |
|                     | Unirse a la llamada de Azure Communication Services con el identificador de grupo   |
| [Interoperabilidad de equipos](../teams-interop.md)      | Sala de llamadas                                             |
|                     | Banner de alerta de transcripción y grabación               |
| Controles de llamada       | Desactivar/activar el audio de la llamada                                       |
|                     | Vídeo activado/desactivado en la llamada                                   |
|                     | Uso compartido de la pantalla                                         |
|                     | Finalizar llamada                                               |
| Galería de participantes | Los participantes remotos se muestran en la cuadrícula              |
|                     | Vista previa de vídeo disponible durante la llamada para el usuario local |
|                     | Avatares predeterminados disponibles cuando el vídeo está desactivado            |
|                     | El contenido de la pantalla compartida se muestra en la galería de participantes |
| Configuración de llamada  | Administración de dispositivos de micrófono                           |
|                     | Administración de dispositivos de cámara                               |
|                     | Administración de dispositivos de altavoz                              |
|                     | Vista previa local disponible para que el usuario compruebe el vídeo        |
| Participantes        | Lista de participantes                                     |

## <a name="chat-use-cases"></a>Casos de uso de chat

| Área         | Casos de uso                                        |
| ------------ | ------------------------------------------------ |
| Tipos de chat   | Unirse a un chat de reunión de Teams                        |
|              | Unirse a un subproceso de chat de Azure Communication Services |
| Acciones de chat | Enviar mensaje de chat                                |
|              | Recibir mensaje de chat                             |
| Eventos de chat  | Indicadores de escritura                                |
|              | Confirmación de lectura                                     |
|              | Participante agregado o quitado                        |
|              | Título de chat cambiado                               |
| Participantes | Lista de participantes                               |

## <a name="supported-identities"></a>Identidades admitidas

Se necesita una identidad de Azure Communication Services para inicializar las bibliotecas cliente con estado y autenticarse en el servicio.
Para más información, consulte [Autenticación](../authentication.md) y [Tokens de acceso](../../quickstarts/access-tokens.md?pivots=programming-language-javascript).

## <a name="teams-interop-use-case"></a>Caso de uso de interoperabilidad de Teams

En escenarios de [interoperabilidad de Teams](../teams-interop.md), los desarrolladores pueden usar componentes de la Biblioteca de interfaz de usuario para unirse a reuniones de Teams mediante Azure Communication Services.
Para habilitar la interoperabilidad de Teams, los desarrolladores pueden usar composiciones de llamada y chat directamente o componentes de interfaz de usuario para crear una experiencia personalizada.
Al habilitar aplicaciones con llamada y chat, es importante recordar que el cliente de chat no se puede inicializar hasta que el participante se haya admitido en la llamada.
Una vez admitido, el cliente de chat se puede inicializar para unirse al subproceso de chat de la reunión.
Vea el diagrama siguiente a modo de orientación:

:::image type="content" source="../media/teams-interop-pattern.png" alt-text="Patrón de interoperabilidad de Teams en llamada y chat":::

Al usar componentes de interfaz de usuario para ofrecer experiencias de interoperabilidad de Teams, la Biblioteca de interfaz de usuario proporciona ejemplos de partes clave de la experiencia.
Por ejemplo:
- [Ejemplo de sala de espera](https://azure.github.io/communication-ui-library/?path=/story/examples-teams-interop--lobby): sala de espera de ejemplo para que el participante espere a ser admitido en la llamada.
- [Banner de cumplimiento](https://azure.github.io/communication-ui-library/?path=/story/examples-teams-interop--compliance-banner): banner de ejemplo para mostrar al usuario si la llamada se está grabando o no.
- [Tema de Teams](https://azure.github.io/communication-ui-library/?path=/story/examples-themes--teams): tema de ejemplo que hace que la Biblioteca de interfaz de usuario se parezca a Microsoft Teams.


## <a name="customization"></a>Personalización

La Biblioteca de interfaz de usuario expone patrones para que los desarrolladores modifiquen los componentes de forma que se ajusten al aspecto y la sensación de su aplicación.
Estas funcionalidades son un área clave de diferenciación entre composiciones y componentes de interfaz de usuario, donde las composiciones proporcionan menos opciones de personalización en favor de una experiencia de integración más sencilla.

| Caso de uso                                            | Compuestos | Componentes de interfaz de usuario |
| --------------------------------------------------- | ---------- | ------------- |
| Temas basados en Fluent                                | X          | X             |
| El diseño de la experiencia se puede componer                     |            | X             |
| Se pueden usar hojas de estilo CSS para modificar las propiedades de estilo  |            | X             |
| Los iconos se pueden reemplazar                               |            | X             |
| Se puede modificar el diseño de la galería de participantes          |            | X             |
| Se puede modificar el diseño del control de llamadas                 | X          | X             |
| Se pueden insertar modelos de datos para modificar los metadatos del usuario | X          | X             |

## <a name="observability"></a>Observabilidad

Como parte de la arquitectura de administración de estado desacoplada de la Biblioteca de interfaz de usuario, los desarrolladores pueden acceder directamente a los clientes de chat y llamadas con estado.

Los desarrolladores pueden enlazar con el cliente con estado para leer el estado, controlar eventos e invalidar el comportamiento para pasar los componentes de interfaz de usuario.

| Caso de uso                                  | Compuestos | Componentes de interfaz de usuario |
| ----------------------------------------- | ---------- | ------------- |
| Se puede acceder al estado del cliente de llamada o chat    | X          | X             |
| Se puede acceder a los eventos de cliente y controlarlos | X          | X             |
| Se puede acceder a los eventos de la interfaz de usuario y controlarlos     | X          | X             |

## <a name="recommended-architecture"></a>Arquitectura recomendada

:::image type="content" source="../media/ui-library-architecture.png" alt-text="Arquitectura cliente-servidor recomendada de la Biblioteca de interfaz de usuario":::

Los componentes compuestos y base se inicializan con un token de acceso de Azure Communication Services. Los tokens de acceso se deben obtener de Azure Communication Services mediante un servicio de confianza que administre. Consulte [Quickstart: Creación y administración de tokens de acceso](../../quickstarts/access-tokens.md?pivots=programming-language-javascript) y el artículo sobre el [tutorial de un servicio de confianza](../../tutorials/trusted-service-tutorial.md) para más información.

Estas bibliotecas cliente también requieren el contexto de la llamada o del chat al que se unirán. De forma similar a los tokens de acceso de usuario, este contexto se debe difundir a los clientes mediante su propio servicio de confianza. La siguiente lista resume las funciones de inicialización y administración de recursos que necesita para la puesta en práctica.

| Responsabilidades de Contoso                                 | Responsabilidades de la Biblioteca de interfaz de usuario                                     |
| -------------------------------------------------------- | --------------------------------------------------------------- |
| Proporcionar un token de acceso de Azure                          | Pasar el token de acceso dado para inicializar los componentes        |
| Proporcionar la función de actualización                                 | Actualizar el token de acceso mediante la función proporcionada por el desarrollador          |
| Recuperar o pasar información de incorporación a llamada o chat          | Pasar la información de llamada y chat para inicializar los componentes |
| Recuperar o pasar información de usuario para cualquier modelo de datos personalizado | Pasar el modelo de datos personalizado a los componentes que se van a representar          |

## <a name="platform-support"></a>Compatibilidad con plataformas

| SDK    | Windows            | macOS                | Ubuntu   | Linux    | Android  | iOS        |
| ------ | ------------------ | -------------------- | -------- | -------- | -------- | ---------- |
| SDK de IU | Chrome\*, nuevo Edge | Chrome\*, Safari\*\* | Chrome\* | Chrome\* | Chrome\* | Safari\*\* |

\*Tenga en cuenta que se admite la versión más reciente de Chrome además de las dos versiones anteriores.

\*\*Tenga en cuenta que se admiten las versiones de Safari 13.1 y posteriores. Todavía no se admite el vídeo de salida para Safari macOS, pero es compatible con iOS. El uso compartido de pantalla saliente solo se admite en iOS de escritorio.

## <a name="accessibility"></a>Accesibilidad

La accesibilidad por diseño es un principio en todos los productos de Microsoft.
La Biblioteca de interfaz de usuario sigue este principio para asegurarse de que todos los componentes de la interfaz de usuario sean totalmente accesibles.
Durante la versión preliminar pública, la Biblioteca de interfaz de usuario seguirá mejorando e incorporará la característica de accesibilidad a los componentes de la interfaz de usuario.
Esperamos agregar más detalles sobre la accesibilidad antes de que la Biblioteca de interfaz de usuario esté disponible con carácter general.

## <a name="localization"></a>Localización

La localización es clave para crear productos que se pueden usar en todo el mundo y por personas que hablan diferentes idiomas.
La Biblioteca de interfaz de usuario proporcionará compatibilidad inmediata con algunos lenguajes y funcionalidades, como RTL.
Los desarrolladores pueden proporcionar sus propios archivos de localización que se usarán para la Biblioteca de interfaz de usuario.
Estas funcionalidades de localización se agregarán antes de la fase de disponibilidad general.

> [!div class="nextstepaction"]
> [Visite el libro de historias de la Biblioteca de interfaz de usuario](https://azure.github.io/communication-ui-library).
