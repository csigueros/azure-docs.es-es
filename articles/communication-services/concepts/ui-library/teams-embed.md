---
title: SDK de inserción de Teams
titleSuffix: An Azure Communication Services - Teams Embed SDK description
description: En este documento, revise las funcionalidades de inserción de Teams y cómo va a funcionar en las aplicaciones.
author: tophpalmer
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 14a063bc6b274c0c93ff700648e670f1c740730d
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2021
ms.locfileid: "114401034"
---
# <a name="teams-embed"></a>Inserción de Teams

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

La inserción de Teams es una funcionalidad de Azure Communication Services que se centra en las interacciones de llamada de negocio a consumidor y de negocio a negocio. El núcleo del sistema de inserción de Teams es la [llamada de vídeo y de voz](../voice-video-calling/calling-sdk-features.md), y se crea en las primitivas de llamada de Azure para ofrecer una experiencia de usuario completa basándose en las reuniones de Microsoft Teams.

El SDK de inserción de Teams es de código cerrado y permite que estas funcionalidades estén disponibles en un formato compuesto y llave en mano. Se coloca la inserción de Teams en el lienzo de la aplicación y el SDK genera una experiencia de usuario completa. Dado que esta experiencia del usuario es muy similar a las reuniones de Microsoft Teams, puede aprovecharse de lo siguiente:

- Reducción del tiempo de desarrollo y complejidad de ingeniería.
- Familiaridad del usuario final con la experiencia de Teams.
- Capacidad de volver a usar el [contenido de entrenamiento del usuario final de Teams](https://support.microsoft.com/office/meetings-in-teams-e0b0ae21-53ee-4462-a50d-ca9b9e217b67).

El SDK de la inserción de Teams proporciona la mayoría de las características admitidas en las reuniones de Teams, entre las que se incluyen:

## <a name="joining-a-meeting"></a>Unión a una reunión

Los usuarios pueden unirse fácilmente a la reunión mediante la URL de reunión de Teams para una experiencia más sencilla y excelente, al igual que la aplicación Teams. Agregar la funcionalidad al usuario para formar parte de reuniones en vivo extensas sin perder la experiencia de la simplicidad de la aplicación Teams.

## <a name="pre-meeting-experience"></a>Experiencia previa a la reunión

Como participante de cualquiera de las reuniones, puede definir una configuración predeterminada para dispositivos de audio y vídeo. Agregue su nombre y traiga su propio avatar de imagen.

## <a name="meeting-experience"></a>Experiencia de reunión

Personalice la experiencia del usuario y ajuste las funcionalidades según sus necesidades. Controlará la experiencia general durante las reuniones.

- [**Uso compartido de contenido**](https://support.microsoft.com/office/share-content-in-a-meeting-in-teams-fcc2bf59-aecd-4481-8f99-ce55dd836ce8): el usuario puede compartir vídeos, fotos o toda la pantalla, y los usuarios verán el contenido compartido.

- [**Varias opciones de diseño para la galería de vídeos**](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae): traiga las funcionalidades para seleccionar las opciones predeterminadas de diseño durante la reunión (galería grande, modo junto, foco, anclado y contenido destacado). Y adapte el diseño según la resolución del dispositivo.

- [**Activar o desactivar vídeo**](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae#bkmk_turnvideoonoff): ofrece a los usuarios la posibilidad de administrar su vídeo durante la reunión.

- **Acciones del asistente**: entre otras muchas acciones, el usuario puede ["levantar la mano"](https://support.microsoft.com/en-us/office/raise-your-hand-in-a-teams-meeting-bb2dd8e1-e6bd-43a6-85cf-30822667b372), silenciar y activar el micrófono, cambiar la configuración de la cámara o el audio y colgar.

- [**Compatibilidad con varios idiomas**](https://support.microsoft.com/topic/languages-supported-in-microsoft-teams-for-education-293792c3-352e-4b24-9fc2-4c28b5de2db8): admite 56 idiomas durante toda la experiencia de los equipos.

## <a name="quality-and-security"></a>Calidad y seguridad

El SDK de inserción de Teams está construido según los estándares de calidad de Teams, por lo que para la calidad del vídeo puede ver [los requisitos de ancho de banda.](/microsoftteams/prepare-network#bandwidth-requirements)

Puede usar un token de acceso de Azure Communication Service. Obtenga más información sobre la [generación y administración de tokens de acceso](../../quickstarts/access-tokens.md).

## <a name="capabilities"></a>Funcionalidades

| Funcionalidades del SDK                                                        | Disponibilidad |
|---------------------------------------------------------------------|--------------|
| *Acciones de reunión*                                                   |              |
| Unirse a una llamada                                                         | Sí          |
| Controlar el *estado e la llamada* (incluye los estados de conexión, el número de participantes y las modalidades, como el estado del micrófono o la cámara).                                           | Sí          |
| Subir eventos de usuario de tipo Mano levantada, Silenciado y envío de evento de vídeo                                                                 | Sí          |
| Compatibilidad con el control de red no confiable                                      | Sí          |
| Quitar participantes de la reunión                                    | Sí          |
| Admite 56 idiomas                                               | Sí          |
| Chat durante la reunión y chat 1n1                                    | No           |
| Llamadas RTC                                                        | No           |
| Grabación y transcripción                                            | No           |
| Uso compartido de la pizarra                                                  | No           |
| Ir a salas                                                 | No           |
| *Experiencia previa a la reunión*                                        |              |
| Unirse a una llamada de grupo con GUID y token de ACS                             | Sí          |
| Unirse a una reunión con la dirección URL de Live Meeting y el token de ACS                    | Sí          |
| Unirse a una reunión con la dirección URL y el token de ACS                         | Sí          |
| Unirse a través de la espera en la sala de espera                                           | Sí          |

| Personalización de la experiencia del usuario                                       | Disponibilidad |
|---------------------------------------------------------------------|--------------|
| *Acciones de reunión*                                                   |              |
| Mostrar la lista de llamadas                                             | Sí          |
| Personalización del diseño: colores, iconos y botones                        | Parcialmente    |
| Personalizar los iconos de la pantalla de llamadas                                     | Sí          |
| Cambiar el diseño de las vistas de reunión                                         | Sí          |
| Cambio de diseño de NxN de llamada dinámica                                    | Sí          |
| Levantar o bajar la mano                                                     | Sí          |
| Silenciar/Anular el silencio                                                        | Sí          |
| Poner en espera                                                         | Sí          |
| Seleccionar enrutamiento de audio                                                | Sí          |
| Seleccionar cámara                                                       | Sí          |
| Compartir foto, pantalla y vídeo                                       | Sí          |
| Iniciar o detener vídeo                                                    | Sí          |
| Evento de presión de icono de usuario                                               | Sí          |
| Evento de presión de la placa de identificación                                              | Sí          |
| Desenfoque de fondo                                                     | Sí          |
| Personalizar el color de fondo de la pantalla                               | No           |
| Personalizar el color de la barra superior o inferior                                  | No           |
| Uso compartido de la pizarra                                                  | No           |
| *Experiencia previa a la reunión*                                            |              |
| La unión puede configurar el nombre para mostrar, habilitar el uso compartido de fotos            | Sí          |
| La unión puede configurarse para mostrar la vista de almacenamiento provisional de llamadas (pantalla previa a la llamada)   | Sí          |
| La unión puede configurarse para mostrar la placa de identificación en la pantalla de llamada             | Sí          |
| Personalizar la pantalla de la sala de espera                                          | Parcialmente    |

Para más información sobre cómo empezar con el SDK de inserción de Teams, siga la [guía de introducción](../../quickstarts/meeting/getting-started-with-teams-embed.md). Si desea obtener más información sobre las funcionalidades del SDK, consulte la [guía de ejemplos](../../quickstarts/meeting/samples-for-teams-embed.md).