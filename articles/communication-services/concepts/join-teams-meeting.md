---
title: Interoperabilidad en las reuniones de Teams
titleSuffix: An Azure Communication Services concept document
description: Unirse a reuniones de Teams
author: tomkau
ms.author: tomkau
ms.date: 10/15/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.openlocfilehash: c0713588eadebbd1d9376faec8e61cf5a88dfd1d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068919"
---
# <a name="join-a-teams-meeting"></a>Unión a una reunión de Teams

> [!IMPORTANT]
> La interoperabilidad de BYOI está en versión preliminar pública y está disponible para todas las aplicaciones de Communication Services y organizaciones de Teams.
>
> Las API y los SDK de versión preliminar se ofrecen sin Acuerdo de Nivel de Servicio y no es aconsejable usarlos para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Communication Services se puede usar para crear aplicaciones que permiten a los usuarios unirse a las reuniones de Teams y participar en ellas. Los [precios estándar de ACS](https://azure.microsoft.com/pricing/details/communication-services/) se aplican a estos usuarios, pero no hay ninguna tarifa adicional por la propia funcionalidad de interoperabilidad. Con el modelo traiga su propia identidad (BYOI), puede controlar la autenticación de los usuarios y los usuarios de las aplicaciones no necesitan licencias de Teams para unirse a las reuniones de Teams. Esta posibilidad resulta idónea para soluciones de negocio a consumidor que permiten a los usuarios de Teams con licencia (por ejemplo, proveedores de atención sanitaria o asesores financieros) y usuarios externos (por ejemplo, pacientes o clientes) unirse a una experiencia de consulta virtual con una aplicación personalizada.

También es posible usar identidades de Teams de Microsoft 365 con los SDK de Azure Communication Services. Puede encontrar más información disponible [aquí](./teams-interop.md).

Actualmente, no es posible que un usuario de Teams se una a una llamada iniciada mediante Calling SDK de Azure Communication Services.

## <a name="enabling-anonymous-meeting-join-in-your-teams-tenant"></a>Habilitación de la unión anónima a reuniones en inquilinos de Teams

Cuando un usuario BYOI se une a una reunión de Teams, se le considera un usuario externo anónimo, de forma parecida a los usuarios que se unen a una reunión de Teams de forma anónima mediante la aplicación web de Teams. La posibilidad de que los usuarios BYOI se unan a reuniones de Teams como usuarios anónimos se controla mediante la configuración existente "permitir la unión a reuniones anónimas", que también controla la unión anónima a reuniones de Teams existentes. Este valor se puede actualizar en el [centro de administración de Teams](https://admin.teams.microsoft.com/meetings/settings) o con el cmdlet de PowerShell [Set-CsTeamsMeetingConfiguration](/powershell/module/skype/set-csteamsmeetingconfiguration) de Teams.  

Las aplicaciones personalizadas compiladas con Azure Communication Services para conectarse y comunicarse con los usuarios de Teams pueden ser usadas por los usuarios finales o por bots, y no hay diferencias en cómo aparecen ante los usuarios de Teams, a menos que el desarrollador de la aplicación lo indique explícitamente como parte de la comunicación. La aplicación personalizada debe considerar el uso de la autenticación del usuario y otras medidas de seguridad para proteger las reuniones de Teams. Piense en las implicaciones de seguridad de permitir que usuarios anónimos unan a las reuniones y use la [guía de seguridad de Teams](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) para configurar las funcionalidades disponibles para los usuarios anónimos.

## <a name="meeting-experience"></a>Experiencia de reunión

Al igual que con la unión a una reunión anónima de Teams, la aplicación debe tener el vínculo de reunión para unirse, que se puede recuperar a través de Graph API o del calendario de Microsoft Teams. El nombre de los usuarios BYOI que se muestra en Teams se puede configurar a través de Calling SDK de Communication Services y se etiquetan como "externos" para permitir que los usuarios de Teams sepan que no se han autenticado mediante Azure Active Directory.

Durante una reunión, los usuarios de Communication Services podrán usar la funcionalidad principal de audio, vídeo, uso compartido de pantalla y chat mediante los SDK de Azure Communication Services. Cuando un usuario de Communication Services deja la reunión o esta finaliza, ya no puede enviar ni recibir nuevos mensajes de chat, pero tendrá acceso a los mensajes enviados y recibidos durante la reunión. Los usuarios anónimos de Communication Services no pueden agregar participantes adicionales a la reunión y no pueden iniciar la grabación ni la transcripción de esta.

En la [página de arquitectura de cliente y servidor](client-and-server-architecture.md) encontrará información adicional sobre los flujos de datos necesarios para unirse a las reuniones de Teams. El [ejemplo de elementos principales de llamada grupal](../samples/calling-hero-sample.md) proporciona código de ejemplo para unirse a una reunión de Teams desde una aplicación web.

## <a name="privacy"></a>Privacidad
La interoperabilidad entre Azure Communication Services y Microsoft Teams permite que las aplicaciones y los usuarios participen en llamadas, reuniones y chats de Teams. Es su responsabilidad asegurarse de que se notifica a los usuarios de la aplicación cuando se habilita la grabación o la transcripción en una llamada o reunión de Teams.

Microsoft le indicará a través de Azure Communication Services API que ha comenzado la grabación o la transcripción, y debe comunicar este hecho, en tiempo real, a los usuarios dentro de la interfaz de usuario de la aplicación. Acepta la compensación de Microsoft por todos los costos y daños incurridos como resultado de su incumplimiento de esta obligación.

## <a name="limitations-and-known-issues"></a>Limitaciones y problemas conocidos

- Un usuario BYOI puede unirse a una reunión de Teams programada para un canal de Teams y usar audio y vídeo, pero no podrá enviar ni recibir ningún mensaje de chat, ya que no es miembro del canal.
- Al usar Microsoft Graph para [enumerar los participantes de una reunión de Teams](/graph/api/call-list-participants), los detalles de los usuarios de Communication Services no se incluyen actualmente.
- Las presentaciones de PowerPoint no se representan para los usuarios de Communication Services.
- Las reuniones de Teams admiten hasta 1000 participantes, pero Calling SDK de Azure Communication Services solo admite actualmente 350 participantes.
- Con [Cloud Video Interop para Microsoft Teams](/microsoftteams/cloud-video-interop), algunos dispositivos han experimentado problemas cuando un usuario de Communication Services comparte su pantalla.
- Características como levantar la mano, el modo de reunión y las salas de descanso solo están disponibles para los usuarios de Teams.
- Calling SDK no admite actualmente subtítulos para reuniones de Teams.
- Los usuarios de Communication Services no pueden unirse a [eventos en directo de Teams](/microsoftteams/teams-live-events/what-are-teams-live-events).
- Los [eventos de controlador de actividad de Teams](/microsoftteams/platform/bots/bot-basics?tabs=csharp) para bots no se desencadenan cuando los usuarios de Communication Services se unen a una reunión de Teams.

## <a name="next-steps"></a>Pasos siguientes

- [Unión a una reunión de Teams](../how-tos/calling-sdk/teams-interoperability.md)
- [Inicio rápido: Unión de una aplicación de llamadas BYOI a una reunión de Teams](../quickstarts/voice-video-calling/get-started-teams-interop.md)
- [Inicio rápido: Unión de una aplicación de chat BYOI a una reunión de Teams](../quickstarts/chat/meeting-interop.md)
