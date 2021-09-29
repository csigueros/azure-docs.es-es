---
title: Administración de la consola de administración local
description: Obtenga información sobre las opciones de la consola de administración local, como la copia de seguridad y la restauración, la definición del nombre de host y la configuración de un proxy en sensores.
ms.date: 1/12/2021
ms.topic: article
ms.openlocfilehash: 9b956dbfa640862d10d0e47aef97e5a1727052c7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625228"
---
# <a name="manage-the-on-premises-management-console"></a>Administración de la consola de administración local

En este artículo se explican las opciones de la consola de administración local, como la copia de seguridad y la restauración, la descarga del archivo de activación de dispositivos confirmados, la actualización de certificados y la configuración de un proxy en sensores.

Se incorpora la consola de administración local de Azure Portal.

## <a name="upload-an-activation-file"></a>Carga de un archivo de activación

La primera vez que inicia sesión, se descarga un archivo de activación de la consola de administración local. Este archivo contiene los dispositivos agregados confirmados que se definen durante el proceso de incorporación. La lista incluye sensores asociados a varias suscripciones.

Después de la activación inicial, el número de dispositivos supervisados puede superar el número de dispositivos confirmados definidos durante la incorporación. Este evento puede ocurrir, por ejemplo, si conecta más sensores a la consola de administración. Si hay una discrepancia entre el número de dispositivos supervisados y el número de dispositivos confirmados, aparece una advertencia en la consola de administración. Si se produce este evento, debe cargar un nuevo archivo de activación.

**Para cargar un archivo de activación:**

1. Vaya a la página **Precios** de Azure Defender para IoT.
1. Seleccione la pestaña **Download the activation file for the management console** (Descargar el archivo de activación de la consola de administración). Se descarga el archivo de activación.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Descargue el archivo de activación.":::

1. Seleccione **Configuración del sistema** en la consola de administración.
1. Seleccione **Activación**.
1. Seleccione **Elegir un archivo** y luego el archivo guardado.

## <a name="manage-certificates"></a>Administración de certificados

Tras la instalación de la consola de administración local, se genera un certificado autofirmado local que se usa para acceder a la aplicación web. Al iniciar sesión en la consola de administración local por primera vez, se les solicita a los usuarios administradores que proporcionen un certificado SSL/TLS. 

Es posible que los administradores deban actualizar los certificados que se cargaron después del registro inicial. Esto puede ocurrir, por ejemplo, si un certificado ha expirado.

**Para actualizar un certificado:**

1. Seleccione **Configuración del sistema**.

1. Seleccione **SSL/TLS Certificates** (Certificados SSL/TLS).

    :::image type="content" source="media/how-to-manage-individual-sensors/certificate-upload.png" alt-text="Carga de un certificado":::

1. En el cuadro de diálogo SSL/TLS Certificates (Certificados SSL/TLS), elimine el certificado existente y agregue uno nuevo.

    - Agregue un nombre de certificado.
    - Cargue un archivo CRT y el archivo de clave.
    - Cargue un archivo PEM si es necesario.

Si se produce un error en la carga, póngase en contacto con su administrador de seguridad o de TI, o revise la información que se encuentra en [Acerca de los certificados](how-to-deploy-certificates.md).

**Para cambiar la configuración de validación del certificado:**

1. Active o desactive la opción **Enable Certificate Validation** (Habilitar validación de certificados). Si la opción está habilitada y se produce un error en la validación, la comunicación entre los componentes pertinentes se detiene y aparece un error de validación en la consola. Si la opción está deshabilitada, no se lleva a cabo la validación del certificado. Consulte [About certificate validation](how-to-deploy-certificates.md#about-certificate-validation) (Acerca de la validación de certificados) para obtener más información.

1. Seleccione **Guardar**.

Para más información sobre la carga de certificados por primera vez, consulte [Lista de comprobación del primer inicio de sesión y activación](how-to-activate-and-set-up-your-sensor.md#first-time-sign-in-and-activation-checklist)

## <a name="define-backup-and-restore-settings"></a>Definición de la configuración de copia seguridad y restauración

La copia de seguridad del sistema de la consola de administración local se realiza de forma automática y diaria. Los datos se guardan en un disco diferente. La ubicación predeterminada es `/var/cyberx/backups`. 

Puede transferir automáticamente este archivo a la red interna. 

> [!NOTE]
> El procedimiento de copia de seguridad y restauración se puede realizar solo en la misma versión. 

Para realizar una copia de seguridad de la máquina de la consola de administración local: 

- Inicie sesión en una cuenta administrativa y escriba `sudo cyberx-management-backup -full`.

Para restaurar el archivo de copia de seguridad más reciente:

- Inicie sesión en una cuenta administrativa y escriba `$ sudo cyberx-management-system-restore`.

Para guardar la copia de seguridad en un servidor SMB externo:

1. Cree una carpeta compartida en el servidor SMB externo.  

   Obtenga la ruta de acceso de la carpeta, el nombre de usuario y la contraseña necesarios para acceder al servidor SMB. 

2. En Defender para IoT, cree un directorio para las copias de seguridad:

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. Edite fstab:  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. Edite o cree las credenciales para el servidor SMB que se va a compartir: 

   - `sudo nano /etc/samba/user` 

5. Agregue: 

   - `username=<user name>`

   - `password=<password>` 

6. Monte el directorio: 

   - `sudo mount -a` 

7. Configure un directorio de copia de seguridad en la carpeta compartida en la consola de administración local de Defender para IoT:  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>Edición del nombre de host

Para editar el nombre de host de la consola de administración configurado en el servidor DNS de la organización:

1. En el panel izquierdo de la consola de administración, seleccione **Configuración del sistema**.  

2. En la sección de redes de la consola, seleccione **Red**. 

3. Escriba el nombre de host configurado en el servidor DNS de la organización. 

4. Seleccione **Guardar**.

## <a name="define-vlan-names"></a>Definición de nombres de VLAN

Los nombres de VLAN no se sincronizan entre el sensor y la consola de administración. Defina nombres idénticos en los componentes.

En el área de redes, seleccione **VLAN** y agregue nombres a los id. de VLAN detectados. Después, seleccione **Guardar**.

## <a name="define-a-proxy-to-sensors"></a>Definición de un proxy para los sensores

Mejore la seguridad del sistema impidiendo el inicio de sesión del usuario directamente en el sensor. En su lugar, use la tunelización de proxy para permitir que los usuarios accedan al sensor desde la consola de administración local con una sola regla de firewall. Esta mejora reduce la posibilidad del acceso no autorizado al entorno de red más allá del sensor.

Use un proxy en entornos en los que no haya conectividad directa a los sensores.

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="Usuario.":::

El siguiente procedimiento conecta un sensor a la consola de administración local y habilita la tunelización en esa consola:

1. Inicie sesión en la CLI del dispositivo de la consola de administración local con credenciales administrativas.

2. Escriba `sudo cyberx-management-tunnel-enable` y seleccione **Entrar**.

4. Escriba `--port 10000` y seleccione **Entrar**.

## <a name="adjust-system-properties"></a>Ajuste de las propiedades del sistema

Las propiedades del sistema controlan diversas operaciones y configuraciones en la consola de administración. Editarlos o modificarlos podría dañar la operación de la consola de administración. Consulte al [Soporte técnico de Microsoft](https://support.microsoft.com) antes de cambiar la configuración.

Para acceder a las propiedades del sistema: 

1. Inicie sesión en la consola de administración local o el sensor.

2. Seleccione **Configuración del sistema**.

3. Seleccione **Propiedades del sistema** en la sección **General**.

## <a name="change-the-name-of-the-on-premises-management-console"></a>Cambio del nombre de la consola de administración local

Puede cambiar el nombre de la consola de administración local. Los nombres nuevos aparecen en el explorador web de la consola, en varias ventanas de la consola y en los registros de solución de problemas. El nombre predeterminado es **consola de administración**.

Para cambiar el nombre:

1. En la parte inferior del panel izquierdo, seleccione el nombre actual.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="Captura de pantalla de la versión de la consola de administración local.":::

2. En el cuadro de diálogo **Edit management console configuration** (Editar la configuración de la consola de administración), escriba el nuevo nombre. El nombre no puede tener más de 25 caracteres.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="Captura de pantalla de la edición de la configuración de la plataforma Defender para IoT.":::

3. Seleccione **Guardar**. Se aplica el nuevo nombre.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="Captura de pantalla que muestra el nombre cambiado de la consola.":::

## <a name="password-recovery"></a>Recuperación de la contraseña

La recuperación de la contraseña de la consola de administración local está ligada a la suscripción a la que está asociado el dispositivo. No se puede recuperar una contraseña si no se sabe a qué suscripción está asociado un dispositivo.

Para restablecer la contraseña:

1. Vaya a la página de inicio de sesión de la consola de administración local.
1. Seleccione **Recuperación de contraseña**.
1. Copie el identificador único.
1. Vaya a la página **Sites and sensors** (Sitios y sensores) de Defender para IoT y seleccione la pestaña **Recover my password** (Recuperar mi contraseña).
1. Escriba el identificador único y seleccione **Recuperar**. Se descarga el archivo de activación.
1. Vaya a la página **Recuperación de contraseña** y cargue el archivo de activación.
1. Seleccione **Next** (Siguiente).
 
   Ahora tiene el nombre de usuario y una nueva contraseña generada por el sistema.

> [!NOTE]
> El sensor está vinculado a la suscripción a la que se asoció originalmente. Solo puede recuperar la contraseña con la misma suscripción a la que está asociada.

## <a name="update-the-software-version"></a>Actualización de la versión de software

En el procedimiento siguiente se describe cómo actualizar la versión de software de la consola de administración local. El proceso de actualización tarda aproximadamente 30 minutos.

Si está trabajando con una consola de administración local y sensores administrados, **actualice primero la consola de administración**.

1. Vaya a [Azure Portal](https://portal.azure.com/).

1. Vaya a Defender para IoT.

1. Vaya a la página **Actualizaciones**.

1. Seleccione una versión de la sección de la consola de administración local.

1. Seleccione **Descargar** y guarde el archivo.

1. Inicie sesión en la consola de administración local y seleccione **Configuración del sistema** en el menú lateral.

1. En el panel **Actualización de la versión**, seleccione **Actualizar**.

1. Seleccione el archivo que descargó de la página **Actualizaciones** de Defender para IoT.

## <a name="mail-server-settings"></a>Configuración del servidor de correo

Defina la configuración del servidor de correo SMTP para la consola de administración local.

Para realizar la definición, siga estos pasos:

1. Inicie sesión en la CLI de la administración local con credenciales de administración.
1. Escriba ```nano /var/cyberx/properties/remote-interfaces.properties```.
1. Seleccione ENTRAR. Aparecerán las solicitudes siguientes.
   `mail.smtp_server=`
   `mail.port=25`
   `mail.sender=`
1. Escriba el nombre del servidor SMTP y el emisor. Luego, seleccione ENTRAR.

## <a name="see-also"></a>Consulte también

[Administración de sensores desde la consola de administración](how-to-manage-sensors-from-the-on-premises-management-console.md)

[Administración de sensores individuales](how-to-manage-individual-sensors.md)
