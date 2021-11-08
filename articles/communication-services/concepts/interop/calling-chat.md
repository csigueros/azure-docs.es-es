---
title: Interoperabilidad con llamadas y chat de Teams
titleSuffix: An Azure Communication Services concept document
description: Interoperabilidad con llamadas y chat de Teams
author: tomkau
ms.author: tomkau
ms.date: 10/15/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.openlocfilehash: 328bfff2366efb416cbd41452a821256f675d0e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078700"
---
# <a name="teams-interoperability-calling-and-chat"></a>Interoperabilidad con Teams: llamadas y chat

> [!IMPORTANT]
> La interoperabilidad con llamadas y chat está en versión preliminar privada y está restringida a un número limitado de usuarios pioneros de Azure Communication Services. Puede [enviar este formulario para solicitar la participación en la versión preliminar](https://forms.office.com/r/F3WLqPjw0D) y revisaremos sus escenarios y evaluaremos su participación en la versión preliminar.
>
> Las API y los SDK de la versión preliminar privada se proporcionan sin contrato de nivel de servicio y no son adecuados para cargas de trabajo de producción y solo se deben usar con usuarios de prueba y datos de prueba. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 
> Para obtener soporte técnico, formular preguntas, proporcionar comentarios o notificar problemas, use el [canal de chat y llamadas ad hoc de interoperabilidad de Teams](https://teams.microsoft.com/l/channel/19%3abfc7d5e0b883455e80c9509e60f908fb%40thread.tacv2/Teams%2520Interop%2520ad%2520hoc%2520calling%2520and%2520chat?groupId=d78f76f3-4229-4262-abfb-172587b7a6bb&tenantId=72f988bf-86f1-41af-91ab-2d7cd011db47). Debe ser miembro del equipo de TAP de Azure Communication Services.

Como parte de esta versión preliminar, los SDK de Azure Communication Services se pueden usar para crear aplicaciones que permiten a los usuarios traer su propia identidad (BYOI) para iniciar llamadas 1:1 o chats 1:n con usuarios de Teams. Se aplican los [precios Estándar de ACS](https://azure.microsoft.com/pricing/details/communication-services/) a estos usuarios, pero no hay ninguna tarifa adicional por la propia funcionalidad de interoperabilidad.



## <a name="enabling-calling-and-chat-interoperability-in-your-teams-tenant"></a>Habilitación de la interoperabilidad con llamadas y chat en el inquilino de Teams
Para habilitar las llamadas y el chat entre los usuarios de Communication Services y el inquilino de Teams, use el nuevo cmdlet de PowerShell de Teams [Set-CsTeamsAcsFederationConfiguration](/powershell/module/teams/set-csteamsacsfederationconfiguration). Este cmdlet solo está disponible para los participantes en la versión preliminar privada. 

Las aplicaciones personalizadas creadas con Azure Communication Services para conectarse y comunicarse con los usuarios de Teams pueden ser usadas por los usuarios finales o por bots, y no hay diferencias en cómo aparecen para los usuarios de Teams, a menos que el desarrollador de la aplicación lo indique explícitamente.

Para iniciar una llamada o un chat con un usuario de Teams, se requiere el identificador de objeto de Azure Active Directory (AAD) del usuario. Este se puede obtener mediante [Microsoft Graph API](/graph/api/resources/users) o desde el directorio local si usa [Azure AD Connect](../../../active-directory/hybrid/how-to-connect-sync-whatis.md) (o algún otro mecanismo) para la sincronización entre el directorio local y AAD.

## <a name="calling"></a>Llamar
Con el SDK de llamadas, un usuario o un punto de conexión de Communication Services pueden iniciar una llamada 1:1 con usuarios de Teams, identificados por su identificador de objeto de Azure Active Directory (AAD). Puede modificar fácilmente una aplicación existente que llame a otros usuarios de Communication Services para que en su lugar llame a un usuario de Teams.
 
[Administración de llamadas](../../how-tos/calling-sdk/manage-calls.md?pivots=platform-web): una guía paso a paso sobre Azure Communication Services en Microsoft Docs

Llamada a otro usuario de ACS:
```js
const acsCallee = { communicationUserId: <'ACS_USER_ID>' }
const call = callAgent.startCall([acsCallee]);
```

Llamada a un usuario de Teams:
```js
const teamsCallee = { microsoftTeamsUserId: '<Teams User AAD Object ID>' }
const call = callAgent.startCall([teamsCallee]);
```
 
**Limitaciones y problemas conocidos**
- Los usuarios de Teams deben estar en modo "TeamsOnly". Los usuarios de Skype Empresarial no pueden recibir llamadas 1:1 de usuarios de Communication Services.
- No se admite la conversión a una llamada de grupo.
- Los usuarios de Communication Services no se muestran correctamente en el historial de llamadas.
- La grabación de llamadas de Communication Services no está disponible para las llamadas 1:1.
- No se admiten las funcionalidades avanzadas de enrutamiento de llamadas, como el reenvío de llamadas, la recogida de llamadas de grupo, el aviso de llamada simultáneo y el correo de voz.
- Los usuarios de Teams no pueden establecer usuarios de Communication Services como destinos de reenvío o transferencia.
- No se admite la bifurcación LyncIpPhone.

## <a name="chat"></a>Chat
Con el SDK de chat, los usuarios y los puntos de conexión de Communication Services pueden iniciar un chat 1:n con usuarios de Teams, identificados por su identificador de objeto de Azure Active Directory (AAD). Puede modificar fácilmente una aplicación existente que cree chats con otros usuarios de Communication Services para que en su lugar cree chats con un usuario de Teams.
                                            
[Inicio rápido: Incorporación de chat a una aplicación](../../quickstarts/chat/get-started.md?pivots=programming-language-javascript)

Creación de un chat con un usuario de Teams:
```js
async function createChatThread() { 
const createChatThreadRequest = {  topic: "Hello, World!"  }; 
const createChatThreadOptions = {
    participants: [ { 
        id: { microsoftTeamsUserId: '<TEAMS_USER_ID>' }, 
        displayName: '<USER_DISPLAY_NAME>' }
    ] }; 
const createChatThreadResult = await chatClient.createChatThread( 
createChatThreadRequest, createChatThreadOptions ); 
const threadId = createChatThreadResult.chatThread.id; return threadId; }
```                                         

**Funcionalidad admitida**
-   Envío y recepción de mensajes (tipo: texto, texto enriquecido, emoticonos) 
-   Un usuario de Communication Services puede editar los mensajes enviados.
-   Eliminación de mensajes enviados
-   Recepción de notificaciones en tiempo real (eventos relacionados con conversaciones y mensajes admitidos actualmente por ACS)
-   Envío y recepción de indicadores de escritura
-   Envío y recepción de recibos de lectura
-   Agregar participantes e historial de mensajes compartidos: un usuario de Teams solo puede agregar usuarios de Teams. Un usuario de Communication Services puede agregar usuarios de Teams y Communication Services.
-   Eliminación de un participante existente del chat
-   Salir del chat
-   Actualización del tema de chat
-   Un usuario de Communication Services puede eliminar el chat.


**Limitaciones y problemas conocidos**
- Se produce un error cuando un usuario de Teams edita los mensajes.
- La eliminación de una conversación por parte del usuario de Communication Services elimina el historial de mensajes del usuario de Teams y quita al usuario de Teams de la conversación.
- La interfaz de usuario del cliente de Teams para usuarios externos es incoherente.
- No se puede iniciar una llamada con los participantes del chat con el cliente de Teams.


## <a name="privacy"></a>Privacidad
La interoperabilidad entre Azure Communication Services y Microsoft Teams permite que las aplicaciones y los usuarios participen en llamadas, reuniones y chats de Teams. Es su responsabilidad asegurarse de que se notifica a los usuarios de la aplicación cuando se habilita la grabación o la transcripción en una llamada o reunión de Teams.

Microsoft le indicará mediante la API de Azure Communication Services que ha comenzado la grabación o la transcripción, y debe comunicar este hecho, en tiempo real, a los usuarios dentro de la interfaz de usuario de la aplicación. Acepta la compensación de Microsoft por todos los costos y daños incurridos como resultado de su incumplimiento de esta obligación.
