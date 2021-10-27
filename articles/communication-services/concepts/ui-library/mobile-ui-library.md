---
title: Biblioteca móvil de interfaz de usuario
titleSuffix: An Azure Communication Services - UI Mobile Library
description: En este documento, se presenta la biblioteca móvil de interfaz de usuario.
author: jorgegarc
ms.author: jorgegarc
ms.date: 09/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: fdf84a2eb2cc57081a315bbf9dc41c14ee96ae8e
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181555"
---
# <a name="ui-mobile-library"></a>Biblioteca móvil de interfaz de usuario

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

La biblioteca móvil de interfaz de usuario es una funcionalidad de Azure Communication Services que se centra en proporcionar componentes de interfaz de usuario para interacciones de llamada de negocio a consumidor y de negocio a negocio. El enfoque principal de la Biblioteca móvil de interfaz de usuario son los componentes para [llamadas de vídeo y voz](../voice-video-calling/calling-sdk-features.md). Nos basamos en los primitivos de llamada de Azure para ofrecer una experiencia de usuario completa para llamadas y reuniones en dispositivos móviles.

El objetivo de la biblioteca móvil de interfaz de usuario es proporcionar estas funcionalidades en un formato compuesto listo para usar. Se coloca el SDK en el lienzo de la aplicación de desarrollo móvil que prefiera y el SDK genera una experiencia de usuario completa. Dado que esta experiencia de usuario es ligera, puede aprovechar el tiempo de desarrollo reducido y la complejidad de la ingeniería.

## <a name="composites"></a>Compuestos

Los compuestos son componentes de nivel superior formados por componentes más pequeños que ofrecen soluciones inmediatas para escenarios de comunicación habituales con el uso de Azure Communication Services.

Los desarrolladores pueden crear fácilmente instancias para los compuestos mediante un token de acceso de Azure Communication Services y los atributos de configuración necesaria para llamar.

| Compuesto                                                                   | Casos de uso                                                                                                                                                                                                                                                                                                  |
| --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [CallComposite](../../quickstarts/ui-library/get-started-call.md)  | Experiencia de llamada que permite a los usuarios iniciar una llamada o unirse a ella. Dentro de la experiencia, los usuarios pueden configurar sus dispositivos, participar en la llamada con vídeo y ver a otros participantes, incluidos aquellos con el vídeo encendido. Para la interoperabilidad de equipos, se incluye la funcionalidad de vestíbulo para que los usuarios puedan esperar a ser admitidos. |

## <a name="scenarios"></a>Escenarios

### <a name="joining-a-videoaudio-call"></a>Unirse a una llamada de audio o vídeo

Los usuarios pueden unirse fácilmente a la llamada mediante la *dirección URL de la reunión de Teams* o pueden configurar una llamada de Azure Communication Services para tener una experiencia más sencilla pero igual de buena que con la aplicación Teams. Se agrega la funcionalidad para que el usuario forme parte de una extensa llamada de audio o vídeo en directo, sin perder la experiencia de la simplicidad y centrándose en lo que realmente importa.

### <a name="pre-call-experience"></a>Experiencia de llamada previa

Como participante de las llamadas, puede proporcionar un nombre y establecer una configuración predeterminada para dispositivos de audio y vídeo, y, entonces, ya puede unirse a la llamada.

:::image type="content" source="../media/mobile-ui/teams-meet.png" alt-text="Experiencia previa a la reunión":::

### <a name="call-experience"></a>Experiencia de llamada

El compuesto de llamadas proporciona una experiencia de un extremo a otro, optimizando el tiempo de desarrollo y enfocándose en un diseño limpio.  

:::image type="content" source="../media/mobile-ui/calling-composite.png" alt-text="Experiencia de reunión":::

**La experiencia de llamada proporciona todas estas funcionalidades en un solo componente compuesto, lo que proporciona una ruta de acceso clara sin código complejo que conduce a un tiempo de desarrollo más rápido.**

### <a name="quality-and-security"></a>Calidad y seguridad

Los compuestos móviles se inicializan con [tokens de acceso de Azure Communication Services](../../quickstarts/access-tokens.md).

### <a name="more-details"></a>Más detalles

Si necesita más información sobre los compuestos móviles, visite el [sitio de casos de uso](mobile-ui-use-cases.md).

## <a name="what-ui-artifact-is-best-for-my-project"></a>¿Qué artefacto de interfaz de usuario es el mejor para mi proyecto?

Comprender estos requisitos le ayudará a elegir la biblioteca cliente adecuada:

- **¿Cuánta personalización desea?** Las bibliotecas cliente principales de Azure Communication no tienen experiencia de usuario y están diseñadas para que pueda compilar cualquier experiencia de usuario que quiera. Los componentes de la Biblioteca de interfaz de usuario proporcionan recursos de interfaz de usuario a cambio de una personalización reducida.

- **¿A qué plataformas quiere dirigirse?** Cada plataforma tiene diferentes funcionalidades.

Se pueden encontrar detalles sobre la disponibilidad de características de la [Biblioteca de interfaz de usuario aquí](mobile-ui-use-cases.md), pero las ventajas principales se resumen a continuación.

| Biblioteca cliente / SDK  | Complejidad de la implementación | Capacidad de personalización | Llamar |  [Interoperabilidad de equipos](../../concepts/teams-interop.md) |
| --------------------- | ------------------------- | --------------------- |  ---- | ----------------------------------------------------------------------------------------------- |
| Componentes compuestos  | Bajo                       | Bajo                   |         ✔    | ✔                                                                                               |
| Bibliotecas cliente principales | Alto                      | Alto                  |         ✔    | ✔                                                                                               |

> [!div class="nextstepaction"]

Para obtener más información sobre cómo empezar con las composiciones de la Biblioteca móvil de interfaz de usuario, siga [nuestra guía de inicio rápido](../../quickstarts/ui-library/get-started-call.md).