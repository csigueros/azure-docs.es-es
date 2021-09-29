---
title: Compatibilidad de Azure API Management con la monetización
description: Conozca las estrategias de monetización que admite Azure API Management para los productos de API.
author: dlepow
ms.author: danlep
ms.date: 08/23/2021
ms.topic: article
ms.service: api-management
ms.openlocfilehash: ad6e4358ebd312c498504da6650045d9019fadbe
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548720"
---
# <a name="how-api-management-supports-monetization"></a>Compatibilidad de API Management con la monetización

Con la plataforma de servicio [Azure API Management](./api-management-key-concepts.md), puede hacer lo siguiente:
* Publicar las API a las que se suscriben los consumidores.
* Eliminar los riesgos de la implementación. 
* Acelerar las escalas de tiempo del proyecto.
* Escalar las API con confianza.

En este documento, nos centramos en las características de API Management que permiten la implementación de la estrategia de monetización, como proporcionar una experiencia fluida para:
* Detectar las API públicas.
* Especificar los detalles de pago.
* Activar la suscripción.
* Consumir la API.
* Supervisar el uso.
* Pagar automáticamente por el uso de la API.

En el diagrama siguiente se presentan estas características clave de API Management:

:::image type="content" source="media/monetization-support/architecture-overview.png" alt-text="Diagrama de las características principales de la monetización de API Management":::

## <a name="api-discovery"></a>Detección de API

Inicie la API e incorpore los consumidores de esta mediante el portal para desarrolladores integrado de API Management. Haga hincapié en el contenido de desarrollo de buena calidad del portal para desarrolladores, lo que permite a los consumidores de API explorar y usar las API sin problemas. Pruebe el contenido y la información proporcionados para ver si se puede acceder fácilmente a ellos, son exhaustivos y se pueden usar de forma sencilla.

Para información sobre cómo agregar contenido y controlar la personalización de marca del portal para desarrolladores, consulte la [información general del portal para desarrolladores](./api-management-howto-developer-portal.md).

## <a name="api-packaging"></a>Empaquetado de API

API Management administra cómo se empaquetan las API y se presentan mediante el concepto de *productos* y *directivas*.

### <a name="products"></a>Productos

Las API se publican [mediante productos](./api-management-howto-add-products.md). Los productos permiten definir:
* Las API a las que puede acceder un suscriptor.
* [Directivas](./api-management-howto-policies.md) de limitación específicas, como limitar una suscripción específica a una cuota de llamadas al mes.

Cuando un consumidor de API se suscribe a un producto, recibe una clave de API que, con ella, realiza llamadas. Inicialmente, la suscripción se establece en un estado `submitted`. Active la suscripción para permitir que los suscriptores usen las API.

Configure los productos de API Management para empaquetar la API subyacente de forma que refleje el modelo de ingresos, con:
* Una relación uno a uno entre cada nivel del modelo de ingresos.
* Un producto de API Management correspondiente.

Los proyectos de ejemplo usan productos de API Management como medio de nivel superior para codificar la estrategia de monetización. Los productos de API Management reflejan los niveles del modelo de ingresos e indexan el modelo de precios específico de cada nivel. Esta configuración proporciona un enfoque flexible y basado en la configuración para preparar la estrategia de monetización.

### <a name="policies"></a>Directivas

Aplique directivas de API Management para controlar la calidad del servicio de cada producto. Los proyectos de ejemplo usan dos características de directiva específicas para controlar la calidad del servicio, en línea con el modelo de ingresos:

| Característica de directiva | Descripción |
| ----- | ----- |
| **Cuota** | Define el número total de llamadas que el usuario puede realizar a la API durante un período de tiempo especificado. Por ejemplo, "100 llamadas al mes". Una vez que el usuario alcanza la cuota, se producirá un error en las llamadas a la API y el autor de la llamada recibirá un código de estado de respuesta `403 Forbidden`. |
| **Límite de frecuencia** | Define el número de llamadas en una ventana de tiempo deslizante que se pueden realizar a la API. Por ejemplo, "200 llamadas por minuto". Está diseñado para evitar picos en el uso de API más allá de la calidad de servicio de pago con el producto elegido. Cuando se supera la tasa de llamadas, el autor de la llamada recibe un código de estado de respuesta `429 Too Many Requests`. |

Para más información sobre directivas, consulte la documentación [Directivas de Azure API Management](./api-management-howto-policies.md).

## <a name="api-consumption"></a>Consumo de API

Conceda a los consumidores de API acceso a las API a través de productos que usan suscripciones de API.

1. Los consumidores de API establecen suscripciones de API al suscribirse a un producto de API Management específico. 
1. Integre el proceso de suscripción con el proveedor de pago mediante la delegación de API Management. 
1. Una vez que se proporcionan correctamente los detalles de pago, los usuarios obtienen acceso a la API con una clave de seguridad única y generada para la suscripción.

Para más información sobre las suscripciones, consulte la documentación [Suscripciones en Azure API Management](./api-management-subscriptions.md).

## <a name="api-usage-monitoring"></a>Supervisión del uso de API

Obtenga información sobre el uso y el rendimiento de la API mediante el análisis integrado de API Management. Estos análisis proporcionan informes por:
* API
* Geography
* Operaciones de la API REST
* Producto
* Solicitud
* Subscription
* Hora 
* Usuario

Revise periódicamente los informes de análisis para comprender cómo los consumidores de API adoptan su estrategia de monetización.

Para más información, consulte [Obtención de análisis de API en Azure API Management](./howto-use-analytics.md).

## <a name="security"></a>Seguridad

Controle el nivel de acceso de cada usuario a cada producto mediante los productos, las directivas de API y las suscripciones de API Management. Evite el uso indebido y el abuso concediendo acceso a la API a nivel de suscripción si el usuario se ha autenticado correctamente con el proveedor de pago, incluso si el producto de API específico es gratuito.

## <a name="integration"></a>Integración

Cree una experiencia de monetización fluida mediante la integración de front-end y back-end entre API Management y el proveedor de pago elegido.  Use la delegación de API Management para la integración de front-end y la API REST para la integración de back-end.

### <a name="delegation"></a>Delegación

En los proyectos de ejemplo, puede usar la [delegación de API Management](./api-management-howto-setup-delegation.md) para realizar integraciones personalizadas con proveedores de pago de terceros. En la demostración se usa la delegación para las experiencias de suscripción a productos y de inicio de sesión y registro.

#### <a name="sign-upsign-in-workflow"></a>Flujo de trabajo de registro o inicio de sesión

1. El desarrollador hace clic en el enlace de inicio de sesión o de suscripción que se encuentra en el portal para desarrolladores de API Management.
1. El explorador redirige al punto de conexión de delegación (configurado en una página de la aplicación del portal de facturación personalizada).
1. La aplicación del portal de facturación personalizada presenta una interfaz de usuario de inicio de sesión o registro.
1. Tras el inicio de sesión o registro correctos, se autentica al usuario y se le redirige a la página del portal para desarrolladores de API Management.

#### <a name="product-subscription-workflow"></a>Flujo de trabajo de suscripción a productos

1. El desarrollador selecciona un producto en el portal para desarrolladores de API Management y hace clic en el botón **Suscribirse**.
1. El explorador redirige al punto de conexión de delegación (configurado en una página de la aplicación del portal de facturación personalizada).
1. Aplicación del portal de facturación personalizada:
    * Presenta una interfaz de usuario configurada en función del proveedor de pago (Stripe o Adyen).
    * Lleva al usuario por el proceso de finalización de la compra pertinente.
1. Se redirige al usuario a la página de inicio del producto de API Management. 
    * El producto estará activo y las claves de API estarán disponibles.

### <a name="rest-api"></a>API DE REST

Use la API REST de API Management para automatizar el funcionamiento de la estrategia de monetización.

Los proyectos de ejemplo usan la API para realizar las siguientes tareas mediante programación:

- Recuperar los productos y las directivas de API Management para habilitar la configuración sincronizada de conceptos similares en proveedores de pago, como Stripe.
- Sondear API Management periódicamente para recuperar las métricas de uso de API de cada suscripción e impulsar el proceso de facturación.

Para más información, consulte la [introducción a la API REST de Azure API Management](/rest/api/apimanagement/#rest-operation-groups).

## <a name="devops"></a>DevOps

Control de versiones y automatización de los cambios de implementación en API Management mediante Azure Resource Manager, incluida la configuración de características que implementan la estrategia de monetización, como:
* Productos
* Directivas
* Portal para desarrolladores

En los proyectos de ejemplo, los scripts de Azure Resource Manager se incrementan mediante un archivo JSON, que define cada modelo de precios del producto de API Management. Con este aumento, puede sincronizar la configuración entre API Management y el proveedor de pago elegido. Toda la solución se administra en un único repositorio de control de código fuente para:
* Coordinar todos los cambios asociados a la evolución de la estrategia de monetización en curso como una sola versión.
* Llevar a cabo los cambios, siguiendo los requisitos de gobernanza y auditoría.

## <a name="initialization-and-deployment"></a>Inicialización e implementación

API Management se puede implementar mediante:
* La interfaz de usuario de Azure Portal, o
* Un enfoque de "infraestructura como código" que usa [plantillas de Azure Resource Manager](https://azure.microsoft.com/services/arm-templates). 

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre las estrategias de monetización de API Management](monetization-overview.md).
* Implemente una integración de Adyen o Stripe de demostración a través del [repositorio de Git](https://github.com/microsoft/azure-api-management-monetization) asociado.