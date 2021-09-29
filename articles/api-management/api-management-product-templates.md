---
title: Plantillas de producto de Azure API Management | Microsoft Docs
description: Aprenda a personalizar el contenido de las páginas de producto en el portal para desarrolladores de Azure API Management.
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: danlep
ms.openlocfilehash: 08b14104339be5a05bc7db25088536f2d0be51de
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639097"
---
# <a name="product-templates-in-azure-api-management"></a>Plantillas de producto en Azure API Management

Azure API Management le ofrece la posibilidad de personalizar el contenido de las páginas del portal para desarrolladores mediante un conjunto de plantillas que configuran su contenido. Por medio de la sintaxis [DotLiquid](http://dotliquidmarkup.org/) y el editor que prefiera, como [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers) (DotLiquid para diseñadores), y un conjunto proporcionado de [recursos de cadena](api-management-template-resources.md#strings), [recursos de glifo](api-management-template-resources.md#glyphs) y [controles de página](api-management-page-controls.md) localizados, puede disponer de una gran flexibilidad para configurar el contenido de las páginas como considere oportuno mediante estas plantillas.  
  
 Las plantillas de esta sección le permiten personalizar el contenido de las páginas de producto en el portal para desarrolladores.  
  
-   [Product list](#ProductList)  
  
-   [Producto](#Product)  
  
> [!NOTE]
>  En la siguiente documentación se incluyen plantillas predeterminadas de ejemplo; sin embargo, están sujetas a cambios debido a mejoras continuas. Puede ver las plantillas predeterminadas en vivo en el portal para desarrolladores; para ello, vaya hasta a las plantillas individuales que desee. Para más información sobre cómo trabajar con plantillas, consulte [Cómo personalizar el portal para desarrolladores de API Management mediante plantillas](./api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="product-list"></a><a name="ProductList"></a> Product list  
 La plantilla **Product list** le permite personalizar el cuerpo de la página de lista de productos en el portal para desarrolladores.  
  
 ![Lista de productos](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
### <a name="default-template"></a>Plantilla predeterminada  
  
```xml  
<search-control></search-control>  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if products.size > 0 %}  
    <ul class="list-unstyled">  
    {% for product in products %}  
        <li>  
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>  
            {{product.description}}  
        </li>     
    {% endfor %}  
    </ul>  
    <paging-control></paging-control>  
    {% else %}  
    {% localized "CommonResources|NoItemsToDisplay" %}  
    {% endif %}  
    </div>  
</div>  
```  
  
### <a name="controls"></a>Controles  
 La plantilla `Product list` puede usar los siguientes [controles de página](api-management-page-controls.md).  
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
-   [search-control](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Modelo de datos  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|Paginación|Entidad [Paging](api-management-template-data-model-reference.md#Paging).|La información de paginación de la colección de productos.|  
|Filtrado|Entidad [Filtering](api-management-template-data-model-reference.md#Filtering).|La información de filtrado de la página de lista de productos.|  
|Productos|Colección de entidades de [producto](api-management-template-data-model-reference.md#Product).|Los productos visibles para el usuario actual.|  
  
### <a name="sample-template-data"></a>Ejemplo de datos de plantilla  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 2,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Filtering": {  
        "Pattern": null,  
        "Placeholder": "Search products"  
    },  
    "Products": [  
        {  
            "Id": "56f9445ffaf7560049060001",  
            "Title": "Starter",  
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "Terms": "",  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        },  
        {  
            "Id": "56f9445ffaf7560049060002",  
            "Title": "Unlimited",  
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",  
            "Terms": null,  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        }  
    ]  
}  
```  
  
##  <a name="product"></a><a name="Product"></a> Product  
 La plantilla **Product list** le permite personalizar el cuerpo de la página de producto en el portal para desarrolladores.  
  
 ![Página de producto del portal para desarrolladores](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
### <a name="default-template"></a>Plantilla predeterminada  
  
```xml  
<h2>{{Product.Title}}</h2>  
<p>{{Product.Description}}</p>  
  
{% assign replaceString0 = '{0}' %}  
  
{% if Limits and Limits.size > 0 %}  
<h3>{% localized "ProductDetailsStrings|WebProductsUsageLimitsHeader"%}</h3>  
<ul>  
  {% for limit in Limits %}  
  <li>{{limit.DisplayName}}</li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if apis.size > 0 %}  
<p>  
  <b>  
    {% if apis.size == 1 %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockSingleApisCount" %}{% endcapture %}  
    {% else %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockMultipleApisCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture apisCount %}{{apis.size}}{% endcapture %}  
    {{ apisCountText | replace : replaceString0, apisCount }}  
  </b>  
</p>  
  
<ul>  
  {% for api in Apis %}  
  <li>  
    <a href="/docs/services/{{api.Id}}">{{api.Name}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if subscriptions.size > 0 %}  
<p>  
  <b>  
    {% if subscriptions.size == 1 %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockSingleSubscriptionsCount" %}{% endcapture %}  
    {% else %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockMultipleSubscriptionsCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture subscriptionsCount %}{{subscriptions.size}}{% endcapture %}  
    {{ subscriptionsCountText | replace : replaceString0, subscriptionsCount }}  
  </b>  
</p>  
  
<ul>  
  {% for subscription in subscriptions %}  
  <li>  
    <a href="/developer#{{subscription.Id}}">{{subscription.DisplayName}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
{% if CannotAddBecauseSubscriptionNumberLimitReached %}  
<b>{% localized "ProductDetailsStrings|TextblockSubscriptionLimitReached" %}</b>  
{% elsif CannotAddBecauseMultipleSubscriptionsNotAllowed == false %}  
<subscribe-button></subscribe-button>  
{% endif %}  
```  
  
### <a name="controls"></a>Controles  
 La plantilla `Product list` puede usar los siguientes [controles de página](api-management-page-controls.md).  
  
-   [subscribe-button](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Modelo de datos  
  
|Propiedad|Tipo|Descripción|  
|--------------|----------|-----------------|  
|Producto|[Producto](api-management-template-data-model-reference.md#Product)|El producto especificado.|  
|IsDeveloperSubscribed|boolean|Si el usuario actual está suscrito a este producto.|  
|SubscriptionState|number|Estado de la suscripción. Los estados posibles son:<br /><br /> -   `0 - suspended`: la suscripción está bloqueada y el suscriptor no puede llamar a ninguna API del producto.<br />-   `1 - active`: la suscripción está activa.<br />-   `2 - expired`: la suscripción ha alcanzado su fecha de expiración y se ha desactivado.<br />-   `3 - submitted`: el desarrollador ha realizado una solicitud de suscripción, pero esta aún no se ha aprobado ni rechazado.<br />-   `4 - rejected`: un administrador ha rechazado la solicitud de suscripción.<br />-   `5 - cancelled`: el desarrollador o el administrador han cancelado la suscripción.|  
|Límites|array|Esta propiedad está en desuso y no debe utilizarse.|  
|DelegatedSubscriptionEnabled|boolean|Si la [delegación](./api-management-howto-setup-delegation.md) está habilitada para esta suscripción.|  
|DelegatedSubscriptionUrl|string|Si la delegación está habilitada, la dirección URL de la suscripción delegada.|  
|IsAgreed|boolean|Si el producto tiene términos, si el usuario actual ha aceptado los términos.|  
|Suscripciones|Colección de entidades de [resumen de suscripción](api-management-template-data-model-reference.md#SubscriptionSummary).|Las suscripciones al producto.|  
|Apis|Colección de entidades [API](api-management-template-data-model-reference.md#API).|Las API de este producto.|  
|CannotAddBecauseSubscriptionNumberLimitReached|boolean|Si el usuario actual es apto para suscribirse a este producto en relación con el límite de suscripción.|  
|CannotAddBecauseMultipleSubscriptionsNotAllowed|boolean|Si el usuario actual es apto para suscribirse a este producto en relación con que se permitan o no varias suscripciones.|  
  
### <a name="sample-template-data"></a>Ejemplo de datos de plantilla  
  
```json  
{  
    "Product": {  
        "Id": "56f9445ffaf7560049060001",  
        "Title": "Starter",  
        "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
        "Terms": "",  
        "ProductState": 1,  
        "AllowMultipleSubscriptions": false,  
        "MultipleSubscriptionsCount": 1  
    },  
    "IsDeveloperSubscribed": true,  
    "SubscriptionState": 1,  
    "Limits": [],  
    "DelegatedSubscriptionEnabled": false,  
    "DelegatedSubscriptionUrl": null,  
    "IsAgreed": false,  
    "Subscriptions": [  
        {  
            "Id": "56f9445ffaf7560049070001",  
            "DisplayName": "Starter  (default)"  
        }  
    ],  
    "Apis": [  
        {  
            "id": "56f9445ffaf7560049040001",  
            "name": "Echo API",  
            "description": null,  
            "serviceUrl": "http://echoapi.cloudapp.net/api",  
            "path": "echo",  
            "protocols": [  
                2  
            ],  
            "authenticationSettings": null,  
            "subscriptionKeyParameterNames": null  
        }  
    ],  
    "CannotAddBecauseSubscriptionNumberLimitReached": false,  
    "CannotAddBecauseMultipleSubscriptionsNotAllowed": true  
}  
```

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre cómo trabajar con plantillas, consulte [Cómo personalizar el portal para desarrolladores de API Management mediante plantillas](api-management-developer-portal-templates.md).
