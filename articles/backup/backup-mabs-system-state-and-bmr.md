---
title: Protección del estado del sistema y reconstrucción completa
description: Use Azure Backup Server para realizar una copia de seguridad del estado del sistema y proporcionar protección de reconstrucción completa (BMR).
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 864a20ce806d1bf8e9e728c77a9c8f17adeed18b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656196"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-by-using-azure-backup-server"></a>Realizar una copia de seguridad del estado del sistema y restaurar a una reconstrucción completa con Azure Backup Server

Azure Backup Server realiza una copia de seguridad del estado del sistema y proporciona protección de reconstrucción completa (BMR).

* **Copia de seguridad del estado de sistema**: realiza una copia de seguridad de los archivos del sistema operativo. Esta copia de seguridad le permite recuperar cuando un equipo se inicia, pero los archivos de sistema y el registro se pierden. Una copia de seguridad del estado del sistema incluye los elementos siguientes:
  * Miembro de dominio: archivos de arranque, base de datos de registro de clase COM+, Registro.
  * Controlador de dominio: Windows Server Active Directory (NTDS), archivos de arranque, base de datos de registro de clase COM+, Registro, volumen del sistema (SYSVOL).
  * Equipo que ejecuta servicios de cluster: metadatos del servidor de clúster
  * Equipo que ejecuta servicios de certificado: Datos del certificado
* **Copia de seguridad de reconstrucción completa**: realiza una copia de seguridad de los archivos del sistema operativo y de todos los datos de volúmenes críticos, excepto los datos de usuario. Por definición, una copia de seguridad de BMR incluye una copia de seguridad del estado del sistema. Proporciona protección cuando un equipo no se inicia y es necesario recuperarlo todo.

En la tabla siguiente se resumen los elementos de los que se pueden realizar copias de seguridad y que se pueden recuperar. Para obtener información sobre las versiones de las aplicaciones que se pueden proteger con el estado del sistema y BMR, consulte [¿De qué se puede realizar una copia de seguridad con Azure Backup Server?](backup-mabs-protection-matrix.md).

|Copia de seguridad|Problema|Recuperación a partir de una copia de seguridad de Azure Backup Server|Recuperación a partir de una copia de seguridad del estado de sistema|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Datos de archivos**<br /><br />Copia de seguridad de datos normal<br /><br />Copia de seguridad de estado de sistema o BMR|Se pierden datos de archivos|Y|N|N|
|**Datos de archivo**<br /><br />Copia de seguridad de Azure Backup Server de los datos de archivo<br /><br />BMR/Copia de seguridad del estado del sistema|Se pierde o daña el sistema operativo|N|Y|Y|
|**Datos de archivo**<br /><br />Copia de seguridad de Azure Backup Server de los datos de archivo<br /><br />BMR/Copia de seguridad del estado del sistema|Servidor perdido (volúmenes de datos intactos)|N|N|Y|
|**Datos de archivo**<br /><br />Copia de seguridad de Azure Backup Server de los datos de archivo<br /><br />BMR/Copia de seguridad del estado del sistema|Servidor perdido (volúmenes de datos perdidos)|Y|N|Y<br /><br />BMR, seguida de una recuperación normal de los datos de archivo de los que se ha realizado una copia de seguridad|
|**Datos de SharePoint**<br /><br />Copia de seguridad de Azure Backup Server de los datos de la granja<br /><br />BMR/Copia de seguridad del estado del sistema|Sitio, listas, elementos de lista y documentos perdidos|Y|N|N|
|**Datos de SharePoint**<br /><br />Copia de seguridad de Azure Backup Server de los datos de la granja<br /><br />BMR/Copia de seguridad del estado del sistema|Se pierde o daña el sistema operativo|N|Y|Y|
|**Datos de SharePoint**<br /><br />Copia de seguridad de Azure Backup Server de los datos de la granja<br /><br />BMR/Copia de seguridad del estado del sistema|Recuperación ante desastres|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Copia de seguridad de Azure Backup Server del host o invitado de Hyper-V<br /><br />BMR/Copia de seguridad del estado del sistema del host|Máquina virtual perdida|Y|N|N|
|Hyper-V<br /><br />Copia de seguridad de Azure Backup Server del host o invitado de Hyper-V<br /><br />BMR/Copia de seguridad del estado del sistema del host|Se pierde o daña el sistema operativo|N|Y|Y|
|Hyper-V<br /><br />Copia de seguridad de Azure Backup Server del host o invitado de Hyper-V<br /><br />BMR/Copia de seguridad del estado del sistema del host|Host de Hyper-V perdido (máquinas virtuales intactas)|N|N|Y|
|Hyper-V<br /><br />Copia de seguridad de Azure Backup Server del host o invitado de Hyper-V<br /><br />BMR/Copia de seguridad del estado del sistema del host|Host de Hyper-V perdido (máquinas virtuales perdidas)|N|N|Y<br /><br />BMR, seguida de una recuperación normal de Azure Backup Server|
|SQL Server/Exchange<br /><br />Copia de seguridad de aplicación de Azure Backup Server<br /><br />BMR/Copia de seguridad del estado del sistema|Datos de aplicación perdidos|Y|N|N|
|SQL Server/Exchange<br /><br />Copia de seguridad de aplicación de Azure Backup Server<br /><br />BMR/Copia de seguridad del estado del sistema|Se pierde o daña el sistema operativo|N|Y|Y|
|SQL Server/Exchange<br /><br />Copia de seguridad de aplicación de Azure Backup Server<br /><br />BMR/Copia de seguridad del estado del sistema|Servidor perdido (registros de base de datos/transacción intactos)|N|N|Y|
|SQL Server/Exchange<br /><br />Copia de seguridad de aplicación de Azure Backup Server<br /><br />BMR/Copia de seguridad del estado del sistema|Servidor perdido (registros de base de datos/transacción perdidos)|N|N|Y<br /><br />BMR, seguida de una recuperación normal de Azure Backup Server|

## <a name="how-system-state-backup-works"></a>Cómo funciona la copia de seguridad del estado del sistema

Cuando se ejecuta una copia de seguridad del estado del sistema, Backup Server se comunica con Copias de seguridad de Windows Server para solicitar una copia de seguridad del estado del sistema del servidor. De forma predeterminada, Backup Server y Copias de seguridad de Windows Server usan la unidad que tiene más espacio libre disponible. La información sobre esta unidad se guarda en el archivo *PSDataSourceConfig.xml*.

Puede personalizar la unidad que usa Backup Server para la copia de seguridad del estado del sistema:

1. En el servidor protegido, vaya a *C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources*.
1. Abra el archivo *PSDataSourceConfig.xml* para editarlo.
1. Cambie el valor \<FilesToProtect\> por la letra de unidad.
1. Guarde y cierre el archivo.

Si se ha establecido un grupo de protección para proteger el estado del sistema del equipo, ejecute una comprobación de coherencia. Si se genera una alerta, seleccione **Modificar grupo de protección** en la alerta y complete las páginas del asistente. Después, ejecute otra comprobación de coherencia.

Si el servidor de protección está en un clúster, es posible que se seleccione una unidad de clúster como la unidad con más espacio libre. Si la propiedad de esa unidad se ha cambiado a otro nodo y se ejecuta una copia de seguridad del estado de sistema, la unidad no estará disponible y se producirá un error en la copia de seguridad. En este caso, modifique *PSDataSourceConfig.xml* para que apunte a una unidad local.

Después, Copias de seguridad de Windows Server crea una carpeta llamada *WindowsImageBackup* en la raíz de la carpeta de restauración. Cuando Copias de seguridad de Windows Server crea la copia de seguridad, todos los datos se colocan en esta carpeta. Cuando finaliza la copia de seguridad, el archivo se transfiere al equipo con Backup Server. Tenga en cuenta la información siguiente:

* Esta carpeta y su contenido no se limpian al finalizar la copia de seguridad o la transferencia. Lo mejor es que considere que el espacio se reserva para la próxima vez que finalice una copia de seguridad.
* La carpeta se crea para cada copia de seguridad. La marca de fecha y hora refleja el momento en que se ha realizado la última copia de seguridad del estado del sistema.

## <a name="how-bmr-backup-works"></a>Cómo funciona la copia de seguridad de BMR

En el caso de BMR (incluida una copia de seguridad del estado del sistema), el trabajo de copia de seguridad se guarda directamente en un recurso compartido en el equipo con Backup Server. No se guarda en una carpeta en el servidor protegido.

Backup Server llama a Copias de seguridad de Windows Server y comparte el volumen de réplica para esa copia de seguridad de BMR. En este caso, no se requiere que Copias de seguridad de Windows Server use la unidad con más espacio libre. En su lugar, usa el recurso compartido que se ha creado para el trabajo.

Cuando finaliza la copia de seguridad, el archivo se transfiere al equipo con Backup Server. Los registros se almacenan en *C:\Windows\Logs\WindowsServerBackup*.

## <a name="prerequisites-and-limitations"></a>Requisitos previos y limitaciones

* No se admite BMR para los equipos que ejecutan Windows Server 2003 o para los equipos que ejecutan un sistema operativo cliente.

* No se puede proteger BMR y el estado del sistema para el mismo equipo en grupos de protección diferentes.

* Un equipo con Backup Server no puede protegerse a sí mismo para BMR.

* No se admite la protección a corto plazo en cinta (de disco a cinta o D2T) para BMR. Se admite el almacenamiento a largo plazo en cinta (de disco a disco y a cinta o D2D2T).

* Para la protección de BMR, es necesario instalar Copias de seguridad de Windows Server en el equipo protegido.

* Para la protección de BMR, a diferencia de la protección del estado del sistema, Backup Server no tiene requisitos de espacio en el equipo protegido. Copias de seguridad de Windows Server transfiere directamente las copias de seguridad al equipo con Backup Server. El trabajo de transferencia de copia de seguridad no aparece en la vista **Trabajos** de Backup Server.

* Backup Server reserva 30 GB de espacio en el volumen de réplica para BMR. Puede cambiar esta asignación de espacio en la página **Asignación de disco** del asistente Modificar grupo de protección. O bien, puede usar los cmdlets de PowerShell Get-DatasourceDiskAllocation y Set-DatasourceDiskAllocation. En el volumen de puntos de recuperación, la protección de BMR requiere unos 6 GB para una retención de cinco días.
  * No se puede reducir el tamaño del volumen de réplica a menos de 15 GB.
  * Backup Server no calcula el tamaño del origen de datos de BMR y da por supuesto que son 30 GB para todos los servidores. Cambie el valor en función del tamaño de las copias de seguridad de BMR que espera que se realicen en el entorno. El tamaño aproximado de una copia de seguridad de BMR se puede calcular como la suma del espacio usado en todos los volúmenes críticos. Volúmenes críticos = volumen de arranque + volumen del sistema + volumen que hospeda los datos del estado del sistema, como Active Directory.

* Cuando se pasa de la protección del estado del sistema a la protección de BMR, esta requiere menos espacio en el *volumen de puntos de recuperación*. Sin embargo, el espacio adicional en el volumen no se recupera. Puede reducir manualmente el tamaño del volumen en la página **Modificar asignación de disco** del asistente Modificar grupo de protección. O bien, puede usar los cmdlets de PowerShell Get-DatasourceDiskAllocation y Set-DatasourceDiskAllocation.

    Cuando se pasa de la protección del estado del sistema a la protección de BMR, esta requiere más espacio en el *volumen de réplica*. y el volumen se amplía automáticamente. Si quiere cambiar las asignaciones de espacio predeterminadas, use el cmdlet de PowerShell Modify-DiskAllocation.

* Cuando se pasa de la protección de BMR a la protección del estado del sistema, se necesita más espacio en el volumen de puntos de recuperación. Backup Server podría intentar aumentar automáticamente el volumen. Si no hay suficiente espacio en el grupo de almacenamiento, se produce un error.

    Cuando se pasa de la protección de BMR a la protección del estado del sistema, se necesita espacio en el equipo protegido. El espacio es necesario porque la protección del estado del sistema primero escribe la réplica en el equipo local y, después, la transfiere al equipo con Backup Server.

## <a name="before-you-begin"></a>Antes de empezar

1. **Implemente Azure Backup Server**. Compruebe que Backup Server está implementado correctamente. Para más información, consulte:
    * [Requisitos del sistema para Azure Backup Server](/system-center/dpm/install-dpm#setup-prerequisites)
    * [Matriz de protección de Backup Server](backup-mabs-protection-matrix.md)

1. **Configure el almacenamiento**. Puede almacenar los datos de la copia de seguridad en disco, en cinta y en la nube con Azure. Para obtener más información, vea [Prepare data storage](/system-center/dpm/plan-long-and-short-term-data-storage) (Preparar el almacenamiento de datos).

1. **Configure el agente de protección**. Instale el agente de protección en el equipo del que quiere hacer una copia de seguridad. Para obtener más información, vea [Deploy the DPM protection agent](/system-center/dpm/deploy-dpm-protection-agent) (Implementar el agente de protección DPM).

## <a name="back-up-system-state-and-bare-metal"></a>Copia de seguridad del estado del sistema y reconstrucción completa

Para realizar la copia de seguridad del estado del sistema y la reconstrucción completa:

1. Para abrir el Asistente para nuevo grupo de protección en la Consola de administrador de Backup Server, seleccione **Protección** > **Acciones** > **Crear grupo de protección**.

1. En la página **Seleccionar tipo de grupo de protección**, seleccione **Servidores** y haga clic en **Siguiente**.

1. En la página **Seleccionar miembros del grupo**, expanda el equipo y seleccione **BMR** o **Estado del sistema**.

    Recuerde en cuenta que no se puede proteger BMR y el estado del sistema para el mismo equipo en grupos diferentes. Además, cuando se selecciona BMR, el estado del sistema se habilita automáticamente. Para obtener más información, vea [Deploy protection groups](/system-center/dpm/create-dpm-protection-groups) (Implementar grupos de protección).

1. En la página **Seleccionar método de protección de datos**, elija cómo quiere controlar la copia de seguridad a corto y largo plazo.

    La copia de seguridad a corto plazo siempre se realiza primero en disco, con la opción de realizar una copia de seguridad desde el disco a Azure mediante Azure Backup (a corto o largo plazo). Como alternativa a la copia de seguridad a largo plazo en la nube, puede configurar la copia de seguridad a largo plazo en un dispositivo de cinta o biblioteca de cintas independiente conectados a Backup Server.

1. En la página **Seleccionar objetivos a corto plazo**, elija cómo quiere realizar la copia de seguridad en el almacenamiento a corto plazo en disco:
    * Para **Duración de retención**, elija cuánto tiempo quiere conservar los datos en el disco.
    * Para **Frecuencia de sincronización**, elija con qué frecuencia quiere ejecutar una copia de seguridad incremental en disco. Si no quiere establecer un intervalo de copia de seguridad, puede seleccionar **Solo antes de un punto de recuperación**. Backup Server ejecutará una copia de seguridad rápida y completa justo antes de la programación de cada punto de recuperación.

1. Si quiere almacenar los datos en cinta para el almacenamiento a largo plazo, en la página **Especificar objetivos a largo plazo**, elija durante cuánto tiempo quiere conservar los datos de la cinta (de 1 a 99 años).
    1. Para **Frecuencia de copia de seguridad**, elija con qué frecuencia se debe ejecutar una copia de seguridad en cinta. La frecuencia se basa en la duración de retención que ha seleccionado:
        * Cuando la duración de retención es de 1 a 99 años, puede realizar las copias de seguridad con frecuencia diaria, semanal, quincenal, mensual, trimestral, semestral o anual.
        * Cuando la duración de retención es de 1 a 11 meses, puede realizar las copias de seguridad con frecuencia diaria, semanal, quincenal o mensual.
        * Cuando la duración de retención es de 1 a 4 semanas, puede realizar una copia de seguridad diaria o semanal.

    1. En la página **Seleccionar detalles de cinta y biblioteca**, seleccione la cinta y la biblioteca que quiere usar. Elija también si los datos deben comprimirse y cifrarse.

1. En la página **Revisar asignación de disco**, revise el espacio en disco del grupo de almacenamiento que está disponible para el grupo de protección.

    * **Tamaño total de datos** es el tamaño de los datos de los que quiere hacer una copia de seguridad.
    * **Disk space to be provisioned on Azure Backup Server** (Espacio en disco que se aprovisionará en Azure Backup Server) es el espacio que Backup Server recomienda para el grupo de protección. Backup Server usa esta configuración para elegir el volumen de copia de seguridad ideal. Puede editar las opciones del volumen de copia de seguridad en **Detalles de asignación de disco**.
    * En el caso de las cargas de trabajo, seleccione el almacenamiento preferido en el menú desplegable. Las modificaciones cambiarán los valores de **Almacenamiento total** y **Free Storage** (Almacenamiento libre) en el panel **Almacenamiento en disco disponible**. El espacio infra-aprovisionado es la cantidad de almacenamiento que Backup Server sugiere agregar al volumen para garantizar la realización de las copias de seguridad sin problemas.

1. En la página **Seleccionar método de creación de réplicas**, seleccione cómo controlar la replicación inicial completa de los datos.

    Si quiere replicar por la red, se recomienda que elija una hora de poco tráfico. En el caso de grandes cantidades de datos o condiciones de red no del todo óptimas, considere la posibilidad de replicar los datos sin conexión mediante medios extraíbles.

1. En la página **Elegir las opciones de la comprobación de coherencia**, seleccione cómo automatizar las comprobaciones de coherencia.

    Puede ejecutar una comprobación únicamente cuando los datos de réplica sean incoherentes, o bien según una programación. Si no quiere configurar la comprobación de coherencia automática, puede ejecutar una comprobación manual en cualquier momento. Para ejecutar una comprobación manual, en el área **Protección** de la consola de administrador de Backup Server, haga clic con el botón derecho en el grupo de protección y seleccione **Realizar comprobación de coherencia**.

1. Si ha elegido realizar una copia de seguridad en la nube mediante Azure Backup, en la página **Especificar datos de la protección en línea**, seleccione las cargas de trabajo de las que quiere realizar una copia de seguridad en Azure.

1. En la página **Especificar la programación de copia de seguridad en línea**, seleccione con qué frecuencia se producirán las copias de seguridad incrementales en Azure.

    Puede programar copias de seguridad para que se ejecuten cada día, semana, mes y año. También puede seleccionar la fecha y la hora en las que se deben ejecutar las copias de seguridad. Pueden realizarse copias de seguridad hasta dos veces al día. Cada vez que se ejecuta una copia de seguridad, se crea un punto de recuperación de datos en Azure a partir de la copia de los datos de copia de seguridad almacenados en el disco del servidor de copia de seguridad.

1. En la página **Especificar directiva de retención en línea**, seleccione cómo se guardan en Azure los puntos de recuperación que se crean a partir de las copias de seguridad diarias, semanales, mensuales y anuales.

1. En la página **Elegir replicación en línea**, seleccione cómo se realiza la replicación inicial completa de los datos.

    Puede replicar mediante la red o realizar una copia de seguridad sin conexión (propagación sin conexión). Las copias de seguridad sin conexión usan la característica Azure Import. Para obtener más información, vea [Flujo de trabajo de copia de seguridad sin conexión en Azure Backup](offline-backup-azure-data-box.md).

1. En la página **Resumen**, revise la configuración. Después de seleccionar **Crear grupo**, se produce la replicación inicial de los datos. Cuando finalice la replicación de los datos, en la página **Estado**, el estado del grupo de protección será **Correcto**. Las copias de seguridad se realizan de acuerdo con la configuración del grupo de protección.

## <a name="recover-system-state-or-bmr"></a>Recuperar el estado del sistema o BMR

Puede recuperar el estado del sistema o BMR en una ubicación de red. Si ha realizado una copia seguridad de BMR, use el Entorno de recuperación de Windows (WinRE) para iniciar el sistema y conectarlo a la red. Después, use Copias de seguridad de Windows Server para recuperar desde la ubicación de red. Si ha realizado una copia seguridad del estado del sistema, basta con que use Copias de seguridad de Windows Server para recuperar desde la ubicación de red.

### <a name="restore-bmr"></a>Restaurar BME

Para ejecutar la recuperación en el equipo con Backup Server:

1. En el panel **Recuperación**, busque el equipo que quiere recuperar. A continuación, seleccione **Reconstrucción completa**.

1. Los puntos de recuperación disponibles se indican en negrita en el calendario. Seleccione la fecha y hora para el punto de recuperación que quiere usar.

1. En la página **Seleccionar tipo de recuperación**, elija **Copiar en una carpeta de red**.

1. En la página **Especificar destino**, seleccione el destino de los datos copiados.

    Recuerde que el destino debe tener espacio suficiente para los datos. Se recomienda crear una nueva carpeta para el destino.

1. En la página **Especificar opciones de recuperación**, seleccione la configuración de seguridad. Después, seleccione si quiere usar instantáneas de hardware basadas en la red de área de almacenamiento (SAN) para una recuperación más rápida. Esta opción solo está disponible si:
    * Tiene una SAN que proporciona esta funcionalidad.
    * Puede crear y dividir un clon para que se pueda escribir en él.
    * El equipo protegido y el equipo con Backup Server están conectados a la misma red.

1. Configure las opciones de notificación.
1. En la página **Confirmación**, seleccione **Recuperar**.

Para configurar la ubicación del recurso compartido:

1. En la ubicación de restauración, vaya a la carpeta que contenga la copia de seguridad.

1. Comparta la carpeta que está un nivel por encima de *WindowsImageBackup*, para que la raíz de la carpeta compartida sea la carpeta *WindowsImageBackup*.

    Si no comparte esta carpeta, la restauración no encontrará la copia de seguridad. Para conectarse con WinRE, necesita un recurso compartido al que pueda tener acceso en WinRE con la dirección IP y las credenciales correctas.

Para restaurar el sistema:

1. Inicie el equipo en el que quiere restaurar la imagen con el DVD de Windows del sistema que va a restaurar.

1. En la primera página, compruebe la configuración regional y de idioma. En la página **Instalar**, seleccione **Reparar el equipo**.

1. En la página **Opciones de recuperación del sistema**, seleccione **Restaure el equipo con una imagen del sistema que haya creado anteriormente**.

1. En la página **Seleccionar una copia de seguridad de imagen del sistema**, elija **Seleccionar una imagen del sistema** > **Avanzadas** > **Buscar una imagen de sistema en la red**. Si aparece una advertencia, seleccione **Sí**. Vaya a la ruta de acceso del recurso compartido, escriba las credenciales y seleccione el punto de recuperación. El sistema busca las copias de seguridad específicas que estén disponibles en ese punto de recuperación. Seleccione el punto de recuperación que quiere usar.

1. En la página **Elegir cómo restaurar la copia de seguridad**, seleccione **Formatear y volver a particionar discos**. En la página siguiente, compruebe la configuración.

1. Para comenzar la restauración, seleccione **Finalizar**. Es necesario reiniciar.

### <a name="restore-system-state"></a>Restaurar el estado del sistema

Para ejecutar la recuperación en Backup Server:

1. En el panel **Recuperación**, busque el equipo que quiere recuperar y seleccione **Reconstrucción completa**.

1. Los puntos de recuperación disponibles se indican en negrita en el calendario. Seleccione la fecha y hora para el punto de recuperación que quiere usar.

1. En la página **Seleccionar tipo de recuperación**, elija **Copiar en una carpeta de red**.

1. En la página **Especificar destino**, seleccione dónde copiar los datos.

    Recuerde que el destino que seleccione debe tener espacio suficiente para los datos. Se recomienda crear una nueva carpeta para el destino.

1. En la página **Especificar opciones de recuperación**, seleccione la configuración de seguridad. Después, seleccione si quiere usar instantáneas de hardware basadas en SAN para una recuperación más rápida. Esta opción solo está disponible si:
    * Tiene una SAN que proporciona esta funcionalidad.
    * Puede crear y dividir un clon para que se pueda escribir en él.
    * El equipo protegido y el servidor con Backup Server están conectados a la misma red.

1. Configure las opciones de notificación.
1. En la página **Confirmación**, seleccione **Recuperar**.

Para ejecutar Copias de seguridad de Windows Server:

1. Seleccione **Acciones** > **Recuperar** > **Este servidor** > **Siguiente**.

1. Seleccione **Otro servidor**, **Especificar tipo de ubicación** y **Carpeta compartida remota**. Escriba la ruta de acceso a la carpeta que contiene el punto de recuperación.

1. En la página **Seleccionar tipo de recuperación**, elija **Estado del sistema**.

1. En la página **Seleccione la ubicación de la recuperación de estado del sistema**, seleccione **Ubicación original**.

1. En la página **Confirmación**, seleccione **Recuperar**.

1. Después de la restauración, reinicie el servidor.

También puede ejecutar la restauración del estado del sistema en un símbolo del sistema:

1. Inicie Copias de seguridad de Windows Server en el equipo que quiere recuperar.

1. Para obtener el identificador de versión, en un símbolo del sistema, escriba:

   `wbadmin get versions -backuptarget \<servername\sharename\>`

1. Use el identificador de versión para iniciar la restauración del estado del sistema. En el símbolo del sistema, escriba:

   `wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>`

1. Confirme que quiere iniciar la recuperación. Puede ver el proceso en la ventana del símbolo del sistema. Se crea un registro de restauración.

1. Después de la restauración, reinicie el servidor.
