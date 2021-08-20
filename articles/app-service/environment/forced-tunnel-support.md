---
title: Configuración de la tunelización forzada
description: Aprenda a habilitar App Service Environment para que funcione cuando se realiza la tunelización forzada del tráfico saliente en una red virtual.
author: ccompy
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.topic: quickstart
ms.date: 05/29/2018
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 8d05b4f8e69023e1ecec7002f394141fff008f0d
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113432975"
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Configuración de App Service Environment con tunelización forzada
> [!NOTE]
> En este artículo se aborda App Service Environment v2, que se usa con planes de App Service aislados
> 

App Service Environment (ASE) es una implementación de Azure App Service en Azure Virtual Network de un cliente. Muchos clientes configuran sus redes virtuales de Azure como extensiones de sus redes locales con redes privadas virtuales o con conexiones de Azure ExpressRoute. La tunelización forzada se produce al redirigir el tráfico de Internet a una VPN o a una aplicación virtual en su lugar. Las aplicaciones virtuales se usan a menudo para inspeccionar y auditar el tráfico de red saliente. 

ASE cuenta con varias dependencias externas, que se describen en el documento sobre [arquitectura de red de App Service Environment][network]. Normalmente todo el tráfico de dependencias saliente de ASE debe pasar a través de la dirección VIP que se aprovisiona con el ASE. Si cambia la ruta para el tráfico hacia o desde el ASE sin seguir esta información, su ASE dejará de funcionar.

En una instancia de Azure Virtual Network, el enrutamiento se realiza según la coincidencia de prefijo más larga (LPM). Si hay más de una ruta con la misma coincidencia LPM, se selecciona una ruta en función de su origen en el orden siguiente:

* Ruta definida por el usuario (UDR)
* Ruta BGP (cuando se utiliza ExpressRoute)
* Ruta del sistema

Para más información sobre el enrutamiento en una red virtual, lea [Rutas definidas por el usuario y reenvío IP][routes]. 

Si desea enrutar el tráfico saliente del ASE a algún otro lugar que no sea directamente a Internet, tiene las siguientes opciones:

* Habilitar el ASE para que tenga acceso directo a Internet
* Configurar la subred del ASE para omitir las rutas de BGP
* Configurar la subred del ASE para usar puntos de conexión de servicio a Azure SQL y Azure Storage
* Agregar sus propias direcciones IP al firewall de Azure SQL de ASE

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Permitir que App Service Environment tenga acceso directo a Internet

Para hacer que el ASE vaya directamente a internet, incluso si la red virtual de Azure se configura con ExpressRoute, haga lo siguiente:

* Configurar ExpressRoute para anunciar 0.0.0.0/0. De manera predeterminada, enruta todo el tráfico saliente en local.
* Crear una UDR con el prefijo de direcciones 0.0.0.0/0 y un tipo de próximo salto de Internet y aplicarlo a la subred de ASE.

Si realiza estos dos cambios, el tráfico destinado a Internet que se origina en la subred de App Service Environment no se fuerza hacia la conexión ExpressRoute.

Si la red ya enruta de tráfico local, es preciso que cree la subred que hospede su ASE y que configure el UDR para él antes de intentar implementar el ASE.  

> [!IMPORTANT]
> Las rutas definidas en una UDR tienen que ser lo suficientemente específicas para que tengan prioridad sobre cualquier otra ruta anunciada por la configuración de ExpressRoute. En el ejemplo anterior se utiliza el intervalo de direcciones amplio 0.0.0.0/0. Puede reemplazarse accidentalmente por los anuncios de ruta con intervalos de direcciones más específicos.
>
> Las instancias de App Service Environment no son compatibles con las configuraciones de ExpressRoute que anuncian rutas entre la ruta de acceso de emparejamiento público y la ruta de acceso de emparejamiento privado. Las configuraciones de ExpressRoute con el emparejamiento público configurado reciben anuncios de ruta de Microsoft. Los anuncios contienen un gran conjunto de intervalos de direcciones de Microsoft Azure. Si los intervalos de direcciones se anuncian en la ruta de acceso de emparejamiento privado, se enrutan todos los paquetes de red salientes de la subred de App Service Environment a la infraestructura de red local de un cliente. Este flujo de red no se admite de forma predeterminada con las instancias de App Service Environment. Una solución a este problema es dejar de anunciar las rutas entre la ruta de acceso de emparejamiento público y la de emparejamiento privado. Otra solución consiste en permitir que App Service Environment funcione en una configuración de tunelización forzada.

![Acceso directo a Internet][1]

## <a name="configure-your-ase-subnet-to-ignore-bgp-routes"></a>Configurar la subred del ASE para omitir las rutas de BGP ## 

Puede configurar la subred del ASE para omitir todas las rutas de BGP.  En este caso, el ASE podrá acceder a sus dependencias sin ningún problema.  Sin embargo, deberá crear UDR para permitir que las aplicaciones accedan a los recursos locales.

Para configurar la subred del ASE para omitir las rutas de BGP:

* Cree una UDR y asígnela a la subred del ASE si todavía no tiene una.
* En Azure Portal, abra la interfaz de usuario de la tabla de rutas asignada a la subred del ASE.  Seleccione Configuración.  Establezca la propagación de la ruta de la puerta de enlace de red virtual en Deshabilitado.  Haga clic en Guardar. La documentación sobre cómo desactivar eso se encuentra en el documento [Creación de una tabla de rutas][routetable].

Después de configurar la subred de ASE para omitir todas las rutas BGP, ya no podrá comunicarse de forma local. Para permitir que las aplicaciones accedan a los recursos locales, edite la UDR asignada a la subred de ASE y agregue rutas para los intervalos de direcciones locales. El tipo de Próximo salto se debe establecer en Puerta de enlace de red virtual. 


## <a name="configure-your-ase-with-service-endpoints"></a>Configuración del ASE con puntos de conexión de servicio ##

Para enrutar todo el tráfico saliente desde el ASE, excepto el que va a SQL Azure y a Azure Storage, siga estos pasos:

1. Cree una tabla de rutas y asígnela a la subred de ASE. Busque las direcciones para la región en [Direcciones de administración de App Service Environment][management]. Cree rutas para dichas direcciones con el próximo salto de Internet. Estas rutas son necesarias porque el tráfico de administración entrante de App Service Environment debe responderse desde la misma dirección a la que se envió.   

2. Habilite puntos de conexión de servicio con Azure SQL y Azure Storage con la subred de ASE.  Tras completar este paso ya se puede configurar la red virtual con la tunelización forzada.

Para más información sobre la implementación de un ASE con una plantilla, lea [Creación de una instancia de App Service Environment mediante una plantilla][template].

Los puntos de conexión de servicio le permiten restringir el acceso de los servicios multiinquilino a un conjunto de subredes y redes virtuales de Azure. Puede leer más acerca de los puntos de conexión de servicio en la documentación de los [puntos de conexión de servicio de red virtual][serviceendpoints]. 

Cuando se habilitan puntos de conexión de servicio en un recurso, hay rutas que se crean con prioridad más alta que las demás. Si usa puntos de conexión de servicio con una ASE con túnel forzado, el tráfico de administración de Azure SQL y de Azure Storage no se enruta a través de tunelización forzada. El resto del tráfico de dependencia de ASE se enruta con tunelización forzada y no se puede perder o el ASE no funcionaría correctamente.

Cuando los puntos de conexión de servicio se habilitan en una subred con una instancia de Azure SQL, todas las instancias de Azure SQL conectadas desde esa subred deben tener habilitados también los puntos de conexión de servicio. Si desea acceder a varias instancias de Azure SQL desde la misma subred, debe habilitar los puntos de conexión de servicio en todas ellas y no solo en una.  Azure Storage no se comporta igual que Azure SQL.  Cuando se habilitan los puntos de conexión de servicio con Azure Storage, se bloquea el acceso a ese recurso desde la subred, pero aún se puede tener acceso a otras cuentas de Azure Storage incluso si no tienen habilitados los puntos de conexión de servicio.  

Si configura la tunelización forzada con un dispositivo de filtro de red, recuerde que el ASE tiene otras dependencias, además de Azure SQL y Azure Storage. Si se bloquea el tráfico a dichas dependencias, el ASE no funcionará correctamente.

![Túnel forzado con puntos de conexión de servicio][2]

## <a name="add-your-own-ips-to-the-ase-azure-sql-firewall"></a>Agregar sus propias direcciones IP al firewall de Azure SQL de ASE ##

Para enrutar a través del túnel todo el tráfico saliente desde el ASE, excepto el que va a Azure Storage, siga los pasos siguientes:

1. Cree una tabla de rutas y asígnela a la subred de ASE. Busque las direcciones para la región en [Direcciones de administración de App Service Environment][management]. Cree rutas para dichas direcciones con el próximo salto de Internet. Estas rutas son necesarias porque el tráfico de administración entrante de App Service Environment debe responderse desde la misma dirección a la que se envió. 

2. Habilite puntos de conexión de servicio con Azure Storage y la subred de ASE

3. Obtenga las direcciones que se usarán para todo el tráfico saliente desde App Service Environment a Internet. Si va a enrutar el tráfico de forma local, estas direcciones son las direcciones IP de la puerta de enlace o los dispositivos NAT. Si quiere enrutar el tráfico de salida de App Service Environment a través de una NVA, la dirección de salida sería la dirección IP pública de la NVA.

4. _Para establecer las direcciones de salida en una instancia existente de App Service Environment_: Vaya a resources.azure.com y, luego, a Subscription/\<subscription id>/resourceGroups/\<ase resource group>/providers/Microsoft.Web/hostingEnvironments/\<ase name>. A continuación, puede ver el archivo JSON que describe su instancia de App Service Environment. Asegúrese de que, en la parte superior, indica **lectura/escritura**. Seleccione **Editar**. Desplácese hasta la parte inferior. Cambie el valor de **userWhitelistedIpRanges** de **null** a algo como lo siguiente. Use las direcciones que desea establecer como el intervalo de direcciones de salida. 

    ```json
    "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"]
    ```

   Seleccione **PUT** en la parte superior. Esta opción desencadena una operación de escalado en App Service Environment y ajusta el firewall.

_Para crear su ASE con las direcciones de salida_: siga las instrucciones que se proporcionan en [Creación de una instancia de App Service Environment con una plantilla][template] y use la plantilla adecuada.  Modifique la sección "resources" en el archivo azuredeploy.json, pero no el bloque "properties" e incluya una línea para **userWhitelistedIpRanges** con sus valores.

```json
"resources": [
    {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/hostingEnvironments",
        "name": "[parameters('aseName')]",
        "kind": "ASEV2",
        "location": "[parameters('aseLocation')]",
        "properties": {
            "name": "[parameters('aseName')]",
            "location": "[parameters('aseLocation')]",
            "ipSslAddressCount": 0,
            "internalLoadBalancingMode": "[parameters('internalLoadBalancingMode')]",
            "dnsSuffix" : "[parameters('dnsSuffix')]",
            "virtualNetwork": {
                "Id": "[parameters('existingVnetResourceId')]",
                "Subnet": "[parameters('subnetName')]"
            },
            "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]
        }
    }
]
```

Estos cambios envían el tráfico a Azure Storage directamente desde el ASE y permiten el acceso a Azure SQL desde direcciones adicionales que no sean la dirección VIP del ASE.

   ![Túnel forzado con lista de permitidos de SQL][3]

## <a name="preventing-issues"></a>Prevención de problemas ##

Si la comunicación entre el ASE y sus dependencias se interrumpe, el ASE no funcionará bien.  Si permanece así demasiado tiempo, se suspenderá. Para anular su suspensión, siga las instrucciones que aparecen en el portal del ASE.

Además de interrumpir simplemente la comunicación, puede afectar de forma desfavorable al ASE introduciendo demasiada latencia. Esto puede suceder si el ASE está demasiado lejos de la red local.  Un ejemplo de ello sería atravesar un océano o continente para acceder a la red local. También se puede introducir latencia debido a restricciones en el ancho de banda saliente o a la congestión de la intranet.


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/asedependencies.png
[2]: ./media/forced-tunnel-support/forcedtunnelserviceendpoint.png
[3]: ./media/forced-tunnel-support/forcedtunnelexceptstorage.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[routetable]: ../../virtual-network/manage-route-table.md#create-a-route-table
