---
title: Interoperabilidad en las reuniones de Teams
titleSuffix: An Azure Communication Services concept document
description: Unirse a reuniones de Teams
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.openlocfilehash: 15936b796e677f913e6814b29c68394a9560285d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609733"
---
# <a name="teams-interoperability"></a>Interoperabilidad de Teams

> [!IMPORTANT]
> La interoperabilidad de BYOI está en versión preliminar pública y está disponible para todas las aplicaciones de Communication Services y organizaciones de Teams.
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

## <a name="overview"></a>Información general

Hay dos maneras de que los usuarios puedan acceder a la experiencia de llamadas de Teams:

- A través de clientes de Teams como **usuarios de Teams**. Esto incluye los clientes de escritorio, dispositivos móviles y Teams web. 
- A través de la experiencia web de la aplicación como **usuarios anónimos de Teams**. 

Los usuarios anónimos de Teams no tienen que ser usuarios de Teams. Azure Communication Services permite compilar y personalizar nuevos puntos de conexión de llamadas de Teams para usuarios y usuarios anónimos de Teams. Puede usar el SDK de llamadas de Communication Services y la biblioteca de interfaz de usuario para la personalización y la integración en cualquier aplicación o producto existente. En el diagrama siguiente se muestra cómo se puede unir a una reunión Teams desde varios puntos de conexión: ![Información general de varios escenarios de interoperabilidad dentro de Azure Communication Services](./media/teams-identities/teams_interop_overview.png)

Cuando un punto de conexión se conecta a una reunión de Teams mediante una identidad de Teams a través de las bibliotecas cliente de Azure Communication Services, el punto de conexión se trata como un usuario de Teams con un cliente de Teams. Los usuarios de Teams tienen acceso a más funcionalidad que los usuarios anónimos de Teams. Los usuarios de Teams pueden unirse a reuniones de Teams, realizar llamadas a otro usuario de Teams, recibir llamadas de números de teléfono y transferir llamadas en curso a la cola de llamadas de Teams. La conectividad del punto de conexión de Communication Services con identidad de Teams se muestra en el diagrama siguiente.

![Introducción a los escenarios de interoperabilidad en Azure Communication Services](./media/teams-identities/teams_interop_m365_identity_interop_overview.png)

## <a name="bring-your-own-identity"></a>Traiga su propia identidad

Traiga su propia identidad (BYOI) es el modelo común de interoperabilidad entre Azure Communication Services y Teams. Admite cualquier proveedor de identidades y esquema de autenticación. El primer escenario que se ha habilitado permite que la aplicación se una a reuniones de Microsoft Teams y Teams tratará a estos usuarios como cuentas externas anónimas, igual que los usuarios que se unen mediante la aplicación web anónima de Teams. Esta funcionalidad es ideal para aplicaciones de negocio a consumidor que reúnen empleados (que conocen Teams) y usuarios externos (con una experiencia de aplicación personalizada) en una reunión. En el futuro, habilitaremos escenarios adicionales, como llamadas directas y chat, que permitirán a la aplicación iniciar llamadas y chats con usuarios de Teams fuera del contexto de una reunión de Teams.

La capacidad de los usuarios de Communication Services de unirse a reuniones de Teams como usuarios anónimos se controla mediante la configuración existente "permitir la unión a reuniones anónimas", que también controla la unión a reuniones anónimas de Teams existentes.  Esta configuración se puede actualizar en el centro de administración de Teams (https://admin.teams.microsoft.com/meetings/settings) o con el cmdlet de PowerShell Set-CsTeamsMeetingConfiguration de Teams (https://docs.microsoft.com/powershell/module/skype/set-csteamsmeetingconfiguration). Al igual que con la unión a una reunión anónima de Teams, la aplicación debe tener el vínculo de reunión para unirse, que se puede recuperar a través de Graph API o del calendario de Microsoft Teams.  El nombre de los usuarios de Communication Services que se muestran en Teams se puede configurar mediante Calling SDK de Communication Services.

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
