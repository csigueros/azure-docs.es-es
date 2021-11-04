---
title: 'Control del comportamiento del almacenamiento en caché de Azure CDN con cadenas de consulta: nivel estándar'
description: El almacenamiento en caché de las cadenas de consulta de Azure CDN controla el modo en que los archivos se almacenan en caché cuando una solicitud web contiene cadenas de consulta. En este artículo se describe el almacenamiento en caché de cadenas de consulta en productos estándar de Azure CDN.
services: cdn
documentationcenter: ''
author: duongau
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/11/2018
ms.author: duau
ms.openlocfilehash: 6e95ae75ab125f824c5125a4441d50257bea6551
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131454834"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Control del comportamiento del almacenamiento en caché de Azure CDN con cadenas de consulta: nivel estándar
> [!div class="op_single_selector"]
> * [Nivel estándar](cdn-query-string.md)
> * [Nivel Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Información general
Azure Content Delivery Network (CDN) le permite controlar cómo se almacenan los archivos en caché para una solicitud web que contiene una cadena de consulta. En una solicitud web con una cadena de consulta, esta última es la parte de la solicitud que hay después del signo de interrogación (?). Una cadena de consulta puede contener uno o más pares clave-valor, en los cuales el nombre de campo y su valor están separados por un signo igual (=). Los pares clave-valor están separados entre ellos por una Y comercial (&). Por ejemplo, http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Si hay más de un par clave-valor en una cadena de consulta de una solicitud, no importa el orden en el que se especifiquen. 

> [!IMPORTANT]
> Los productos estándar y premium de Azure CDN ofrecen la misma funcionalidad de almacenamiento en caché de cadenas de consulta, pero la interfaz de usuario es distinta. En este artículo se describe la interfaz de **Azure CDN Estándar de Microsoft**, **Azure CDN Estándar de Akamai** y **Azure CDN Estándar de Verizon**. Para información sobre el almacenamiento en caché de cadenas de consulta con el **Azure CDN premium de Verizon**, consulte [Control del comportamiento de almacenamiento en caché de Azure CDN con cadenas de consulta: nivel premium](cdn-query-string-premium.md).

Existen tres modos de cadena de consulta:

- **Pasar por alto las cadenas de consulta**: modo predeterminado. En este modo, el nodo de punto de presencia (POP) de CDN pasa las cadenas de consulta del solicitante al servidor de origen en la primera solicitud y almacena en caché el recurso. Todas las solicitudes posteriores del recurso que se ofrecen desde el punto de presencia omiten las cadenas de consulta hasta que expira el recurso en caché.

- **Omitir el almacenamiento en caché de cadenas de consulta**: en este modo, las solicitudes con cadenas de consulta no se almacenan en caché en el nodo POP de CDN. El nodo POP recupera el recurso directamente del servidor de origen y lo pasa al solicitante con cada solicitud.

- **Almacenar en caché todas las URL únicas**: en este modo, cada solicitud con un URL único, incluida la cadena de consulta, se trata como un recurso único con su propia memoria caché. Por ejemplo, la respuesta desde el servidor de origen a una solicitud de example.ashx?q=test1 se almacena en caché en el nodo POP y se devuelve en los sucesivos almacenamientos en caché con la misma cadena de consulta. Se almacena en caché una solicitud de example.ashx?q=test2 como un recurso independiente con su propia configuración de período de vida.
   
    >[!IMPORTANT] 
    > No use este modo cuando la cadena de consulta contenga parámetros que vayan a cambiar con cada solicitud, por ejemplo, un identificador de sesión o un nombre de usuario, puesto que dará lugar a una relación de acierto de caché baja.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Modificación de la configuración del almacenamiento en caché de cadenas de consultas para perfiles de red CDN estándar
1. Abra un perfil de CDN y, después, seleccione el punto de conexión de CDN que quiere administrar.
   
   ![Puntos de conexión de perfil de la red CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. En el panel izquierdo, en Configuración, haga clic en **Reglas de caché**.
   
    ![Botón Reglas de caché de CDN](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. En la lista **Comportamiento del almacenamiento en caché de cadenas de consulta**, seleccione un modo de cadena de consulta y, después, haga clic en **Guardar**.
   
   ![Opciones del almacenamiento en caché de cadenas de consultas de CDN](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Es posible que los cambios en la configuración de la cadena de caché no sean visibles de forma inmediata, ya que el registro puede tardar en propagarse a través de Azure CDN:
> - En los perfiles de **Azure CDN Estándar de Microsoft**, la propagación se completa normalmente en 10 minutos. 
> - En los perfiles de **Azure CDN estándar**, la propagación normalmente se completa en un minuto. 
> - En los perfiles **Azure CDN Estándar de Verizon** y **Azure CDN Premium de Verizon**, la propagación se completa normalmente en 10 minutos. 



