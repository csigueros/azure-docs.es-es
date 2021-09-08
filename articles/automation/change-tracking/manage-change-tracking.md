---
title: Administración de Change Tracking e Inventario en Azure Automation
description: En este artículo se describe cómo utilizar Change Tracking e Inventario para realizar el seguimiento de los cambios en el software y en los servicios de Microsoft en su entorno.
services: automation
ms.subservice: change-inventory-management
ms.date: 12/10/2020
ms.topic: conceptual
ms.openlocfilehash: d67acd025e453cee41a2461276d2f5df3066a141
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112518463"
---
# <a name="manage-change-tracking-and-inventory"></a>Administración de Change Tracking e Inventario

Al agregar un nuevo archivo o clave del registro para su seguimiento, Azure Automation lo habilita para [Change Tracking e Inventario](overview.md). En este artículo se describe cómo configurar el seguimiento, revisar los resultados del seguimiento y administrar las alertas cuando se detectan cambios.

Antes de realizar los procedimientos de este artículo, asegúrese de haber habilitado Change Tracking e Inventario en las máquinas virtuales mediante una de estas técnicas:

* [Habilitación de Change Tracking e Inventario desde una cuenta de Automation](enable-from-automation-account.md)
* [Habilitación de Change Tracking e Inventario desde Azure Portal](enable-from-portal.md)
* [Habilitación de Change Tracking e Inventario desde un runbook](enable-from-runbook.md)
* [Habilitación de Change Tracking e Inventario desde una máquina virtual de Azure](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Limitación del ámbito de implementación

Change Tracking e Inventario usa una configuración de ámbito en el área de trabajo para definir los equipos de destino que reciben los cambios. Para obtener más información, consulte [Limitación del ámbito de implementación de Change Tracking e Inventario](manage-scope-configurations.md).

## <a name="track-files"></a>Seguimiento de archivos

Puede usar Change Tracking e Inventario para realizar el seguimiento de los cambios en archivos y carpetas o directorios. En esta sección se explica cómo configurar el seguimiento de archivos en Windows y Linux.

### <a name="configure-file-tracking-on-windows"></a>Configuración del seguimiento de archivos en Windows

Use los pasos siguientes para configurar el seguimiento de archivos en equipos Windows:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En Azure Portal, seleccione **Todos los servicios**. En la lista de recursos, escriba **Automation**. Cuando comience a escribir, la lista filtrará las sugerencias en función de la entrada. Seleccione **Cuentas de Automation**.

3. En la lista de cuentas de Automation, seleccione la cuenta que eligió al habilitar Seguimiento de cambios e inventario.

4. En la cuenta de Automation, seleccione **Change Tracking** en **Administración de configuración**.

5. Seleccione **Editar configuración** (el símbolo de engranaje).

6. En la página Configuración del área de trabajo, seleccione **Archivos de Windows** y, a continuación, haga clic en **+ Agregar** para agregar un nuevo archivo para realizar su seguimiento.

7. En la opción Agregar archivo de Windows para el panel de Change Tracking, escriba la información del archivo o carpeta cuyo seguimiento se va a realizar y haga clic en **Guardar**. En la tabla siguiente se definen las propiedades que puede usar para la información.

    |Propiedad  |Descripción  |
    |---------|---------|
    |habilitado     | True si se aplica la configuración y False en caso contrario.        |
    |Nombre del elemento     | Nombre descriptivo del archivo cuyo seguimiento se va a realizar.        |
    |Grupo     | Un nombre de grupo para agrupar lógicamente los archivos.        |
    |Escriba la ruta de acceso     | Ruta de acceso para buscar el archivo, por ejemplo: **c:\temp\\\*.txt**. También puede usar variables de entorno, como `%winDir%\System32\\\*.*`.       |
    |Tipo de ruta de acceso     | Tipo de ruta de acceso. Los valores posibles son Archivo y Carpeta.        |    
    |Recursividad     | True si se usa recursividad al buscar el elemento cuyo seguimiento se va a realizar y False en caso contrario.        |    
    |Cargar contenido de archivo | True para cargar el contenido del archivo en los cambios bajo seguimiento y False en caso contrario.|

    Si planea configurar la supervisión de archivos y carpetas con caracteres comodín, tenga en cuenta lo siguiente:

    - Los caracteres comodín son necesarios para realizar el seguimiento de varios archivos.
    - Los caracteres comodín solo se pueden usar en el último segmento de una ruta de acceso, como *C:\carpeta\archivo* o */etc/* .conf*.
    - Si una variable de entorno tiene una ruta de acceso no válida, la validación será correcta, pero se producirá un error en dicha ruta cuando se ejecute el inventario.
    - Al establecer la ruta de acceso, evite las rutas de acceso generales como *c:* .**, ya que daría lugar a que se recorrieran demasiadas carpetas.

8. Asegúrese de especificar True para **Cargar contenido de archivo**. Esta opción habilita el seguimiento del contenido del archivo para la ruta de acceso de archivo indicada.

### <a name="configure-file-tracking-on-linux"></a>Configuración del seguimiento de archivos en Linux

Use los pasos siguientes para configurar el seguimiento de archivos en equipos Linux:

1. Seleccione **Editar configuración** (el símbolo de engranaje).

2. En la página Configuración del área de trabajo, seleccione **Archivos de Linux** y, a continuación, seleccione **+ Agregar** para agregar un nuevo archivo para realizar su seguimiento.

3. En la página **Agregar archivo de Linux para el seguimiento de cambios**, escriba la información del archivo o directorio cuyo seguimiento se va a realizar y, después, seleccione **Guardar**. En la tabla siguiente se definen las propiedades que puede usar para la información.

    |Propiedad  |Descripción  |
    |---------|---------|
    |habilitado     | True si se aplica la configuración y False en caso contrario.        |
    |Nombre del elemento     | Nombre descriptivo del archivo cuyo seguimiento se va a realizar.        |
    |Grupo     | Un nombre de grupo para agrupar lógicamente los archivos.        |
    |Escriba la ruta de acceso     | La ruta de acceso para buscar el archivo, por ejemplo: **/etc/*.conf**.       |
    |Tipo de ruta de acceso     | Tipo de ruta de acceso. Los valores son Archivo y Directorio.        |
    |Recursividad     | True si se usa recursividad al buscar el elemento cuyo seguimiento se va a realizar y False en caso contrario.        |
    |Usar sudo     | True si se usa sudo al buscar el elemento y False en caso contrario.         |
    |Vínculos     | Configuración que determina cómo tratar los vínculos simbólicos cuando se recorren directorios. Los valores posibles son:<br> Omitir: ignora los vínculos simbólicos y no incluye los archivos y directorios de referencia.<br>Seguir: sigue los vínculos simbólicos durante la recursión y también incluye los archivos o directorios de referencia.<br>Administrar: sigue los vínculos simbólicos y permite modificar el contenido devuelto.<br>**Nota:** No se recomienda la opción Administrar, ya que no admite la recuperación del contenido del archivo.    |
    |Cargar contenido de archivo | True para cargar el contenido del archivo en los cambios bajo seguimiento y False en caso contrario. |

4. Asegúrese de especificar **True** para **Cargar contenido de archivo**. Esta opción habilita el seguimiento del contenido del archivo para la ruta de acceso de archivo indicada.

   ![Agregar archivo de Linux](./media/manage-change-tracking/add-linux-file.png)

## <a name="track-file-contents"></a>Seguimiento del contenido de archivos

El seguimiento del contenido de archivos le permite ver el contenido de un archivo antes y después de que se produzca un cambio del que se realiza el seguimiento. La característica guarda el contenido del archivo en una [cuenta de almacenamiento](../../storage/common/storage-account-overview.md) después de que se produzca cada cambio. Estas son algunas reglas que deben seguirse para el seguimiento del contenido de archivos:

* Para almacenar el contenido del archivo se requiere una cuenta de almacenamiento estándar que use el modelo de implementación de Resource Manager.
* De forma predeterminada, las cuentas de almacenamiento aceptan conexiones de clientes en cualquier red. Si ha protegido su cuenta de almacenamiento para permitir solo cierto tráfico, debe modificar las reglas de configuración para permitir que la cuenta de Automation se conecte a ella. Consulte [Configuración de redes virtuales y firewalls de Azure Storage](../../storage/common/storage-network-security.md).
* No use cuentas de almacenamiento de modelos de implementación prémium y clásica. Consulte [Acerca de las cuentas de Azure Storage](../../storage/common/storage-account-create.md).
* Solo puede conectar la cuenta de almacenamiento a una cuenta de Automation.
* Change Tracking e Inventario se deben habilitar en la cuenta de Automation.

### <a name="enable-tracking-for-file-content-changes"></a>Habilitación del seguimiento de los cambios del contenido de archivos

Siga los pasos a continuación para habilitar el seguimiento de los cambios en el contenido del archivo:

1. Seleccione **Editar configuración** (el símbolo de engranaje).

2. Seleccione **Contenido del archivo** y, después, seleccione **Vínculo**. Esta acción abre la página **Agregar ubicación de contenido para Change Tracking**.

   ![Agregar una ubicación de contenido](./media/manage-change-tracking/enable.png)

3. Seleccione la suscripción y la cuenta de almacenamiento que se va a usar para almacenar el contenido del archivo.

5. Si desea habilitar el seguimiento del contenido de los archivos para todos los archivos que se sigan, seleccione **Activar** para **cargar el contenido del archivo para todas las configuraciones**. Puede cambiar esto para la ruta de acceso de cada archivo más adelante.

   ![Establecimiento de la cuenta de almacenamiento](./media/manage-change-tracking/storage-account.png)

6. Change Tracking e Inventario muestran los URI de la cuenta de almacenamiento y la firma de acceso compartido (SAS) cuando habilita el seguimiento de cambios del contenido de archivos. Las firmas expiran después de 365 días y puede volver a crearlas seleccionando **Regenerar**.

   ![Enumeración de claves de cuenta](./media/manage-change-tracking/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Visualización del contenido de un archivo del que se realiza el seguimiento

Cuando Change Tracking e Inventario detecta un cambio de un archivo del que se realiza el seguimiento, puede ver el contenido del archivo en el panel Cambiar detalles.  

![Enumeración de cambios](./media/manage-change-tracking/change-list.png)

1. En la página **Seguimiento de cambios** de la cuenta de Automation, elija un archivo en la lista de cambios y, después, seleccione **Ver cambios de contenido del archivo** para ver el contenido del archivo. En el panel para cambiar detalles se muestra la información estándar del archivo antes y después de cada propiedad.

   ![Cambiar detalles](./media/manage-change-tracking/change-details.png)

2. El contenido del archivo se muestra en una vista en paralelo. Puede seleccionar **Alineado** para ver una vista alineada de los cambios.

## <a name="track-registry-keys"></a>Seguimiento de las claves del Registro

Use los pasos siguientes para configurar las claves del registro para realizar un seguimiento en los equipos Windows:

1. En la página **Seguimiento de cambios** de la cuenta de Automation, seleccione **Editar configuración** (el símbolo del engranaje).

2. En la página Configuración del área de trabajo, seleccione **Registro de Windows**.

3. Seleccione **+ Agregar** para agregar una nueva clave del Registro para realizar su seguimiento.

4. En la página **Agregar registro de Windows a Change Tracking**, escriba la información de la clave cuyo seguimiento se va a realizar y seleccione **Guardar**. En la tabla siguiente se definen las propiedades que puede usar para la información. Al especificar una ruta de acceso del registro, debe ser la clave y no un valor.

    |Propiedad  |Descripción  |
    |---------|---------|
    |habilitado     | True si se aplica la configuración y False en caso contrario.        |
    |Nombre del elemento     | Nombre descriptivo de la clave del Registro cuyo seguimiento se va a realizar.        |
    |Grupo     | Nombre de grupo para agrupar lógicamente las claves del Registro.        |
    |Clave del registro de Windows   | Nombre de clave con una ruta de acceso; por ejemplo, `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup`.      |

## <a name="search-logs-for-change-records"></a>Búsqueda de registros de cambios en los registros

Puede realizar varias búsquedas en los registros de Azure Monitor para consultar los registros de cambios. Con la página Change Tracking abierta, haga clic en **Log Analytics** para abrir la página Registros. En la tabla siguiente se proporcionan ejemplos de búsquedas de registros para los registros de cambios.

|Consultar  |Descripción  |
|---------|---------|
|`ConfigurationData`<br>&#124; `where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124; `where SvcState == "Stopped"`<br>&#124; `summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Muestra los registros de inventario más recientes para los servicios de Windows que se han establecido en Automático, pero se han notificado como Detenidos. Los resultados se limitan al registro más reciente para el equipo y el nombre del software especificados.    |
|`ConfigurationChange`<br>&#124; `where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124; `order by TimeGenerated desc`|Muestra los registros de cambios del software eliminado.|

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información sobre las configuraciones de ámbito, consulte [Limitación del ámbito de implementación de Change Tracking e Inventario](manage-scope-configurations.md).
* Si necesita buscar en los registros almacenados en los registros de Azure Monitor, consulte [Introducción a las consultas de registro en Azure Monitor](../../azure-monitor/logs/log-query-overview.md).
* Si finaliza con las implementaciones, consulte [Eliminación de Change Tracking e Inventario](remove-feature.md).
* Para eliminar las máquinas virtuales de Change Tracking e Inventario, consulte [Eliminación de máquinas virtuales de Change Tracking e Inventario](remove-vms-from-change-tracking.md).
* Para solucionar problemas de la característica, consulte [Solución de problemas de Change Tracking e Inventario](../troubleshoot/change-tracking.md).
