---
title: Controles de página de Azure API Management | Microsoft Docs
description: Aprenda sobre los controles de página disponibles para su uso en las plantillas del portal para desarrolladores de Azure API Management.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: danlep
ms.openlocfilehash: cdaa82308e4208306327dc064642c2b0897597d5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635211"
---
# <a name="azure-api-management-page-controls"></a>Controles de página de Azure API Management
Azure API Management proporciona los siguientes controles para su uso en las plantillas del portal para desarrolladores.  
  
Para usar un control, colóquelo en la ubicación deseada en la plantilla del portal para desarrolladores. Algunos controles, como el control [app-actions](#app-actions), tienen parámetros, como se muestra en el ejemplo siguiente:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
Los valores de los parámetros se pasan como parte del modelo de datos de la plantilla. En la mayoría de los casos, puede pegarlos simplemente en el ejemplo proporcionado para cada control para que funcione correctamente. Para más información sobre los valores de parámetros, puede ver la sección del modelo de datos de cada plantilla en la que se puede usar un control.  

Para más información sobre cómo trabajar con plantillas, consulte [Cómo personalizar el portal para desarrolladores de API Management mediante plantillas](./api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Controles de página de las plantillas del portal para desarrolladores  
  
-   [app-actions](#app-actions)  
-   [basic-signin](#basic-signin)  
-   [paging-control](#paging-control)  
-   [providers](#providers)  
-   [search-control](#search-control)  
-   [sign-up](#sign-up)  
-   [subscribe-button](#subscribe-button)  
-   [subscription-cancel](#subscription-cancel)  
  
##  <a name="app-actions"></a><a name="app-actions"></a> app-actions  
 El control `app-actions` proporciona una interfaz de usuario para la interacción con aplicaciones en la página del perfil de usuario del portal para desarrolladores.  
  
 ![control app&#45;actions](./media/api-management-page-controls/APIM-app-actions-control.png "control app-actions de APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parámetros  
  
|Parámetro|Descripción|  
|---------------|-----------------|  
|appId|El identificador de la aplicación.|  
  
### <a name="developer-portal-templates"></a>Plantillas del portal para desarrolladores  
 El control `app-actions` se puede usar en las siguientes plantillas del portal para desarrolladores:  
  
-   [Aplicaciones](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a><a name="basic-signin"></a> basic-signin  
 El control `basic-signin` ofrece un control para la recolección de información de inicio de sesión del usuario en la página de inicio de sesión del portal para desarrolladores.  
  
 ![control basic&#45;signin](./media/api-management-page-controls/APIM-basic-signin-control.png "control basic-signin de APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parámetros  
 Ninguno.  
  
### <a name="developer-portal-templates"></a>Plantillas del portal para desarrolladores  
 El control `basic-signin` se puede usar en las siguientes plantillas del portal para desarrolladores:  
  
-   [Inicio de sesión](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a><a name="paging-control"></a> paging-control  
 El control `paging-control` proporciona funcionalidad de paginación en las páginas del portal para desarrolladores que muestran una lista de elementos.  
  
 ![control paging-control](./media/api-management-page-controls/APIM-paging-control.png "control paging-control de APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parámetros  
 Ninguno.  
  
### <a name="developer-portal-templates"></a>Plantillas del portal para desarrolladores  
 El control `paging-control` se puede usar en las siguientes plantillas del portal para desarrolladores:  
  
-   [Lista de API](api-management-api-templates.md#APIList)  
  
-   [Issue list](api-management-issue-templates.md#IssueList)  
  
-   [Product list](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a><a name="providers"></a> providers  
 El control `providers` proporciona un control para la selección de proveedores de autenticación en la página de inicio de sesión del portal para desarrolladores.  
  
 ![control providers](./media/api-management-page-controls/APIM-providers-control.png "control providers de APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parámetros  
 Ninguno.  
  
### <a name="developer-portal-templates"></a>Plantillas del portal para desarrolladores  
 El control `providers` se puede usar en las siguientes plantillas del portal para desarrolladores:  
  
-   [Inicio de sesión](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a><a name="search-control"></a> search-control  
 El control `search-control` proporciona funcionalidad de búsqueda en las páginas del portal para desarrolladores que muestran una lista de elementos.  
  
 ![control search-control](./media/api-management-page-controls/APIM-search-control.png "control search-control de APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parámetros  
 Ninguno.  
  
### <a name="developer-portal-templates"></a>Plantillas del portal para desarrolladores  
 El control `search-control` se puede usar en las siguientes plantillas del portal para desarrolladores:  
  
-   [Lista de API](api-management-api-templates.md#APIList)  
  
-   [Product list](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a><a name="sign-up"></a> sign-up  
 El control `sign-up` proporciona un control para la recolección de información de perfil de usuario en la página de inicio de sesión del portal para desarrolladores.  
  
 ![control sign&#45;up](./media/api-management-page-controls/APIM-sign-up-control.png "control sign-up de APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parámetros  
 Ninguno.  
  
### <a name="developer-portal-templates"></a>Plantillas del portal para desarrolladores  
 El control `sign-up` se puede usar en las siguientes plantillas del portal para desarrolladores:  
  
-   [Sign up](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a><a name="subscribe-button"></a> subscribe-button  
 El control `subscribe-button` proporciona un control para la suscripción de un usuario a un producto.  
  
 ![control subscribe&#45;button](./media/api-management-page-controls/APIM-subscribe-button-control.png "control subscribe-button de APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parámetros  
 Ninguno.  
  
### <a name="developer-portal-templates"></a>Plantillas del portal para desarrolladores  
 El control `subscribe-button` se puede usar en las siguientes plantillas del portal para desarrolladores:  
  
-   [Producto](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a><a name="subscription-cancel"></a> subscription-cancel  
 El control `subscription-cancel` proporciona un control para la cancelación de una suscripción a un producto en la página de perfil de usuario del portal para desarrolladores.  
  
 ![control subscription&#45;cancel](./media/api-management-page-controls/APIM-subscription-cancel-control.png "control subscription-cancel de APIM")  
  
### <a name="usage"></a>Uso  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parámetros  
  
|Parámetro|Descripción|  
|---------------|-----------------|  
|subscriptionId|El identificador de la suscripción para cancelar.|  
|cancelUrl|Dirección URL de cancelación de la suscripción.|  
  
### <a name="developer-portal-templates"></a>Plantillas del portal para desarrolladores  
 El control `subscription-cancel` se puede usar en las siguientes plantillas del portal para desarrolladores:  
  
-   [Producto](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre cómo trabajar con plantillas, consulte [Cómo personalizar el portal para desarrolladores de API Management mediante plantillas](api-management-developer-portal-templates.md).
