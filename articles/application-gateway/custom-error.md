---
title: Creación de páginas de error personalizadas de Azure Application Gateway
description: Este artículo muestra cómo crear páginas de error personalizadas de Application Gateway. Mediante una página de error personalizada puede usar su propia marca y diseño.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/16/2019
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5bdae2055f46f6f933325c95b86d427951c6cfbc
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222656"
---
# <a name="create-application-gateway-custom-error-pages"></a>Creación de páginas de error personalizadas de Application Gateway

Application Gateway permite crear páginas de error personalizadas, en lugar de mostrar las páginas de error predeterminadas. Mediante una página de error personalizada puede usar su propia marca y diseño.

Por ejemplo, puede definir su propia página de mantenimiento si la aplicación web no está accesible. O bien, puede crear una página de acceso no autorizado si se envía una solicitud malintencionada a una aplicación web.

Las páginas de error personalizadas se admiten para los dos escenarios siguientes:

- **Página de mantenimiento**: esta página de error personalizada se envía en lugar de una página de puerta de enlace incorrecta 502. Se muestra cuando Application Gateway no tiene ningún back-end para enrutar el tráfico. Por ejemplo, cuando hay un mantenimiento programado o cuando un problema imprevisto afecta al acceso al grupo de back-end.
- **Página de acceso no autorizado**: esta página de error personalizada se envía en lugar de una página de acceso no autorizado 403. Se muestra cuando el WAF de Application Gateway detecta tráfico malintencionado y lo bloquea.

Si se origina un error en los servidores de back-end, se pasa de vuelta sin modificar al llamador. No se muestra una página de error personalizada. Application Gateway puede mostrar una página de error personalizada cuando una solicitud no puede conectar con el back-end.

Las páginas de error personalizadas se pueden definir en el nivel global y en el nivel del agente de escucha:

- **Nivel global**: la página de error se aplica al tráfico de todas las aplicaciones web implementadas en esa puerta de enlace de aplicación.
- **Nivel del agente de escucha**: la página de error se aplica al tráfico recibido en ese agente de escucha.
- **Ambos**: la página de error personalizada definida en el nivel del agente de escucha reemplaza la establecida en el nivel global.

Para crear una página de error personalizada, debe tener:

- un código de estado de respuesta HTTP.
- la ubicación correspondiente de la página de error. 
- un blob de Azure Storage públicamente accesible para la ubicación.
- un tipo de extensión *.htm o *.html. 

El tamaño de la página de error debe ser inferior a 1 MB. Puede hacer referencia a imágenes o CSS internas o externas para este archivo HTML. Para los recursos a los que se hace referencia externamente, use direcciones URL absolutas a las que se pueda acceder públicamente. Tenga en cuenta el tamaño del archivo HTML al usar imágenes internas (imagen insertada codificada en Base64) o CSS. Actualmente no se admiten vínculos relativos con archivos en la misma ubicación del blob.

Después de especificar una página de error, la puerta de enlace de la aplicación lo descarga desde la ubicación de Blob Storage y lo guarda en la memoria caché de la puerta de enlace de aplicaciones local. A continuación, la puerta de enlace de aplicaciones proporciona esa página HTML, mientras que el cliente captura directamente los recursos a los que se hace referencia externamente. Para modificar una página de error personalizada existente, debe apuntar a una ubicación de blob distinta en la configuración de la puerta de enlace de aplicaciones. La puerta de enlace de aplicaciones no comprueba periódicamente la ubicación del blob para recuperar las nuevas versiones.

## <a name="portal-configuration"></a>Configuración del portal

1. Vaya a Application Gateway en el portal y elija una puerta de enlace de aplicaciones.

    ![Instantánea en la que aparece la página de información general de una puerta de enlace de aplicación.](media/custom-error/ag-overview.png)
2. Haga clic en **Agentes de escucha** y vaya a un agente de escucha determinado en el que desea especificar una página de error.

    ![Agentes de escucha de Application Gateway](media/custom-error/ag-listener.png)
3. Configure una página de error personalizada para un error 403 de WAF o una página de mantenimiento 502 en el nivel del agente de escucha.

    > [!NOTE]
    > Actualmente no se admite la creación de páginas de error personalizadas de nivel global desde Azure Portal.

4. Especifique una dirección URL para el blob accesible públicamente para un código de estado de error especificado y haga clic en **Guardar**. Application Gateway ahora está configurado con la página de error personalizada.

   ![Códigos de error de Application Gateway](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Configuración de Azure PowerShell

También puede usar Azure PowerShell para configurar una página de errores personalizada. Por ejemplo, una página de error personalizada general:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

O una página de error específica del nivel del agente de escucha:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$listener01 = Get-AzApplicationGatewayHttpListener -Name <listener-name> -ApplicationGateway $appgw

$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

Para más información, consulte [Add-AzApplicationGatewayCustomError](/powershell/module/az.network/add-azapplicationgatewaycustomerror) y [Add-AzApplicationGatewayHttpListenerCustomError](/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror).

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de los diagnósticos de Application Gateway, consulte [Mantenimiento del back-end, registros de diagnóstico y métricas de Application Gateway](application-gateway-diagnostics.md).
