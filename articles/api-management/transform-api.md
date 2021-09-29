---
title: 'Tutorial: Transformación y protección de una API con Azure API Management | Microsoft Docs'
description: En este tutorial, aprenderá a proteger una API en API Management con directivas de transformación y limitación (limitación de frecuencia).
author: dlepow
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/28/2020
ms.author: danlep
ms.openlocfilehash: be58732e3279b7bfaccee76a8d5bc9d09ea664ac
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656405"
---
# <a name="tutorial-transform-and-protect-your-api"></a>Tutorial: Transformación y protección de una API

En este tutorial se muestra cómo puede transformar una API para que no revele información sobre el back-end privado. Por ejemplo, puede que quiera ocultar la información sobre la pila de tecnología que se ejecuta en el back-end o las URL originales que aparecen en el cuerpo de la respuesta HTTP de la API y, en su lugar, redirigirlas a la puerta de enlace de APIM.

En este tutorial se muestra lo fácil que es agregar protección para la API de back-end configurando un límite de frecuencia con Azure API Management. Por ejemplo, puede que quiera limitar la frecuencia de llamadas API para que los desarrolladores no las usen en exceso. Para más información, consulte [Directivas de API Management](api-management-policies.md).

En este tutorial, aprenderá a:

> [!div class="checklist"]
>
> -   Transformación una API para eliminar encabezados de respuesta
> -   Reemplazo de URL originales en el cuerpo de la respuesta de API con las URL de puerta de enlace de APIM
> -   Protección de una API agregando una directiva de límite de frecuencia (limitación)
> -   Prueba de las transformaciones

:::image type="content" source="media/transform-api/api-management-management-console.png" alt-text="Directivas en el portal":::

## <a name="prerequisites"></a>Requisitos previos

-   Conocer la [terminología de API Management de Azure](api-management-terminology.md).
-   Comprender el [concepto de directivas en API Management de Azure](api-management-howto-policies.md).
-   Complete el siguiente inicio rápido: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
-   Además, realice el siguiente tutorial: [Importación y publicación de la primera API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Transformación una API para eliminar encabezados de respuesta

En esta sección se muestra cómo ocultar los encabezados HTTP que no quiera mostrar a los usuarios. En este ejemplo, se muestra cómo eliminar los siguientes encabezados en la respuesta HTTP:

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>Prueba de la respuesta original

Para ver la respuesta original, siga estos pasos:

1. En la instancia de servicio de API Management, seleccione **API**.
1. Seleccione **Demo Conference API** en la lista de API.
1. Seleccione la pestaña **Prueba** en la parte superior de la pantalla.
1. Seleccione la operación **GetSpeakers** y elija **Enviar**.

La respuesta original debe ser similar a la siguiente:

:::image type="content" source="media/transform-api/original-response.png" alt-text="Respuesta de la API original":::

Como puede ver, la respuesta incluye los encabezados **X-AspNet-Version** y **X-Powered-By**.

### <a name="set-the-transformation-policy"></a>Establecimiento de la directiva de transformación

1. Seleccione **Demo Conference API** > **Diseño** > **Todas las operaciones**.
4. En la sección **Procesamiento de salida**, seleccione el icono del editor de código ( **</>** ).

   :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png" alt-text="Ir a la directiva de salida" border="false":::

1. Coloque el cursor dentro del elemento **&lt;outbound&gt;** y seleccione **Show snippets** (Mostrar fragmentos de código) en la esquina superior derecha.
1. En la ventana derecha, en **Transformation policies** (Directivas de transformación), seleccione **Set HTTP header** (Establecer encabezado HTTP) dos veces (para insertar dos fragmentos de código de directiva).

   :::image type="content" source="media/transform-api/transform-api.png" alt-text="Directiva de establecimiento de encabezado HTTP":::

1. Modificar el código **\<outbound>** para que se parezca a esto:

   ```
   <set-header name="X-Powered-By" exists-action="delete" />
   <set-header name="X-AspNet-Version" exists-action="delete" />
   ```

   :::image type="content" source="media/transform-api/set-policy.png" alt-text="Establecer encabezado HTTP":::

1. Seleccione **Guardar**.

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Reemplazo de URL originales en el cuerpo de la respuesta de API con las URL de puerta de enlace de APIM

En esta sección se explica cómo ocultar las direcciones URL originales que aparecen en el cuerpo de la respuesta HTTP de la API y, en su lugar, redirigirlas a la puerta de enlace de APIM.

### <a name="test-the-original-response"></a>Prueba de la respuesta original

Para ver la respuesta original, siga estos pasos:

1. Seleccione **Demo Conference API** > **Probar**.
1. Seleccione la operación **GetSpeakers** y elija **Enviar**.

    Como puede ver, la respuesta incluye las direcciones URL de back-end originales:

    :::image type="content" source="media/transform-api/original-response2.png" alt-text="Direcciones URL de back-end originales en la respuesta":::


### <a name="set-the-transformation-policy"></a>Establecimiento de la directiva de transformación

1.  Seleccione **Demo Conference API** > **Todas las operaciones** > **Diseño**.
1.  En la sección **Procesamiento de salida**, seleccione el icono del editor de código ( **</>** ).
1.  Coloque el cursor dentro del elemento **&lt;outbound&gt;** y seleccione **Show snippets** (Mostrar fragmentos de código) en la esquina superior derecha.
1.  En la ventana derecha, en **Transformation policies** (Directivas de transformación), haga clic en **Mask URLs in content** (Enmascarar direcciones URL en contenido). 
1.  Seleccione **Guardar**.

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Protección de una API agregando la directiva de límite de frecuencia (limitación)

En esta sección se explica cómo agregar protección para la API de back-end configurando límites de frecuencia. Por ejemplo, puede que quiera limitar la frecuencia de llamadas API para que los desarrolladores no las usen en exceso. En este ejemplo, el límite se establece en 3 llamadas cada 15 segundos para cada identificador de suscripción. Después de 15 segundos, un desarrollador puede volver a tratar de llamar a la API.

1.  Seleccione **Demo Conference API** > **Todas las operaciones** > **Diseño**.
1.  En la sección **Procesamiento de entrada**, seleccione el icono del editor de código ( **</>** ).
1.  Coloque el cursor dentro del elemento **&lt;inbound&gt;** y seleccione **Show snippets** (Mostrar fragmentos de código) en la esquina superior derecha.

    :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png" alt-text="Establecimiento de la directiva de entrada" border="false":::

1.  En la ventana derecha, en **Access restriction policies** (Directivas de restricción de acceso), seleccione **+ Limit call rate per key** (+ Limitar la frecuencia de llamadas por clave).
1.  Cambie el código **rate-limit-by-key** (en el elemento **\<inbound\>** ) por el código siguiente:

    ```
    <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
    ```

## <a name="test-the-transformations"></a>Prueba de las transformaciones

Llegados a este punto, si examina el código en el editor de código, las directivas tienen este aspecto:

   ```
   <policies>
      <inbound>
        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
        <base />
      </inbound>
      <backend>
        <base />
      </backend>
      <outbound>
        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
        <redirect-content-urls />
        <base />
      </outbound>
      <on-error>
        <base />
      </on-error>
   </policies>
   ```

En el resto de esta sección se prueban transformaciones de directiva que estableció en este artículo.

### <a name="test-the-stripped-response-headers"></a>Prueba de los encabezados de respuesta eliminados

1. Seleccione **Demo Conference API** > **Probar**.
1. Seleccione la operación **GetSpeakers** y elija **Enviar**.

    Como puede ver, los encabezados se han eliminado:

    :::image type="content" source="media/transform-api/final-response1.png" alt-text="Encabezados de respuesta eliminados":::

### <a name="test-the-replaced-url"></a>Prueba de la URL reemplazada

1. Seleccione **Demo Conference API** > **Probar**.
1. Seleccione la operación **GetSpeakers** y elija **Enviar**.

    Como puede ver, la dirección URL se ha reemplazado.

    :::image type="content" source="media/transform-api/final-response2.png" alt-text="Dirección URL reemplazada":::

### <a name="test-the-rate-limit-throttling"></a>Prueba del límite de frecuencia (limitación)

1. Seleccione **Demo Conference API** > **Probar**.
1. Seleccione la operación **GetSpeakers**. Seleccione **Enviar** tres veces en una fila.

    Después de enviar la solicitud 3 veces, recibirá la respuesta **429 Demasiadas solicitudes**.

    :::image type="content" source="media/transform-api/test-throttling.png" alt-text="Demasiadas solicitudes":::

1. Espere unos 15 segundos y seleccione de nuevo **Enviar**. Esta vez debería obtener una respuesta **200 OK**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:

> [!div class="checklist"]
>
> -   Transformación una API para eliminar encabezados de respuesta
> -   Reemplazo de URL originales en el cuerpo de la respuesta de API con las URL de puerta de enlace de APIM
> -   Protección de una API agregando la directiva de límite de frecuencia (limitación)
> -   Prueba de las transformaciones

Avance hasta el siguiente tutorial:

> [!div class="nextstepaction"]
> [Supervisión de una API](api-management-howto-use-azure-monitor.md)
