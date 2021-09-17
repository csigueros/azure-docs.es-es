---
title: Azure Policy y firewall de aplicaciones web de Azure
description: El firewall de aplicaciones web (WAF) de Azure combinado con Azure Policy puede ayudar a aplicar los estándares de la organización y evaluar el cumplimiento a escala para los recursos de WAF.
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 7738976cbc3f7b87862cab73a5b5f52c6d499d47
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606117"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Azure Policy y firewall de aplicaciones web de Azure

El firewall de aplicaciones web (WAF) de Azure combinado con Azure Policy puede ayudar a aplicar los estándares de la organización y evaluar el cumplimiento a escala para los recursos de WAF. Azure Policy es una herramienta de gobernanza que proporciona una vista agregada para evaluar el estado general del entorno, con la posibilidad de explorar en profundidad hasta el nivel de recurso y de directiva. Azure Policy también ayuda a lograr el cumplimiento de los recursos gracias a la corrección masiva de los recursos existentes y la corrección automática de los nuevos.

## <a name="azure-policy-for-web-application-firewall"></a>Azure Policy para Web Application Firewall

Hay varias definiciones de directiva de Azure integradas para administrar los recursos de WAF. A continuación se muestra un desglose de las definiciones de directiva y sus funcionalidades:

1. **El firewall de aplicaciones web (WAF) debe estar habilitado para Azure Front Door Service**: Los servicios de Azure Front Door Service se evalúan en si hay un WAF presente en la creación de recursos. La definición de directiva tiene tres efectos: Auditar, Denegar y Deshabilitar. Auditoría realiza un seguimiento cuando Azure Front Door Service no tiene un WAF y permite a los usuarios ver qué es lo que no cumple Azure Front Door Service. La denegación impide que se cree un Azure Front Door Service en caso de que no se adjunte un WAF. Deshabilitar desactiva la asignación de directiva.

2. **El firewall de aplicaciones web (WAF) debe estar habilitado para Application Gateway**: App Gateways se evalúan en si hay un WAF presente en la creación de recursos. La definición de directiva tiene tres efectos: Auditar, Denegar y Deshabilitar. Auditar realiza un seguimiento cuando una Application Gateway no tiene un WAF y permite a los usuarios ver lo que Application Gateway no cumple. La denegación impide que se cree una Application Gateway en caso de que no se adjunte un WAF. Deshabilitar desactiva la asignación de directiva.

3. **El firewall de aplicaciones web (WAF) debe usar el modo especificado para Azure Front Door Service**: Exige que el modo de detección o prevención esté activo en todas las directivas de firewall de aplicaciones web para Azure Front Door Service. La definición de directiva tiene tres efectos: Auditar, Denegar y Deshabilitar. Auditar realiza un seguimiento cuando un WAF no se ajusta al modo especificado. La denegación impide que se cree WAF si no está en el modo correcto. Deshabilitar desactiva la asignación de directiva.

4. **El firewall de aplicaciones web (WAF) debe usar el modo especificado para Application Gateway**: Exige que el modo de detección o prevención esté activo en todas las directivas de firewall de aplicaciones web para Application Gateway. La definición de directiva tiene tres efectos: Auditar, Denegar y Deshabilitar. Auditar realiza un seguimiento cuando un WAF no se ajusta al modo especificado. La denegación impide que se cree WAF si no está en el modo correcto. Deshabilitar desactiva la asignación de directiva.

## <a name="launch-an-azure-policy"></a>Inicio de Azure Policy

1.  En la página principal de Azure, escriba Directiva en la barra de búsqueda y haga clic en el icono de Azure Policy.

2.  En el servicio Azure Policy, en **Creación**, seleccione **Asignaciones**.

:::image type="content" source="../media/waf-azure-policy/policy-home.png" alt-text="Pestaña Asignaciones dentro de Azure Policy":::

3.  En la página asignaciones, seleccione el icono **Directivas de asignación** en la parte superior.

:::image type="content" source="../media/waf-azure-policy/assign-policy.png" alt-text="Pestaña Datos básicos en la página Asignar directiva":::

4.  En la pestaña de conceptos básicos de la página de Directivas de asignación, actualice los campos siguientes:
    1.  **Ámbito**: seleccione las suscripciones y los grupos de recursos de Azure a los que debería afectar la definición de directiva.
    2.  **Exclusiones**: seleccionar los recursos del ámbito que se van a excluir de la asignación de directiva.
    3.  **Definición de directiva**: seleccione la definición de directiva que se va a aplicar al ámbito con exclusiones. Escriba "Web Application Firewall" en la barra de búsqueda para elegir la directiva de Azure Policy de Web Application Firewall pertinente.

:::image type="content" source="../media/waf-azure-policy/policy-listing.png" alt-text="Captura de pantalla que muestra la pestaña &quot;Definiciones de directiva&quot; en la página &quot;Definiciones disponibles&quot;.":::

5.  Seleccione la pestaña **Parámetros** y actualice los parámetros de asignación de directiva. Para aclarar mejor lo que hace el parámetro, mantenga el mouse sobre el icono de información junto al nombre del parámetro para una mayor aclaración.

6.  Seleccione **Revisar y crear** para finalizar la asignación de directiva. La asignación de directiva tarda aproximadamente 15 minutos en activarse para los nuevos recursos.
