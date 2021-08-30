---
title: Integración de NAT Gateway - Azure App Service | Microsoft Docs
description: Describe cómo se integra NAT Gateway con Azure App Service.
services: app-service
author: madsd
ms.assetid: 0a84734e-b5c1-4264-8d1f-77e781b28426
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 08/04/2021
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 851e1a04b7fa5ac14848c9d679118a8ebf4ada77
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780602"
---
# <a name="virtual-network-nat-gateway-integration"></a>Integración de Virtual Network NAT Gateway

NAT Gateway es un servicio totalmente administrado y altamente resistente, que se puede asociar a una o varias subredes y garantiza que todo el tráfico de Internet saliente se redirija a través de la puerta de enlace. Con App Service, hay dos escenarios importantes para los que puede usar NAT Gateway. 

NAT Gateway proporciona una IP pública estática y predecible para el tráfico saliente de Internet saliente. También aumenta considerablemente los [puertos SNAT](../troubleshoot-intermittent-outbound-connection-errors.md) disponibles en escenarios en los que tiene un gran número de conexiones simultáneas con la misma combinación de dirección pública y puerto.

Para obtener más detalles y conocer los precios, consulte la [información general sobre NAT Gateway](../../virtual-network/nat-gateway/nat-overview.md).

:::image type="content" source="./media/nat-gateway-integration/nat-gateway-overview.png" alt-text="En el diagrama se muestra el tráfico de Internet que fluye a NAT Gateway en una instancia de Azure Virtual Network.":::

> [!Note] 
> El uso de NAT Gateway con App Service depende de la integración con red virtual regional, por lo que se necesita el plan **Standard**, **Premium**, **PremiumV2** o **PremiumV3** de App Service.

## <a name="configuring-nat-gateway-integration"></a>Configuración de la integración de NAT Gateway

Para configurar la integración de NAT Gateway con App Service, es necesario completar los siguientes pasos:

* Configure la integración con red virtual regional con su aplicación tal y como se describe en [Integración de su aplicación con una instancia de Azure Virtual Network](../web-sites-integrate-with-vnet.md).
* Asegúrese de que [Redirigir todo](../web-sites-integrate-with-vnet.md#routes) esté habilitado para su integración con red virtual, de modo que el tráfico dirigido a Internet se vea afectado por las rutas de su red virtual.
* Aprovisione una puerta de enlace NAT con una IP pública y asóciela a la subred de la integración con red virtual.

Configure NAT Gateway a través del portal:

1. Vaya a la interfaz de usuario **Redes** en el portal de App Service y seleccione "Integración con red virtual" en la sección "Tráfico saliente". Asegúrese de que la aplicación esté integrada con una subred y de que **Redirigir todo** se haya habilitado.
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-route-all-enabled.png" alt-text="Captura de pantalla de &quot;Redirigir todo&quot; habilitado para Integración con red virtual.":::
1. En el menú de Azure Portal o en la **página principal**, seleccione **Crear un recurso**. Aparece la ventana **Nuevo**.
1. Busque "Puerta de enlace NAT" y selecciónelo en la lista de resultados.
1. Rellene la información de **Aspectos básicos** y elija la región donde se encuentra la aplicación.
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-create-basics.png" alt-text="Captura de pantalla de la pestaña &quot;Aspectos básicos&quot; en &quot;Creación de una puerta de enlace NAT&quot;.":::
1. En la pestaña **IP de salida**, cree una IP pública o selecciona otra ya existente.
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-create-outbound-ip.png" alt-text="Captura de pantalla de la pestaña &quot;IP de salida&quot; en &quot;Creación de una puerta de enlace NAT&quot;.":::
1. En la pestaña **Subred**, seleccione la subred usada para la integración con red virtual.
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-create-subnet.png" alt-text="Captura de pantalla de la pestaña &quot;Subred&quot; en &quot;Creación de una puerta de enlace NAT&quot;.":::
1. Rellene las etiquetas si es necesario y haga clic en **Crear** para crear la puerta de enlace NAT. Una vez aprovisionada la puerta de enlace NAT, haga clic en **Ir al grupo de recursos** y seleccione la nueva puerta de enlace. Puede ver la IP pública que su aplicación usará para el tráfico de Internet saliente en la hoja "IP de salida".
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-public-ip.png" alt-text="Captura de pantalla de la hoja &quot;IP de salida&quot; en el portal de la puerta de enlace NAT."::: 

Si prefiere usar la CLI para configurar su entorno, estos son los comandos importantes. Como requisito previo, debe crear una aplicación web que tenga configurado Integración con red virtual.

Asegúrese de que **Redirigir todo** esté configurado para su integración con red virtual. (*Nota:* El valor mínimo necesario de `az version` es 2.27):

```azurecli-interactive
az webapp config set --resource-group [myResourceGroup] --name [myWebApp] --vnet-route-all-enabled
```

Cree una IP pública y una puerta de enlace NAT:

```azurecli-interactive
az network public-ip create --resource-group [myResourceGroup] --name myPublicIP --sku standard --allocation static

az network nat gateway create --resource-group [myResourceGroup] --name myNATgateway --public-ip-addresses myPublicIP --idle-timeout 10
```

Asocie la puerta de enlace NAT a la subred de la integración con red virtual:

```azurecli-interactive
az network vnet subnet update --resource-group [myResourceGroup] --vnet-name [myVnet] --name [myIntegrationSubnet] --nat-gateway myNATgateway
```

## <a name="scaling-nat-gateway"></a>Escalado de la puerta de enlace NAT

Se puede usar la misma puerta de enlace NAT en varias subredes de la misma red virtual, lo que permite usar una puerta de enlace NAT en múltiples aplicaciones y planes de App Service.

NAT Gateway admite direcciones IP públicas y prefijos de IP públicas. Una puerta de enlace NAT puede admitir hasta 16 direcciones IP entre direcciones y prefijos de IP individuales. Cada dirección IP asigna 64 000 puertos (puertos SNAT), por lo que permite hasta un millón de puertos disponibles. Para obtener más información, consulte la [sección "Escalado" ](../../virtual-network/nat-gateway/nat-gateway-resource.md#scaling) de NAT Gateway.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre la puerta de enlace NAT, consulte la [documentación de NAT Gateway](../../virtual-network/nat-gateway/nat-overview.md).

Para obtener más información sobre Integración con red virtual, consulte la [documentación correspondiente](../web-sites-integrate-with-vnet.md).