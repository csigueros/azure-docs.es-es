---
title: Suscripciones en Azure API Management | Microsoft Docs
description: Información acerca del concepto de las suscripciones en Azure API Management. Los consumidores obtienen acceso a las API mediante suscripciones de Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/27/2021
ms.author: apimpm
ms.openlocfilehash: 9bbdc914eae50a7226b22952b40cdb4b28849239
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111675"
---
# <a name="subscriptions-in-azure-api-management"></a>Suscripciones en Azure API Management

En Azure API Management, las suscripciones son la forma más habitual de acceso a las API publicadas por medio de una instancia de API Management para los consumidores. En este artículo se proporciona información general del concepto.

## <a name="what-are-subscriptions"></a>¿Qué son las suscripciones?

Si publica API por medio de API Management, puede proteger fácilmente el acceso a las API mediante claves de suscripción. Use las API publicadas mediante la inclusión de una clave de suscripción válida en las solicitudes HTTP al llamar a esas API. Sin una clave de suscripción válida, las llamadas:
* Son rechazadas inmediatamente por la puerta de enlace de API Management. 
* No se reenvían a los servicios back-end.

Para acceder a las API, necesita una suscripción y una clave de suscripción. Una *suscripción* es un contenedor con nombre para un par de claves de suscripción. 

La regeneración periódica de claves es una precaución de seguridad habitual, por lo que la mayoría de los productos de Azure que requieren una clave de suscripción generan claves en pares. Cada aplicación que usa el servicio puede cambiar de la *clave A* a la *clave B* y regenerar la clave A con una interrupción mínima, y viceversa. 

Además:

* Los desarrolladores pueden obtener suscripciones sin la aprobación de los publicadores de API. 
* Los publicadores de API pueden crear suscripciones directamente para los consumidores de API.

> [!TIP]
> API Management también admite otros mecanismos para proteger el acceso a las API, por ejemplo:
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [Certificados de cliente](api-management-howto-mutual-certificates-for-clients.md)
> - [Restringir IP de autor de llamada](./api-management-access-restriction-policies.md#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Ámbito de las suscripciones

Las suscripciones se pueden asociar a diversos ámbitos: producto, todas las API o una API individual.

### <a name="subscriptions-for-a-product"></a>Suscripciones para un producto

Tradicionalmente, las suscripciones de API Management se han asociado a un solo ámbito de [producto de API](api-management-terminology.md). Desarrolladores:
* Encontraban la lista de productos en el portal para desarrolladores. 
* Enviaban las solicitudes de suscripción de los productos que querían usar. 
* Usaban las claves de esas suscripciones (aprobadas automáticamente o por publicadores de API) para acceder a todas las API del producto. 
    * Puede acceder a las API con o sin la clave de suscripción, independientemente del ámbito de la suscripción (producto, global o API).

En este momento, en la sección **Perfil de usuario** del portal para desarrolladores solo aparecen las suscripciones del ámbito del producto. 

> [!NOTE]
> Si usa una clave de suscripción con ámbito de API, las *directivas* configuradas en el ámbito del producto no se aplican a esa suscripción.

![Suscripciones de producto](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> En determinadas situaciones, es posible que los publicadores de API deseen publicar un producto de API para el público sin necesidad de suscripciones. Pueden anular la selección de la opción **Require subscription** (Requerir suscripción) en la página **Settings** (Configuración) del producto en Azure Portal. Como resultado, se puede tener acceso a todas las API del producto sin una clave de API.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Suscripciones para todas las API o una API individual

Con la incorporación del nivel [Consumo](https://aka.ms/apimconsumptionblog) de API Management, la administración de claves de suscripción se simplifica. 

#### <a name="two-more-subscription-scopes"></a>Dos ámbitos de suscripción más

Ahora que los ámbitos de suscripción ya no están limitados a un producto de API, puede crear claves que concedan acceso a:
* una sola API, o 
* todas las API dentro de una instancia de API Management. 

No es necesario crear un producto para agregarle API. 

Cada instancia de API Management ahora incluye una suscripción inmutable de todas las API. Esta suscripción facilita y agiliza la prueba y la depuración de las API de la consola de prueba.

#### <a name="standalone-subscriptions"></a>Suscripciones independientes

API Management ahora permite suscripciones *independientes*. Ya no es necesario asociar suscripciones a una cuenta de desarrollador. Esta característica resulta útil en escenarios similares a cuando varios desarrolladores o equipos comparten una suscripción.

La creación de una suscripción sin asignarle un propietario la convierte en una suscripción independiente. Para conceder a los desarrolladores y al resto del equipo acceso a la clave de suscripción independiente:
* Comparta manualmente la clave de suscripción.
* Use un sistema personalizado para poner a disposición del equipo la clave de suscripción.

#### <a name="creating-subscriptions-in-azure-portal"></a>Creación de suscripciones en Azure Portal

Los publicadores de API ahora pueden [crear suscripciones](api-management-howto-create-subscriptions.md) directamente en Azure Portal:

![Suscripciones flexibles](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre API Management:

+ Aprenda otros [conceptos](api-management-terminology.md) en API Management.
+ Siga nuestros [tutoriales](import-and-publish.md) para más información sobre API Management.
+ Consulte nuestra [página de preguntas frecuentes](api-management-faq.yml) para ver las preguntas habituales.
