---
title: Creación e instalación de archivos de configuración de cliente VPN de P2S para la autenticación con certificados
titleSuffix: Azure VPN Gateway
description: Aprenda cómo generar e instalar archivos de configuración de cliente VPN para Windows, Linux (strongSwan) y macOS. Este artículo se aplica a las configuraciones de punto a sitio de VPN Gateway que usan la autenticación de certificado.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/15/2021
ms.author: cherylmc
ms.openlocfilehash: efa2c4c120ab54e27126d1f40c433cdc1b66a85e
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114288930"
---
# <a name="generate-and-install-vpn-client-configuration-files-for-p2s-certificate-authentication"></a>Generación e instalación de archivos de configuración de cliente VPN para la autenticación con certificados de P2S

Cuando se conecta a una red virtual de Azure de punto a sitio con la autenticación de certificado, se usa el cliente VPN instalado de forma nativa en el sistema operativo desde el que se conecta. Todas las opciones de configuración necesarias para los clientes VPN se incluyen en un archivo ZIP de configuración del cliente VPN. Los valores incluidos en el archivo ZIP le ayudan a configurar los clientes VPN para Windows, Mac IKEv2 o Linux de forma fácil.

Los archivos de configuración del cliente VPN que usted genera son específicos de la configuración de puerta de enlace VPN de punto a sitio para la red virtual. Si se produce algún cambio en la configuración de VPN de punto a sitio después de generar los archivos (por ejemplo, cambios en el tipo de protocolo VPN o en el tipo de autenticación), debe generar archivos de configuración de cliente VPN nuevos y aplicar la nueva configuración a todos los clientes VPN que quiera conectar.

* Para más información sobre las conexiones de punto a sitio, consulte [Acerca de las conexiones VPN de punto a sitio](point-to-site-about.md).
* Para obtener instrucciones OpenVPN, consulte [Configuración de OpenVPN para la puerta de enlace de VPN de punto a sitio de Azure (versión preliminar)](vpn-gateway-howto-openvpn.md) y [Configuración de los clientes OpenVPN](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Generación de los archivos de configuración de cliente VPN

Para generar archivos de configuración de cliente, puede usar PowerShell o Azure Portal. Cualquiera de los métodos devuelve el mismo archivo ZIP. Descomprima el archivo para ver las siguientes carpetas:

* **WindowsAmd64** y **WindowsX86**, que contienen los paquetes del instalador de Windows de 32 y 64 bits, respectivamente. El paquete del instalador **WindowsAmd64** es para todos los clientes de Windows de 64 bits, no solo de AMD.
* La carpeta **Genérico** contiene información general que usará para crear su propia configuración de cliente VPN, La carpeta Genérico se proporciona si se ha configurado la opción IKEv2 o SSTP + IKEv2 en la puerta de enlace. Si solo se ha configurado SSTP, la carpeta Genérico no aparece.

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Generación de archivos mediante Azure Portal

1. En Azure Portal, navegue a la puerta de enlace de red virtual correspondiente a la red virtual a la que desea conectarse.
1. En la página de la puerta de enlace de red virtual, seleccione **Configuración de punto a sitio** para abrir la página de configuración correspondiente.
1. En la parte superior de la página Configuración de punto a sitio, haga clic en **Descargar cliente VPN**. Esta operación no descarga el software de cliente VPN, sino que genera el paquete de configuración que se usa para configurar los clientes VPN. La generación del paquete de configuración del cliente tarda unos minutos. Durante este tiempo, es posible que no vea ninguna indicación hasta que se haya generado el paquete.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/download-client.png" alt-text="Descargue la configuración de cliente de VPN" lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/download-client.png":::.
1. Una vez generado el paquete de configuración, el explorador indica que hay disponible un archivo ZIP de configuración de cliente. Tiene el mismo nombre que su puerta de enlace. Descomprima el archivo para ver las carpetas.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>Generación de archivos mediante PowerShell

1. Al generar archivos de configuración de cliente VPN, el valor de "-AuthenticationMethod" es "EapTIs". Genere los archivos de configuración de cliente VPN con el comando siguiente:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Copie la dirección URL en el explorador para descargar el archivo ZIP y, luego, descomprima el archivo para ver las carpetas.

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="mac-macos"></a><a name="installmac"></a>Mac (macOS)

Para conectarse a Azure, debe configurar manualmente el cliente VPN IKEv2 nativo. Azure no proporciona un archivo *mobileconfig*. Puede encontrar toda la información que necesita para la configuración en la carpeta **Genérico**. 

Si no ve la carpeta Genérico en la descarga, es posible que IKEv2 no se haya seleccionado como tipo de túnel. Tenga en cuenta que la SKU de nivel Básico de VPN Gateway no admite IKEv2. En la puerta de enlace de VPN, compruebe que la SKU no es Básica. Luego, seleccione IKEv2 y vuelva a generar el archivo ZIP para recuperar la carpeta Genérico.

que son los archivos siguientes:

* **VpnSettings.xml**, con configuración importante, como el tipo de túnel y la dirección del servidor. 
* **VpnServerRoot.cer**, con el certificado raíz necesario para validar la puerta de enlace de VPN de Azure durante la instalación de la conexión de punto a sitio.

Siga los pasos siguientes para configurar el cliente de VPN nativo en equipos Mac para realizar una autenticación mediante certificado. Estos pasos deben completarse en todos los equipos Mac que quiera conectar a Azure.

### <a name="import-root-certificate-file"></a>Importación de archivo de certificado raíz

1. Copie en el archivo de certificado raíz en su Mac. Haga doble clic en el certificado. El certificado se instalará automáticamente o verá la página **Agregar certificados**.
1. En la página **Agregar certificados**, seleccione **Inicio de sesión**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/login.png" alt-text="Captura de pantalla que muestra la página Agregar certificados con el inicio de sesión seleccionado.":::
1. Haga clic en **Agregar** para importar el archivo.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add.png" alt-text="Captura de pantalla que muestra la página Agregar certificados con Agregar seleccionado.":::

### <a name="verify-certificate-install"></a>Comprobación de la instalación del certificado

Compruebe que tanto el cliente como el certificado raíz están instalados. Se necesita y se usa un certificado de cliente para la autenticación. Para obtener información acerca de cómo instalar un certificado de cliente, consulte [Instalación de certificados de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

1. Abra la aplicación **Acceso a Llaveros**.
1. Vaya a la pestaña **Certificados**.
1. Compruebe que tanto el cliente como el certificado raíz están instalados.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/keychain.png" alt-text="Captura de pantalla que muestra Acceso a Llaveros con certificados instalados." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/keychain.png":::
  
### <a name="create-vpn-client-profile"></a>Creación de un perfil de cliente VPN

1. Vaya a **Preferencias del sistema -> Red**. En la página Red, seleccione **"+"** para crear un nuevo perfil de conexión de cliente VPN para una conexión de punto a sitio a la red virtual de Azure.
1. Para **Interfaz**, en el menú desplegable, seleccione **VPN**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/select-vpn.png" alt-text="Captura de pantalla que muestra la ventana Red con la opción de seleccionar una interfaz y VPN seleccionada." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/select-vpn.png":::

1. Para **Tipo de VPN**, en el menú desplegable, seleccione **IKEv2**. En **Nombre de servicio**, especifique un nombre descriptivo para el perfil.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-type.png" alt-text="Captura de pantalla que muestra la ventana Red con la opción para seleccionar una interfaz, seleccionar un tipo de VPN y escribir un nombre de servicio." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/vpn-type.png":::

1. Seleccione **Crear** para crear el perfil de conexión de cliente VPN.
1. En la carpeta **Genérico**, abra el archivo **VpnSettings.xml** mediante un editor de texto y copie el valor de la etiqueta **VpnServer**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/server-tag.png" alt-text="Captura de pantalla que muestra el archivo VpnSettings.xml abierto con la etiqueta VpnServer resaltada." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/server-tag.png":::

1. Pegue el valor de la etiqueta **VpnServer** en los campos **Dirección de servidor** e **Id. remoto** del perfil.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/paste-value.png" alt-text="Captura de pantalla que muestra la ventana Red con el valor pegado." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/paste-value.png":::

1. Configure los valores de autenticación. Hay dos conjuntos de instrucciones. Elija las instrucciones correspondientes a la versión del sistema operativo.

   **Catalina:** 

     * En **Configuración de la autenticación**, seleccione **Editar**. 
     * Seleccione **Certificado**, haga clic en **Seleccionar** y seleccione el certificado de cliente correcto que instaló anteriormente. A continuación, haga clic en **Aceptar**.
   
        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png" alt-text="La captura de pantalla muestra la ventana Red con la opción Ninguna seleccionada en Configuración de autenticación y la opción Certificado también seleccionada.":::

   **Big Sur:**

      * Haga clic en **Configuración de autenticación** y luego seleccione **Certificado**. 

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/authentication-certificate.png" alt-text="Captura de pantalla que muestra la configuración de autenticación con el certificado seleccionado." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/authentication-certificate.png":::

      * Haga clic en **Seleccionar** para abrir la página **Choose An Identity** (Elegir una identidad). En la página **Choose An Identity** (Elegir una identidad) se muestra una lista de certificados para elegir. Si no está seguro de qué certificado usar, puede hacer clic en **Mostrar certificado** para ver más información sobre cada certificado.

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/show-certificate.png" alt-text="Captura de pantalla que muestra las propiedades del certificado." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/show-certificate.png":::
      * Seleccione el certificado adecuado y luego **Continuar**.

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-identity.png" alt-text="Captura de pantalla que muestra Choose An Identity (Elegir una identidad), donde puede seleccionar un certificado." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/choose-identity.png":::
   
      * En la página **Configuración de autenticación**, compruebe que se muestra el certificado correcto y, a continuación, haga clic en **Aceptar**.

        :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png" alt-text="Captura de pantalla que muestra el cuadro de diálogo Choose An Identity (Elegir una identidad), donde puede seleccionar el certificado adecuado." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/certificate.png":::

1. En el campo **Id. local**, especifique el nombre del certificado tanto para Catalina como para Big Sur. En este ejemplo, es `P2SChildCert`.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/local-id.png" alt-text="Captura de pantalla que muestra el valor de id. local." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/local-id.png":::
1. Seleccione **Aplicar** para guardar los cambios. 
1. Seleccione **Conectar** para iniciar la conexión de punto a sitio con la red virtual de Azure.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/select-connect.png" alt-text="Captura de pantalla que muestra el botón Conectar." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/select-connect.png":::

1. Una vez establecida la conexión, el estado se muestra como **Conectado** y puede ver la dirección IP que se extrajo del grupo de direcciones del cliente VPN.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/connected.png" alt-text="Captura de pantalla que muestra Conectado." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/connected.png":::

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (GUI de StrongSwan)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>Instalación de strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>Generación de certificados

Si aún no ha generado certificados, siga estos pasos:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>Instalación y configuración

Las siguientes instrucciones se crearon en Ubuntu 18.0.4. Ubuntu 16.0.10 no admite la GUI de strongSwan. Si desea usar Ubuntu 16.0.10, deberá usar la [línea de comandos](#linuxinstallcli). Los ejemplos siguientes pueden no coincidir con las pantallas que ve, en función de la versión de Linux y strongSwan que tenga.

1. Abra la herramienta **Terminal** para instalar **strongSwan** y su Network Manager (Administrador de red) ejecutando el comando del ejemplo.

   ```
   sudo apt install network-manager-strongswan
   ```
1. Seleccione **Configuración** y, a continuación, seleccione **Red**. Seleccione el botón **+** para crear una conexión.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/edit-connections.png" alt-text="Captura de pantalla que muestra la página Conexiones de red." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/edit-connections.png":::

1. Seleccione **IPsec/IKEv2 (strongSwan)** en el menú y haga doble clic.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-connection.png" alt-text="Captura de pantalla que muestra la página para agregar VPN." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/add-connection.png":::

1. En la página **Add VPN** (Agregar VPN), agregue un nombre para la conexión VPN.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-type.png" alt-text="Captura de pantalla que muestra la opción de elegir un tipo de conexión." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/choose-type.png":::
1. Abra el archivo **VpnSettings.xml** de la carpeta **Generic** (Genérico) que está incluida en los archivos de configuración de cliente descargados. Busque la etiqueta denominada **VpnServer** y copie el nombre, que comienza con "azuregateway" y termina con ".cloudapp.net".

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-server.png" alt-text="Captura de pantalla que muestra la opción de copiar datos." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/vpn-server.png":::
1. Pegue este nombre en el campo **Address** (Dirección) de la nueva conexión VPN de la sección **Gateway** (Puerta de enlace). Luego, seleccione el icono de carpeta al final del campo **Certificate** (Certificado), vaya a la carpeta **Generic** (Genérico) y seleccione el archivo **VpnServerRoot**.
1. En la sección **Client** (Cliente) de la conexión, para **Authentication** (Autenticación), seleccione **Certificate/private key** (Certificado/clave privada). En **Certificate** (Certificado) y **Private key** (Clave privada), elija el certificado y la clave privada que se crearon anteriormente. En **Options** (Opciones), seleccione **Request an inner IP address** (Solicitar una dirección IP interna). Después, seleccione **Agregar**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/ip-request.png" alt-text="Captura de pantalla que muestra la opción de solicitar una dirección IP interna." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/ip-request.png":::

1. Ajuste la conexión en **Activado**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/turn-on.png" alt-text="Captura de pantalla que muestra la opción de copiar." lightbox="./media/point-to-site-vpn-client-configuration-azure-cert/expanded/turn-on.png":::

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (CLI de StrongSwan)

### <a name="install-strongswan"></a>Instalación de strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Generación de certificados

Si aún no ha generado certificados, siga estos pasos:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Instalar y configurar

1. Descargue el paquete de VPNClient desde Azure Portal.
1. Extrae el archivo.
1. Desde la carpeta **Generic**, copie o mueva **VpnServerRoot.cer** a **/etc/ipsec.d/cacerts**.
1. Copie o mueva **cp client.p12** a **/etc/ipsec.d/private/** . Este archivo es un certificado de cliente para la puerta de enlace de VPN.
1. Abra el archivo **VpnSettings.xml** y copie el valor `<VpnServer>`. Usará este valor en el paso siguiente.
1. Ajuste los valores en el ejemplo siguiente y después agregue el ejemplo a la configuración de **/etc/ipsec.conf**.
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
1. Agregue los siguientes valores a **/etc/ipsec.secrets**.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

1. Ejecute los comandos siguientes:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Pasos siguientes

Vuelva al artículo original desde el que estaba trabajando y, después, complete la configuración de punto a sitio.

* [Pasos de configuración de PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).
* [Pasos de configuración de Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
