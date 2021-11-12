---
title: Cuotas y límites de Form Recognizer
titleSuffix: Azure Applied AI Services
description: Referencia rápida, descripción detallada y procedimientos recomendados para las cuotas y los límites del servicio Azure Form Recognizer
services: cognitive-services
author: vkurpad
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 09/30/2021
ms.author: vikurpad
ms.openlocfilehash: 8a1b57571acdbfd8dce29e45218cfd096b0906f6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478129"
---
# <a name="form-recognizer-service-quotas-and-limits"></a>Cuotas y límites del servicio Form Recognizer

Este artículo contiene una referencia rápida y la **descripción detallada** de las cuotas y los límites del servicio Azure Form Recognizer para todos los [planes de tarifa](https://azure.microsoft.com/pricing/details/form-recognizer/). También contiene algunos procedimientos recomendados para evitar la limitación de solicitudes. 

Para el uso con el [SDK de Form Recognizer](quickstarts/try-v3-csharp-sdk.md), la [API de REST de Form Recognizer](quickstarts/try-v3-rest-api.md), el [Estudio de Form Recognizer](quickstarts/try-v3-form-recognizer-studio.md) y la [Herramienta de etiquetado de muestras](https://fott-2-1.azurewebsites.net/).

| Quota | Gratis (F0)<sup>1</sup> | Estándar (S0) |
|--|--|--|
| **Límite de solicitudes simultáneas** | 1 | 15 (valor predeterminado) |
| Ajustable | No<sup>2</sup> | Sí<sup>2</sup> |
| **Límite del modelo de redactado** | 5 | 100 (valor predeterminado) |


<sup>1</sup> Para el plan de tarifa **Gratis (F0)** , consulte también las asignaciones mensuales en la [página de precios](https://azure.microsoft.com/pricing/details/form-recognizer/).
<sup>2</sup> Consulte los [procedimientos recomendados](#example-of-a-workload-pattern-best-practice) y las [instrucciones de ajuste](#create-and-submit-support-request).

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>Descripción detallada, ajuste de cuota y procedimientos recomendados
En términos generales, el reconocimiento facial hace referencia al método de comprobar o identificar a un individuo a partir de su cara. El servicio Form Recognizer emplea el escalado automático para que los recursos de proceso necesarios sean "a petición" y, al mismo tiempo, mantener bajos los costes del cliente, desaprovisionar los recursos no usados gracias a no mantener una cantidad excesiva de capacidad de hardware. Cada vez que la aplicación reciba un código de respuesta 429 ("Demasiadas solicitudes") mientras la carga de trabajo se encuentra dentro de los límites definidos (consulte [Referencia rápida de cuotas y límites](#form-recognizer-service-quotas-and-limits)), la explicación más probable es que el servicio se esté escalando verticalmente según la demanda y que no haya alcanzado aún la escala necesaria, por lo que no tiene inmediatamente suficientes recursos para atender la solicitud. Este estado suele ser transitorio y no debería durar mucho.

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>Procedimientos recomendados generales para mitigar la limitación durante el escalado automático
Para minimizar los problemas relacionados con la limitación (código de respuesta 429), se recomienda usar las técnicas siguientes:
- Implemente lógica de reintento en la aplicación
- Evite cambios bruscos en la carga de trabajo. Aumente la carga de trabajo gradualmente. <br/>
*Ejemplo.* La aplicación usa Form Recognizer y la carga de trabajo actual es de 10 TPS (transacciones por segundo). En el siguiente segundo, aumenta la carga a 40 TPS (es decir, cuatro veces más). El servicio comienza inmediatamente el escalado vertical para satisfacer la nueva carga, pero es probable que no pueda hacerlo en un segundo, por lo que algunas de las solicitudes obtendrán el código de respuesta 429.

En las secciones siguientes se describen los casos específicos de ajuste de las cuotas.
Vaya a [Form Recognizer: aumento del límite de solicitudes simultáneas](#create-and-submit-support-request).

### <a name="increasing-transactions-per-second-request-limit"></a>Aumento del límite de solicitudes de transacciones por segundo
De forma predeterminada, el número de solicitudes simultáneas está limitado a 15 transacciones por segundo para un recurso de Form Recognizer recurso. En el plan de tarifa Estándar, esta cantidad se puede aumentar. Antes de enviar la solicitud, asegúrese de que está familiarizado con el material de [esta sección](#detailed-description-quota-adjustment-and-best-practices) y tenga en cuenta estos [procedimientos recomendados](#example-of-a-workload-pattern-best-practice).

El aumento del límite de solicitudes simultáneas **no** afecta directamente a los costos. El servicio Form Recognizer usa el modelo de "pago por lo que se usa". El límite define hasta dónde se puede escalar el servicio antes de empezar a limitar las solicitudes.

El valor existente del parámetro de límite de solicitudes simultáneas **no** es visible en Azure Portal, en las herramientas de línea de comandos o las solicitudes de API. Para comprobar el valor existente, cree una solicitud de soporte técnico de Azure.

#### <a name="have-the-required-information-ready"></a>Tenga lista la información necesaria:
- Id. de recurso de Form Recognizer
- Region

- **Cómo obtener información (modelo base)** :
  - Vaya a [Azure Portal](https://portal.azure.com/).
  - Seleccione el recurso de Form Recognizer para el que quiere aumentar el límite de transacciones.
  - Seleccione *Propiedades* (grupo *Administración de recursos*)
  - Copie y guarde los valores de los siguientes campos:
    - **Identificador del recurso**
    - **Ubicación** (la región del punto de conexión)

#### <a name="create-and-submit-support-request"></a>Creación y envío de una solicitud de soporte técnico
Inicie el aumento del límite de transacciones por segundo (TPS) para el recurso mediante el envío de la solicitud de soporte técnico:

- Asegúrese de que tiene la [información necesaria](#have-the-required-information-ready).
- Vaya a [Azure Portal](https://portal.azure.com/).
- Seleccione el recurso de Form Recognizer para el que quiere aumentar el límite de TPS.
- Seleccione *Nueva solicitud de soporte técnico* (grupo *Soporte técnico y solución de problemas*).
- Aparecerá una nueva ventana con información rellenada automáticamente sobre la suscripción y el recurso de Azure.
- Rellene el campo *Resumen* (por ejemplo, "Aumento del límite de TPS de Form Recognizer").
- En *Tipo de problema*, seleccione "Quota or usage validation" (Validación de cuota o utilización).
- Haga clic en *Siguiente: Soluciones*
- Continúe con la creación de la solicitud.
- En la pestaña *Detalles*, rellene el campo *Descripción*:
  - Una nota de que la solicitud es sobre la cuota de **Form Recognizer**.
  - Proporcione una expectativa de TPS a la que le gustaría escalar.    
  - La información de recursos de Azure que [recopiló antes](#have-the-required-information-ready).
  - Termine de escribir la información necesaria y haga clic en el botón *Crear* en la pestaña *Revisar y crear*.
  - Anote el número de la solicitud de soporte técnico que aparece en las notificaciones de Azure Portal. Pronto se pondrán en contacto con usted para continuar con el procesamiento.

## <a name="example-of-a-workload-pattern-best-practice"></a>Ejemplo de un procedimiento recomendado para un patrón de carga de trabajo
En este ejemplo se presenta la estrategia que se recomienda seguir para mitigar la posible limitación de solicitudes debido a que el [escalado automático está en curso](#detailed-description-quota-adjustment-and-best-practices). No es una receta exacta, sino una mera plantilla que le invitamos a seguir y ajustar según sea necesario.

Supongamos que un recurso de Form Recognizer tiene establecido el límite predeterminado. Inicie la carga de trabajo para enviar las solicitudes de análisis. Si observa una limitación frecuente con el código de respuesta 429, empiece por dar marcha atrás en la solicitud GET analyze response (obtener respuesta de análisis) y vuelva a intentarlo con el patrón 2-3-5-8. En general, se recomienda no llamar a get analyze response (obtener respuesta de análisis) más de una vez cada 2 segundos para una solicitud POST correspondiente. 

Si se encuentra limitado por el número de solicitudes POST de documentos que se envían, considere la posibilidad de agregar un retraso entre las solicitudes. Si la carga de trabajo requiere un mayor grado de procesamiento simultáneo, deberá crear una solicitud de soporte técnico para aumentar los límites de servicio en las transacciones por segundo.

Por lo general, se recomienda encarecidamente probar la carga de trabajo y los patrones de carga de trabajo antes de pasar a producción.

