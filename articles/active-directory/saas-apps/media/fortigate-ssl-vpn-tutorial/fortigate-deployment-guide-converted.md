---
title: Guía de implementación de FortiGate
description: Configure y trabaje con el producto de firewall de nueva generación FortiGate de Fortinet.
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: jeedes
ms.openlocfilehash: b1b3a2dca9e11e12a72ec99685ff71beb279473e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353930"
---
# <a name="fortigate-azure-virtual-machine-deployment-guide"></a>Guía de implementación de máquinas virtuales de Azure para FortiGate

Con esta guía de implementación, obtendrá información sobre cómo configurar y trabajar con el producto de firewall de nueva generación FortiGate de Fortinet implementado como una máquina virtual de Azure. Además, configurará la aplicación de la Galería de Azure AD FortiGate SSL VPN para proporcionar autenticación de VPN mediante Azure Active Directory.

## <a name="redeem-the-fortigate-license"></a>Canje de la licencia de FortiGate

El producto de firewall de nueva generación FortiGate de Fortinet está disponible como una máquina virtual en infraestructura como servicio (IaaS) de Azure. Existen dos modelos de licencia para esta máquina virtual: pago por uso y traiga su propia licencia (BYOL).

Si ha adquirido una licencia de FortiGate de Fortinet para usarla con la opción de implementación de máquina virtual BYOL, puede canjearla desde la página de activación del producto de Fortinet: https://support.fortinet.com. El archivo de licencia resultante tendrá una extensión de archivo .lic.

## <a name="download-firmware"></a>Descarga del firmware

En el momento de escribir este artículo, la máquina virtual de Azure de Fortinet FortiGate no se incluye con la versión de firmware necesaria para la autenticación de SAML. La versión más reciente debe obtenerse de Fortinet.

1. Inicie sesión en https://support.fortinet.com/.
2. Vaya a **Download** (Descargar) > **Firmware Images** (Imágenes de firmware).
3. A la derecha de **Release Notes** (Notas de la versión), seleccione **Download** (Descargar).
4. Seleccione **v6.00** > **6.4** > **6.4.2**.
5. Para descargar **FGT_VM64_AZURE-v6-build1723-FORTINET.out**, seleccione el vínculo **HTTPS** en la misma fila.
6. Guarde el archivo para más adelante.

## <a name="deploy-the-fortigate-vm"></a>Implementación de la máquina virtual de FortiGate

1. Vaya a Azure Portal e inicie sesión en la suscripción en la que desea implementar la máquina virtual de FortiGate.
2. Cree un nuevo grupo de recursos o abra el grupo de recursos en el que desea implementar la máquina virtual de FortiGate.
3. Seleccione **Agregar**.
4. En **Buscar en Marketplace**, escriba *Forti*. Seleccione **Fortinet FortiGate Next-Generation Firewall** (Firewall de nueva generación FortiGate de Fortinet).
5. Seleccione el plan de software (traiga su propia licencia si tiene una licencia o pago por uso de lo contrario). Seleccione **Crear**.
6. Rellene la configuración de la VM.

    ![Captura de pantalla de creación de una máquina virtual.](virtual-machine.png)

7. Establezca **Tipo de autenticación** en **Contraseña** y proporcione credenciales administrativas para la VM.
8. Seleccione **Revisar + crear** > **Crear**.
9. Espere a que la implementación de la VM se complete.


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Establecimiento de una dirección IP pública estática y asignación de un nombre de dominio completo

Para una experiencia de usuario coherente, establezca la dirección IP pública asignada a la VM de FortiGate para que se asigne de forma estática. Además, asígnela a un nombre de dominio completo (FQDN).

1. Vaya a Azure Portal y abra la configuración de la máquina virtual de FortiGate.
2. En la pantalla **Información general**, seleccione la dirección IP pública.

    ![Captura de pantalla de FortiGate SSL VPN.](public-ip-address.png)

3. Seleccione **Estática** > **Guardar**.

Si posee un nombre de dominio enrutable públicamente para el entorno en el que se va a implementar la VM de FortiGate, cree un registro de host (A) para la VM. Este registro se asigna a la dirección IP pública anterior que está asignada estáticamente.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port-8443"></a>Creación de una regla del grupo de seguridad de red de entrada para el puerto TCP 8443

1. Vaya a Azure Portal y abra la configuración de la máquina virtual de FortiGate.
2. En el menú de la izquierda, seleccione **Redes**. Se muestran la interfaz de red y las reglas del puerto de entrada.
3. Seleccione **Agregar regla de puerto de entrada**.
4. Cree una nueva regla de puerto de entrada para TCP 8443.

    ![Captura de pantalla de Agregar regla de seguridad de entrada.](port-rule.png)

5. Seleccione **Agregar**.

## <a name="create-a-second-virtual-nic-for-the-vm"></a>Creación de una segunda NIC virtual para la máquina virtual

Para que los recursos internos estén disponibles para los usuarios, se debe agregar una segunda NIC virtual a la máquina virtual de FortiGate. La red virtual de Azure en la que reside la NIC virtual debe tener una conexión enrutable a esos recursos internos.

1. Vaya a Azure Portal y abra la configuración de la máquina virtual de FortiGate.
2. Si aún no se ha detenido la máquina virtual de FortiGate, seleccione **Detener** y espere a que se cierre la máquina virtual.
3. En el menú de la izquierda, seleccione **Redes**.
4. Seleccione **Asociar interfaz de red**.
5. Seleccione **Crear y asociar una interfaz de red**.
6. Configure las propiedades de la nueva interfaz de red y, a continuación, seleccione **Crear**.

    ![Captura de pantalla de la creación de una interfaz de red.](new-network-interface.png)

7. Inicie la máquina virtual de FortiGate.


## <a name="configure-the-fortigate-vm"></a>Configuración de la máquina virtual de FortiGate

Las secciones siguientes le guiarán a través de la configuración de la VM de FortiGate.

### <a name="install-the-license"></a>Instalación de la licencia

1. Vaya a `https://<address>`. Aquí, `<address>` es el FQDN o la dirección IP pública asignada a la VM de FortiGate.

2. Continúe después de los errores de certificado.
3. Inicie sesión con las credenciales de administrador proporcionadas durante la implementación de la VM de FortiGate.
4. Si la implementación usa el modelo traiga su propia licencia, verá un aviso para cargar una licencia. Seleccione el archivo de licencia creado anteriormente y cárguelo. Seleccione **Aceptar** y reinicie la VM de FortiGate.

    ![Captura de pantalla de la licencia de VM de FortiGate.](license.png)

5. Después del reinicio, inicie sesión de nuevo con las credenciales de administrador para validar la licencia.

### <a name="update-firmware"></a>Actualización del firmware

1. Vaya a `https://<address>`. Aquí, `<address>` es el FQDN o la dirección IP pública asignada a la VM de FortiGate.

2. Continúe después de los errores de certificado.
3. Inicie sesión con las credenciales de administrador proporcionadas durante la implementación de la VM de FortiGate.
4. En el menú de la izquierda, seleccione **System** (Sistema) > **Firmware**.
5. En **Firmware Management** (Administración de firmware), haga clic en **Browse** (Examinar) y seleccione el archivo de firmware descargado anteriormente.
6. Ignore la advertencia y haga clic en **Backup config and upgrade** (Realizar copia de seguridad de la configuración y actualizar).

    ![Captura de pantalla de administración de firmware.](backup-configure-upgrade.png)

7. Seleccione **Continuar**.
8. Cuando se le pida que guarde la configuración de FortiGate (como un archivo .conf), haga clic en **Save** (Guardar).
9. Espere a que se cargue y se aplique el firmware. Espere a que se reinicie la VM de FortiGate.
10. Una vez reiniciada, inicie sesión de nuevo con las credenciales de administrador.
11. Cuando se le pida que configure el panel, seleccione **Later** (Más adelante).
12. Cuando comience el vídeo del tutorial, seleccione **OK** (Aceptar).

### <a name="change-the-management-port-to-tcp-8443"></a>Cambio del puerto de administración a TCP 8443

1. Vaya a `https://<address>`. Aquí, `<address>` es el FQDN o la dirección IP pública asignada a la VM de FortiGate.

2. Continúe después de los errores de certificado.
3. Inicie sesión con las credenciales de administrador proporcionadas durante la implementación de la VM de FortiGate.
4. En el menú de la izquierda, seleccione **System** (Sistema).
5. En **Administration Settings** (Configuración de administración), cambie el puerto HTTPS a **8443** y seleccione **Apply** (Aplicar).
6. Una vez que se aplica el cambio, el explorador intenta volver a cargar la página de administración, pero se produce un error. A partir de ahora, la dirección de esta página es `https://<address>:8443`.

    ![Captura de pantalla de carga del certificado remoto.](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Carga del certificado de firma de SAML de Azure AD

1. Vaya a `https://<address>:8443`. Aquí, `<address>` es el FQDN o la dirección IP pública asignada a la VM de FortiGate.

2. Continúe después de los errores de certificado.
3. Inicie sesión con las credenciales de administrador proporcionadas durante la implementación de la VM de FortiGate.
4. En el menú de la izquierda, seleccione **System** (Sistema) > **Certificates** (Certificados).
5. Seleccione **Importar** > **Certificado remoto**.
6. Busque el certificado descargado de la implementación de la aplicación personalizada FortiGate en el inquilino de Azure. Selecciónelo y elija **OK** (Aceptar).

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Carga y configuración de un certificado SSL personalizado

Es posible que quiera configurar la VM de FortiGate con su propio certificado SSL que admita el FQDN que está usando. Si tiene acceso a un certificado SSL empaquetado con la clave privada en formato PFX, se puede usar para este propósito.

1. Vaya a `https://<address>:8443`. Aquí, `<address>` es el FQDN o la dirección IP pública asignada a la VM de FortiGate.

2. Continúe después de los errores de certificado.
3. Inicie sesión con las credenciales de administrador proporcionadas durante la implementación de la VM de FortiGate.
4. En el menú de la izquierda, seleccione **System** (Sistema) > **Certificates** (Certificados).
5. Seleccione **Import** (Importar) > **Local Certificate** (Certificado local) > **PKCS #12 Certificate** (Certificado PKCS núm. 12).
6. Vaya al archivo .PFX que contiene el certificado SSL y la clave privada.
7. Proporcione la contraseña del archivo .PFX y un nombre descriptivo para el certificado. Después, seleccione **Aceptar**.
8. En el menú de la izquierda, seleccione **System** (Sistema) > **Settings** (Configuración).
9. En **Administration Settings** (Configuración de administración), expanda la lista desplegable situada junto al **certificado de servidor HTTPS** y seleccione el certificado SSL importado anteriormente.
10. Seleccione **Aplicar**.
11. Cierre la ventana del explorador y vaya a `https://<address>:8443`.
12. Inicie sesión con las credenciales de administrador de FortiGate. Ahora debería ver el certificado SSL correcto en uso.

### <a name="configure-authentication-timeout"></a>Configuración del tiempo de espera de la autenticación

1. Vaya a Azure Portal y abra la configuración de la máquina virtual de FortiGate.
2. En el menú de la izquierda, seleccione **Serial Console** (Consola serie).
3. Inicie de sesión en la consola serie con las credenciales de administrador de la VM de FortiGate.
4. En la consola serie ejecute los siguientes comandos:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```

### <a name="ensure-network-interfaces-are-obtaining-ip-addresses"></a>Asegurarse de que las interfaces de red obtienen direcciones IP

1. Vaya a `https://<address>:8443`. Aquí, `<address>` es el FQDN o la dirección IP pública asignada a la VM de FortiGate.

2. Inicie sesión con las credenciales de administrador proporcionadas durante la implementación de la VM de FortiGate.
3. En el menú de la izquierda, seleccione **Redes**.
4. En Red, seleccione **Interfaces**.
5. Examine el puerto 1 (interfaz externa) y el puerto 2 (interfaz interna) para asegurarse de que obtienen una dirección IP de la subred de Azure correcta.
    a. Si alguno de los puertos no obtiene una dirección IP de la subred (mediante DHCP), haga clic con el botón derecho en el puerto y seleccione **Editar**.
    b. Junto a Modo de direccionamiento, asegúrese de que está seleccionado **DHCP**.
    c. Seleccione **Aceptar**.

    ![Captura de pantalla del direccionamiento de la interfaz de red.](addressing.png)

### <a name="ensure-fortigate-vm-has-correct-route-to-on-premises-corporate-resources"></a>Asegurarse de que la máquina virtual de FortiGate tiene la ruta correcta a los recursos corporativos locales

Las máquinas virtuales de Azure de host múltiple tienen todas las interfaces de red en la misma red virtual (pero quizás distintas subredes). Esto suele significar que ambas interfaces de red tienen una conexión a los recursos corporativos locales que se publican mediante FortiGate. Por esta razón, es necesario crear entradas de rutas personalizadas que garanticen que el tráfico sale de la interfaz correcta cuando se realizan solicitudes de recursos corporativos locales.

1. Vaya a `https://<address>:8443`. Aquí, `<address>` es el FQDN o la dirección IP pública asignada a la VM de FortiGate.

2. Inicie sesión con las credenciales de administrador proporcionadas durante la implementación de la máquina virtual de FortiGate.
3. En el menú de la izquierda, seleccione **Redes**.
4. En Red, seleccione **Rutas estáticas**.
5. Seleccione **Crear nuevo**.
6. Junto a Destino, seleccione **Subred**.
7. En Subred, especifique la información de subred en la que residen los recursos corporativos locales (por ejemplo, 10.1.0.0/255.255.255.0).
8. Junto a Dirección de puerta de enlace, especifique la puerta de enlace en la subred de Azure a la que está conectado el puerto 2 (por ejemplo, esto suele finalizar en 1, como 10.6.1.1).
9. Junto a Interfaz, seleccione la interfaz de red interna, el puerto 2.
10. Seleccione **Aceptar**.

    ![Captura de pantalla de la configuración de una ruta.](route.png)

## <a name="configure-fortigate-ssl-vpn"></a>Configuración de FortiGate SSL VPN

Siga los pasos que encontrará en [Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con FortiGate SSL VPN](../../fortigate-ssl-vpn-tutorial.md)
