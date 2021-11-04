---
title: Directrices de privacidad para asistentes de voz en Windows
titleSuffix: Azure Cognitive Services
description: Instrucciones para habilitar la activación por voz de un agente de voz de forma predeterminada.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 074335f9ccf9030a83354355a483ef5cbe0b58d3
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500191"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Directrices de privacidad para asistentes de voz en Windows

Es importante que los usuarios obtengan información clara acerca de cómo se recopilan y usan sus datos de voz, y también que controlen esta recopilación y el modo de realizarla. Estas facetas principales de la privacidad (*divulgación* y *consentimiento*) son especialmente importantes para los asistentes de voz integrados en Windows, dada la naturaleza de escucha siempre activa de su uso.

Los desarrolladores que crean asistentes de voz en Windows deben incluir elementos de interfaz de usuario claros dentro de sus aplicaciones que reflejen las funcionalidades de escucha del asistente.

> [!NOTE]
> Si no se proporciona la divulgación adecuada y el consentimiento de una aplicación de asistente, incluidas las actualizaciones de la aplicación, es posible que el ayudante deje de estar disponible para la activación por voz hasta que se resuelvan los problemas de privacidad.

## <a name="minimum-requirements-for-feature-inclusion"></a>Requisitos mínimos para la inclusión de características

Los usuarios de Windows pueden ver y controlar la disponibilidad de sus aplicaciones de asistente en **`Settings > Privacy > Voice activation`** .

 > [!div class="mx-imgBorder"]
 > [![Captura de pantalla que muestra las opciones para controlar la disponibilidad de Cortana. ](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "Una entrada de configuración de privacidad de activación por voz de Windows para una aplicación de asistente")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

Para que se le pueda incluir en esta lista, póngase en contacto con Microsoft en winvoiceassistants@microsoft.com como primer paso. De forma predeterminada, los usuarios deben habilitar de forma explícita la activación por voz de un nuevo asistente en **`Settings > Privacy > Voice Activation`** , mientras que una aplicación puede hacerlo mediante vinculación de protocolo con `ms-settings:privacy-voiceactivation`. Una aplicación permitida aparece en la lista una vez que se ha ejecutado y ha usado las API `Windows.ApplicationModel.ConversationalAgent`. Su configuración de activación por voz se puede modificar una vez que la aplicación ha obtenido consentimiento de micrófono por parte del usuario.

Dado que la configuración de privacidad de Windows incluye información sobre cómo funciona la activación por voz y tiene una interfaz de usuario estándar para controlar los permisos, se cumplen los requisitos de revelación y consentimiento. El asistente permanece en esta lista de permitidos siempre que no:

* Engañe o desinforme al usuario sobre la activación por voz o el control de datos de voz por parte del asistente
* Interfiera de forma indebida con otro asistente
* Incumpla cualquier otra directiva relevante de Microsoft

Si se detecta cualquiera de los hechos anteriores, Microsoft puede quitar un asistente de la lista de permitidos hasta que se resuelvan los problemas.

> [!NOTE]
> En todos los casos, el permiso de activación por voz requiere el permiso micrófono. Si una aplicación de asistente no tiene acceso al micrófono, no será válida para la activación por voz y aparecerá en la configuración de privacidad de activación por voz en estado deshabilitado.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>Requisitos adicionales para la inclusión en el consentimiento del micrófono

Los autores de asistentes que quieran facilitar el uso de la activación por voz por parte de los usuarios pueden hacerlo si satisfacen requisitos adicionales de revelación y consentimiento sin necesidad de un desplazamiento adicional a la página de configuración. Una vez aprobada, la activación por voz está disponible en cuanto el usuario concede permiso de micrófono al asistente. Para ello, un asistente debe realizar lo siguiente **antes** de solicitar consentimiento de micrófono (por ejemplo, mediante la API `AppCapability.RequestAccessAsync`):

1. Proporcionar una indicación clara y destacada al usuario de que la aplicación quiere escuchar la voz del usuario en una palabra clave, *aunque la aplicación no se esté ejecutando*, y de que quiere el consentimiento del usuario
1. Incluir información relevante sobre el uso de datos y las directivas de privacidad, como un vínculo a una declaración de privacidad oficial
1. Evitar cualquier directiva o texto inductivo (por ejemplo, "haga clic en sí en el siguiente símbolo del sistema") en el flujo de la experiencia que revele el comportamiento de captura de audio

Si una aplicación cumple todo lo anterior, puede habilitar la capacidad de activación por voz junto con el consentimiento de micrófono. Póngase en contacto con winvoiceassistants@microsoft.com para obtener más información y revisar una experiencia de primer uso.

> [!NOTE]
> La activación por voz sobre el bloqueo no es apta para la habilitación automática con el acceso al micrófono y seguirá requiriendo que un usuario que visite la página privacidad de activación por voz para habilitar el acceso sobre el bloqueo para un asistente.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Información sobre procedimientos recomendados de asistentes de voz en Windows](windows-voice-assistants-best-practices.md)
