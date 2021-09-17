---
title: Creación de un sondeo personalizado mediante el portal
titleSuffix: Azure Application Gateway
description: Más información acerca de la creación de un sondeo personalizado para Puerta de enlace de aplicaciones mediante el portal
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 1244cc0fdd9a5c978ee64c1aa7ce4af20272c818
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634445"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Creación de un sondeo personalizado para Puerta de enlace de aplicaciones mediante el portal

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [PowerShell del Administrador de recursos de Azure](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

En este artículo, agregará un sondeo de estado personalizado a una puerta de enlace de aplicaciones existente mediante Azure Portal. Azure Application Gateway usa los sondeos de estado para supervisar el estado de los recursos del grupo de servidores back-end.

## <a name="before-you-begin"></a>Antes de empezar

Si no dispone aún de una puerta de enlace de aplicaciones, visite [Creación de una puerta de enlace de aplicaciones](./quick-create-portal.md) para crear una puerta de enlace de aplicaciones con la que trabajar.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Creación de sondeo para la SKU de Application Gateway v2

Los sondeos se configuran en un proceso de dos pasos a través del portal. En el primer paso se introducen los valores necesarios para la configuración del sondeo. En el segundo paso se prueba el estado del back-end mediante esta configuración de sondeo y se guarda el sondeo. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>Especificación de las propiedades del sondeo

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Si aún no tiene cuenta, puede registrarse para obtener [una evaluación gratuita durante un mes](https://azure.microsoft.com/free).

2. En el panel Favoritos de Azure Portal, haga clic en Todos los recursos. Haga clic en la puerta de enlace de aplicaciones en la hoja Todos los recursos. Si la suscripción que seleccionó ya tiene varios recursos en ella, puede escribir partners.contoso.net en el cuadro Filtrar por nombre… para acceder fácilmente a la puerta de enlace de la aplicación.

3. Seleccione **Sondeos de estado** y, después, seleccione **Agregar** para agregar un nuevo sondeo de estado.

   ![Incorporación de un nuevo sondeo][4]

4. En la página **Agregar sondeo de estado**, rellene la información necesaria para el sondeo y, cuando finalice, seleccione **Aceptar**.

   |**Configuración** | **Valor** | **Detalles**|
   |---|---|---|
   |**Nombre**|customProbe|Este valor es un nombre descriptivo para el sondeo al que se puede acceder en el portal.|
   |**Protocolo**|HTTP o HTTPS | El protocolo que usa el sondeo de estado. |
   |**Host**|es decir, contoso.com|Este valor es el nombre del host virtual (diferente del nombre de host de la máquina virtual) que se ejecuta en el servidor de aplicaciones. El sondeo se envía a \<protocol\>://\<host name\>:\<port\>/\<urlPath\>. También puede ser la dirección IP privada del servidor, la dirección IP pública o la entrada DNS de la dirección IP pública.  Con esta acción, se intentará acceder al servidor cuando se utilice con una entrada de ruta de acceso basada en archivos y se validará que existe un archivo específico en el servidor como una comprobación de estado.|
   |**Seleccionar el nombre de host de la configuración de HTTP de back-end**|Sí o no|Establece el encabezado de *host* del sondeo en el nombre de host de la configuración HTTP a la que está asociado este sondeo. Se requiere especialmente en el caso del back-end multiinquilino, como en el caso de Azure App Service. [Más información](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Seleccionar el puerto de la configuración HTTP de back-end**| Sí o no|Establece el *puerto* del sondeo de estado en el puerto desde la configuración HTTP a la que está asociado este sondeo. Si elige no, puede especificar un puerto de destino personalizado que se deba usar. |
   |**Puerto**| 1-65535 | Puerto personalizado que se usará para los sondeos de estado | 
   |**Path**|/ o cualquier ruta de acceso válida|El resto de la dirección URL completa del sondeo personalizado. Las rutas de acceso válidas comienzan por '/'. Para la ruta de acceso predeterminada de "http:\//contoso.com", use solo "/".  También puede especificar una ruta de acceso de servidor a un archivo para realizar una comprobación de estado estática en lugar de basada en web.  Se deben usar rutas de acceso de archivo mientras se use una dirección IP pública o privada, o la entrada DNS de IP pública como entrada de nombre de host.|
   |**Intervalo (segundos)**|30|La frecuencia con que se ejecuta el sondeo para comprobar el estado. No se recomienda establecer un valor inferior a 30 segundos.|
   |**Tiempo de espera (segundos)**|30|El período que espera el sondeo antes de agotarse el tiempo de espera. Si no se recibe una respuesta válida dentro del período de tiempo de espera, el sondeo se marca como error. El intervalo de tiempo de espera debe ser lo suficientemente alto como para que se pueda realizar una llamada http para asegurarse de que la página de mantenimiento de back-end está disponible. Tenga en cuenta que el valor de tiempo de espera no debe ser mayor que el valor de 'Intervalo' usado en esta configuración de sondeo o el valor de 'Tiempo de espera de solicitud' de la configuración de HTTP que se asociará a este sondeo.|
   |**Umbral incorrecto**|3|Número de intentos erróneos consecutivos necesarios para que se considere incorrecto. El umbral se puede establecer en 1 o más.|
   |**Usar condiciones de coincidencia de sondeo**|Sí o no|De forma predeterminada, una respuesta HTTP (S) con el código de estado entre 200 y 399 se considera correcta. Puede cambiar el intervalo aceptable de códigos de respuesta de back-end o el cuerpo de respuesta de back-end. [Más información](./application-gateway-probe-overview.md#probe-matching)|
   |**Configuración HTTP**|Selección de lista desplegable|El sondeo se asociará a las configuraciones HTTP seleccionadas aquí y, por lo tanto, supervisará el estado del grupo de servidores back-end asociado a la configuración de HTTP seleccionada. Usará el mismo puerto para la solicitud de sondeo que el que se usa en la configuración HTTP seleccionada. Solo puede elegir las configuraciones HTTP que no estén asociadas a ningún otro sondeo personalizado. <br>Tenga en cuenta que solo están disponibles para la asociación las configuraciones HTTP que tienen el mismo protocolo que el protocolo elegido en esta configuración de sondeo y el mismo estado en el modificador *Seleccionar el nombre de host de la configuración de HTTP de back-end*.|
   
   > [!IMPORTANT]
   > El sondeo supervisará el estado del back-end solo si está asociado a una o varias configuraciones HTTP. Supervisará los recursos de back-end de los grupos de servidores back-end asociados a las configuraciones HTTP a las que está asociado este sondeo. La solicitud de sondeo se enviará como \<protocol\>://\<hostName\>:\<port\>/\<urlPath\>.

### <a name="test-backend-health-with-the-probe"></a>Prueba del estado del back-end con el sondeo

Después de especificar las propiedades de sondeo, puede probar el estado de los recursos de back-end para comprobar que la configuración del sondeo es correcta y que los recursos de back-end funcionan según lo previsto.

1. Seleccione **Probar** y observe el resultado del sondeo. Application Gateway prueba el estado de todos los recursos de back-end de los grupos de servidores back-end asociados a las configuraciones HTTP que se usan para este sondeo. 

   ![Prueba del estado del back-end][5]

2. Si hay recursos de back-end incorrectos, consulte la columna **Detalles** para comprender el motivo de este estado del recurso. Si el recurso se ha marcado como incorrecto debido a una configuración de sondeo incorrecta, seleccione el vínculo **Volver al sondeo** y edite la configuración del sondeo. En caso contrario, si el recurso se ha marcado como incorrecto debido a un problema con el back-end, resuelva los problemas con el recurso de back-end y, a continuación, vuelva a probar el back-end; para ello, seleccione el vínculo **Volver al sondeo** y, después, **Probar**.

   > [!NOTE]
   > Puede guardar el sondeo aunque haya recursos de back-end incorrectos, pero no se recomienda. Esto se debe a que Application Gateway no reenviará solicitudes a los servidores de back-end desde el grupo de back-end que el sondeo ha determinado que es incorrecto. Si no hay recursos correctos en un grupo de back-end, no podrá acceder a la aplicación y recibirá un error HTTP 502.

   ![Visualización del resultado del sondeo][6]

3. Seleccione **Agregar** para guardar el sondeo. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Creación del sondeo para la SKU de Application Gateway v1

Los sondeos se configuran en un proceso de dos pasos a través del portal. El primer paso consiste en crear el sondeo. En el segundo paso, agregue el sondeo a la configuración HTTP de back-end de la puerta de enlace de aplicaciones.

### <a name="create-the-probe"></a><a name="createprobe"></a>Creación del sondeo

1. Inicie sesión en [Azure Portal](https://portal.azure.com). Si aún no tiene cuenta, puede registrarse para obtener [una evaluación gratuita durante un mes](https://azure.microsoft.com/free).

2. En el panel Favoritos de Azure Portal, seleccione **Todos los recursos**. Seleccione la puerta de enlace de aplicaciones en la página **Todos los recursos**. Si la suscripción que seleccionó ya tiene varios recursos en ella, puede escribir partners.contoso.net en el cuadro Filtrar por nombre… para acceder fácilmente a la puerta de enlace de la aplicación.

3. Seleccione **Sondeos** y, después, seleccione **Agregar** para agregar un sondeo.

   ![Agregar hoja Sondeo con la información rellena][1]

4. En la hoja **Agregar sondeo de estado**, rellene la información necesaria para el sondeo y, cuando finalice, seleccione **Aceptar**.

   |**Configuración** | **Valor** | **Detalles**|
   |---|---|---|
   |**Nombre**|customProbe|Este valor es un nombre descriptivo para el sondeo al que se puede acceder en el portal.|
   |**Protocolo**|HTTP o HTTPS | El protocolo que usa el sondeo de estado. |
   |**Host**|es decir, contoso.com|Este valor es el nombre del host virtual (diferente del nombre de host de la máquina virtual) que se ejecuta en el servidor de aplicaciones. El sondeo se envía a (protocolo)://(nombre de host):(puerto de httpsetting)/urlPath.  Este valor se aplica cuando se configura un entorno multisitio en Application Gateway. Si Application Gateway se configura para un único sitio, escriba '127.0.0.1'.  También puede especificar una ruta de acceso de servidor a un archivo para realizar una comprobación de estado estática en lugar de basada en web. Se deben usar rutas de acceso de archivo mientras se use una dirección IP pública o privada, o la entrada DNS de IP pública como entrada de nombre de host.|
   |**Seleccionar el nombre de host de la configuración de HTTP de back-end**|Sí o no|Establece el encabezado *host* del sondeo en el nombre de host del recurso de back-end del grupo de servidores back-end asociado a la configuración de HTTP a la que también está asociado este sondeo. Se requiere especialmente en el caso del back-end multiinquilino, como en el caso de Azure App Service. [Más información](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Path**|/ o cualquier ruta de acceso válida|El resto de la dirección URL completa del sondeo personalizado. Las rutas de acceso válidas comienzan por '/'. Para la ruta de acceso predeterminada de "http:\//contoso.com", use "/". También puede especificar una ruta de acceso de servidor a un archivo para realizar una comprobación de estado estática en lugar de basada en web.  Se deben usar rutas de acceso de archivo mientras se use una dirección IP pública o privada, o la entrada DNS de IP pública como entrada de nombre de host.|
   |**Intervalo (segundos)**|30|La frecuencia con que se ejecuta el sondeo para comprobar el estado. No se recomienda establecer un valor inferior a 30 segundos.|
   |**Tiempo de espera (segundos)**|30|El período que espera el sondeo antes de agotarse el tiempo de espera. Si no se recibe una respuesta válida dentro del período de tiempo de espera, el sondeo se marca como error. El intervalo de tiempo de espera debe ser lo suficientemente alto como para que se pueda realizar una llamada http para asegurarse de que la página de mantenimiento de back-end está disponible. Tenga en cuenta que el valor de tiempo de espera no debe ser mayor que el valor de 'Intervalo' usado en esta configuración de sondeo o el valor de 'Tiempo de espera de solicitud' de la configuración de HTTP que se asociará a este sondeo.|
   |**Umbral incorrecto**|3|Número de intentos erróneos consecutivos necesarios para que se considere incorrecto. El umbral se puede establecer en 1 o más.|
   |**Usar condiciones de coincidencia de sondeo**|Sí o no|De forma predeterminada, una respuesta HTTP (S) con el código de estado entre 200 y 399 se considera correcta. Puede cambiar el intervalo aceptable de códigos de respuesta de back-end o el cuerpo de respuesta de back-end. [Más información](./application-gateway-probe-overview.md#probe-matching)|

   > [!IMPORTANT]
   > El nombre de host no es el mismo que el del servidor. Este valor es el nombre del host virtual que se ejecuta en el servidor de aplicaciones. El sondeo se envía a \<protocol\>://\<hostName\>:\<port from http settings\>/\<urlPath\>

### <a name="add-probe-to-the-gateway"></a>Adición de un sondeo a la puerta de enlace

Una vez creado el sondeo, es el momento de agregarlo a la puerta de enlace. La configuración del sondeo se establece en la configuración de http del back-end de la puerta de enlace de aplicaciones.

1. Haga clic en la **configuración de HTTP** de la instancia de Application Gateway para abrir la hoja Configuración y haga clic en la configuración de HTTP del back-end actual que aparece en la ventana.

   ![ventana de configuración de https][2]

2. En la página de configuración de **appGatewayBackEndHttpSettings**, active la casilla **Usar sondeo personalizado** y elija, en el menú desplegable **Sondeo personalizado**, el sondeo creado en la sección [Creación del sondeo](#createprobe).
   Cuando haya terminado, haga clic en **Guardar** y se aplicará la configuración.

## <a name="next-steps"></a>Pasos siguientes

Vea el estado de los recursos de back-end según la determinación del sondeo mediante la [visualización del estado del back-end](./application-gateway-diagnostics.md#back-end-health).

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
