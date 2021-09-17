---
title: Configuración de una conexión VPN de usuario P2S mediante autenticación de Azure Active Directory
titleSuffix: Azure Virtual WAN
description: Aprenda a configurar la autenticación de Azure Active Directory para una VPN de usuario de Virtual WAN (punto a sitio).
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 08/20/2021
ms.author: cherylmc
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 38006136ddd5a5331699b2768c35ebd794e563a8
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778589"
---
# <a name="configure-a-point-to-site-user-vpn-connection---azure-active-directory-authentication"></a>Configuración de una conexión VPN de usuario de punto a sitio: autenticación de Azure Active Directory

En este tutorial se muestra cómo configurar la autenticación de Azure AD para una VPN de usuario en Virtual WAN para conectarse a los recursos de Azure mediante una conexión de VPN OpenVPN. La autenticación de Azure Active Directory solo está disponible para puertas de enlace que usan el protocolo OpenVPN. Para más información sobre Virtual WAN, consulte la [Introducción a Virtual WAN](virtual-wan-about.md).

[!INCLUDE [OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

En este artículo aprenderá a:

* Creación de una instancia de Virtual WAN
* Crear una configuración de VPN de usuario
* Descargar un perfil de VPN de usuario de WAN virtual
* Crear un centro virtual
* Editar un centro para agregar una puerta de enlace P2S
* Conectar una red virtual a un centro virtual
* Descargar y aplicar la configuración de cliente VPN
* Visualizar la instancia de Virtual WAN

:::image type="content" source="./media/virtual-wan-about/virtualwanp2s.png" alt-text="Diagrama de Virtual WAN.":::

## <a name="before-you-begin"></a>Antes de empezar

Antes de comenzar con la configuración, compruebe que se cumplen los criterios siguientes:

* Tiene una red virtual a la que quiere conectarse. Compruebe que ninguna de las subredes de sus redes locales se superpone a las redes virtuales a las que quiere conectarse. Para crear una red virtual en Azure Portal consulte este [Inicio rápido](../virtual-network/quick-create-portal.md).

* Su red virtual no tiene ninguna puerta de enlace de red virtual. Si la red virtual tiene alguna puerta de enlace (ya sea VPN o ExpressRoute), tiene que quitarla. Esta configuración requiere que las redes virtuales estén conectadas a la puerta de enlace del centro de conectividad de Virtual WAN.

* Obtenga un intervalo de direcciones IP para la región del concentrador. El centro de conectividad es una red virtual que Virtual WAN crea y usa. El intervalo de direcciones que especifique para el centro de conectividad no se puede superponer a ninguna de las redes virtuales existentes a las que ya esté conectado. Igualmente no se puede superponer a los intervalos de direcciones con las que esté conectadas en el entorno local. Si no está familiarizado con los intervalos de direcciones IP ubicados en la configuración de la red local, póngase de acuerdo con alguien que pueda proporcionarle estos detalles.

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Creación de una instancia de Virtual WAN

Desde un explorador, navegue al [Portal de Azure](https://portal.azure.com) e inicie sesión con su cuenta de Azure.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-user-vpn-configuration"></a><a name="user-config"></a>Crear una configuración de VPN de usuario

Una configuración de VPN de usuario define los parámetros para conectarse a los clientes remotos. Es importante crear la configuración de VPN de usuario antes de definir el centro virtual con la configuración de P2S, ya que debe especificar la configuración de VPN de usuario que quiere usar.

1. Vaya a la página **Virtual WAN ->Configuraciones de VPN de usuario** y haga clic en **+Crear una configuración de VPN de usuario**.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/user-vpn.png" alt-text="Captura de pantalla de Crear una configuración de VPN de usuario.":::
1. Especifique los parámetros en la página **Aspectos básicos**.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/basics.png" alt-text="Captura de pantalla de la página Aspectos básicos.":::

   * **Nombre de la configuración**: escriba el nombre que desea asignar a la configuración de VPN de usuario.
    * **Tipo de túnel**: seleccione OpenVPN en el menú desplegable.
1. Haga clic en **Azure Active Directory** para abrir la página.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/values.png" alt-text="Captura de pantalla de la página Azure Active Directory.":::

    Cambie **Azure Active Directory** a **Sí** y proporcione los siguientes valores en función de los detalles del inquilino. Puede ver los valores necesarios en la página Azure Active Directory para aplicaciones empresariales del portal.
   * **Método de autenticación**: seleccione Azure Active Directory.
   * **Público**: escriba el identificador de aplicación de la [aplicación de Azure VPN](openvpn-azure-ad-tenant.md) Enterprise registrada en el inquilino de Azure AD. 
   * **Emisor** - `https://sts.windows.net/<your Directory ID>/`
   * **Inquilino de AAD** - `https://login.microsoftonline.com/<your Directory ID>`
1. Haga clic en **Crear** para crear la configuración de VPN de usuario. Esta configuración se selecciona más adelante en el ejercicio.

## <a name="create-an-empty-hub"></a><a name="site"></a>Creación de un centro vacío

En este ejercicio se crea un centro virtual vacío. En la sección siguiente se agrega una puerta de enlace a un centro ya existente. Pero también es posible combinar estos pasos y crear el centro con la configuración de la puerta de enlace P2S todo a la vez.

[!INCLUDE [Create an empty hub](../../includes/virtual-wan-empty-hub-include.md)]

## <a name="add-a-p2s-gateway-to-a-hub"></a><a name="hub"></a>Incorporación de una puerta de enlace P2S a un centro

En esta sección se muestra cómo agregar una puerta de enlace a un centro virtual ya existente. El centro puede tardar hasta 30 minutos en terminar de actualizarse.

1. Vaya a la página **Centros de conectividad** de la red WAN virtual.
1. Seleccione el centro al que quiere asociar la configuración del servidor VPN y haga clic en los puntos suspensivos ( **...** ) para mostrar el menú. Luego haga clic en **Editar centro de conectividad virtual**.

   :::image type="content" source="media/virtual-wan-point-to-site-azure-ad/select-hub.png" alt-text="Captura de pantalla que muestra la opción Editar centro de conectividad virtual seleccionada en el menú." lightbox="media/virtual-wan-point-to-site-azure-ad/select-hub.png":::

1. En la página **Editar centro de conectividad virtual**, active las casillas **Incluir puerta de enlace de VPN para los sitios VPN** e **Incluir puerta de enlace de punto a sitio** para mostrar la configuración. Luego configure los valores.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/edit-virtual-hub.png" alt-text="Captura de pantalla que muestra la página Editar centro de conectividad virtual.":::

   * **Unidades de escalado de puerta de enlace**: seleccione las unidades de escalado de puerta de enlace. Las unidades de escalado representan la capacidad agregada de la puerta de enlace de VPN de usuario. Si selecciona 40 o más unidades de escalado de puerta de enlace, planee el grupo de direcciones de cliente en consecuencia. Para obtener información sobre cómo afecta este valor al grupo de direcciones de cliente, vea [Acerca de los grupos de direcciones de cliente](about-client-address-pools.md). Para obtener información sobre las unidades de escalado de puerta de enlace, vea [Preguntas más frecuentes](virtual-wan-faq.md#for-user-vpn-point-to-site--how-many-clients-are-supported).
   * **Configuración de VPN de usuario**: seleccione la configuración que ha creado antes.
   * **Grupo de direcciones de cliente**: especifique el grupo de direcciones de cliente desde el que se van a asignar direcciones IP a los clientes VPN. Este valor corresponde a las unidades de escalado de puerta de enlace que 
1. Haga clic en **Confirmar**. La actualización del centro puede tardar hasta 30 minutos.

## <a name="connect-vnet-to-hub"></a><a name="connect-vnet"></a>Conexión de una red virtual a un centro de conectividad

En esta sección va a crear una conexión entre el centro virtual y la red virtual.

[!INCLUDE [Connect virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="download-user-vpn-profile"></a><a name="device"></a>Descarga de un perfil de VPN de usuario

Todas las opciones de configuración necesarias para los clientes VPN se incluyen en un archivo ZIP de configuración del cliente VPN. Los valores del archivo ZIP ayudan a configurar los clientes VPN. Los archivos de configuración del cliente VPN que genera son específicos de la configuración de VPN de usuario de la puerta de enlace. En esta sección se generan y se descargan los archivos que se usan para configurar los clientes VPN.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-user-vpn-clients"></a>Configuración de clientes VPN de usuario

Cada equipo que se conecta debe tener un cliente instalado. Puede configurar cada cliente mediante los archivos de perfil de cliente de usuario de VPN descargados en los pasos anteriores. Use el artículo correspondiente al sistema operativo al que quiera conectarse.

### <a name="to-configure-macos-vpn-clients-preview"></a>Para configurar clientes VPN de macOS (versión preliminar)

Para obtener instrucciones de cliente de **macOS**, vea [Configuración de un cliente VPN: macOS (versión preliminar)](openvpn-azure-ad-client-mac.md).

### <a name="to-configure-windows-vpn-clients"></a>Para configurar clientes VPN de Windows

[!INCLUDE [Download Azure VPN client](../../includes/vpn-gateway-download-vpn-client.md)]

#### <a name="to-import-a-vpn-client-profile-windows"></a><a name="import"></a>Para importar un perfil de cliente VPN (Windows)

1. En la página, seleccione **Importar**.

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/import/import-1.png" alt-text="Captura de pantalla que muestra la página Importar.":::

1. Busque el archivo xml de perfil y selecciónelo. Con el archivo seleccionado, seleccione **Abrir**.

    ![Captura de pantalla que muestra un cuadro de diálogo Abrir en el que puede seleccionar un archivo.](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

1. Especifique el nombre del perfil y seleccione **Guardar**.

    ![Captura de pantalla que muestra la opción Nombre de conexión agregada y el botón Guardar seleccionado.](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

1. Seleccione **Conectar** para conectarse a la VPN.

    ![Captura de pantalla que muestra el botón Conectar para la conexión que acaba de crear.](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

1. Una vez conectado, el icono se volverá verde y dirá **Conectado**.

    ![Captura de pantalla que muestra la conexión con el estado Conectado y la opción para desconectarla.](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile---windows"></a><a name="delete"></a>Para eliminar un perfil de cliente: Windows

1. Seleccione los puntos suspensivos junto al perfil de cliente que quiera eliminar. Después, seleccione **Quitar**.

    ![Captura de pantalla que muestra la opción Quitar seleccionada en el menú.](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

1. Seleccione **Quitar** para eliminar.

    ![Captura de pantalla que muestra un cuadro de diálogo de confirmación con la opción Quitar o Cancelar.](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues---windows"></a><a name="diagnose"></a>Diagnóstico de problemas de conexión: Windows

1. Para diagnosticar problemas de conexión, puede usar la herramienta **Diagnosticar**. Seleccione los puntos suspensivos (...) junto a la conexión de VPN que desea diagnosticar para que se muestre el menú. Después, seleccione **Diagnosticar**.

    ![Captura de pantalla que muestra la opción de diagnóstico de la selección en el menú.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

1. En la página **Propiedades de conexión**, seleccione **Ejecutar diagnóstico**.

    ![Captura de pantalla que muestra el botón Ejecutar diagnóstico para una conexión.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

1. Inicie sesión con sus credenciales.

    ![Captura de pantalla que muestra el cuadro de diálogo Iniciar sesión para esta acción.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

1. Ver los resultados del diagnóstico.

    ![Captura de pantalla que muestra los resultados del diagnóstico.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Visualización de la instancia de Virtual WAN

1. Vaya a la instancia de Virtual WAN.
2. En la página Información general, cada punto del mapa representa un concentrador.
3. En la sección de concentradores y conexiones, puede ver estado del concentrador, sitio, región, estado de la conexión VPN y bytes de entrada y salida.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Limpieza de recursos

Cuando ya no necesite los recursos que ha creado, elimínelos. Algunos recursos de Virtual WAN deben eliminarse en un orden determinado debido a las dependencias. La eliminación puede tardar unos 30 minutos.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Virtual WAN, consulte la página [Introducción a Virtual WAN](virtual-wan-about.md).
