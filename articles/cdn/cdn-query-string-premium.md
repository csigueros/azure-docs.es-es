---
title: 'Control del comportamiento del almacenamiento en caché de Azure CDN con cadenas de consulta: nivel premium'
description: El almacenamiento en caché de las cadenas de consulta de Azure CDN controla el modo en que los archivos se almacenan en caché cuando una solicitud web contiene cadenas de consulta. En este artículo se describe el almacenamiento en caché de cadenas de consulta en productos de Azure CDN premium de Verizon.
services: cdn
documentationcenter: ''
author: duongau
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/11/2018
ms.author: duau
ms.openlocfilehash: 7bf5a9ad9665ae0942f7aa7f61319fccf47bbea3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470060"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Control del comportamiento del almacenamiento en caché de Azure CDN con cadenas de consulta: nivel premium
> [!div class="op_single_selector"]
> * [Nivel estándar](cdn-query-string.md)
> * [Nivel Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Información general
Azure Content Delivery Network (CDN) le permite controlar cómo se almacenan los archivos en caché para una solicitud web que contiene una cadena de consulta. En una solicitud web con una cadena de consulta, esta última es la parte de la solicitud que hay después del signo de interrogación (?). Una cadena de consulta puede contener uno o más pares clave-valor, en los cuales el nombre de campo y su valor están separados por un signo igual (=). Los pares clave-valor están separados entre ellos por una Y comercial (&). Por ejemplo, http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Si hay más de un par clave-valor en una cadena de consulta de una solicitud, no importa el orden en el que se especifiquen. 

> [!IMPORTANT]
> Los productos estándar y premium de CDN ofrecen la misma funcionalidad de almacenamiento en caché de cadenas de consulta, pero la interfaz de usuario es distinta. En este artículo se describe la interfaz de **Azure CDN Premium de Verizon**. Para información sobre el almacenamiento en caché de cadenas de consulta con los productos estándar de Azure CDN Estándar, consulte [Control del comportamiento del almacenamiento en caché de Azure CDN con cadenas de consulta: nivel estándar](cdn-query-string.md).
>


Existen tres modos de cadena de consulta:

- **standard-cache**: este es el modo predeterminado. En este modo, el nodo de punto de presencia (POP) de CDN pasa las cadenas de consulta del solicitante al servidor de origen en la primera solicitud y almacena en caché el recurso. Todas las solicitudes posteriores del recurso que se ofrecen desde el servidor POP omiten las cadenas de consulta hasta que expira el recurso en caché.

    >[!IMPORTANT] 
    > Si está habilitada la autorización de token en cualquier ruta de acceso de esta cuenta, el modo de caché estándar es el único modo que se puede usar. 

- **no-cache**: en este modo, las solicitudes con cadenas de consultas no se almacenan en caché en el nodo POP de CDN. El nodo POP recupera el recurso directamente del servidor de origen y lo pasa al solicitante con cada solicitud.

- **unique-cache**: en este modo, cada solicitud con un URL único, incluida la cadena de consulta, se trata como un recurso único con su propia memoria caché. Por ejemplo, la respuesta desde el servidor de origen a una solicitud de example.ashx?q=test1 se almacena en caché en el nodo POP y se devuelve en los sucesivos almacenamientos en caché con la misma cadena de consulta. Se almacena en caché una solicitud de example.ashx?q=test2 como un recurso independiente con su propia configuración de período de vida.
   
    >[!IMPORTANT] 
    > No use este modo cuando la cadena de consulta contenga parámetros que vayan a cambiar con cada solicitud, por ejemplo, un identificador de sesión o un nombre de usuario, puesto que dará lugar a una relación de acierto de caché baja.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Modificación de la configuración del almacenamiento en caché de cadenas de consultas para perfiles de red CDN premium
1. Abra un perfil de CDN y, después, haga clic en **Administrar**.
   
    ![Botón de administración del perfil de la red CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Se abre el Portal de administración de CDN.
2. Desplace el mouse sobre la pestaña **HTTP Large** (HTTP grande) y, luego, mantenga el mouse sobre el menú flotante **Configuración de caché**. Haga clic en **Query-String Caching** (Almacenamiento en caché de cadenas de consulta).
   
    Aparecen las opciones del almacenamiento en caché de cadenas de consultas.
   
    ![Opciones del almacenamiento en caché de cadenas de consultas de CDN](./media/cdn-query-string-premium/cdn-query-string.png)
3. Seleccione un modo de cadena de consulta y, después, haga clic en **Actualizar**.

> [!IMPORTANT]
> Es posible que los cambios en la configuración de la cadena de caché no sean visibles de forma inmediata, ya que el registro puede tardar en propagarse a través de CDN. La propagación suele completarse en 10 minutos.
 

