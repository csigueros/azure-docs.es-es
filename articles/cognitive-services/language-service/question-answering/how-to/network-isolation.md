---
title: 'Aislamiento de red y Private Link: respuesta a preguntas'
description: Los usuarios pueden restringir el acceso público a los recursos de respuesta a preguntas.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 9ab30b82a9bac9d06e2d5228b2004c36d465d392
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458216"
---
#  <a name="network-isolation-and-private-endpoints"></a>Aislamiento de red y puntos de conexión privados

En los pasos siguientes se describe cómo restringir el acceso público a los recursos de respuesta a preguntas y cómo habilitar Azure Private Link. Proteja un recurso de Cognitive Services del acceso público mediante la [configuración de la red virtual](../../../cognitive-services-virtual-networks.md?tabs=portal).

## <a name="private-endpoints"></a>Puntos de conexión privados

Un punto de conexión privado de Azure es una interfaz de red que le conecta de forma privada y segura a un servicio con la tecnología de Azure Private Link.  La respuesta a preguntas le ayuda a crear puntos de conexión privados en el servicio Azure Search.

Los puntos de conexión privados se proporcionan mediante [Azure Private Link](../../../../private-link/private-link-overview.md), como un servicio independiente. Para obtener más información sobre los costos, vea la [página de precios](https://azure.microsoft.com/pricing/details/private-link/).

## <a name="steps-to-enable-private-endpoint"></a>Pasos para habilitar el punto de conexión privado

1. Asigne el rol *Colaborador* al recurso de idioma (en función del contexto, puede aparecer como un recurso de Text Analytics) en la instancia de servicio de Azure Search. Para esta operación se necesita acceso de *Propietario* a la suscripción. Vaya a la pestaña Identidad del recurso del servicio para obtener la identidad.

> [!div class="mx-imgBorder"]
> ![Identidad de Text Analytics](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoints-identity.png)

2. Agregue la identidad anterior como *Colaborador*; para ello, vaya a la pestaña IAM del servicio Azure Search.

![IAM de servicio administrado](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. Seleccione *Add role assignments* (Agregar asignaciones de roles), agregue la identidad y elija *Guardar*.

![Asignación de roles administrados](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. Ahora, vaya a la pestaña *Redes* de la instancia del servicio Azure Search y cambie los datos de conectividad de punto de conexión de *Públicos* a *Privados*. Esta operación es un proceso de larga duración y puede tardar hasta 30 minutos en completarse. 

![Redes de Azure Search administradas](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. Vaya a la pestaña *Redes* del recurso de idioma y, en *Permitir acceso desde*, seleccione la opción *Redes y puntos de conexión privados seleccionados* y haga clic en *Guardar*.
 
> [!div class="mx-imgBorder"]
> ![Redes de Text Analytics](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-custom-qna.png)

Se establecerá una conexión de punto de conexión privado entre el recurso de idioma y la instancia del servicio de Azure Cognitive Search. Puede comprobar la conexión del punto de conexión privado en la pestaña *Redes* de la instancia del servicio Azure Cognitive Search. Una vez completada toda la operación, puede usar el recurso de idioma con la respuesta a preguntas habilitada.

![Servicio de redes administrado](../../../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)

## <a name="support-details"></a>Detalles sobre compatibilidad
 * No se admiten cambios en el servicio Azure Cognitive Search una vez que se habilita el acceso privado a los recursos de idioma. Si cambia el servicio Azure Cognitive Search desde la pestaña "Características" después de haber habilitado el acceso privado, ya no se podrá utilizar el recurso de idioma.

 * Después de establecer la conexión de punto de conexión privado, si cambia las redes del servicio Azure Cognitive Search a "Públicas", no podrá usar el recurso de idioma. Para que la conexión del punto de conexión privado funcione, las redes del servicio Azure Search deben ser "Privadas".

## <a name="restrict-access-to-cognitive-search-resource"></a>Restricción del acceso a los recursos de Cognitive Search

Siga los pasos que se indican a continuación para restringir el acceso público a los recursos de idioma de la respuesta a preguntas. Proteja un recurso de Cognitive Services del acceso público mediante la [configuración de la red virtual](../../../cognitive-services-virtual-networks.md?tabs=portal).

Después de restringir el acceso al recurso de Cognitive Services en función de la red virtual, vaya a las bases de conocimiento en el portal de Language Studio desde la red o el explorador locales.
- Conceda acceso a la [red local](../../../cognitive-services-virtual-networks.md?tabs=portal#configuring-access-from-on-premises-networks).
- Conceda acceso al [explorador o máquina local](../../../cognitive-services-virtual-networks.md?tabs=portal#managing-ip-network-rules).
- Agregue la **dirección IP pública de la máquina en la sección Firewall** de la pestaña **Redes**. De forma predeterminada, `portal.azure.com` muestra la dirección IP pública de la máquina de exploración actual (seleccione esta entrada) y, a continuación, seleccione **Guardar**.

     > [!div class="mx-imgBorder"]
     > [ ![Captura de pantalla de la interfaz de usuario de configuración de redes virtuales y firewall]( ../../../qnamaker/media/network-isolation/firewall.png) ](  ../../../qnamaker/media/network-isolation/firewall.png#lightbox)
