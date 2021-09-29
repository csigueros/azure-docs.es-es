---
title: Directivas de Azure API Management | Microsoft Docs
description: Obtenga información acerca de cómo crear, editar y configurar directivas en Administración de API. Vea ejemplos de código y otros recursos disponibles.
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/25/2021
ms.author: danlep
ms.openlocfilehash: 37e25908bd08ca9618bc56bb2e10ff8e48a46fc6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649685"
---
# <a name="policies-in-azure-api-management"></a>Directivas de Azure API Management

En Azure API Management, los publicadores de API pueden cambiar el comportamiento de la API a través de la configuración mediante directivas. Las directivas son una colección de declaraciones que se ejecutan secuencialmente en la solicitud o respuesta de una API. Algunas instrucciones populares son:

* Conversión de formato de XML a JSON.
* Limitación de la frecuencia de llamadas para restringir el número de llamadas entrantes de un desarrollador. 

Hay muchas más directivas disponibles y listas para usar.

Las directivas se aplican en la puerta de enlace entre el consumidor de la API y la API administrada. Mientras que la puerta de enlace recibe solicitudes y las reenvía, sin modificaciones, a la API subyacente, una directiva puede aplicar cambios tanto en la solicitud entrante como en la respuesta saliente.

Salvo que la directiva especifique lo contrario, las expresiones de directiva pueden utilizarse como valores de atributo o valores de texto en cualquiera de las directivas de API Management. Algunas directivas como [Flujo de control][Control flow] y [Establecer variable][Set variable] se basan en expresiones de directiva. Para más información, consulte los artículos [Directivas avanzadas][Advanced policies] y [Expresiones de directiva][Policy expressions].

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>Descripción de la configuración de directivas

Las definiciones de directivas son documentos XML que describen una secuencia de instrucciones de entrada y salida. Puede editar el XML directamente en la ventana de definición, que también proporciona lo siguiente:
* Una lista de instrucciones a la derecha.
* Las instrucciones aplicables al ámbito actual habilitadas y resaltadas.

Al hacer clic en una declaración habilitada se agregará el XML correspondiente en la ubicación del cursor en la vista de definición. 

> [!NOTE]
> Si la directiva que desea agregar no está habilitada, asegúrese de que se encuentra en el ámbito correcto para esa directiva. Cada instrucción de la directiva está diseñada para su uso en determinados ámbitos y secciones de la directiva. Para revisar las secciones y los ámbitos de una directiva, compruebe la sección **Uso** en la [referencia de directivas][Policy Reference].

La configuración se divide en `inbound`, `backend`, `outbound` y `on-error`. Esta serie de instrucciones de una directiva determinada se ejecuta en orden para una solicitud y una respuesta.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

Si se produce un error durante el procesamiento de una solicitud:
* Los pasos restantes de las secciones `inbound`, `backend` o `outbound` se omiten.
* La ejecución salta a las instrucciones de la sección `on-error`.

Al colocar instrucciones de directiva en la sección `on-error`, puede hacer lo siguiente:
* Revise el error mediante la propiedad `context.LastError`.
* Inspeccione y personalice la respuesta del error mediante la directiva `set-body`.
* Configure lo que sucede si se produce un error. 

Para más información, consulte [Control de errores en las directivas de API Management](./api-management-error-handling-policies.md).
* Pasos integrados
* Errores que pueden producirse durante el procesamiento de instrucciones de directiva. 

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>Configuración de directivas

Para información sobre cómo configurar directivas, consulte el artículo sobre [edición o establecimiento de directivas](set-edit-policies.md).

## <a name="policy-reference"></a>Referencia de las directivas

Consulte [Referencia de directivas](./api-management-policies.md) para ver una lista completa de declaraciones de directivas y su configuración.

## <a name="policy-samples"></a>Ejemplos de directivas

Vea [ejemplos de directivas](./policy-reference.md) para obtener más código de ejemplo.

## <a name="examples"></a>Ejemplos

### <a name="apply-policies-specified-at-different-scopes"></a>Aplicación de las directivas especificadas en distintos ámbitos

Si tiene una directiva en el nivel global y una directiva configurada para una API, cuando se use esa API en concreto, se aplicarán ambas directivas. API Management permite el orden determinista de las instrucciones de directiva combinadas mediante el elemento `base`. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

En la definición de directiva de ejemplo anterior:
* La instrucción `cross-domain` se ejecutaría antes que las directivas superiores.
* La directiva `find-and-replace` se ejecutaría después que las directivas superiores. 

>[!NOTE]
> Si quita la etiqueta `<base />` en el ámbito de la API, solo se aplicarán las directivas configuradas en el ámbito de la API. No se aplicarán las directivas de producto ni las de ámbito global.

### <a name="restrict-incoming-requests"></a>Restricción de las solicitudes de entrada

Para agregar una nueva instrucción a fin de restringir las solicitudes de entrada a direcciones IP concretas, sitúe el cursor exactamente dentro del contenido del elemento XLM `inbound` y haga clic en la instrucción **Restrict caller IPs** (Restringir las IP del autor de llamada).

![Restriction policies][policies-restrict]

Así, se agregará un fragmento de código XML al elemento `inbound` que ofrece orientación sobre cómo configurar la instrucción.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Para limitar las solicitudes de entrada y aceptar solo las procedentes de una dirección IP de 1.2.3.4, modifique el XML de la manera siguiente:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo trabajar con directivas, consulte:

+ [API de transformación](transform-api.md)
+ En la [Referencia de directivas](./api-management-policies.md) se muestra una lista completa de declaraciones de directivas y su configuración
+ [Ejemplos de directivas](./policy-reference.md)   

[Policy Reference]: ./api-management-policies.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: ./mock-api-responses.md

[Advanced policies]: ./api-management-advanced-policies.md
[Control flow]: ./api-management-advanced-policies.md#choose
[Set variable]: ./api-management-advanced-policies.md#set-variable
[Policy expressions]: ./api-management-policy-expressions.md

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
