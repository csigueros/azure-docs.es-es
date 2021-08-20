---
title: Control de la IP de salida de Azure Functions mediante un servicio NAT Gateway de Azure Virtual Network
description: Tutorial paso a paso que explica cómo configurar NAT para una función conectada a un servicio Azure Virtual Network
ms.topic: tutorial
ms.author: kyburns
ms.date: 2/26/2021
ms.openlocfilehash: 9b91ccd725fbd6ae1e3a974ecb70b4c01201adb4
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113432273"
---
# <a name="tutorial-control-azure-functions-outbound-ip-with-an-azure-virtual-network-nat-gateway"></a>Tutorial: Control de la IP de salida de Azure Functions mediante un servicio NAT Gateway de Azure Virtual Network

La traducción de direcciones de red (NAT) virtual simplifica la conectividad de Internet solo saliente para las redes virtuales. Cuando se configura en una subred, todas las conexiones salientes usan las direcciones IP públicas estáticas que se hayan especificado. El proceso NAT puede ser útil para instancias de Azure Functions o Web Apps que necesitan consumir un servicio de terceros que use una lista de permitidos para direcciones IP como medida de seguridad. Para más información, consulte [¿Qué es Virtual Network NAT?](../virtual-network/nat-gateway/nat-overview.md).

En este tutorial se muestra cómo usar el servicio Virtual Network NAT para enrutar el tráfico saliente desde una función desencadenada por HTTP. Esta función le permite comprobar su propia dirección IP saliente. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una red virtual
> * Crear una aplicación de funciones de plan Prémium.
> * Crear una dirección IP pública
> * Crear un servicio NAT Gateway
> * Configurar una aplicación de funciones para enrutar el tráfico saliente a través de NAT Gateway

## <a name="topology"></a>Topología

El siguiente diagrama muestra la arquitectura de la solución que se ha creado:

![Interfaz de usuario para la integración de NAT Gateway](./media/functions-how-to-use-nat-gateway/topology.png)

La ejecución de Functions en el plan Premium tiene las mismas capacidades de hospedaje que las aplicaciones web en Azure App Service, que incluye la característica de integración de red virtual. Para obtener más información sobre la integración de red virtual, incluida la solución de problemas y la configuración avanzada, vea [Integración de su aplicación con una red virtual de Azure](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Prerrequisitos

En este tutorial, es importante que comprenda el direccionamiento IP y las subredes. Puede empezar con [este artículo que trata los aspectos básicos del direccionamiento y las subredes](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Hay muchos más artículos y vídeos disponibles en línea.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Si ya ha completado el tutorial sobre la [integración de Azure Functions con Azure Virtual Network](./functions-create-vnet.md), puede consultar directamente [Creación de una función desencadenada por HTTP](#create-function).

## <a name="create-a-virtual-network"></a>Creación de una red virtual

1. En el menú de Azure Portal, seleccione **Crear un recurso**. En Azure Marketplace, seleccione **Redes** > **Red virtual**.

1. En **Creación de una red virtual**, escriba o seleccione los valores especificados, tal como se muestra en la tabla siguiente:

    | Configuración | Value |
    | ------- | ----- |
    | Suscripción | Seleccione su suscripción.|
    | Resource group | Seleccione **Crear nuevo**, escriba *myResourceGroup* y, después, seleccione **Aceptar**. |
    | Nombre | Escriba *myResourceGroup-vnet*. |
    | Location | Seleccione **Este de EE. UU**.|

1. Seleccione **Next: IP Addresses** (Siguiente: Direcciones IP); y para el **espacio de direcciones IPv4**, escriba *10.10.0.0/16*.

1. Seleccione **Agregar subred** y escriba *Tutorial-Net* para el **nombre de subred** y *10.10.1.0/24* para **Intervalo de direcciones de subred**.

    ![Pestaña de direcciones IP para crear una red virtual](./media/functions-how-to-use-nat-gateway/create-vnet-2-ip-space.png)

1. Seleccione **Agregar** y, después, **Revisar y crear**. Deje el resto tal como está y seleccione **Crear**.

1. En **Crear red virtual**, seleccione **Crear**.

A continuación, cree una aplicación de funciones en el [plan Prémium](functions-premium-plan.md). Este plan ofrece escala sin servidor, al mismo tiempo que admite la integración de red virtual.

## <a name="create-a-function-app-in-a-premium-plan"></a>Creación de una aplicación de funciones en un plan Premium

En este tutorial se muestra cómo crear la aplicación de función en un [plan Premium](functions-premium-plan.md). La misma funcionalidad también está disponible cuando se usa un [plan dedicado (App Service)](dedicated-plan.md).

> [!NOTE]  
> Para conseguir una experiencia óptima con este tutorial, elija .NET para la pila en tiempo de ejecución; y elija Windows como sistema operativo. Además, cree la aplicación de funciones en la misma región que la red virtual.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-the-virtual-network"></a>Conexión de la aplicación de funciones a la red virtual

Ahora puede conectar la aplicación de funciones a la red virtual.

1. En la aplicación de funciones, seleccione **Redes** en el menú de la izquierda y, en **Integración de VNet**, seleccione **Haga clic aquí para configurar**.

    :::image type="content" source="./media/functions-how-to-use-nat-gateway/networking-0.png" alt-text="Elección de una red en la aplicación de funciones":::

1. En la página de **Integración con red virtual**, seleccione **Agregar VNET**.

1. En **Estado de la característica de red**, use la configuración de la tabla que aparece debajo de la imagen:

    ![Definición de la red virtual de la aplicación de funciones](./media/functions-how-to-use-nat-gateway/networking-3.png)

    | Configuración      | Valor sugerido  | Descripción      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | MyResourceGroup-vnet | Esta red virtual es la que se ha creado anteriormente. |
    | **Subred** | Creación de una subred nueva | Cree una subred en la red virtual para aplicación de funciones que se va a usar. La integración de red virtual se debe configurar para usar una subred vacía. |
    | **Nombre de subred** | Function-Net | Nombre de la nueva subred. |
    | **Bloque de direcciones de la red virtual** | 10.10.0.0/16 | Solo debe tener un bloque de direcciones definido. |
    | **El bloque de direcciones de la subred** | 10.10.2.0/24   | El tamaño de la subred restringe el número total de instancias a las que la aplicación de funciones del plan Premium puede escalar horizontalmente. En este ejemplo se usa una subred de `/24` con 254 direcciones disponibles en el host. Esta subred tiene un aprovisionamiento excesivo, pero es fácil de calcular. |

1. Seleccione **Aceptar** para agregar la subred. Cierre las páginas de **Integración con red virtual** y de **Estado de característica de red** para volver a la página de aplicación de funciones.

La aplicación de funciones podrá acceder ahora a la red virtual. A continuación, agregará una función desencadenada por HTTP a la aplicación de funciones.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Creación de una función de desencadenador de HTTP

1. En el menú de la izquierda de la ventana **Funciones**, seleccione **Funciones** y, a continuación, seleccione **Agregar** en el menú superior. 
 
1. En la ventana **Nueva función**, seleccione el **desencadenador HTTP** y acepte el nombre predeterminado para la **nueva función** o escriba otro nombre. 

1. En **Código y prueba**, reemplace el código de script de C# generado por la plantilla (.csx) por el código siguiente: 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
    
        var client = new HttpClient();
        var response = await client.GetAsync(@"https://ifconfig.me");
        var responseMessage = await response.Content.ReadAsStringAsync();
    
        return new OkObjectResult(responseMessage);
    }
    ```

    Este código llama a un sitio web externo que devuelve la dirección IP del autor de la llamada, que en este caso es la función. Este método permite determinar fácilmente la dirección IP de salida que utiliza la aplicación de funciones.

Ya está listo para ejecutar la función y comprobar las direcciones IP de salida actuales.

## <a name="verify-current-outbound-ips"></a>Comprobación de las direcciones IP de salida actuales

Ahora puede ejecutar la función. Pero primero regístrese en el portal y vea qué direcciones IP de salida usa la aplicación de funciones.  

1. En la aplicación de funciones, seleccione **Propiedades** y revise el campo **Direcciones IP de salida**.

    ![Visualización de las direcciones IP de salida de una aplicación de funciones](./media/functions-how-to-use-nat-gateway/function-properties-ip.png)

1. Vuelva a la función de desencadenador HTTP, seleccione **Código y prueba** y después **Probar/ejecutar**.

    ![Probar función](./media/functions-how-to-use-nat-gateway/function-code-test.png)

1. Seleccione **Ejecutar** para ejecutar la función. A continuación, cambie a **Salida**. 

    ![Salida de la prueba de la función](./media/functions-how-to-use-nat-gateway/function-test-1-output.png)

1. Compruebe que la dirección IP del cuerpo de la respuesta HTTP coincida con uno de los valores de direcciones IP de salida que vio anteriormente.

Ahora puede crear una dirección IP pública y usar un servicio NAT Gateway para modificar esta dirección IP de salida.

## <a name="create-public-ip"></a>Creación de una IP pública

1. En el grupo de recursos, seleccione **Agregar**, busque en Azure Marketplace la **dirección IP pública** y seleccione **Crear**. Utilice la configuración de la tabla debajo de la imagen:

    ![Creación de una dirección IP pública](./media/functions-how-to-use-nat-gateway/create-public-ip.png)

    | Configuración      | Valor sugerido  |
    | ------------ | ---------------- |
    | **Versión de la dirección IP** | IPv4 |
    | **SKU** | Estándar |
    | **Nivel** | Regional |
    | **Nombre** | IP de salida |
    | **Suscripción** | Asegúrese de que se muestra la suscripción | 
    | **Grupos de recursos** | myResourceGroup (o el nombre asignado al grupo de recursos) |
    | **Ubicación** | Este de EE. UU. (o la ubicación que asignó a los demás recursos) |
    | **Zona de disponibilidad** | Ninguna zona |

1. Seleccione **Crear** para iniciar la implementación.

1. Una vez finalizada la implementación, vaya al recurso de dirección IP pública recién creado y consulte la dirección IP en **Información general**.

    ![Visualización de la dirección IP pública](./media/functions-how-to-use-nat-gateway/public-ip-overview.png)

## <a name="create-nat-gateway"></a>Creación de una instancia de NAT Gateway

Ahora, vamos a crear el servicio NAT Gateway. En el [tutorial anterior sobre redes virtuales](functions-create-vnet.md), el nombre de subred sugerido era `Function-Net` y el nombre de red virtual sugerido era `MyResourceGroup-vnet`.

1. En el grupo de recursos, seleccione **Agregar**, busque en Azure Marketplace la **dirección IP pública** y seleccione **Crear**. Use la configuración de la tabla que aparece debajo de la imagen para rellenar la pestaña **Básico**:

    ![Creación de una instancia de NAT Gateway](./media/functions-how-to-use-nat-gateway/create-nat-1-basics.png)

    | Configuración      | Valor sugerido  |
    | ------------ | ---------------- |  
    | **Suscripción** | Su suscripción | 
    | **Grupos de recursos** | myResourceGroup (o el nombre asignado al grupo de recursos) |
    | **Nombre de NAT Gateway** | myNatGateway |
    | **Región** | Este de EE. UU. (o la ubicación que asignó a los demás recursos) |
    | **Zona de disponibilidad** | None |

1. Seleccione **Next: Outbound IP** (Siguiente: Dirección IP de salida). En el campo **Direcciones IP públicas**, seleccione la dirección IP pública creada anteriormente. Deje sin seleccionar los **prefijos de dirección IP pública**.

1. Seleccione **Next: Subnet** (Siguiente: Subred). Seleccione el recurso *myResourceGroup-vnet* en el campo **Red virtual** y *Function-Net* como subred.

    ![Selección de una subred](./media/functions-how-to-use-nat-gateway/create-nat-3-subnet.png)

1. Seleccione **Revisar y crear** y, a continuación, **Crear** para enviar la implementación.

Una vez finalizada la implementación, el servicio NAT Gateway estará listo para enrutar el tráfico procedente de la subred de la aplicación de funciones a Internet.

## <a name="update-function-configuration"></a>Actualización de la configuración de la función

Ahora, deberá agregar la configuración de la aplicación `WEBSITE_VNET_ROUTE_ALL` y establezca el valor en `1`.  Esta configuración fuerza el tráfico de salida a través de la red virtual y el servicio NAT Gateway asociado. Sin esta configuración, el tráfico de Internet no se enrutará a través de la red virtual integrada y se verán las mismas direcciones IP de salida. 

1. Vaya a la aplicación de funciones en Azure Portal y seleccione **Configuración** en el menú de la izquierda.

1. En **Configuración de la aplicación**, seleccione **+ Mueva configuración de la aplicación** y use los siguientes valores para rellenar los campos:

    |Nombre del campo  |Value |
    |---|---|
    |**Nombre**    |WEBSITE_VNET_ROUTE_ALL|
    |**Valor**   |1|

1. Seleccione **Aceptar** para cerrar el cuadro de diálogo de la nueva configuración de la aplicación.

1. Seleccione **Guardar** y después **Continuar** para guardar la configuración.

La aplicación de funciones ya está configurada para enrutar el tráfico a través de su red virtual asociada.

## <a name="verify-new-outbound-ips"></a>Comprobación de las nuevas direcciones IP de salida

Repita los [pasos anteriores](#verify-current-outbound-ips) para volver a ejecutar la función. Ahora debería ver la dirección IP de salida que configuró en el servicio NAT en la salida de la función.

## <a name="clean-up-resources"></a>Limpieza de recursos

Ha creado recursos para completar este tutorial. Se le facturará por el consumo de estos recursos en función del [estado de la cuenta](https://azure.microsoft.com/account/) y los [precios de los servicios](https://azure.microsoft.com/pricing/). Para evitar costos adicionales, elimine los recursos cuando ya no los necesite. 

[!INCLUDE [functions-quickstart-cleanup-inner](../../includes/functions-quickstart-cleanup-inner.md)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Opciones de redes de Azure Functions](functions-networking-options.md)
