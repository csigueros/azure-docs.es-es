---
title: Escenarios de casos de uso de la biblioteca móvil de la interfaz de usuario
titleSuffix: An Azure Communication Services - UI Mobile Library use cases scenarios
description: En este documento se presentan las funcionalidades de la biblioteca móvil de la interfaz de usuario y cómo funcionará en las aplicaciones.
author: jorgegarc
ms.author: jorgegarc
ms.date: 09/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: cc849bb98faea5cbf7f3ec7828cc8318061dc5a5
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181561"
---
# <a name="ui-library-ios-and-android-capabilities"></a>Funcionalidades de la biblioteca de interfaz de usuario (iOS y Android)

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

La biblioteca de la interfaz de usuario para iOS y Android admite la llamada a casos de uso mediante la **composición de llamadas**.
Las composiciones permiten a los desarrolladores integrar fácilmente una experiencia de llamada completa en su aplicación con solo un par de líneas de código. Estas composiciones abarcan todo el ciclo de vida de la llamada, desde la configuración hasta el final de la llamada.

## <a name="calling"></a>Llamar

| Área                                                                                            | Casos de uso                                              |
| ----------------------------------------------------------------------------------------------- | ------------------------------------------------------ |
| Tipos de llamada                                                                                      | Unirse a una reunión de Teams                                     |
|                                                                                                 | Unirse a la llamada de Azure Communication Services con el identificador de grupo   |
| [Interoperabilidad de equipos](../../concepts/teams-interop.md) | Sala de llamadas                                             |
|                                                                                                 | Banner de alerta de transcripción y grabación               |
| Galería de participantes                                                                             | Los participantes remotos se muestran en la cuadrícula              |
|                                                                                                 | Vista previa de vídeo disponible durante la llamada para el usuario local |
|                                                                                                 | Avatares predeterminados disponibles cuando el vídeo está desactivado            |
|                                                                                                 | El contenido de la pantalla compartida se muestra en la galería de participantes |
|                                                                                     | Lista de participantes                                     |
| Configuración de llamada                                                                              | Administración de dispositivos de micrófono                           |
|                                                                                                 | Administración de dispositivos de cámara                               |
|                                                                                                 | Administración de dispositivos de altavoz                              |
|                                                                                                 | Vista previa local disponible para que el usuario compruebe el vídeo        |
| Controles de llamada                                                                                   | Desactivar/activar el audio de la llamada                                       |
|                                                                                                 | Vídeo activado/desactivado en la llamada                                   |
|                                                                                                 | Finalizar llamada                                               |

## <a name="supported-identities"></a>Identidades admitidas

Se necesita una identidad de Azure Communication Services para inicializar las composiciones y completar la autenticación en el servicio.
Para más información, consulte [Autenticación](../authentication.md) y [Tokens de acceso](../../quickstarts/access-tokens.md).

## <a name="teams-interop"></a>Interoperabilidad de equipos

![Patrón de interoperabilidad de Teams en llamada y chat](../media/mobile-ui/teams-interop-diagram.png)

En escenarios de [interoperabilidad con Teams](../teams-interop.md), los desarrolladores pueden usar componentes de la biblioteca móvil de la interfaz de usuario para unirse a reuniones de Teams mediante Azure Communication Services.
Para habilitar la interoperabilidad con Teams, los desarrolladores pueden usar la composición de llamadas, que abarca el ciclo de vida de unión a una llamada de interoperabilidad con Teams.

:::image type="content" source="../media/mobile-ui/teams-meet.png" alt-text="Experiencia previa a la reunión":::

## <a name="theming"></a>Temas

La composición de llamadas de la biblioteca de la interfaz de usuario para iOS y Android proporciona interfaces que permiten a los desarrolladores pasar un color primario para cambiar el tema de la experiencia. La composición usa ese color principal para proporcionar un tema adecuado en la experiencia.

| Android                            | iOS                                     |
| -------------------------------------------------------- | --------------------------------------------------------------- |
| :::image type="content" source="../media/mobile-ui/android-color.png" alt-text="temas de android"::: | :::image type="content" source="../media/mobile-ui/ios-dark.png" alt-text="temas de iOS":::  |


## <a name="screen-size"></a>Tamaño de la pantalla

La composición de llamadas permite adaptarse a cualquier tamaño de pantalla compatible, desde pantallas de 5 pulgadas a tabletas, obtener el diseño de la lista de participantes dinámica, aportar claridad a la vista y centrarse en la conversación.

|Modo de división | Modo tableta|
|---------|---------|
| :::image type="content" source="../media/mobile-ui/meet-splitscreen.png" alt-text="pantalla dividida"::: |  :::image type="content" source="../media/mobile-ui/tablet-landscape.png" alt-text="modo tableta"::: |

## <a name="recommended-architecture"></a>Arquitectura recomendada

Las composiciones se inicializan con un token de acceso de Azure Communication Services. Los tokens de acceso se deben obtener de Azure Communication Services mediante un servicio de confianza que administre. Consulte [Quickstart: Creación y administración de tokens de acceso](../../quickstarts/access-tokens.md) y el artículo sobre el [tutorial de un servicio de confianza](../../tutorials/trusted-service-tutorial.md) para más información.

:::image type="content" source="../media/mobile-ui/ui-library-architecture.png" alt-text="Diagrama de arquitectura recomendado":::

Estas bibliotecas de cliente también requieren el contexto de la llamada a la que se unirán. De forma similar a los tokens de acceso de usuario, este contexto se debe difundir a los clientes mediante su propio servicio de confianza. La siguiente lista resume las funciones de inicialización y administración de recursos que necesita para la puesta en práctica.

| Responsabilidades de Contoso                                 | Responsabilidades de la Biblioteca de interfaz de usuario                                     |
| -------------------------------------------------------- | --------------------------------------------------------------- |
| Proporcionar un token de acceso de Azure                          | Pasar el token de acceso dado para inicializar los componentes        |
| Proporcionar la función de actualización                                 | Actualizar el token de acceso mediante la función proporcionada por el desarrollador          |
| Recuperar o pasar información de incorporación a llamada o chat          | Pasar la información de llamada y chat para inicializar los componentes |
| Recuperar o pasar información de usuario para cualquier modelo de datos personalizado | Pasar el modelo de datos personalizado a los componentes que se van a representar          |

## <a name="platform-support"></a>Compatibilidad con plataformas

|Plataforma | Versiones|
|---------|---------|
| iOS     | iOS 13 o versiones posteriores |
| Android | versión 23 o posterior    |

## <a name="accessibility"></a>Accesibilidad

- La accesibilidad por diseño es un principio en todos los productos de Microsoft.
- La Biblioteca de interfaz de usuario sigue este principio para asegurarse de que todos los componentes de la interfaz de usuario sean totalmente accesibles.
- Durante la versión preliminar pública, la Biblioteca de interfaz de usuario seguirá mejorando e incorporará la característica de accesibilidad a los componentes de la interfaz de usuario.

## <a name="localization"></a>Localización

- La localización es clave para crear productos que se puedan usar en todo el mundo y por personas que hablan diferentes idiomas.
- La Biblioteca de interfaz de usuario proporcionará compatibilidad inmediata con algunos lenguajes y funcionalidades, como RTL.
- Los desarrolladores pueden proporcionar sus propios archivos de localización que se usarán para la Biblioteca de interfaz de usuario.

> [!div class="nextstepaction"]

Para obtener más información sobre cómo empezar a usar composiciones de la biblioteca móvil de la interfaz de usuario, siga [nuestra guía de inicio rápido](../../quickstarts/ui-library/get-started-call.md).