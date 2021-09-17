---
title: 'Tutorial: Uso de Azure Virtual WAN para crear una conexión de punto a sitio a Azure'
description: En este tutorial, aprenderá a usar Azure Virtual WAN para crear una conexión VPN de usuario (de punto a sitio) a Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 08/23/2021
ms.author: cherylmc
ms.openlocfilehash: e08123ce666efebf7db2e4c07167125a76b46609
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779918"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Tutorial: Creación de una conexión VPN de usuario mediante Azure Virtual WAN

Este tutorial muestra cómo usar Virtual WAN para conectarse a los recursos de Azure mediante una conexión de OpenVPN o de IPsec/IKE (IKEv2) con una configuración VPN de usuario (P2S). Este tipo de conexión requiere que se configure el cliente VPN nativo en cada equipo cliente que se conecte.
* Si desea crear una conexión VPN de usuario mediante autenticación de Azure AD, use en su lugar el artículo [Configuración de la autenticación de Azure Active Directory para una VPN de usuario](virtual-wan-point-to-site-azure-ad.md).
* Para más información sobre Virtual WAN, consulte la [Introducción a Virtual WAN](virtual-wan-about.md).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una instancia de Virtual WAN
> * Creación de la configuración de VPN de usuario
> * Creación del centro virtual y la puerta de enlace
> * Generación de archivos de configuración del cliente
> * Configuración de clientes VPN
> * Conexión a una red virtual
> * Visualizar la instancia de Virtual WAN
> * Modificar la configuración

:::image type="content" source="./media/virtual-wan-about/virtualwanp2s.png" alt-text="Diagrama de Virtual WAN.":::

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Creación de una instancia de Virtual WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-user-vpn-configuration"></a><a name="p2sconfig"></a>Crear una configuración de VPN de usuario

La configuración de VPN de usuario (P2S) define los parámetros para que los clientes remotos se puedan conectar. Las instrucciones que siga dependerán del método de autenticación que desee usar.

En los pasos siguientes, al seleccionar el método de autenticación, tiene tres opciones. Cada método tiene requisitos específicos. Seleccione uno de los métodos siguientes y, a continuación, complete los pasos.

* **Certificados de Azure:** para esta configuración, se requieren certificados. Debe generar u obtener certificados. Se requiere un certificado de cliente para cada cliente. Además, es necesario cargar la información sobre el certificado raíz (clave pública). Para obtener más información sobre los certificados necesarios, consulte [Generación y exportación de certificados](certificates-point-to-site.md).

* **Autenticación de Azure Active Directory:** utilice el artículo [Configuración de la autenticación de Azure Active Directory para una VPN de usuario](virtual-wan-point-to-site-azure-ad.md) que contiene los pasos específicos necesarios para esta configuración.

* **Autenticación basada en Radius:** obtenga la dirección IP Radius, el secreto del servidor Radius y la información sobre el certificado.

### <a name="configuration-steps"></a>Pasos de configuración

[!INCLUDE [Create P2S configuration](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>Creación de un centro de conectividad virtual y una puerta de enlace

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-client-configuration-files"></a><a name="download"></a>Generación de archivos de configuración del cliente

Cuando se conecta a una red virtual mediante VPN de usuario (P2S), se utiliza el cliente VPN instalado de forma nativa en el sistema operativo desde el que se conecta. Todas las opciones de configuración necesarias para los clientes VPN se incluyen en un archivo ZIP de configuración del cliente VPN. Los valores del archivo ZIP ayudan a configurar con facilidad los clientes VPN. Los archivos de configuración del cliente VPN que genera son específicos de la configuración de VPN de usuario de la puerta de enlace. En esta sección, generará y descargará los archivos que se utilizan para configurar los clientes VPN.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="configure-client"></a>Configuración de clientes VPN

Use el paquete de perfiles descargado para configurar los clientes VPN de acceso remoto. El procedimiento para cada sistema operativo es diferente. Siga las instrucciones que se aplican al sistema.
Una vez que haya terminado de configurar el cliente, puede conectarse.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-vnet-to-hub"></a><a name="connect-vnet"></a>Conexión de una red virtual a un centro de conectividad

En esta sección, creará una conexión entre el centro virtual y la red virtual. Para este tutorial, no es necesario configurar los valores de enrutamiento.

[!INCLUDE [Connect virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="view-a-virtual-wan"></a><a name="viewwan"></a>Vista de una red WAN virtual

1. Vaya a la **red WAN virtual**.

1. En la página **Información general**, cada punto del mapa representa un centro de conectividad.

1. En la sección de **centros de conectividad y conexiones**, puede ver estado del centro de conectividad, sitio, región, estado de la conexión VPN y bytes de entrada y salida.

## <a name="modify-settings"></a>Modificar la configuración

### <a name="modify-client-address-pool"></a><a name="address-pool"></a>Modificación del grupo de direcciones de clientes

[!INCLUDE [Modify client address pool](../../includes/virtual-wan-client-address-pool-include.md)]

### <a name="modify-dns-servers"></a><a name="dns"></a>Modificación de los servidores DNS

1. Vaya a **Centro virtual -> VPN de usuario (punto a sitio)** .

1. Haga clic en el valor situado junto a **Servidores DNS personalizados** para que se abra la página **Editar la instancia de VPN Gateway de usuario**.

1. En la página **Editar la instancia de VPN Gateway de usuario**, edite el campo **Servidores DNS personalizados**. Escriba las direcciones IP del servidor DNS en los cuadros de texto **Servidores DNS personalizados**. Puede especificar hasta cinco servidores DNS.

1. En la parte inferior de la página, haga clic en **Editar** para validar la configuración.

1. Haga clic en **Confirmar** para guardar la configuración. Los cambios en esta página pueden tardar hasta 30 minutos en completarse.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Limpieza de recursos

Cuando ya no necesite los recursos que ha creado, elimínelos. Algunos recursos de Virtual WAN deben eliminarse en un orden determinado debido a las dependencias. La eliminación puede tardar unos 30 minutos.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes


> [!div class="nextstepaction"]
> * [Administración de acceso seguro a recursos en redes virtuales de radio](manage-secure-access-resources-spoke-p2s.md)
