---
title: 'Azure Front Door: Supervisión de la coincidencia de reglas de enrutamiento | Microsoft Docs'
description: Este artículo le ayudará a comprender el modo en que Azure Front Door hace coincidir la regla de enrutamiento que se usará para una solicitud entrante
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 1aaabce5dc13098d183ee595d27b5b45a3fd0caa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128586295"
---
# <a name="how-requests-are-matched-to-a-routing-rule"></a>Cómo se hacen coincidir las solicitudes con una regla de enrutamiento

Después de establecer una conexión y de completar un protocolo de enlace TLS, cuando una solicitud llega a un entorno de Front Door, una de las primeras cosas que hace Front Door es determinar con qué regla de enrutamiento concreta hacer coincidir la solicitud y, luego, emprende la acción definida en la configuración. El siguiente documento explica el modo en que Front Door determina qué configuración de enrutamiento se usará al procesar una solicitud HTTP.

## <a name="structure-of-a-front-door-route-configuration"></a>Estructura de una configuración de enrutamiento de Front Door
Una configuración de regla de enrutamiento de Front Door se compone de dos partes principales: un "lado izquierdo" y un "lado derecho". La solicitud entrante se hace coincidir con el lado izquierdo de la ruta mientras que el lado derecho define cómo se procesa la solicitud.

### <a name="incoming-match-left-hand-side"></a>Coincidencia de entrada (lado izquierdo)
Las propiedades siguientes determinan si la solicitud entrante coincide con la regla de enrutamiento (o el lado izquierdo):

* **Protocolos HTTP** (HTTP/HTTPS)
* **Hosts** (por ejemplo, www\.foo.com, \*.bar.com)
* **Rutas de acceso** (por ejemplo, /\*, /Users/\*, /file.gif)

Estas propiedades se expanden de forma interna para que cada combinación de protocolo, host y ruta de acceso sea un conjunto de posible coincidencia.

### <a name="route-data-right-hand-side"></a>Datos de ruta (lado derecho)
La decisión de cómo procesar la solicitud depende de si el almacenamiento en caché está habilitado o no para la ruta específica. Por lo tanto, si no tenemos una respuesta almacenada en caché para la solicitud, reenviaremos la solicitud al back-end adecuado en el grupo de back-end configurado.

## <a name="route-matching"></a>Búsqueda de coincidencia de ruta
Esta sección se centrará en cómo se busca la coincidencia con una regla de enrutamiento de Front Door determinada. El concepto básico es que siempre coincidimos con la regla **coincidencia más específica primero** fijándonos solo en el "lado izquierdo".  En primer lugar, buscamos la coincidencia en función del protocolo HTTP, el host de front-end y, luego, la ruta de acceso.

### <a name="frontend-host-matching"></a>Coincidencia de host de front-end
Al comparar los hosts de front-end, utilizamos la lógica que se define a continuación:

1. Busque cualquier enrutamiento con una coincidencia exacta en el host.
2. Si no hay una coincidencia de hosts de front-end exacta, rechace la solicitud y envíe un error 400 de solicitud incorrecta.

Para explicar más este proceso, echemos un vistazo a un ejemplo de configuración de rutas de Front Door (solo para el lado izquierdo):

| Regla de enrutamiento | Hosts de front-end | Path |
|-------|--------------------|-------|
| Un | foo.contoso.com | /\* |
| B | foo.contoso.com | /users/\* |
| C | www\.fabrikam.com, foo.adventure-works.com  | /\*, /images/\* |

Si las siguientes solicitudes entrantes se enviaron a Front Door, coincidirían con las siguientes reglas de enrutamiento anteriores:

| Host de front-end entrante | Reglas de enrutamientos que coinciden |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www\.fabrikam.com | C |
| images.fabrikam.com | Error 400: Bad Request |
| foo.adventure-works.com | C |
| contoso.com | Error 400: Bad Request |
| www\.adventure-works.com | Error 400: Bad Request |
| www\.northwindtraders.com | Error 400: Bad Request |

### <a name="path-matching"></a>Coincidencia de ruta de acceso
Después de determinar el host de front-end específico y filtrar las reglas de enrutamiento posibles a solo las rutas con ese host de front-end, Front Door filtra las reglas de enrutamiento según la ruta de acceso de la solicitud. Utilizamos una lógica similar que los hosts de front-end:

1. Busque cualquier regla enrutamiento con una coincidencia exacta con la ruta de acceso.
2. Si ninguna ruta de acceso presenta una coincidencia exacta, busque las reglas de enrutamiento con una ruta de acceso con un carácter comodín que coincida.
3. Si no hay reglas de enrutamiento con una ruta coincidente, rechace la solicitud y devuelva una respuesta de error HTTP 400: solicitud incorrecta.

>[!NOTE]
> Las rutas de acceso sin un carácter comodín se consideran con coincidencia exacta. Incluso si la ruta de acceso termina en una barra diagonal, todavía se considera una coincidencia exacta.

Para explicarlo mejor, echemos un vistazo a otra serie de ejemplos:

| Regla de enrutamiento | Host de front-end    | Path     |
|-------|---------|----------|
| Un     | www\.contoso.com | /        |
| B     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /path/   |

Dada esa configuración, daría lugar a la tabla de búsqueda de coincidencias de ejemplo siguiente:

| Solicitud entrante    | Ruta coincidente |
|---------------------|---------------|
| www\.contoso.com/            | Un             |
| www\.contoso.com/a           | B             |
| www\.contoso.com/ab          | C             |
| www\.contoso.com/abc         | D             |
| www\.contoso.com/abzzz       | B             |
| www\.contoso.com/abc/        | E             |
| www\.contoso.com/abc/d       | F             |
| www\.contoso.com/abc/def     | G             |
| www\.contoso.com/abc/defzzz  | F             |
| www\.contoso.com/abc/def/ghi | F             |
| www\.contoso.com/path        | B             |
| www\.contoso.com/path/       | H             |
| www\.contoso.com/path/zzz    | B             |

>[!WARNING]
> </br> Si no hay ninguna regla de enrutamiento para un host de front-end con coincidencia exacta con una ruta de acceso de ruta comodín (`/*`), entonces no será una coincidencia con una regla de enrutamiento.
>
> Configuración de ejemplo:
>
> | Enrutar | Host             | Path    |
> |-------|------------------|---------|
> | Un     | profile.contoso.com | /api/\* |
>
> Tabla de búsqueda de coincidencias:
>
> | Solicitud entrante       | Ruta coincidente |
> |------------------------|---------------|
> | profile.domain.com/other | Ninguno. Error 400: Bad Request |

### <a name="routing-decision"></a>Decisión de enrutamiento

Una vez que haya emparejado con una única regla de enrutamiento de Front Door, elija cómo procesar la solicitud. Si Front Door tiene disponible una respuesta almacenada en caché para la regla de enrutamiento emparejada, la respuesta almacenada en caché se envía de vuelta al cliente. Si Front Door no tiene ninguna respuesta almacenada en caché para la regla de enrutamiento emparejada, lo que se evalúa a continuación es si ha configurado [URL wewrite (una ruta de acceso de reenvío personalizada)](front-door-url-rewrite.md) para la regla de enrutamiento emparejada. Si no hay definida ninguna ruta de acceso de reenvío personalizada, la solicitud se reenvía al back-end adecuado del grupo de back-end configurado tal cual. Si se ha definido una ruta de acceso de reenvío personalizada, la ruta de acceso de la solicitud se actualiza según la [ruta de acceso de reenvío personalizada](front-door-url-rewrite.md) definida y luego se reenvía al back-end.

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [crear una instancia de Front Door](quickstart-create-front-door.md).
- Más información acerca de cómo [funciona Front Door](front-door-routing-architecture.md).
