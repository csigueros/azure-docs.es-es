---
title: Interoperabilidad en las reuniones de Teams
titleSuffix: An Azure Communication Services concept document
description: Unirse a reuniones de Teams
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e884079bc159dbbc76d6443dc0e095c4d8f596ab
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2021
ms.locfileid: "114668103"
---
# <a name="teams-interoperability"></a>Interoperabilidad de Teams

> [!IMPORTANT]
> La interoperabilidad de BYOI está en versión preliminar pública y ampliamente disponible a petición. Para habilitar o deshabilitar la [interoperabilidad de los inquilinos de equipos](../concepts/teams-interop.md), complete [este formulario](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).
>
> La interoperabilidad de Microsoft 365 autenticada está en versión preliminar privada y restringida mediante controles de servicio para los usuarios pioneros de Azure Communication Services. Para unirse al programa de acceso pionero, complete [este formulario](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8MfnD7fOYZEompFbYDoD4JUMkdYT0xKUUJLR001ODdQRk1ITTdOMlRZNSQlQCN0PWcu).
>
> Las API y los SDK de versión preliminar se ofrecen sin Acuerdo de Nivel de Servicio y no es aconsejable usarlos para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Communication Services se puede usar para crear aplicaciones personalizadas que interaccionen con Microsoft Teams. Los usuarios finales de la aplicación de Communication Services pueden interactuar con los participantes de Teams mediante voz, vídeo, chat o uso compartido de la pantalla. En el siguiente vídeo se demuestra esta funcionalidad:


<br>
<br>


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWGTqQ]


Azure Communication Services admite dos tipos de interoperabilidad con Teams en función de la identidad del usuario final:

- **Traiga su propia identidad.** Puede controlar la autenticación de los usuarios y los usuarios de las aplicaciones personalizadas no necesitan tener identidades de Azure Active Directory ni licencias de Teams para unirse a las reuniones de Teams. Teams trata la aplicación como usuario externo anónimo.
- **Identidad de Microsoft 365 Teams.** La aplicación actúa en nombre de la identidad de Microsoft 365 del usuario final y sus recursos de Teams configurados. Estas aplicaciones autenticadas pueden realizar llamadas y unirse a reuniones sin problemas en nombre de los usuarios de Microsoft 365.

Las aplicaciones pueden implementar ambos esquemas de autenticación y dejar la opción de autenticación al usuario final.

## <a name="bring-your-own-identity"></a>Traiga su propia identidad
Traiga su propia identidad (BYOI) es el modelo común de interoperabilidad entre Azure Communication Services y Teams. Admite cualquier proveedor de identidades y esquema de autenticación. La aplicación puede unirse a reuniones de Microsoft Teams y Teams tratará estos usuarios como cuentas externas anónimas. El nombre de los usuarios de Communication Services que se muestran en Teams se puede configurar mediante Calling SDK de Communication Services.

Esta funcionalidad es ideal para aplicaciones de negocio a consumidor que reúnen a empleados (que conocen Teams) y usuarios externos (con una experiencia de aplicación personalizada) en una reunión. Los detalles de la reunión que deben compartirse con usuarios externos de la aplicación se pueden recuperar mediante Graph API o el calendario de Microsoft Teams.

Los usuarios externos podrán usar la funcionalidad principal de audio, vídeo, uso compartido de pantalla y chat mediante los SDK de Azure Communication Services. Características como levantar la mano, el modo de reunión y las salas de descanso solo estarán disponibles para los usuarios de Teams. Los usuarios de Communication Services pueden enviar y recibir mensajes solo mientras están presentes en la reunión de Teams y si la reunión no está programada para un canal.

La aplicación personalizada debe considerar el uso de la autenticación del usuario y otras medidas de seguridad para proteger las reuniones de Teams. Piense en las implicaciones de seguridad de permitir que usuarios anónimos unan a las reuniones y use la [guía de seguridad de Teams](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) para configurar las funcionalidades disponibles para los usuarios anónimos.

En la [página de arquitectura de cliente y servidor](client-and-server-architecture.md) encontrará información adicional sobre los flujos de datos necesarios para unirse a las reuniones de Teams. El [ejemplo de elementos principales de llamada grupal](../samples/calling-hero-sample.md) proporciona código de ejemplo para unirse a una reunión de Teams desde una aplicación web.

## <a name="microsoft-365-teams-identity"></a>Identidad de Microsoft 365 Teams
Calling SDK de Azure Communication Services se puede usar con las identidades de Microsoft 365 Teams para admitir experiencias de Teams similares a las de interoperabilidad con Teams. Las identidades de Microsoft 365 Teams se proporcionan y se autentican mediante Azure Active Directory. La aplicación puede realizar o aceptar llamadas con una identidad de Microsoft 365 normal. Todos los atributos y detalles sobre el usuario están vinculados al usuario de Azure Active Directory.

Este modelo de identidad es ideal para casos de uso en los que se necesita una interfaz de usuario personalizada, donde el cliente de Teams no está disponible para la plataforma o si el cliente de Teams no admite un nivel suficiente de personalización. Por ejemplo, se puede usar una aplicación para responder llamadas telefónicas en nombre del número de RTC aprovisionado en Teams del usuario final y tener una interfaz de usuario optimizada para una recepcionista o un centro de llamadas.  

La funcionalidad de uso compartido de pantalla y llamadas está disponible mediante Calling SDK de Communication Services. La administración de las llamadas está disponible mediante Graph API, la configuración en el cliente de Teams o el portal de administración de Teams. La funcionalidad de chat está disponible con Graph API.

Los usuarios de Teams se autentican con la biblioteca MSAL en Azure Active Directory en la aplicación cliente. Los tokens de autenticación se intercambian por el token de Microsoft 365 Teams mediante Identity SDK de Communication Services. Se recomienda implementar un intercambio de tokens en los servicios de back-end a medida que se firmen las solicitudes de intercambio con credenciales para Azure Communication Services. En los servicios de back-end puede exigir cualquier autenticación adicional.

Para más información sobre la funcionalidad, únase a nuestro programa TAP para de acceso pionero al rellenar [este formulario](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8MfnD7fOYZEompFbYDoD4JUMkdYT0xKUUJLR001ODdQRk1ITTdOMlRZNSQlQCN0PWcu).

## <a name="comparison"></a>De comparación

|Criterios|Traiga su propia identidad| Identidad de Microsoft 365 Teams|
|---|---|---|
|Aplicable| En escenarios de negocio a consumidor para aplicaciones de consumidor | En escenarios de negocio a negocio o de negocio a consumidor en aplicaciones empresariales |
|Proveedor de identidades|Any|Azure Active Directory|
|Autenticación y autorización|Personalizado*| De Azure Active Directory y personalizada*|
|Llamada a disponible con | Calling SDK de Communication Services | Calling SDK de Communication Services |
|Chat disponible con | Chat SDK de Communication Services | Graph API |
|Compatibilidad con RTC| Llamada de voz saliente, enrutamiento directo saliente, [detalles](./telephony-sms/telephony-concept.md) | llamada entrante asignada a la identidad de Teams, llamada saliente mediante el plan de llamada|

\* La lógica del servidor que emite tokens de acceso puede realizar cualquier autenticación y autorización personalizada de la solicitud.

## <a name="privacy"></a>Privacidad
La interoperabilidad entre Azure Communication Services y Microsoft Teams permite que las aplicaciones y los usuarios participen en llamadas, reuniones y chats de Teams. Es su responsabilidad asegurarse de que se notifica a los usuarios de la aplicación cuando se habilita la grabación o la transcripción en una llamada o reunión de Teams.

Microsoft le indicará a través de la API de Azure Communication Services que ha comenzado la grabación o la transcripción y debe comunicar este hecho, en tiempo real, a los usuarios dentro de la interfaz de usuario de la aplicación. Acepta la compensación de Microsoft por todos los costos y daños incurridos como resultado de su incumplimiento de esta obligación.

## <a name="pricing"></a>Precios
Todo el uso de los SDK y las API de Azure Communication Service incrementa los [medidores de facturación de Azure Communication Service](https://azure.microsoft.com/pricing/details/communication-services/). Las interacciones con Microsoft Teams, como unirse a una reunión o iniciar una llamada telefónica mediante un número asignado de Teams, incrementarán estos medidores, pero no hay ningún cargo adicional por la propia funcionalidad de interoperabilidad de Teams y no hay distinción de precios entre las opciones de autenticación BYOI y Microsoft 365.

Si la aplicación de Azure tiene un usuario final que pasa 10 minutos en una reunión con un usuario de Microsoft Teams, la combinación de esos dos usuarios consumió 20 minutos de llamada. Los 10 minutos que se han pasado por la aplicación personalizada y el uso de las API y los SDK de Azure se facturarán al recurso. Sin embargo, los 10 minutos consumidos por el usuario final en la aplicación de Teams nativa están cubiertos por la licencia de Teams vigente y Azure no los tiene en cuenta.

## <a name="teams-in-government-clouds-gcc"></a>Teams en las nubes de la administración pública (GCC)
Por ahora la interoperabilidad de Azure Communication Services no es compatible con las implementaciones de Teams mediante las [nubes de la administración pública (GCC) de Microsoft 365](/MicrosoftTeams/plan-for-government-gcc).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Unión de una aplicación de llamada BYOI a una reunión de Teams](../quickstarts/voice-video-calling/get-started-teams-interop.md)
> [Autenticación de usuarios de Microsoft 365](../quickstarts/manage-teams-identity.md)
