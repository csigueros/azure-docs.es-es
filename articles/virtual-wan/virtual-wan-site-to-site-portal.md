---
title: 'Tutorial: Creación de conexiones de sitio a sitio mediante Azure Virtual WAN'
description: Aprenda a usar Azure Virtual WAN para crear una conexión VPN de sitio a sitio en Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 08/18/2021
ms.author: cherylmc
ms.openlocfilehash: dbc48719f2897c22717319e1e07b5b3b3146fe84
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122638231"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Tutorial: Creación de una conexión de sitio a sitio mediante Azure Virtual WAN

Este tutorial muestra cómo usar Virtual WAN para conectarse a los recursos de Azure a través de una conexión VPN de IPsec/IKE (IKEv1 e IKEv2). Este tipo de conexión requiere un dispositivo VPN local que tenga una dirección IP pública asignada. Para más información sobre Virtual WAN, consulte la [Introducción a Virtual WAN](virtual-wan-about.md).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una instancia de Virtual WAN
> * Crear un concentrador
> * Crear una puerta de enlace de VPN de sitio a sitio
> * Crear un sitio
> * Conectar un sitio a un centro de conectividad
> * Conectar un sitio VPN a un centro de conectividad
> * Conectar una red virtual a un concentrador
> * Descarga de un archivo de configuración
> * Ver o editar la puerta de enlace de VPN

> [!NOTE]
> Si tiene muchos sitios, normalmente usará un [asociado de Virtual WAN](https://aka.ms/virtualwan) para crear esta configuración. De todas formas, puede crear esta configuración usted mismo si está familiarizado con las redes y tiene experiencia en la configuración de su propio dispositivo VPN.
>

:::image type="content" source="./media/virtual-wan-about/virtualwan.png" alt-text="La captura de pantalla muestra un diagrama de redes para Virtual WAN.":::

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar con la configuración, compruebe que se cumplen los criterios siguientes:

[!INCLUDE [Before you begin](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Creación de una instancia de Virtual WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-hub"></a><a name="hub"></a>Creación de un centro de conectividad

Un centro de conectividad es una red virtual que puede contener puertas de enlace para las funcionalidades de sitio a sitio, ExpressRoute o de punto a sitio. Para este tutorial, comenzará rellenando la pestaña **Aspectos básicos** del centro de conectividad virtual y, a continuación, continuará con la pestaña de sitio a sitio en la sección siguiente. Tenga en cuenta que es posible crear un centro de conectividad vacío (un centro que no contenga ninguna puerta de enlace) y, más adelante, agregar puertas de enlace (S2S, P2S, ExpressRoute, etc.). Una vez que haya creado el centro de conectividad, se le facturará, aunque no lo conecte a ningún sitio ni cree ninguna puerta de enlace en él.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-site-to-site-vpn-gateway"></a><a name="gateway"></a>Crear una puerta de enlace de VPN de sitio a sitio

En esta sección, configurará la conectividad de sitio a sitio y, a continuación, continuará con la creación del centro de conectividad y la puerta de enlace de VPN S2S. Un centro de conectividad y una puerta de enlace pueden tardar unos 30 minutos en crearse.

[!INCLUDE [Create a gateway](../../includes/virtual-wan-tutorial-s2s-gateway-include.md)]

## <a name="create-site"></a><a name="site"></a>Crear sitio

En esta sección, va a crear un sitio. Los sitios se corresponden con las ubicaciones físicas. Puede crear tantos sitios como necesite. Por ejemplo, si tiene una sucursal en Nueva York, otra en Londres y otra en Los Ángeles, crearía tres sitios independientes. Estos sitios contienen los puntos de conexión de dispositivo VPN local. Puede crear hasta 1000 sitios en cada centro de conectividad virtual de una instancia de Virtual WAN. Si tiene varios centros de conectividad, puede crear 1000 sitios en cada uno de ellos. Si tiene un dispositivo CPE de un asociado de Virtual WAN, hable con él para conocer en qué consiste su automatización de Azure. Normalmente, la automatización implica que, con un simple clic, se puede exportar a Azure la información de las ramas a gran escala y configurar la conectividad entre el CPE y la puerta de enlace de VPN de Azure Virtual WAN. Para más información, consulte la [guía de automatización de Azure para asociados de CPE](virtual-wan-configure-automation-providers.md).

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connect-vpn-site-to-hub"></a><a name="connectsites"></a>Conectar el sitio VPN al centro de conectividad

En esta sección, conectará el sitio VPN al centro de conectividad.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="connect-vnet-to-hub"></a><a name="vnet"></a>Conexión de una red virtual a un centro de conectividad

En esta sección, va a crear una conexión entre el centro de conectividad y la red virtual.

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="download-vpn-configuration"></a><a name="device"></a>Descarga de la configuración de VPN

Use el archivo de configuración del dispositivo VPN para configurar el dispositivo VPN local. Los pasos básicos se describen a continuación. La información sobre lo que contiene el archivo de configuración y cómo configurar el dispositivo VPN es la siguiente: 

1. Vaya a la página **Centro de conectividad virtual -> VPN (Sitio a sitio)** .

1. En la parte superior de la página **VPN (de sitio a sitio)** , seleccione **Descargar la configuración de VPN**. Verá una serie de mensajes mientras Azure crea una cuenta de almacenamiento en el grupo de recursos "microsoft-network-[location]", donde "location" es la ubicación de la red WAN.

1. Una vez el archivo se haya terminado de crear, haga clic en el vínculo para descargarlo. Para obtener información sobre el contenido del archivo, consulte [Archivo de configuración del dispositivo VPN](#config-file) en esta sección.

1. Aplique la configuración al dispositivo VPN local. Para obtener más información, consulte [Configuración del dispositivo VPN](#vpn-device) en esta sección.

1. Después de aplicar la configuración a los dispositivos VPN, no es necesario mantener la cuenta de almacenamiento que creó Azure. Puede eliminarla.

### <a name="about-the-vpn-device-configuration-file"></a><a name="config-file"></a>Archivo de configuración del dispositivo VPN

El archivo de configuración de dispositivo contiene la configuración que se debe usar al configurar el dispositivo VPN local. Cuando visualice este archivo, tenga en cuenta la siguiente información:

* **vpnSiteConfiguration**: en esta sección se indica la configuración de los detalles del dispositivo como un sitio de conexión a la red virtual WAN. Incluye el nombre y la dirección IP pública del dispositivo de rama.
* **vpnSiteConnections**: en esta sección se proporciona información sobre la siguiente configuración:

    * **Espacio de direcciones** de la red virtual de concentradores virtuales.<br>Ejemplo:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Espacio de direcciones** de las redes virtuales que están conectadas al concentrador.<br>Ejemplo:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **Direcciones IP** de vpngateway del concentrador virtual. Dado que cada conexión del elemento vpngateway consta de dos túneles con una configuración activo-activo, verá ambas direcciones IP en este archivo. En este ejemplo puede ver "Instance0" e "Instance1" para cada sitio.<br>Ejemplo:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Detalles de configuración de conexión de Vpngateway** como BGP, clave precompartida, etc. La PSK es la clave precompartida que se genera automáticamente para usted. Puede modificar la conexión cuando quiera en la página de información general para una PSK personalizada.
  
### <a name="example-device-configuration-file"></a>Archivo de configuración de dispositivo de ejemplo

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.3.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a><a name="vpn-device"></a>Configuración del dispositivo VPN

>[!NOTE]
> Si trabaja con una solución de asociado de WAN virtual, se produce automáticamente la configuración de los dispositivos de la VPN. El controlador de dispositivos obtiene el archivo de configuración de Azure y lo aplica al dispositivo para configurar la conexión con Azure. Esto significa que no es necesario saber cómo configurar manualmente el dispositivo VPN.
>

Si necesita instrucciones para configurar el dispositivo, puede utilizar las que se proporcionan en la [página de scripts de configuración de dispositivo VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) con las siguientes advertencias:

* Las instrucciones que aparecen en la página de dispositivos VPN no están escritas para Virtual WAN, pero puede usar los valores de Virtual WAN desde el archivo de configuración para configurar manualmente el dispositivo VPN.
 
* Los scripts descargables de configuración de dispositivo que son para VPN Gateway no funcionan para Virtual WAN, ya que la configuración es diferente.

* Las nuevas instancias de Virtual WAN admiten IKEv1 e IKEv2.

* Virtual WAN puede usar dispositivos VPN e instrucciones de dispositivo basados en rutas y basados en directivas.

## <a name="view-or-edit-gateway-settings"></a><a name="gateway-config"></a>Visualización o edición de la configuración de la puerta de enlace

Puede ver y editar la configuración de su puerta de enlace VPN en cualquier momento. Para ello, navegue hasta el **Centro de conectividad virtual -> VPN (Sitio a sitio)** y seleccione **Ver/Configurar**.

:::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-1.png" alt-text="Captura de pantalla que muestra la página &quot;VPN (sitio a sitio)&quot; con una flecha que apunta a la acción &quot;Ver/configurar&quot;." lightbox="media/virtual-wan-site-to-site-portal/view-configuration-1-expand.png":::

En la página **Editar VPN Gateway**, puede ver los valores siguientes:

* **Dirección IP pública**: la asigna Azure.
* **Dirección IP privada**: la asigna Azure.
* **Dirección IP de BGP predeterminada**: la asigna Azure.
* **Dirección IP de BGP personalizada**: este campo está reservado para APIPA (direcciones IP privadas automáticas). Azure admite IP de BGP en los intervalos 169.254.21.* y 169.254.22.*. Azure acepta conexiones BGP en estos intervalos, pero marcará la conexión con la IP de BGP predeterminada.

   :::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-2.png" alt-text="La captura de pantalla muestra la página Editar VPN Gateway con el botón Editar resaltado." lightbox="media/virtual-wan-site-to-site-portal/view-configuration-2-expand.png":::

## <a name="clean-up-resources"></a><a name="cleanup"></a>Limpieza de recursos

Cuando ya no necesite los recursos que ha creado, elimínelos. Algunos recursos de Virtual WAN deben eliminarse en un orden determinado debido a las dependencias. La eliminación puede tardar unos 30 minutos.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

Más información sobre Virtual WAN en:

> [!div class="nextstepaction"]
> * [Preguntas más frecuentes sobre Virtual WAN](virtual-wan-faq.md)
