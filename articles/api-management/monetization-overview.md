---
title: Monetización con Azure API Management
description: Aprenda a configurar la estrategia de monetización para Azure API Management en seis fases sencillas.
author: v-hhunter
ms.author: v-hhunter
ms.date: 08/23/2021
ms.topic: article
ms.service: api-management
ms.openlocfilehash: 3e969ad51232007a04f3391090e122bf865aec20
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868715"
---
# <a name="monetization-with-azure-api-management"></a>Monetización con Azure API Management

Las API web modernas apuntalan la economía digital. Proporcionan la propiedad intelectual (IP) de una empresa a terceros y generan ingresos de la siguiente manera:

- Empaquetando IP en forma de datos, algoritmos o procesos.
- Permitiendo que otras partes detecten y consuman direcciones IP útiles de una manera coherente y sin problemas.
- Ofreciendo un mecanismo para el pago directo o indirecto por este uso.

Un tema común en los casos de éxito de la API es un *modelo de negocio correcto*. El valor se crea y se intercambia entre todas las partes, de forma sostenible.

Las empresas emergentes, las organizaciones establecidas y todo lo que hay entre ellas suelen intentar transformar digitalmente a partir del modelo de negocio. Las API permiten realizar el modelo de negocio, lo que permite una manera más fácil y rentable de comercializar, adoptar, consumir y escalar la dirección IP subyacente.

Las organizaciones que publican su primera API se enfrentan a un conjunto complejo de decisiones. Aunque la plataforma Azure API Management anula el escalado del riesgo y acelera los elementos clave, las organizaciones todavía necesitan configurar y crear su API en torno a su modelo técnico y empresarial único.

## <a name="developing-a-monetization-strategy"></a>Desarrollo de una estrategia de monetización

La *monetización* es el proceso de convertir algo en dinero; en este caso, el valor de la API. Las interacciones de la API suelen implicar tres partes distintas en la cadena de valores:

:::image type="content" source="media/monetization-overview/values-overview.png" alt-text="Diagrama de la cadena de valores de monetización":::

Las categorías de la estrategia de monetización de la API incluyen:

| Estrategia de monetización de la API | Descripción |
| ----- | ----- |
| **Gratis** | Una API facilita la integración de negocio a negocio, como la racionalización de una cadena de suministro. La API no se monetiza, pero ofrece un valor significativo al habilitar la eficacia de los procesos empresariales para el proveedor de API y el consumidor de API. |
| **El consumidor paga** | Los consumidores de API pagan en función del número de interacciones que tienen con la API. En este documento, nos centramos en este enfoque. |
| **Se paga al consumidor** | Por ejemplo, un consumidor de API usa la API para insertar publicidad en su sitio web y recibe una parte de los ingresos generados. |
| **Monetización indirecta** | La monetización de la API no está controlada por el número de interacciones con la API, sino por otras fuentes de ingresos que facilita la API. |

>[!NOTE]
>El proveedor de API establece la estrategia de monetización y debe diseñarse para satisfacer las necesidades del consumidor de API.

Dado que una amplia gama de factores influyen en el diseño, la monetización de la API no viene como una solución única. La estrategia de monetización diferencia la API de los competidores y maximiza los ingresos generados.

En los pasos siguientes se explica cómo implementar una estrategia de monetización para la API.

:::image type="content" source="media/monetization-overview/implementing-strategy.png" alt-text="Diagrama de los pasos para implementar la estrategia de monetización":::

### <a name="step-1---understand-your-customer"></a>Paso 1: Comprender al cliente

1. Asigne las fases del recorrido probable de los consumidores de API, desde la primera detección de la API a la escala máxima.

    Por ejemplo, un conjunto de fases del cliente podría ser:

    | Fase del cliente | Descripción |
    | ----- | ----- |
    | **Investigación** | Habilite el consumidor de API para probar la API sin costo ni problemas. |
    | **Implementación** | Proporcione acceso suficiente a la API para admitir el trabajo de desarrollo y pruebas necesario para integrarse con ella. |
    | **Versión preliminar** | Permita que el cliente inicie su oferta y comprenda la demanda inicial. |
    | **Uso de producción inicial** | Admita la adopción temprana de la API en producción cuando los niveles de uso no se entiendan completamente y sea necesario un enfoque adverso al riesgo. |
    | **Crecimiento inicial** | Habilite el consumidor de API para aumentar el uso de la API en respuesta al aumento de la demanda de los usuarios finales. |
    | **Escala** | Incentivice al consumidor de API para que se comprometa a un mayor volumen de compra una vez que la API alcance constantemente altos niveles de uso cada mes. |
    | **Crecimiento global** | Recompense a los usuarios de API que usan la API a escala global al ofrecer el precio óptimo al por mayor. |

1. Analice el valor que la API va a generar para el cliente en cada fase de su recorrido. 
1. Considere la posibilidad de aplicar una estrategia de precios basada en valor si se entiende bien el valor directo de la API para el cliente.
1. Calcule los niveles de uso previstos de duración de la API para un cliente y el número esperado de clientes a lo largo de la vigencia de la API.

### <a name="step-2---quantify-the-costs"></a>Paso 2: Cuantificar los costos

Calcule el costo total de propiedad de la API.

| Coste | Descripción |
| ----- | ----- |
| **Costo de adquisición de clientes (COCA)** | El costo de marketing, ventas e incorporación. Las API más correctas tienden a tener un COCA cero a medida que aumentan los niveles de adopción. Las API deben ser en gran medida de autoservicio en la incorporación. Entre los factores se incluyen la documentación y la integración sin problemas con los sistemas de pago. |
| **Costos de ingeniería** | Los recursos humanos necesarios para compilar, probar, operar y mantener la API a lo largo de su vigencia. Tiende a ser el componente de costo más significativo. Siempre que sea posible, aproveche las tecnologías PaaS y sin servidor en la nube para minimizar. |
| **Costos de infraestructura** | Los costos de las plataformas subyacentes, proceso, red y almacenamiento necesarios para admitir la API a lo largo de su duración. Aproveche las plataformas en la nube para lograr un modelo de costos de infraestructura que se escale verticalmente proporcionalmente en línea con los niveles de uso de API. |

### <a name="step-3---conduct-market-research"></a>Paso 3: Realizar investigaciones de mercado

1. Investigue el mercado para identificar a los competidores. 
1. Analice las estrategias de monetización de la competencia. 
1. Comprenda las características específicas (funcionales y no funcionales) que ofrecen con su API.

### <a name="step-4---design-the-revenue-model"></a>Paso 4: Diseñar el modelo de ingresos

Diseñe un modelo de ingresos basado en el resultado de los pasos anteriores. Puede trabajar en dos dimensiones:

| Dimensión | Descripción |
| --------- | ----------- |
| **Calidad de servicio** | Coloque restricciones en el nivel de servicio que ofrece estableciendo un límite en el uso de la API. Defina una cuota para las llamadas API que se pueden realizar durante un período de tiempo (por ejemplo, 50 000 llamadas al mes) y, a continuación, bloquee las llamadas una vez que se alcance esa cuota. <br> También puede establecer un límite de llamadas, limitando el número de llamadas que se pueden realizar en un breve período (por ejemplo, 100 llamadas por segundo). <br> Los límites de gasto y de número de llamadas se aplican juntos, lo que impide que los usuarios consuman su cuota mensual en una breve ráfaga intensiva de llamadas API. |
| **Precio** | Defina el precio unitario que se va a pagar por cada llamada API. |

Maximice el valor de duración (LTV) que genera a partir de cada cliente mediante el diseño de un modelo de ingresos que admita al cliente en cada fase del recorrido del cliente.

1. Haga que los clientes puedan escalar y crecer lo más fácilmente posible:
    - Sugiera a los clientes que suban al siguiente nivel en el modelo de ingresos. 
    - Por ejemplo, recompense a los clientes que compran un mayor volumen de llamadas API con un precio unitario más bajo.
1. Mantenga el modelo de ingresos tan simple como sea posible:
    - Equilibre la necesidad de proporcionar una opción con el riesgo de sobrecargar a los clientes con una matriz de opciones. 
    - Mantenga bajo el número de dimensiones usadas para diferenciar entre los niveles del modelo de ingresos.
1. Sea transparente:
    - Proporcione documentación clara sobre las distintas opciones.
    - Proporcione a los clientes herramientas para elegir el modelo de ingresos que mejor se adapte a sus necesidades.

Identifique la gama de modelos de precios necesarios. Un *modelo de precios* describe un conjunto específico de reglas para que el proveedor de API convierta el consumo del consumidor de API en ingresos.

Por ejemplo, para admitir las [fases de cliente anteriores](#step-1---understand-your-customer), se necesitan seis tipos de suscripción:

| Tipo de suscripción | Descripción |
| ----- | ----- |
| `Free` | Permite al consumidor de API realizar pruebas de la API de forma gratuita y sin compromiso para determinar si cumple un caso de uso. Quita todas las barreras de entrada. |
| `Freemium` | Permite al consumidor de API usar la API de forma gratuita, pero pasando a un servicio de pago a medida que aumenta la demanda. |
| `Metered` | El consumidor de API puede realizar tantas llamadas como quiera al mes y pagará una cantidad fija por llamada. |
| `Tier` | El consumidor de API paga por un número establecido de llamadas al mes. Si superan este límite, pagan un importe por uso por encima del límite por llamada adicional. Si regularmente incurren en dicho uso, pueden actualizar al siguiente nivel. |
| `Tier + Overage` | El consumidor de API paga por un número establecido de llamadas al mes. Si superan este límite, pagan una cantidad establecida por llamada adicional. |
| `Unit` | El consumidor de API paga por una cantidad establecida de llamadas al mes. Si superan este límite, tienen que pagar otra unidad de llamadas. |

El modelo de ingresos definirá el conjunto de productos de API. Cada producto de API implementa un modelo de precios específico para dirigirse a una fase específica del ciclo de vida del consumidor de API.

Aunque los modelos de precios generalmente no deben cambiar, es posible que tenga que adaptar la configuración y la aplicación de los modelos de precios para el modelo de ingresos. Por ejemplo, puede ajustar los precios para que coincidan con los de un competidor.

A la vista de los ejemplos anteriores, los modelos de precios se podrían aplicar para crear un modelo de ingresos generales como se muestra a continuación:

| Fase del ciclo de vida del cliente | Modelo de precios | Configuración del modelo de precios | Calidad de servicio |
| ------------------------ | ------------------------- | ------------------ | ----------------------------------------------------------------------------------------------------------- |
| Investigación | Gratuito | Sin implementar. | Cuota establecida para limitar al consumidor a 100 llamadas al mes. |
| Implementación | Precios Freemium | Niveles escalonados: <ul> <li>El importe fijo del primer nivel es de 0 USD.</li> <li>Los siguientes niveles por cargo por importe unitario se establecen para cobrar 0,20 USD por cada 100 llamadas.</li></ul> | No hay cuotas establecidas. El consumidor puede seguir haciendo llamadas y pagar por ellas con un límite máximo de 100 llamadas por minuto. |
| Vista previa | Uso medido | Precio establecido para cobrar al consumidor: 0,15 USD/100 llamadas. | No hay cuotas establecidas. El consumidor puede seguir haciendo llamadas y pagar por ellas con un límite máximo de 200 llamadas por minuto. |
| Uso de producción inicial | Nivel | Precio establecido para cobrar al consumidor: 14,95 USD/mes. | Cuota establecida para limitar al consumidor a 50 000 llamadas al mes con un límite máximo de 100 llamadas por minuto. |
| Crecimiento inicial | Nivel + Uso por encima del límite | Niveles escalonados: <ul><li>El importe fijo del primer nivel es de 89,95 USD al mes para las primeras 100 000 llamadas.</li><li>Los siguientes niveles por cargo por importe unitario se establecen para cobrar 0,10 USD por cada 100 llamadas.</li></ul> | No hay cuotas establecidas. El consumidor puede seguir haciendo llamadas adicionales y pagar por ellas con un límite máximo de 100 llamadas por minuto. |
| Escala | Nivel + Uso por encima del límite | Niveles escalonados:<ul><li>El importe fijo del primer nivel es de 449,95 USD al mes para las primeras 500 000 llamadas.</li><li>Los siguientes niveles por cargo por importe unitario se establecen para cobrar 0,06 USD por cada 100 llamadas.</li></ul> | No hay cuotas establecidas. El consumidor puede seguir haciendo llamadas adicionales y pagar por ellas con un límite máximo de 1200 llamadas por minuto. |
| Crecimiento global | Unidad | Niveles escalonados, donde cada importe fijo de nivel es de 749,95 USD/mes para 1 500 000 llamadas. | No hay cuotas establecidas. El consumidor puede seguir haciendo llamadas adicionales y pagar por ellas con un límite máximo de 3500 llamadas por minuto. |

**Dos ejemplos de cómo interpretar el modelo de ingresos en función de la tabla anterior:**

* **Modelo de precios por niveles**  
   Se aplica para admitir a los consumidores de API durante la **fase de producción inicial** del ciclo de vida. Con la configuración del modelo de precios por niveles, el consumidor:  
    * Paga 14,95 USD al mes.  
    * Puede realizar hasta un máximo de 50 000 llamadas al mes. 
    * Tiene un límite máximo de 100 llamadas por minuto.

* **Fase de escalado del ciclo de vida** Se implementa mediante la aplicación del modelo de precios de **Nivel + Uso por encima del límite**, donde los consumidores:
    * Pagan 449,95 USD al mes por las primeras 500 000 llamadas. 
    * Se les cobran 0,06 USD adicionales/100 llamadas después de las primeras 50 000. 
    * Tienen un límite máximo de 1200 llamadas por minuto.

### <a name="step-5---calibrate"></a>Paso 5: Calibrar

Calibrar los precios en el modelo de ingresos para:

- Establecer los precios con el fin de evitar la sobrevaloración o la infravaloración de la API, en función de la investigación de mercado del paso 3 anterior.
- Evitar los puntos del modelo de ingresos que no parezcan justos o instar a los clientes a que trabajen en torno al modelo para lograr precios más favorables.
- Asegurarse de que el modelo de ingresos está orientado a generar un valor total de duración (TLV) suficiente para cubrir el costo total de propiedad más el margen.
- Comprobar que la calidad de las ofertas de servicio en cada nivel de modelo de ingresos puede ser compatible con la solución. 
    - Por ejemplo, si ofrece compatibilidad con 3500 llamadas por minuto, asegúrese de que la solución de un extremo a otro se puede escalar para admitir ese nivel de rendimiento.

### <a name="step-6---release-and-monitor"></a>Paso 6: Liberar y supervisar

Elija una solución adecuada para recopilar el pago por el uso de las API.  Los proveedores tienden a estar en dos grupos:

- **Plataformas de pago, como [Stripe](https://stripe.com/)**
    
    Calcule el pago en función de las métricas de uso de API sin procesar aplicando el modelo de ingresos específico que el cliente ha elegido. Configure la plataforma de pago para reflejar la estrategia de monetización.
- **Proveedores de pago, como [Adyen](https://www.adyen.com/)**

    Solo se ocupan de facilitar la transacción de pago. Deberá aplicar la estrategia de monetización (por ejemplo, traducir las métricas de uso de API en un pago) antes de llamar a este servicio.

Use Azure API Management para acelerar la implementación y eliminar riesgos mediante las funcionalidades integradas que se proporcionan en API Management. Para obtener más información sobre las características específicas de API Management, vea [Compatibilidad de API Management con la monetización](monetization-support.md).

Implemente una solución que genere flexibilidad en la forma de codificar la estrategia de monetización en los sistemas subyacentes con el mismo enfoque que el proyecto de ejemplo. Con la codificación flexible, puede responder dinámicamente y minimizar el riesgo y el costo de realizar cambios.

Siga la [documentación del repositorio GitHub de monetización](https://github.com/microsoft/azure-api-management-monetization) para implementar el proyecto de ejemplo en su propia suscripción de Azure.

Supervise periódicamente cómo se consume la API para permitirle tomar decisiones basadas en evidencias. Por ejemplo, si la evidencia muestra que está abandonando clientes, repita los pasos del 1 al 5 anteriores para descubrir y abordar el origen.

## <a name="ongoing-evolution"></a>Evolución en curso

Revise la estrategia de monetización periódicamente revisando y reevaluando todos los pasos anteriores. Es posible que tenga que desarrollar su estrategia de monetización con el tiempo a medida que obtenga más información sobre los clientes, lo que cuesta proporcionar la API y cómo responde a los cambios competitivos del mercado.

Recuerde que la estrategia de monetización es solo una faceta de una implementación correcta de la API. Otras facetas incluyen:
* La experiencia del desarrollador
* La calidad de la documentación
* Los términos legales
* La capacidad de escalar la API para cumplir los niveles de servicio confirmados.

## <a name="next-steps"></a>Pasos siguientes
* [Compatibilidad de API Management con la monetización](monetization-support.md).
* Implemente una integración de Adyen o Stripe de demostración a través del [repositorio de Git](https://github.com/microsoft/azure-api-management-monetization) asociado.