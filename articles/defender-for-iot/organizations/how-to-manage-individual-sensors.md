---
title: Administración de sensores individuales
description: Obtenga información sobre cómo administrar sensores individuales, lo que incluye la administración de archivos de activación y certificados, la realización de copias de seguridad y la actualización de un sensor independiente.
ms.date: 08/25/2021
ms.topic: how-to
ms.openlocfilehash: fd708b2ab259b38ea6983c4d4a6dac319e416218
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123424294"
---
# <a name="manage-individual-sensors"></a>Administración de sensores individuales

En este artículo se describe cómo administrar sensores individuales. Las tareas incluyen la administración de archivos de activación, la realización de copias de seguridad y la actualización de un sensor independiente.

También puede realizar ciertas tareas de administración de sensores desde la consola de administración local, donde se pueden administrar varios sensores simultáneamente.

Use Azure Portal para la incorporación y el registro de sensores.

## <a name="manage-sensor-activation-files"></a>Administración de archivos de activación del sensor

El sensor se incorpora a Azure Defender para IoT desde Azure Portal. Cada uno de los sensores se incorporaron como sensor conectado localmente o como sensor conectado a la nube.

Descargue un archivo de activación único para cada sensor que implemente. Para obtener más información sobre cuándo y cómo usar un nuevo archivo, consulte [Carga de nuevos archivos de activación](#upload-new-activation-files). Si no puede cargar el archivo, vea [Solución de problemas de carga de archivos de activación](#troubleshoot-activation-file-upload).

### <a name="about-activation-files-for-locally-connected-sensors"></a>Acerca de los archivos de activación para los sensores conectados localmente

Los sensores administrados localmente se asocian a una suscripción de Azure. El archivo de activación de los sensores conectados localmente contiene una fecha de expiración. Un mes antes de esta fecha, aparece un mensaje de advertencia en la parte superior de la consola del sensor. La advertencia permanecerá hasta que se haya actualizado el archivo de activación.

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="Captura de pantalla de la configuración del sistema.":::

Puede continuar trabajando con las características de Defender para IoT aunque el archivo de activación haya expirado. 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>Acerca de los archivos de activación para los sensores conectados a la nube

Los sensores conectados a la nube se asocian a Defender para IoT Hub. Estos sensores no están limitados por los períodos de tiempo del archivo de activación. El archivo de activación de los sensores conectados a la nube se usa para garantizar la conexión a Defender para IoT Hub.

### <a name="upload-new-activation-files"></a>Carga de nuevos archivos de activación

Es posible que tenga que cargar un nuevo archivo de activación para un sensor incorporado cuando:

- Un archivo de activación expira en un sensor conectado localmente. 

- Quiere trabajar en otro modo de administración de sensor. 

- Quiere asignar una nueva instancia de Defender para IoT Hub a un sensor conectado a la nube.

**Para agregar un nuevo archivo de activación:**

1. Vaya a la página **Sensor Management** (Administración de sensores).

2. Seleccione el sensor para el que quiere cargar un nuevo archivo de activación.

3. Elimínelo.

4. Vuelva a incorporar el sensor desde la página **Incorporación** en el nuevo modo o con una nueva instancia de Defender para IoT Hub.

5. Descargue el archivo de activación de la página **Download activation file** (Descargar archivo de activación).

6. Guarde el archivo.

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="Descarga del archivo de activación de Defender para IoT Hub.":::

7. Inicie sesión en la consola del sensor de Defender para IoT.

8. En la consola del sensor, seleccione **Configuración del sistema** > **Reactivación**.

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="Selección de reactivación en la pantalla Configuración del sistema.":::

9. Seleccione **Cargar** y elija el archivo guardado.

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="Carga del archivo guardado.":::

10. Seleccione **Activar**.

### <a name="troubleshoot-activation-file-upload"></a>Solución de problemas de carga de archivos de activación

Recibirá un mensaje de error si no se pudo cargar el archivo de activación. Puede que se hayan producido los siguientes eventos:

- **En el caso de los sensores conectados localmente**: el archivo de activación no es válido. Si el archivo no es válido, vaya al portal de Defender para IoT. En la página **Sensor Management** (Administración de sensores), seleccione el sensor con el archivo no válido y descargue un nuevo archivo de activación.

- **En el caso de los sensores conectados a la nube**: El dispositivo no se puede conectar a Internet. Compruebe la configuración de red del sensor. Si el sensor necesita conectarse mediante un proxy web para tener acceso a Internet, compruebe que el servidor proxy está configurado correctamente en la pantalla **Sensor Network Configuration** (Configuración de red del sensor). Compruebe que \*.azure-devices.net:443 está permitido en el firewall y/o el proxy. Si no se admiten caracteres comodín o quiere tener mayor control, el nombre de dominio completo específico de Defender for IoT Hub se debería abrir en el firewall y/o el proxy. Para obtener más información, vea [Referencia: Puntos de conexión de IoT Hub](../../iot-hub/iot-hub-devguide-endpoints.md).  

- **En el caso de los sensores conectados a la nube**: El archivo de activación es válido pero Defender para IoT lo rechazó. Si no puede resolver este problema, puede descargar otra activación desde la página Sites and Sensors (Sitios y sensores) de Defender para IoT. Si esto no funciona, póngase en contacto con Soporte técnico de Microsoft.

## <a name="manage-certificates"></a>Administración de certificados

Tras la instalación del sensor, se genera un certificado autofirmado local que se usa para acceder a la aplicación web del sensor. Al iniciar sesión en el sensor por primera vez, se les solicita a los usuarios administradores que proporcionen un certificado SSL/TLS. 

Es posible que los administradores de sensores deban actualizar los certificados que se cargaron después del registro inicial. Esto puede ocurrir, por ejemplo, si un certificado ha expirado.

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

## <a name="connect-a-sensor-to-the-management-console"></a>Conexión de un sensor a la consola de administración

En esta sección se describe cómo garantizar la conexión entre el sensor y la consola de administración local. Esto es necesario si trabaja en una red aislada y quiere enviar información de alertas y dispositivos a la consola de administración desde el sensor. Esta conexión también permite que la consola de administración inserte la configuración del sistema en el sensor y realice otras tareas de administración en el sensor.

Para conectarse:

1. Inicie sesión en la consola de administración local.

2. Seleccione **Configuración del sistema**.

3. En la sección **Programa de instalación del sensor – Cadena de conexión**, copie la cadena de conexión generada automáticamente.

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="Copia de la cadena de conexión de esta pantalla."::: 

4. Inicie sesión en la consola del sensor.

5. En el panel izquierdo, seleccione **Configuración del sistema**.

6. Seleccione **Management Console Connection** (Conexión de la consola de administración).

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="Captura de pantalla del cuadro de diálogo de conexión de la consola de administración.":::

7. Pegue la cadena de conexión en el cuadro **Cadena de conexión** y seleccione **Conectar**.

8. En la consola de administración local, en la ventana **Administración de sitios**, asigne el sensor a una zona.

## <a name="change-the-name-of-a-sensor"></a>Cambio del nombre de un sensor

Puede cambiar el nombre de la consola del sensor. Los nuevos nombres aparecen en el explorador web de la consola, en varias ventanas de la consola y en los registros de solución de problemas.

El proceso para cambiar los nombres de los sensores varía en los sensores conectados localmente y los sensores conectados a la nube. El nombre predeterminado es **sensor**.

### <a name="change-the-name-of-a-locally-connected-sensor"></a>Cambio del nombre de un sensor conectado localmente

Para cambiar el nombre:

1. En la parte inferior del panel izquierdo de la consola, seleccione la etiqueta del sensor actual.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="Captura de pantalla que muestra la etiqueta del sensor.":::

1. En el cuadro de diálogo **Edit sensor name** (Editar nombre de sensor), escriba un nombre.

1. Seleccione **Guardar**. Se aplica el nuevo nombre.

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>Cambio del nombre de un sensor conectado a la nube

Si el sensor se registró como sensor conectado a la nube, el nombre del sensor se define con el nombre asignado durante el registro. El nombre se incluye en el archivo de activación que cargó al iniciar sesión por primera vez. Para cambiar el nombre del sensor, debe cargar un nuevo archivo de activación.

Para cambiar el nombre:

1. En el portal de Azure Defender para IoT, vaya a la página Sites and Sensors (Sitios y sensores).

1. En esta página, elimine el sensor.

1. Regístrese con el nuevo nombre seleccionando **Onboard sensor** (Incorporar un sensor) en la página de introducción.

1. Descargue un nuevo archivo de activación.

1. Inicie sesión en la consola del sensor de Defender para IoT.

1. En la consola del sensor, seleccione **Configuración del sistema** y, a continuación, seleccione **Reactivación**.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Carga del archivo de activación para reactivar el sensor.":::

1. Seleccione **Cargar** y seleccione el archivo guardado.

1. Seleccione **Activar**.

## <a name="update-the-sensor-network-configuration"></a>Actualización de la configuración de red del sensor

La configuración de red del sensor se definió durante la instalación del sensor. Puede cambiar los parámetros de configuración. También puede establecer una configuración de proxy.

Si crea otra dirección IP, es posible que deba volver a iniciar sesión.

Para cambiar la configuración:

1. En el menú lateral, seleccione **Configuración del sistema**.

2. En la ventana **Configuración del sistema**, seleccione **Red**.

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="Configuración de las opciones de red.":::

3. Establezca los parámetros:

    | Parámetro | Descripción |
    |--|--|
    | Dirección IP | Dirección IP del sensor |
    | Máscara de subred | Dirección de la máscara |
    | Puerta de enlace predeterminada | Dirección IP de puerta de enlace predeterminada |
    | DNS | Dirección del servidor DNS |
    | Hostname | Nombre de host del sensor |
    | Proxy | Nombre de puerto y host de proxy |

4. Seleccione **Guardar**.

## <a name="synchronize-time-zones-on-the-sensor"></a>Sincronización de zonas horarias en el sensor

Puede configurar la hora y la región del sensor para que todos los usuarios vean la misma hora y la misma región.

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="Configuración de la hora y la región.":::

| Parámetro | Descripción |
|--|--|
| Zona horaria | Zona horaria definida a efectos de:<br />- Alertas<br />- Widgets de estadísticas y tendencias<br />- Informes de minería de datos<br />   \- Informes de evaluación de riesgos<br />- Vectores de ataque |
| Formato de fecha | Seleccione una de las siguientes opciones de formato:<br />- dd/MM/aaaa HH:mm:ss<br />- MM/dd/aaaa HH:mm:ss<br />- aaaa/MM/dd HH:mm:ss |
| Fecha y hora | Muestra la fecha actual y la hora local en el formato que seleccionó.<br />Por ejemplo, si la ubicación real es América y Nueva York, pero la zona horaria se establece en Europa y Berlín, la hora se muestra según la hora local de Berlín. |

Para configurar la hora del sensor:

1. En el menú lateral, seleccione **Configuración del sistema**.

2. En la ventana **Configuración del sistema**, seleccione **Time & Regional** (Horaria y regional).

3. Establezca los parámetros y seleccione **Guardar**.

## <a name="set-up-backup-and-restore-files"></a>Configuración de archivos de copia de seguridad y restauración

La copia de seguridad del sistema se realiza automáticamente a las 3:00 horas. Los datos se guardan en un disco diferente en el sensor. La ubicación predeterminada es `/var/cyberx/backups`.

Puede transferir automáticamente este archivo a la red interna.

> [!NOTE]
> - El procedimiento de copia de seguridad y restauración solo se puede realizar entre las mismas versiones.
> - En algunas arquitecturas, la copia de seguridad está deshabilitada. Puede habilitarla en el archivo `/var/cyberx/properties/backup.properties`.

Al controlar un sensor mediante la consola de administración local, puede usar la programación de copia de seguridad del sensor para recopilar estas copias de seguridad y almacenarlas en la consola de administración o en un servidor externo de copia de seguridad.

**Elementos de los que se hace copia de seguridad**: configuraciones y datos.

**Elementos de los que no se hace una copia de seguridad:** archivos PCAP y registros. Puede realizar copias de seguridad y restaurar los archivos PCAP y los registros manualmente.

El nombre de los archivos de copia de seguridad de sensores se asigna automáticamente con el siguiente formato: `<sensor name>-backup-version-<version>-<date>.tar`. Un ejemplo es `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`.

Para configurar la copia de seguridad:

- Inicie sesión en una cuenta administrativa y escriba `$ sudo cyberx-xsense-system-backup`.

Para restaurar el archivo de copia de seguridad más reciente:

- Inicie sesión en una cuenta administrativa y escriba `$ sudo cyberx-xsense-system-restore`.

Para guardar la copia de seguridad en un servidor SMB externo:

1. Cree una carpeta compartida en el servidor SMB externo.

    Obtenga la ruta de acceso de la carpeta, el nombre de usuario y la contraseña necesarios para acceder al servidor SMB.

2. En el sensor, cree un directorio para las copias de seguridad:

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. Edite `fstab`:

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. Edite y cree credenciales para compartir el servidor SMB:

    `sudo nano /etc/samba/user` 

5. Agregue:

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. Monte el directorio:

    `sudo mount -a`

7. Configure un directorio de copia de seguridad en la carpeta compartida en el sensor de Defender para IoT:  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>Restauración de sensores

Puede restaurar las copias de seguridad desde la consola de del sensor y mediante la CLI.

**Para restaurar desde la consola:**

- Seleccione **Restore Image** (Restaurar imagen) en la ventana de configuración **Sensor System** (Sistema de sensores).

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="Restauración de la imagen seleccionando el botón.":::

La consola mostrará los errores de restauración.

**Para restaurar mediante la CLI:**

- Inicie sesión en una cuenta administrativa y escriba `$ sudo cyberx-management-system-restore`.

## <a name="update-a-standalone-sensor-version"></a>Actualización de una versión de sensor independiente

En el procedimiento siguiente se describe cómo actualizar un sensor independiente mediante la consola del sensor. El proceso de actualización tarda aproximadamente 30 minutos.

1. Vaya a [Azure Portal](https://portal.azure.com/).

2. Vaya a Defender para IoT.

3. Vaya a la página **Actualizaciones**.

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="Captura de pantalla de la página Actualizaciones de Defender para IoT.":::

4. Seleccione **Descargar** en la sección **Sensores** y guarde el archivo.

5. En la barra lateral de la consola del sensor, seleccione **Configuración del sistema**.

6. En el panel **Actualización de la versión**, seleccione **Actualizar**.

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="Captura de pantalla del panel de actualización.":::

7. Seleccione el archivo que descargó de la página **Actualizaciones** de Defender para IoT.

8. Se inicia el proceso de actualización, durante el cual el sistema se reinicia dos veces. Después del primer reinicio (antes de completar el proceso de actualización), el sistema se abre con la ventana de información de inicio de sesión. Después de iniciar sesión, la versión de actualización aparece en la parte inferior izquierda de la barra lateral.

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="Captura de pantalla de la versión de actualización que aparece después de iniciar sesión.":::

## <a name="forward-sensor-failure-alerts"></a>Reenvío de alertas de error del sensor

Puede reenviar alertas a terceros para proporcionar información sobre:

- Sensores desconectados

- Errores de copia de seguridad remota

Esta información se envía cuando se crea una regla de reenvío para las notificaciones del sistema.

> [!NOTE]
> Los administradores pueden enviar notificaciones del sistema.

Para enviar notificaciones:

1. Inicie sesión en la consola de administración local.
1. Seleccione **Reenvío** en el menú lateral.
1. Cree una regla de reenvío.
1. Seleccione **Report System Notifications** (Informar de las notificaciones del sistema).

Para obtener más información sobre las reglas de reenvío, vea [Reenvío de la información de las alertas](how-to-forward-alert-information-to-partners.md).

## <a name="adjust-system-properties"></a>Ajuste de propiedades del sistema

Las propiedades del sistema controlan diversas operaciones y configuraciones en el sensor. Su edición o modificación podría dañar el funcionamiento de la consola del sensor.

Consulte con el [Soporte técnico de Microsoft](https://support.microsoft.com/) antes de cambiar la configuración.

Para acceder a las propiedades del sistema:

1. Inicie sesión en la consola de administración local o el sensor.

2. Seleccione **Configuración del sistema**.

3. Seleccione **Propiedades del sistema** en la sección **General**.

## <a name="see-also"></a>Consulte también

[Investigación y paquetes de inteligencia sobre amenazas](how-to-work-with-threat-intelligence-packages.md)

[Administración de sensores desde la consola de administración](how-to-manage-sensors-from-the-on-premises-management-console.md)