---
title: Creación y administración de entornos de ejecución de integración
description: En este artículo se explican los pasos para crear y administrar entornos de ejecución de integración en Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 10/22/2021
ms.openlocfilehash: 10e893886d98fc5ea6d79bf8092cc5b0d948d84a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309725"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Creación y administración de un entorno de ejecución de integración autohospedado

En este artículo se describe cómo crear y administrar un entorno de ejecución de integración autohospedado (SHIR) que permite examinar orígenes de datos en Azure Purview.

> [!NOTE]
> Integration Runtime de Purview no se puede compartir con una instancia de Integration Runtime de Azure Data Factory o Azure Synapse Analytics en la misma máquina. Debe instalarse en una máquina independiente.

## <a name="prerequisites"></a>Requisitos previos

- Las versiones compatibles de Windows son:
  - Windows 8.1
  - Windows 10
  - Windows Server 2012
  - Windows Server 2012 R2
  - Windows Server 2016
  - Windows Server 2019

No se admite la instalación del entorno de ejecución de integración autohospedado en un controlador de dominio.

- El entorno de ejecución de integración autohospedado requiere un sistema operativo de 64 bits con .NET Framework 4.7.2 o posterior. Consulte [Requisitos de sistema de .NET Framework](/dotnet/framework/get-started/system-requirements) para más información.
- La configuración mínima recomendada para la máquina del entorno de ejecución de integración autohospedado es un procesador de 2 GHz con 4 núcleos, 8 GB de RAM y 80 GB de espacio disponible en disco duro. Para obtener información detallada sobre los requisitos del sistema, consulte la [descarga](https://www.microsoft.com/download/details.aspx?id=39717).
- Si el equipo host está en hibernación, el entorno de ejecución de integración autohospedado no responde a las solicitudes de datos. Configure un plan de energía adecuado en el equipo antes de instalar el entorno de ejecución de integración autohospedado. Si la máquina está configurada para hibernar, se mostrará un mensaje en el instalador del entorno de ejecución de integración autohospedado.
- Debe ser administrador de la máquina para instalar y configurar correctamente el entorno de ejecución de integración autohospedado.
- Las ejecuciones de examen se suceden con una frecuencia específica según la programación que haya configurado. El uso del procesador y la RAM en la máquina sigue el mismo patrón en los momentos de máxima actividad y en los de inactividad. El uso de recursos también depende en gran medida de la cantidad de datos que se examinen. Cuando hay varios trabajos de examen en curso, puede ver que el uso de los recursos aumenta durante las horas pico.
- Puede que las tareas produzcan errores durante la extracción de datos en formatos Parquet, ORC o Avro.

> [!IMPORTANT]
> Si va a usar el entorno de ejecución de integración autohospedado para examinar archivos de Parquet, debe instalar **JRE 8 (Java Runtime Environment) de 64 bits u OpenJDK** en el equipo de IR. Consulte nuestra [Java Runtime Environment sección en la parte inferior de la página para obtener](#java-runtime-environment-installation) una guía de instalación.

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Configuración de un entorno de ejecución de integración autohospedado

Para crear y configurar un entorno de ejecución de integración autohospedado, use los procedimientos siguientes.

## <a name="create-a-self-hosted-integration-runtime"></a>Creación de una instancia de Integration Runtime autohospedada

1. En la página principal de [Purview Studio](https://web.purview.azure.com/resource/), seleccione **Asignación de datos** en el panel de navegación izquierdo.

2. En **Sources and scanning** (Orígenes y examen) en el panel izquierdo, seleccione **Integration runtimes** (Entornos de ejecución de integración) y, a continuación, **+ New** (+ Nuevo).

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="Seleccione en IR.":::

3. En la página **Integration runtime setup** (Configuración de Integration Runtime), seleccione **Self-Hosted** (Autohospedado) para crear un entorno de ejecución de integración autohospedado y, luego, seleccione **Continuar** (Continuar).

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="Cree un nuevo entorno de ejecución de integración autohospedado.":::

4. Escriba un nombre para el entorno de ejecución de integración y seleccione Create (Crear).

5. En la página **Integration Runtime settings** (Parámetros de Integration Runtime), siga los pasos descritos en la sección **Manual setup** (Instalación manual). Tendrá que descargar el entorno de ejecución de integración desde el sitio de descarga en una máquina virtual o la máquina en la que desee ejecutarlo.

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="Obtener la clave.":::

   - Copie y pegue la clave de autenticación.

   - Descargue el entorno de ejecución de integración autohospedado desde [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) en una máquina Windows local. Ejecute al programa de instalación. Se admiten versiones del entorno de ejecución de integración autohospedado, como 5.4.7803.1 y 5.6.7795.1. 

   - En la página **Registro de Integration Runtime (autohospedado)** , pegue una de las dos claves guardadas anteriormente y seleccione **Registrar**.

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="Introducir la clave.":::

   - En la página **Nuevo nodo de Integration Runtime (autohospedado)** , seleccione **Finalizar**.

6. Verá la siguiente ventana cuando el entorno de ejecución de integración autohospedado se haya registrado correctamente:

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="Se ha registrado correctamente.":::

### <a name="configure-proxy-server-settings"></a>Configuración de un servidor proxy

Si selecciona la opción **Usar proxy del sistema** para el proxy HTTP, el entorno de ejecución de integración autohospedado utilizará la configuración de proxy de diahost.exe.config y diawp.exe.config. Cuando estos archivos no especifican ningún proxy, el entorno de ejecución de integración autohospedado se conecta al servicio en la nube directamente sin pasar por ningún proxy. En el procedimiento siguiente se proporcionan instrucciones para actualizar el archivo diahost.exe.config:

1. En el Explorador de archivos, cree una copia segura de C:\Archivos de programa\Microsoft Integration Runtime\5.0\Shared\diahost.exe.config como copia de seguridad del archivo original.
1. Abra el Bloc de notas ejecutándolo como administrador.
1. En el Bloc de notas, abra el archivo de texto C:\Archivos de programa\Microsoft Integration Runtime\5.0\Shared\diahost.exe.config.
1. Encuentre la etiqueta predeterminada de **system.net** como se muestra en el siguiente código:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Después, puede agregar los detalles del servidor proxy tal y como se muestran en el ejemplo siguiente:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    La etiqueta de proxy permite propiedades adicionales para especificar la configuración requerida, como `scriptLocation`. Consulte [\<proxy\>Elemento (Configuración de red)](/dotnet/framework/configure-apps/file-schema/network/proxy-element-network-settings) para ver la sintaxis.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```

1. Guarde el archivo de configuración en su ubicación original. Luego reinicie el servicio de host del entorno de ejecución de integración autohospedado, lo que aplica los cambios.

   Para reiniciar el servicio, use el applet de servicios del panel de control. O bien en Integration Runtime Configuration Manager, seleccione el botón **Detener servicio** y, después, seleccione **Iniciar servicio**.

   Si el servicio no se inicia, es probable que se deba a que se ha agregado una sintaxis de etiqueta XML incorrecta en el archivo de configuración de la aplicación que se ha editado.

> [!IMPORTANT]
> No olvide actualizar tanto diahost.exe.config como diawp.exe.config.

También tiene que asegurarse de que Microsoft Azure se encuentra en la lista de permitidos de su empresa. Puede descargar la lista de direcciones IP válidas de Azure. Los intervalos IP para cada nube, desglosados por región y servicios etiquetados en la nube en cuestión, ya están disponibles en el Centro de descarga de Microsoft: 
   - Pública: https://www.microsoft.com/download/details.aspx?id=56519

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>Posibles síntomas de problemas relacionados con el firewall y el servidor proxy

Si ve mensajes de error como los siguientes, lo más probable es que se deba a una configuración incorrecta del firewall o el servidor proxy. Esta configuración impide que el entorno de ejecución de integración autohospedado se conecte a orígenes de datos o cuentas de almacenamiento administrados de Azure. Para asegurarse de que tanto el firewall como el servidor proxy están configurados correctamente, consulte la sección anterior.

- Al intentar registrar el entorno de ejecución de integración autohospedado, recibe el siguiente mensaje de error: "No se pudo registrar este nodo de Integration Runtime. Confirme que la clave de autenticación es válida y que el servicio host del servicio de integración se ejecuta en este equipo".
- Al abrir Integration Runtime Configuration Manager, se ve uno de estos dos estados, **Desconectado** o **Conectando**. Al ver los registros de eventos de Windows, en **Visor de eventos** > **Registros de aplicaciones y servicios** > **Microsoft Integration Runtime** aparecen mensajes de error como este:

  ```output
  Unable to connect to the remote server
  A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
  ```

## <a name="networking-requirements"></a>Requisitos de red

Para que funcione correctamente, la máquina del entorno de ejecución de integración autohospedado se tendrá que conectar a varios recursos:

* Los orígenes que quiera examinar mediante el entorno de ejecución de integración autohospedado.
* Cualquier instancia de Azure Key Vault que se use para almacenar las credenciales del recurso de Purview.
* La cuenta administrada de Storage y los recursos de centro de eventos creados por Purview.

Los recursos administrados de Storage y centro de eventos se pueden encontrar en la suscripción, en un grupo de recursos que contiene el nombre del recurso de Purview. Azure Purview usa estos recursos para ingerir los resultados del examen, entre otras muchas cosas, por lo que el entorno de ejecución de integración autohospedado tendrá que poder conectarse directamente con estos recursos.

Estos son los dominios y puertos que se deben permitir por medio de firewalls corporativos y de máquina.

> [!NOTE]
> Para los dominios indicados con "\<managed Purview storage account>", agregará el nombre de la cuenta de almacenamiento administrada asociada al recurso de Purview. Puede encontrar este recurso en el portal. Busque en los grupos de recursos un grupo denominado managed-rg-\<your Purview Resource name>. Por ejemplo: managed-rg-contosoPurview. Usará el nombre de la cuenta de almacenamiento en este grupo de recursos.
> 
> Para los dominios indicados con "\<managed Event Hub resource>", agregará el nombre de la cuenta de centro de eventos administrada asociada al recurso de Purview. Puede encontrarlo en el mismo grupo de recursos que la cuenta de almacenamiento administrada.

| Nombres de dominio                  | Puertos de salida | Descripción                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net` | 443            | Infraestructura global que usa Purview para ejecutar sus exámenes. Se necesita un carácter comodín, ya que no hay ningún recurso dedicado. |
| `<managed Event Hub resource>.servicebus.windows.net` | 443            | Purview lo usa para conectarse al bus de servicio asociado. Se tendrá en cuenta al permitir el dominio anterior, pero si usa puntos de conexión privados, tendrá que probar el acceso a este dominio único.|
| `*.frontend.clouddatahub.net` | 443            | Infraestructura global que usa Purview para ejecutar sus exámenes. Se necesita un carácter comodín, ya que no hay ningún recurso dedicado. |
| `<managed Purview storage account>.core.windows.net`          | 443            | Lo usa el entorno de ejecución de integración autohospedado para conectarse a la cuenta de almacenamiento de Azure administrada.|
| `<managed Purview storage account>.queue.core.windows.net` | 443            | Colas que usa Purview para ejecutar el proceso de examen. |
| `download.microsoft.com` | 443           | Opcional para las actualizaciones de SHIR. |

En función de los orígenes, es posible que también tenga que permitir los dominios de otros orígenes externos o de Azure. A continuación, se proporcionan algunos ejemplos, así como el dominio de Azure Key Vault, si se va a conectar a las credenciales almacenadas en el almacén de claves.

| Nombres de dominio                  | Puertos de salida | Descripción                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `<storage account>.core.windows.net`          | 443            | Opcional, para conectarse a una cuenta de Azure Storage. |
| `*.database.windows.net`      | 1433           | Opcional, para conectarse a Azure SQL Database o Azure Synapse Analytics. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Opcional, para conectarse a Azure Data Lake Store Gen 1. |
| `<datastoragename>.dfs.core.windows.net`    | 443            | Opcional, para conectarse a Azure Data Lake Store Gen 2. |
| `<your Key Vault Name>.vault.azure.net` | 443           | Obligatorio si alguna credencial se almacena en Azure Key Vault. |
| Varios dominios | Dependiente          | Dominios para cualquier otro origen al que se conectará SHIR. |
  
> [!IMPORTANT]
> En la mayoría de los entornos, también tendrá que confirmar que el DNS está configurado correctamente. Para confirmarlo, puede usar **nslookup** desde la máquina SHIR a fin de comprobar la conectividad a cada uno de los dominios anteriores. Cada nslookup debe devolver la dirección IP del recurso. Si usa [puntos de conexión privados](catalog-private-link.md), se debe devolver la dirección IP privada y no la dirección IP pública. Si no se devuelve ninguna dirección IP, o si al usar puntos de conexión privados se devuelve la dirección IP pública, tendrá que solucionar la asociación entre DNS y VNET, o el emparejamiento entre el punto de conexión privado y la red virtual.

## <a name="manage-a-self-hosted-integration-runtime"></a>Administración de un entorno de ejecución de integración autohospedado

Para editar un entorno de ejecución de integración autohospedado, vaya a **Integration runtimes** (Entornos de ejecución de integración) en **Management center** (Centro de administración), seleccione el entorno de ejecución de integración y seleccione Editar. Ahora puede actualizar la descripción, copiar la clave o volver a generar nuevas claves.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="Editar IR.":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="Editar los detalles de IR.":::

Para eliminar un entorno de ejecución de integración autohospedado, vaya a **Integration runtimes** (Entornos de ejecución de integración) en el centro de administración, seleccione el entorno de ejecución de integración y, a continuación, seleccione **Eliminar**. Una vez eliminado el entorno de ejecución de integración, se producirá un error en cualquier examen en curso que dependa de él.

## <a name="java-runtime-environment-installation"></a>Instalación de Java Runtime Environment

Si va a examinar archivos de Parquet mediante el entorno de ejecución de integración autohospedado con Purview, deberá instalar Java Runtime Environment u OpenJDK en la máquina de IR autohospedada.

Si examina archivos de Parquet mediante IR autohospedado, el servicio localiza el entorno de ejecución de Java primero mediante la consulta del Registro *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* en busca de JRE; si no lo encuentra, consulta la variable del sistema *`JAVA_HOME`* de OpenJDK.

- **Para usar JRE**: el IR de 64 bits necesita JRE de 64 bits. Puede encontrarlo [aquí](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Para usar OpenJDK**: se admite desde la versión 3.13 de IR. Empaquete jvm.dll con todos los demás ensamblados de OpenJDK necesarios en la máquina del IR autohospedado y establezca la variable de entorno del sistema JAVA_HOME en el valor que corresponda.

## <a name="proxy-server-considerations"></a>Consideraciones acerca del servidor proxy

Si en su entorno de red corporativo se usa un servidor proxy para acceder a Internet, configure el entorno de ejecución de integración autohospedado para utilizar la configuración del proxy adecuada. Puede establecer el proxy durante la fase de registro inicial.

:::image type="content" source="media/manage-integration-runtimes/self-hosted-proxy.png" alt-text="Especificación del proxy":::

Cuando se configura, el entorno de ejecución de integración autohospedado usa el servidor proxy para conectarse al origen y destino del servicio en la nube (que usan el protocolo HTTP o HTTPS). Por este motivo, debe seleccionar **Cambiar vínculo** durante la configuración inicial.

:::image type="content" source="media/manage-integration-runtimes/set-http-proxy.png" alt-text="Configuración del proxy":::

Hay tres opciones de configuración:

- **No utilizar proxy**: el entorno de ejecución de integración autohospedado no usa expresamente ningún proxy para conectarse a servicios en la nube.
- **Usar proxy del sistema**: el entorno Integration Runtime autohospedado utiliza la configuración de proxy de diahost.exe.config y diawp.exe.config. Si estos archivos no especifican ninguna configuración de proxy, el entorno de ejecución de integración autohospedado se conecta al servicio en la nube directamente sin pasar por ningún proxy.
- **Usar proxy personalizado**: establezca la configuración del proxy HTTP que se utilizará para el entorno de ejecución de integración autohospedado, en lugar de usar las configuraciones de diahost.exe.config y diawp.exe.config. Se requieren los valores de **Dirección** y **Puerto**. Los valores de **Nombre de usuario** y **Contraseña** son opcionales, y dependen de la configuración de la autenticación del proxy. Todas las configuraciones se cifran con DPAPI de Windows en el entorno de ejecución de integración autohospedado y se almacenan localmente en el equipo.

El servicio host del entorno de ejecución de integración se reinicia automáticamente después de guardar la configuración de proxy actualizada.

Tras registrar correctamente el entorno de ejecución de integración autohospedado, si quiere ver o actualizar la configuración de proxy, utilice Microsoft Integration Runtime Configuration Manager.

1. Inicie el **Administrador de configuración de Microsoft Integration Runtime**.
3. En **Proxy HTTP**, seleccione el vínculo **Cambiar** para abrir el cuadro de diálogo **Establecer proxy HTTP**.
4. Seleccione **Next** (Siguiente). En ese momento ve una advertencia en la que se le pide permiso para guardar la configuración del proxy y reiniciar el servicio de host del entorno de ejecución de integración.

Puede usar la herramienta Configuration Manager para ver y actualizar el proxy HTTP.

> [!NOTE]
> Si configura un servidor proxy con la autenticación NTLM, el servicio de host del entorno de ejecución de integración se ejecutará en la cuenta del dominio. Si más adelante cambia la contraseña de dicha cuenta, no olvide actualizar la configuración del servicio y reiniciarlo. Debido a este requisito, se recomienda acceder al servidor proxy con una cuenta de dominio dedicada que no requiera que se actualice la contraseña con frecuencia.

## <a name="installation-best-practices"></a>Procedimientos recomendados de instalación

Para instalar el entorno de ejecución de integración autohospedado, descargue un paquete de instalación de identidad administrada del [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).

- Configure un plan de energía en el equipo host para el entorno de ejecución de integración autohospedado con el fin de que el equipo no hiberne. Si el equipo host hiberna, el entorno de ejecución de integración autohospedado se pone en modo sin conexión.
- Realice regularmente una copia de las credenciales asociadas con el entorno de ejecución de integración autohospedado.

## <a name="next-steps"></a>Pasos siguientes

- [Cómo detectan los exámenes los recursos eliminados](concept-scans-and-ingestion.md#how-scans-detect-deleted-assets)

- [Uso de puntos de conexión privados con Purview](catalog-private-link.md)
