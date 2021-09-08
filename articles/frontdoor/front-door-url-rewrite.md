---
title: 'Azure Front Door: Reescritura de direcciones URL | Microsoft Docs'
description: Este artículo le ayudará a comprender cómo Azure Front Door realiza la reescritura de direcciones URL para las rutas, si se ha configurado.
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
ms.openlocfilehash: 4aedc6e92b02cf81003ecf4b40a5096bf80c7448
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441052"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Reescritura de direcciones URL (ruta de acceso de reenvío personalizada)
Azure Front Door admite la reescritura de direcciones URL mediante la configuración de una **ruta de acceso de reenvío personalizada** opcional que se usará al construir la solicitud de reenvío al back-end. De forma predeterminada, si no se proporciona una ruta de reenvío personalizada, Front Door copia la ruta de acceso de la dirección URL entrante en la dirección URL de la solicitud reenviada. El encabezado host usado en la solicitud reenviada se configura para el back-end seleccionado. Lea [Encabezado de host de back-end](front-door-backend-pool.md#hostheader) para más información sobre lo que hace y cómo configurarlo.

La parte más eficaz de la reescritura de direcciones URL mediante la ruta de acceso de reenvío personalizada es que esta copia cualquier parte de la ruta de acceso entrante que coincida con una ruta de acceso comodín en la ruta de acceso de reenvío (estos segmentos aparecen en **verde** en el siguiente ejemplo):
</br>

:::image type="content" source="./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg" alt-text="Reescritura de direcciones URL de Azure Front Door Service":::

## <a name="url-rewrite-example"></a>Ejemplo de reescritura de direcciones URL
Considere la posibilidad de una regla de enrutamiento con la siguiente combinación de hosts de front-end y rutas de acceso configuradas:

| Hosts      | Rutas de acceso       |
|------------|-------------|
| www\.contoso.com | /\*   |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

La primera columna de la tabla siguiente muestra ejemplos de solicitudes entrantes y la segunda muestra cuál sería la ruta coincidente "más específica".  Las columnas tercera y subsiguientes de la tabla son ejemplos de **rutas de reenvío personalizadas** configuradas.

Por ejemplo, si leemos en la segunda fila, dice que para la solicitud entrante `www.contoso.com/sub`, si la ruta de acceso de reenvío personalizada fuera `/`, la ruta de acceso reenviada sería `/sub`. Si la ruta de reenvío personalizada fuera `/fwd/`, la ruta de acceso reenviada sería `/fwd/sub`. Y así sucesivamente, para las restantes columnas. Las partes **destacadas** de las rutas de acceso siguientes representan las porciones que forman parte de la coincidencia de caracteres comodín.

| Solicitud entrante       | Ruta de acceso de coincidencia más específica | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/**sub**     | /\*                      | /**sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www\.contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/**bar** | /foo/\*                  | /**bar**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |

> [!NOTE]
> Azure Front Door solo admite la reescritura de direcciones URL de una ruta de acceso estática a otra. La conservación de la ruta de acceso sin coincidencia se admite en la SKU estándar o prémium de Azure Front Door. Consulte [Conservar la ruta de acceso sin coincidencia](standard-premium/concept-rule-set-url-redirect-and-rewrite.md#preserve-unmatched-path) para obtener más detalles.
> 

## <a name="optional-settings"></a>Configuración opcional
Hay valores opcionales adicionales que también puede especificar para cualquier configuración de regla de enrutamiento:

* **Cache Configuration** (Configuración de caché): si la opción está deshabilitada o no se ha especificado, las solicitudes que coincidan con esta regla de enrutamiento no intentarán usar el contenido almacenado en caché y, en su lugar, lo capturarán siempre desde el back-end. Obtenga más información sobre [Almacenamiento en caché con Front Door](front-door-caching.md).

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [crear una instancia de Front Door](quickstart-create-front-door.md).
- Más información acerca de cómo [funciona Front Door](front-door-routing-architecture.md).
