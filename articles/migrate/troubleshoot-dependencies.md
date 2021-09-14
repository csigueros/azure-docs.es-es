---
title: Solución de problemas con el análisis de dependencias tanto sin agente como basado en agentes
description: Obtenga ayuda para visualizar las dependencias en Azure Migrate.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: 671820e4c3ca3a4b8069f6be6d2a884a97da0739
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123535642"
---
# <a name="troubleshoot-dependency-visualization"></a>Solución de problemas de visualización de dependencias

Este artículo ayuda a solucionar problemas relacionados con el análisis de dependencias tanto sin agente como basado en agentes, que _solo disponible para servidores de VMware_. [Obtenga más información](concepts-dependency-visualization.md) sobre los tipos de visualización de dependencia admitidos en Azure Migrate.

## <a name="visualize-dependencies-for-1-hour-with-agentless-dependency-analysis"></a>Visualización de dependencias durante más de una hora con el análisis de dependencias sin agente

Con el análisis de dependencia sin agente, puede visualizar dependencias o exportarlas en un mapa para una duración de hasta 30 días.

## <a name="visualize-dependencies-for-10-servers-with-agentless-dependency-analysis"></a>Visualización de dependencias para más de 10 servidores con el análisis de dependencias sin agente

Azure Migrate ofrece una plantilla de Power BI que puede usar para visualizar las conexiones de red de muchos servidores a la vez, así como para filtrar por proceso y servidor. [Más información](how-to-create-group-machine-dependencies-agentless.md#visualize-network-connections-in-power-bi) sobre cómo visualizar las dependencias de muchos servidores juntos.

## <a name="dependencies-export-csv-shows-unknown-process-with-agentless-dependency-analysis"></a>El archivo de exportación de dependencias muestra "Proceso desconocido con el análisis de dependencias sin agente
En el análisis de dependencias sin agente, los nombres de los procesos se capturan de la mejor manera posible. En ciertos escenarios, aunque se capturan los nombres de los servidores de origen y destino y el puerto de destino, no es factible determinar los nombres de los procesos en ambos extremos de la dependencia. En tales casos, el proceso se marca como "Proceso desconocido".

## <a name="unable-to-export-dependency-data-in-a-csv-due-to-the-error-403-this-request-is-not-authorized-to-perform-this-operation"></a>No se pueden exportar los datos de dependencias en un archivo .csv debido al error "403: Esta solicitud no está autorizada para realizar esta operación"
Si el proyecto de Azure Migrate tiene conectividad de punto de conexión privado, la solicitud para exportar los datos de dependencias se debe iniciar desde un cliente conectado a la red virtual de Azure mediante una red privada. Para resolver este error, abra Azure Portal en la red local o en el servidor del dispositivo e intente exportar de nuevo.

## <a name="common-agentless-dependency-analysis-errors"></a>Errores comunes en el análisis de dependencias sin agente

Azure Migrate admite el análisis de dependencias sin agente mediante Azure Migrate: Discovery and assessment (Detección y evaluación). Actualmente, el análisis de dependencias sin agente solo se admite para VMware. [Obtenga más información](how-to-create-group-machine-dependencies-agentless.md) sobre los requisitos para el análisis de dependencias sin agente.

En la tabla siguiente, se resume la lista de errores del análisis de dependencias sin agente.

> [!Note]
> También se pueden encontrar los mismos errores con el inventario de software, ya que sigue la misma metodología que el análisis de dependencias sin agente para recopilar los datos necesarios.

| **Error** | **Causa** | **Acción** |
|--|--|--|
| **9000**: no se puede detectar el estado de las herramientas de VMware en el servidor. | Es posible que las herramientas de VMware no estén instaladas en el servidor o que la versión instalada esté dañada. | Asegúrese de que las herramientas de VMware posteriores a la versión 10.2.1 estén instaladas y en ejecución en el servidor. |
| **9001**: las herramientas de VMware no están instaladas en el servidor. | Es posible que las herramientas de VMware no estén instaladas en el servidor o que la versión instalada esté dañada. | Asegúrese de que las herramientas de VMware posteriores a la versión 10.2.1 estén instaladas y en ejecución en el servidor. |
| **9002**: las herramientas de VMware no están en ejecución en el servidor. | Es posible que las herramientas de VMware no estén instaladas en el servidor o que la versión instalada esté dañada. | Asegúrese de que las herramientas de VMware posteriores a la versión 10.2.0 estén instaladas y en ejecución en el servidor. |
| **9003**: no se admite el tipo de sistema operativo que se ejecuta en el servidor. | El sistema operativo que se ejecuta en el servidor no es Windows ni Linux. | Solo se admiten los tipos de SO Windows y Linux. Si el servidor es Windows o Linux, compruebe el tipo de sistema operativo especificado en vCenter Server. |
| **9004**: el servidor no está en estado de ejecución. | El servidor está apagado. | Asegúrese de que el servidor esté en ejecución. |
| **9005**: no se admite el tipo de sistema operativo que se ejecuta en el servidor. | El sistema operativo que se ejecuta en el servidor no es Windows ni Linux. | Solo se admiten los tipos de SO Windows y Linux. Actualmente, no se admite el sistema operativo \<FetchedParameter>. |
| **9006**: la dirección URL necesaria para descargar el archivo de metadatos de detección del servidor está vacía. | Este podría ser un problema transitorio porque el agente de detección del dispositivo no funciona según lo previsto. | El problema se debe resolver automáticamente en el ciclo siguiente en un plazo de 24 horas. Si el problema persiste, envíe un caso de soporte técnico de Microsoft. |
| **9007**: no se encuentra en el servidor el proceso que ejecuta el script para recopilar los metadatos. | Este podría ser un problema transitorio porque el agente de detección del dispositivo no funciona según lo previsto. | El problema se debe resolver automáticamente en el ciclo siguiente en un plazo de 24 horas. Si el problema persiste, envíe un caso de soporte técnico de Microsoft. |
| **9008**: no se puede recuperar el estado del proceso que se ejecuta en el servidor para recopilar los metadatos. | Este problema podría ser transitorio debido a un error interno. | El problema se debe resolver automáticamente en el ciclo siguiente en un plazo de 24 horas. Si el problema persiste, envíe un caso de soporte técnico de Microsoft. |
| **9009**: el Control de cuentas de usuario (UAC) de Windows impide la ejecución de operaciones de detección en el servidor. | La configuración del Control de cuentas de usuario de Windows restringe la detección desde el servidor de las aplicaciones instaladas. | En el servidor afectado, disminuya el nivel de la configuración de **Control de cuentas de usuario** en el Panel de control. |
| **9010**: el servidor está apagado. | El servidor está apagado. | Asegúrese de que el servidor esté encendido. |
| **9011**: no se encuentra en el servidor el archivo que contiene los metadatos detectados. | Este problema podría ser transitorio debido a un error interno. | El problema se debe resolver automáticamente en el ciclo siguiente en un plazo de 24 horas. Si el problema persiste, envíe un caso de soporte técnico de Microsoft. |
| **9012**: el archivo que contiene los metadatos detectados en el servidor está vacío. | Este problema podría ser transitorio debido a un error interno. | El problema se debe resolver automáticamente en el ciclo siguiente en un plazo de 24 horas. Si el problema persiste, envíe un caso de soporte técnico de Microsoft. |
| **9013**: cada vez que se inicia sesión en el servidor, se crea un perfil de usuario temporal nuevo. | Cada vez que se inicia sesión en el servidor, se crea un perfil de usuario temporal nuevo. | Envíe una incidencia de soporte técnico de Microsoft para recibir ayuda para solucionar este problema. |
| **9014**: no se puede recuperar el archivo que contiene los metadatos detectados debido a un error detectado en el host ESXi. Código de error: %ErrorCode; detalles: %ErrorMessage | Se encontró un error en el host ESXi \<HostName>. Código de error: %ErrorCode; detalles: %ErrorMessage. | Asegúrese de que el puerto 443 esté abierto en el host ESXi en el que se ejecuta el servidor.<br/><br/> [Obtenga más información](troubleshoot-dependencies.md#error-9014-httpgetrequesttoretrievefilefailed) sobre cómo corregir el problema.|
| **9015**: la cuenta de usuario de VCenter Server que se proporcionó para la detección de servidores no tiene habilitados los privilegios de operaciones de invitado. | Los privilegios necesarios para las operaciones de invitado no están habilitados en la cuenta de usuario de VCenter Server. | Asegúrese de que la cuenta de usuario de vCenter Server tenga habilitados los privilegios para **Máquinas virtuales** > **Operaciones de invitado** para interactuar con el servidor y extraer los datos necesarios. <br/><br/> [Obtenga más información](tutorial-discover-vmware.md#prepare-vmware) sobre cómo configurar la cuenta de vCenter Server con los privilegios necesarios. |
| **9016**: no se pueden detectar los metadatos porque el agente de operaciones de invitado del servidor no está actualizado. | Las herramientas de VMware no están instaladas en el servidor o la versión instalada no está actualizada. | Asegúrese de que las herramientas de VMware estén instaladas, actualizadas y en ejecución en el servidor. La versión de las herramientas de VMware debe ser la versión 10.2.1 o posterior. |
| **9017**: no se encuentra en el servidor el archivo que contiene los metadatos detectados. | Este podría ser un problema transitorio debido a un error interno. | Envíe una incidencia de soporte técnico de Microsoft para recibir ayuda para solucionar este problema. |
| **9018**: PowerShell no está instalado en el servidor. | PowerShell no se encuentra en el servidor. | Asegúrese de que PowerShell 2.0 o posterior esté instalado en el servidor. <br/><br/> [Más información](troubleshoot-dependencies.md#error-9018-powershellnotfound) sobre cómo corregir el problema.|
| **9019**: no se pueden detectar los metadatos porque hay errores de operaciones de invitado en el servidor. | No se han podido realizar las operaciones de invitado de VMware en el servidor. El problema se encontró al probar las credenciales siguientes en el servidor: \<FriendlyNameOfCredentials>. | Asegúrese de que las credenciales del servidor del dispositivo sean válidas y que el nombre de usuario de las credenciales esté en formato de nombre principal de usuario (UPN). (Busque en las posibles causas el nombre descriptivo de las credenciales que probó Azure Migrate). |
| **9020**: no se puede crear el archivo necesario para contener los metadatos detectados en el servidor. | El rol asociado a las credenciales proporcionadas en el dispositivo o una directiva de grupo local restringe la creación del archivo en la carpeta necesaria. El problema se encontró al probar las credenciales siguientes en el servidor: \<FriendlyNameOfCredentials>. | 1. Compruebe si las credenciales proporcionadas en el dispositivo tienen permiso de creación de archivos en la carpeta \<folder path/folder name> del servidor. <br/>2. Si las credenciales proporcionadas en el dispositivo no tienen los permisos necesarios, proporcione otro conjunto de credenciales o edite uno existente. (Busque en las posibles causas el nombre descriptivo de las credenciales que probó Azure Migrate). |
| **9021**: no se puede crear el archivo necesario para contener los metadatos detectados en la ruta de acceso correcta del servidor. | Las herramientas de VMware informan de una ruta de acceso de archivo incorrecta para crear el archivo. | Asegúrese de que las herramientas de VMware posteriores a la versión 10.2.0 estén instaladas y en ejecución en el servidor. |
| **9022**: se ha denegado el acceso para ejecutar el cmdlet Get-WmiObject en el servidor. | El rol asociado a las credenciales proporcionadas en el dispositivo o una directiva de grupo local restringe el acceso al objeto WMI. El problema se encontró al probar las credenciales siguientes en el servidor: \<FriendlyNameOfCredentials>. | 1. Compruebe si las credenciales proporcionadas en el dispositivo tienen privilegios de administrador para crear archivos y tienen WMI habilitado. <br/>2. Si las credenciales proporcionadas en el dispositivo no tienen los permisos necesarios, proporcione otro conjunto de credenciales o edite uno existente. (Busque en las posibles causas el nombre descriptivo de las credenciales que probó Azure Migrate).<br/><br/> [Obtenga más información](troubleshoot-dependencies.md#error-9022-getwmiobjectaccessdenied) sobre cómo corregir el problema.|
| **9023**: no se puede ejecutar PowerShell porque valor de la variable de entorno %SystemRoot% está vacío. | El valor de la variable de entorno %SystemRoot% está vacío para el servidor. | 1. Compruebe si la variable de entorno devuelve un valor vacío mediante la ejecución del comando echo %systemroot% en el servidor afectado. <br/>2.- Si el problema persiste, envíe un caso de soporte técnico de Microsoft. |
| **9024**: no se puede realizar la detección porque el valor de la variable de entorno %TEMP% está vacío. | El valor de la variable de entorno %TEMP% está vacío para el servidor. | 1. Compruebe si la variable de entorno devuelve un valor vacío mediante la ejecución del comando echo %temp% en el servidor afectado. <br/>2. Si el problema persiste, envíe una incidencia de soporte técnico de Microsoft. |
| **9025**: no se puede realizar la detección porque PowerShell está dañado en el servidor. | PowerShell está dañado en el servidor. | Vuelva a instalar PowerShell y compruebe que esté en ejecución en el servidor afectado. |
| **9026**: no se pueden ejecutar operaciones de invitado en el servidor. | El estado actual del servidor no permite que se ejecuten operaciones de invitado. | 1. Asegúrese de que el servidor afectado esté en ejecución.<br/>2. Si el problema persiste, envíe una incidencia de soporte técnico de Microsoft. |
| **9027**: no se pueden detectar los metadatos porque el agente de operaciones de invitado no está en ejecución en el servidor. | No es posible establecer contacto con el agente de operaciones de invitado en el servidor. | Asegúrese de que las herramientas de VMware posteriores a la versión 10.2.0 estén instaladas y en ejecución en el servidor. |
| **9028**: no se puede crear el archivo necesario para contener los metadatos detectados debido a almacenamiento insuficiente en el servidor. | No hay espacio de almacenamiento suficiente en el disco del servidor. | Asegúrese de que haya espacio disponible suficiente en el almacenamiento en disco del servidor afectado. |
| **9029**: las credenciales proporcionadas en el dispositivo no tienen permisos de acceso para ejecutar PowerShell. | Las credenciales del dispositivo no tienen permisos de acceso para ejecutar PowerShell. El problema se encontró al probar las credenciales siguientes en el servidor: \<FriendlyNameOfCredentials>. | 1. Asegúrese de que las credenciales del dispositivo puedan acceder a PowerShell en el servidor.<br/>2. Si las credenciales del dispositivo no tienen el acceso necesario, proporcione otro conjunto de credenciales o edite uno existente. (Busque en las posibles causas el nombre descriptivo de las credenciales que probó Azure Migrate). |
| **9030**: no se pueden recopilar los metadatos detectados porque el host ESXi en el que se hospeda el servidor está desconectado. | El host ESXi en el que reside el servidor está desconectado. | Asegúrese de que el host ESXi que ejecuta servidor esté conectado. |
| **9031**: no se pueden recopilar los metadatos detectados porque el host ESXi en el que se hospeda el servidor no responde. | El host ESXi en el que reside el servidor tiene un estado no válido. | Asegúrese de que el host ESXi que ejecuta el servidor esté conectado y en ejecución. |
| **9032**: no se puede realizar la detección debido a un error interno. | El problema que se encontró se debe a un error interno. | Siga los pasos de [este sitio web](troubleshoot-dependencies.md#error-9032-invalidrequest) para corregir el problema. Si el problema persiste, abra un caso de soporte técnico de Microsoft. |
| **9033**: no se puede realizar la detección porque el nombre de usuario de las credenciales proporcionadas en el dispositivo para el servidor tiene caracteres que no son válidos. | Las credenciales del dispositivo contienen caracteres no válidos en el nombre de usuario. El problema se encontró al probar las credenciales siguientes en el servidor: \<FriendlyNameOfCredentials>. | Asegúrese de que las credenciales del dispositivo no tengan caracteres no válidos en el nombre de usuario. Puede volver al administrador de configuración del dispositivo para editar las credenciales. (Busque en las posibles causas el nombre descriptivo de las credenciales que probó Azure Migrate). |
| **9034**: no se puede realizar la detección porque el nombre de usuario de las credenciales proporcionadas en el dispositivo para el servidor no está en formato UPN. | Las credenciales del dispositivo no tienen el nombre de usuario en formato UPN. El problema se encontró al probar las credenciales siguientes en el servidor: \<FriendlyNameOfCredentials>. | Asegúrese de que las credenciales del dispositivo tengan el nombre de usuario en formato UPN. Puede volver al administrador de configuración del dispositivo para editar las credenciales. (Busque en las posibles causas el nombre descriptivo de las credenciales que probó Azure Migrate). |
| **9035**: no se puede realizar la detección porque el modo de lenguaje de PowerShell no está configurado correctamente. | El modo de lenguaje de PowerShell no está establecido en **Lenguaje completo**. | Asegúrese de que el modo de lenguaje de PowerShell esté establecido en **Lenguaje completo**. |
| **9036**: no se puede realizar la detección porque el nombre de usuario de las credenciales proporcionadas en el dispositivo para el servidor no está en formato UPN. | Las credenciales del dispositivo no tienen el nombre de usuario en formato UPN. El problema se encontró al probar las credenciales siguientes en el servidor: \<FriendlyNameOfCredentials>. | Asegúrese de que las credenciales del dispositivo tengan el nombre de usuario en formato UPN. Puede volver al administrador de configuración del dispositivo para editar las credenciales. (Busque en las posibles causas el nombre descriptivo de las credenciales que probó Azure Migrate). |
| **9037**: la recopilación de metadatos está temporalmente en pausa debido al tiempo de respuesta elevado del servidor. | El servidor está tardando demasiado tiempo en responder. | El problema se debe resolver automáticamente en el ciclo siguiente en un plazo de 24 horas. Si el problema persiste, envíe un caso de soporte técnico de Microsoft. |
| **10000**: no se admite el tipo de sistema operativo que se ejecuta en el servidor. | El sistema operativo que se ejecuta en el servidor no es Windows ni Linux. | Solo se admiten los tipos de SO Windows y Linux. Actualmente, no se admite el sistema operativo \<GuestOSName>. |
| **10001**: no se encuentra en el servidor el script necesario para recopilar los metadatos de detección. | Es posible que el script necesario para realizar la detección se haya eliminado o quitado de la ubicación esperada. | Envíe una incidencia de soporte técnico de Microsoft para recibir ayuda para solucionar este problema. |
| **10002**: se agotó el tiempo de espera de las operaciones de detección en el servidor. | Este podría ser un problema transitorio porque el agente de detección del dispositivo no funciona según lo previsto. | El problema se debe resolver automáticamente en el ciclo siguiente en un plazo de 24 horas. Si no se soluciona, siga los pasos de [este sitio web](troubleshoot-dependencies.md#error-10002-scriptexecutiontimedoutonvm) para corregir el problema. Si el problema persiste, abra una incidencia de soporte técnico de Microsoft.|
| **10003**: el proceso que ejecuta las operaciones de detección se cerró con un error. | El proceso que ejecuta las operaciones de detección se cerró de manera abrupta debido a un error.| El problema se debe resolver automáticamente en el ciclo siguiente en un plazo de 24 horas. Si el problema persiste, envíe un caso de soporte técnico de Microsoft. |
| **10004**: no se proporcionaron credenciales en el dispositivo para el tipo de sistema operativo del servidor. | No se agregaron en el dispositivo las credenciales para el tipo de sistema operativo del servidor. | 1. Asegúrese de agregar en el dispositivo las credenciales para el tipo de sistema operativo del servidor afectado.<br/> 2. Ahora puede agregar varias credenciales de servidor en el dispositivo. |
| **10005**: las credenciales proporcionadas en el dispositivo para el servidor no son válidas. | Las credenciales proporcionadas en el dispositivo no son válidas. El problema se encontró al probar las credenciales siguientes en el servidor: \<FriendlyNameOfCredentials>. | 1. Asegúrese de que las credenciales proporcionadas en el dispositivo sean válidas y de que se pueda acceder al servidor con las credenciales.<br/> 2. Ahora puede agregar varias credenciales de servidor en el dispositivo.<br/> 3. Vuelva al administrador de configuración del dispositivo para proporcionar otro conjunto de credenciales o editar uno existente. (Busque en las posibles causas el nombre descriptivo de las credenciales que probó Azure Migrate). <br/><br/> [Más información](troubleshoot-dependencies.md#error-10005-guestcredentialnotvalid) sobre cómo corregir el problema.|
| **10006**: no se admite el tipo de sistema operativo que se ejecuta en el servidor. | El sistema operativo que se ejecuta en el servidor no es Windows ni Linux. | Solo se admiten los tipos de SO Windows y Linux. Actualmente, no se admite el sistema operativo \<GuestOSName>. |
| **10007**: no se pueden procesar los metadatos detectados desde el servidor. | Se ha producido un error al analizar el contenido del archivo que contiene los metadatos detectados. | Envíe una incidencia de soporte técnico de Microsoft para recibir ayuda para solucionar este problema. |
| **10008**: no se puede crear el archivo necesario para contener los metadatos detectados en el servidor. | El rol asociado a las credenciales proporcionadas en el dispositivo o una directiva de grupo local restringe la creación del archivo en la carpeta necesaria. El problema se encontró al probar las credenciales siguientes en el servidor: \<FriendlyNameOfCredentials>. | 1. Compruebe si las credenciales proporcionadas en el dispositivo tienen permiso de creación de archivos en la carpeta \<folder path/folder name> del servidor.<br/>2. Si las credenciales proporcionadas en el dispositivo no tienen los permisos necesarios, proporcione otro conjunto de credenciales o edite uno existente. (Busque en las posibles causas el nombre descriptivo de las credenciales que probó Azure Migrate). |
| **10009**: no se pueden escribir los metadatos detectados en el archivo del servidor. | El rol asociado a las credenciales proporcionadas en el dispositivo o una directiva de grupo local restringe la escritura en el archivo en el servidor. El problema se encontró al probar las credenciales siguientes en el servidor: \<FriendlyNameOfCredentials>. | 1. Compruebe si las credenciales proporcionadas en el dispositivo tienen permiso de escritura de archivos en la carpeta \<folder path/folder name> del servidor.<br/>2. Si las credenciales proporcionadas en el dispositivo no tienen los permisos necesarios, proporcione otro conjunto de credenciales o edite uno existente. (Busque en las posibles causas el nombre descriptivo de las credenciales que probó Azure Migrate). |
| **10010**: no se puede realizar la detección porque en el servidor falta el comando %CommandName; necesario para recopilar algunos metadatos. | El paquete que contiene el comando %CommandName; no está instalado en el servidor. | Asegúrese de que el paquete que contiene el comando %CommandName; esté instalado en el servidor. |
| **10011**: las credenciales proporcionadas en el dispositivo se usaron para iniciar y cerrar una sesión interactiva. | El inicio y cierre de una sesión interactiva fuerza la descarga de las claves del Registro en el perfil de la cuenta que se usa. Esta condición hace que las claves no estén disponibles para usarlas en el futuro. | Utilice los métodos de resolución que se documentan en [este sitio web](/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error#resolutionus/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error). |
| **10012**: no se han proporcionado credenciales en el dispositivo para el servidor. | No se han proporcionado credenciales para el servidor o se han proporcionado credenciales de dominio con un nombre de dominio incorrecto en el dispositivo. [Más información](troubleshoot-dependencies.md#error-10012-credentialnotprovided) sobre la causa de este error. | 1. Asegúrese de que se proporcionaron credenciales en el dispositivo para el servidor y de que se pueda acceder al servidor con las credenciales. <br/>2. Ahora puede agregar varias credenciales de servidores en el dispositivo. Vuelva al administrador de configuración del dispositivo para proporcionar las credenciales del servidor.|


## <a name="error-970-dependencymapinsufficientprivilegesexception"></a>Error 970: DependencyMapInsufficientPrivilegesException

### <a name="cause"></a>Causa
El error suele aparecer para servidores Linux cuando no se han proporcionado credenciales con los privilegios necesarios en el dispositivo.

### <a name="remediation"></a>Corrección
Tiene dos opciones:

- Asegúrese de haber proporcionado una cuenta de usuario raíz.
- Asegúrese de que la cuenta tenga estos permisos en los archivos /bin/netstat y /bin/ls:
   - CAP_DAC_READ_SEARCH
   - CAP_SYS_PTRACE

Para comprobar si la cuenta de usuario proporcionada en el dispositivo tiene los privilegios necesarios:

1. Inicie sesión en el servidor en el que encontró este error con la misma cuenta de usuario que se menciona en el mensaje de error.
1. Ejecute los siguientes comandos en un shell de Azure. Si no tiene los privilegios necesarios para el análisis de dependencias sin agente, recibirá errores.

   ````
   ps -o pid,cmd | grep -v ]$
   netstat -atnp | awk '{print $4,$5,$7}'
   ````
1. Establezca los permisos necesarios en los archivos /bin/netstat y /bin/ls mediante la ejecución de estos comandos:

   ````
   sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls
   sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat
   ````
1. Puede validar si los comandos anteriores asignaron o no los permisos necesarios a la cuenta de usuario.

   ````
   getcap /usr/bin/ls
   getcap /usr/bin/netstat
   ````
1. Vuelva a ejecutar los comandos proporcionados en el paso 2 para obtener una salida correcta.


## <a name="error-9014-httpgetrequesttoretrievefilefailed"></a>Error 9014: HTTPGetRequestToRetrieveFileFailed

### <a name="cause"></a>Causa
El problema se produce cuando el agente de detección de VMware del dispositivo intenta descargar el archivo de salida que contiene los datos de dependencias del sistema de archivos del servidor mediante el host ESXi en el que se hospeda el servidor.

### <a name="remediation"></a>Corrección
- Puede probar la conectividad TCP con el host ESXi _(nombre proporcionado en el mensaje de error)_ en el puerto 443 (debe estar abierto en hosts ESXi para extraer los datos de dependencias) desde el dispositivo. Abra PowerShell en el servidor del dispositivo y ejecute el comando siguiente:
    ````
    Test -NetConnection -ComputeName <Ip address of the ESXi host> -Port 443
    ````

- Si el comando devuelve una conectividad correcta, vaya al **proyecto de Azure Migrate** > **Discovery and assessment** > **Overview** > **Manage** > **Appliances** (Detección y evaluación > Información general > Administrar > Dispositivos), seleccione el nombre del dispositivo y, luego, **Refresh services** (Actualizar servicios).

## <a name="error-9018-powershellnotfound"></a>Error 9018: PowerShellNotFound

### <a name="cause"></a>Causa
El error suele aparecer para servidores que ejecutan Windows Server 2008 o una versión anterior.

### <a name="remediation"></a>Corrección
Instale la versión de PowerShell necesaria (2.0 o posterior) en esta ubicación del servidor: ($SYSTEMROOT)\System32\WindowsPowershell\v1.0\powershell.exe. [Más información](/powershell/scripting/windows-powershell/install/installing-windows-powershell) sobre cómo instalar PowerShell en Windows Server.

Después de instalar la versión de PowerShell necesaria, compruebe si se resolvió el error; para ello, siga los pasos que se indican en [este sitio web](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli).

## <a name="error-9022-getwmiobjectaccessdenied"></a>Error 9022: GetWMIObjectAccessDenied

### <a name="remediation"></a>Corrección
Asegúrese de que la cuenta de usuario proporcionada en el dispositivo tenga acceso al espacio de nombres de WMI y a los espacios de nombres secundarios. Para establecer el acceso:

1.  Vaya al servidor que informa de este error.
1.  Busque y seleccione **Ejecutar** en el menú **Inicio**. En el cuadro de diálogo **Ejecutar**, escriba **wmimgmt.msc** en el cuadro de texto **Abrir** y seleccione **Entrar**.
1.  Se abre la consola wmimgmt, en la que puede encontrar **Control WMI (local)** en el panel izquierdo. Haga clic con el botón derecho y seleccione **Propiedades** en el menú.
1.  En el cuadro de diálogo **Propiedades: Control WMI (local)** , seleccione la pestaña **Seguridad**.
1.  En la pestaña **Seguridad**, seleccione la opción **Seguridad** para abrir el cuadro de diálogo **Seguridad para Root**.
1.  Seleccione el botón **Opciones avanzadas** para abrir el cuadro de diálogo **Configuración de seguridad avanzada para Root**. 
1.  Seleccione el botón **Agregar** para abrir el cuadro de diálogo **Entrada de permiso para Root**.
1.  Haga clic en **Seleccionar una entidad de seguridad** para abrir el cuadro de diálogo **Seleccionar usuarios, equipos, cuentas de servicio o grupos**.
1. Seleccione los nombres de usuario o grupos a los que desea conceder acceso a WMI y haga clic en **Aceptar**.
1. Asegúrese de conceder permisos de ejecución y seleccione **Este espacio de nombres y espacios de subnombres** en la lista desplegable **Se aplica a**.
1. Seleccione **Aplicar** para guardar la configuración y cerrar todos los cuadros de diálogo.

Después de obtener el acceso necesario, compruebe si se resolvió el error; para ello, siga los pasos que se indican en [este sitio web](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli).

## <a name="error-9032-invalidrequest"></a>Error 9032: InvalidRequest

### <a name="cause"></a>Causa
Los motivos de este problema pueden ser varios. Una razón es cuando el nombre de usuario proporcionado (credenciales del servidor) en el administrador de configuración del dispositivo tiene caracteres XML no válidos. Los caracteres no válidos provocan un error al analizar la solicitud SOAP.

### <a name="remediation"></a>Corrección
- Asegúrese de que el nombre de usuario de las credenciales del servidor no tenga caracteres XML no válidos y esté en formato username@domain.com. Este formato se conoce popularmente como formato UPN.
- Después de editar las credenciales en el dispositivo, compruebe si se resolvió el error; para ello, siga los pasos que se indican en [este sitio web](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli).


## <a name="error-10002-scriptexecutiontimedoutonvm"></a>Error 10002: ScriptExecutionTimedOutOnVm

### <a name="cause"></a>Causa
- Este error se produce cuando el servidor funciona lento o no responde y se empieza a agotar el tiempo de espera del script ejecutado para extraer los datos de dependencias.
- Una vez que el agente de detección encuentra este error en el servidor, el dispositivo deja de intentar realizar el análisis de dependencias sin agente en el servidor a fin de evitar sobrecargar el servidor que no responde.
- Seguirá viendo el error hasta que compruebe el problema con el servidor y reinicie el servicio de detección.

### <a name="remediation"></a>Corrección
1. Inicie sesión en el servidor en el que se produce este error.
1. Ejecute los siguientes comandos en PowerShell:
   ````
   Get-WMIObject win32_operatingsystem;
   Get-WindowsFeature  | Where-Object {$_.InstallState -eq 'Installed' -or ($_.InstallState -eq $null -and $_.Installed -eq 'True')};
   Get-WmiObject Win32_Process;
   netstat -ano -p tcp | select -Skip 4;
   ````
1. Si los comandos generan el resultado en unos segundos, vaya al **proyecto de Azure Migrate** > **Discovery and assessment** > **Overview** > **Manage** > **Appliances** (Detección y evaluación > Información general > Administrar > Dispositivos), seleccione el nombre del dispositivo y, luego, **Refresh services** (Actualizar servicios).
1. Si se está agotando el tiempo de espera de los comandos sin generar ninguna salida, debe realizar lo siguiente:

   - Averiguar qué procesos consumen una gran cantidad de memoria o CPU en el servidor.
   - Intentar proporcionar más núcleos o memoria a ese servidor y volver a ejecutar los comandos.

## <a name="error-10005-guestcredentialnotvalid"></a>Error 10005: GuestCredentialNotValid

### <a name="remediation"></a>Corrección
- Para garantizar la validez de las credenciales _(el nombre descriptivo que se proporciona en el error)_ , seleccione **Revalidate credentials** (Revalidar credenciales) en el administrador de configuración del dispositivo.
- Asegúrese de que puede iniciar sesión en el servidor afectado con la misma credencial proporcionada en el dispositivo.
- Puede intentar usar otra cuenta de usuario (del mismo dominio, en caso de que el servidor esté unido a un dominio) de ese servidor en lugar de la cuenta de administrador.
- El problema puede producirse cuando la comunicación entre el catálogo global y el controlador de dominio está rota. Para comprobar este problema, cree una nueva cuenta de usuario en el controlador de dominio y proporcione esta cuenta en el dispositivo. Es posible que también tenga que reiniciar el controlador de dominio.
- Después de llevar a cabo los pasos de corrección, compruebe si se resolvió el error; para ello, siga los pasos que se indican en [este sitio web](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli).

## <a name="error-10012-credentialnotprovided"></a>Error 10012: CredentialNotProvided

### <a name="cause"></a>Causa
Este error se produce cuando se ha proporcionado una credencial de dominio con un nombre de dominio incorrecto en el administrador de configuración del dispositivo. Por ejemplo, si ha proporcionado credenciales de dominio con nombre de usuario user@abc.com, pero ha proporcionado el nombre de dominio como def.com, esas credenciales no se utilizarán si el servidor está conectado a def.com y recibirá este mensaje de error.

### <a name="remediation"></a>Corrección
- Vaya al administrador de configuración del dispositivo para agregar una credencial de servidor o editar una existente, tal como se explica en la causa.
- Después de llevar a cabo los pasos de corrección, compruebe si se resolvió el error; para ello, siga los pasos que se indican en [este sitio web](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli).

## <a name="mitigation-verification-by-using-vmware-powercli"></a>Comprobación de la mitigación con VMware PowerCLI

Después de usar los pasos de mitigación para los errores anteriores, compruebe si la mitigación ha funcionado mediante la ejecución de algunos comandos de PowerCLI desde el servidor del dispositivo. Si los comandos se ejecutan correctamente, significa que el problema se ha resuelto. De lo contrario, compruebe y siga los pasos de corrección de nuevo.

1. Ejecute los comandos siguientes para configurar PowerCLI en el servidor del dispositivo:
   ````
   Install-Module -Name VMware.PowerCLI -AllowClobber
   Set-PowerCLIConfiguration -InvalidCertificateAction Ignore
   ````
1. Conéctese a vCenter Server desde el dispositivo; para ello, proporcione la dirección IP de vCenter Server en el comando y las credenciales en el símbolo del sistema:
   ````
   Connect-VIServer -Server <IPAddress of vCenter Server>
   ````
1. Conéctese al servidor de destino desde el dispositivo; para ello, proporcione el nombre y las credenciales del servidor, tal como se han proporcionado en el dispositivo:
   ````
   $vm = get-VM <VMName>
   $credential = Get-Credential
   ````
1. Para el análisis de dependencias sin agente, ejecute los siguientes comandos para ver si obtiene una salida correcta.

      - Para servidores Windows:
    
          ```` 
          Invoke-VMScript -VM $vm -ScriptText "powershell.exe 'Get-WmiObject Win32_Process'" -GuestCredential $credential 
      
          Invoke-VMScript -VM $vm -ScriptText "powershell.exe 'netstat -ano -p tcp'" -GuestCredential $credential 
          ```` 
      - Para servidores Linux:
          ````
          Invoke-VMScript -VM $vm -ScriptText "ps -o pid,cmd | grep -v ]$" -GuestCredential $credential
    
          Invoke-VMScript -VM $vm -ScriptText "netstat -atnp | awk '{print $4,$5,$7}'" -GuestCredential $credential
          ````
1. Después de comprobar que la mitigación funcionó, vaya al **proyecto de Azure Migrate** > **Discovery and assessment** > **Overview** > **Manage** > **Appliances** (Detección y evaluación > Información general > Administrar > Dispositivos), seleccione el nombre del dispositivo y, luego, **Refresh services** (Actualizar servicios) para iniciar un ciclo de detección nuevo.

## <a name="my-log-analytics-workspace-isnt-listed-when-you-try-to-configure-the-workspace-in-azure-migrate-for-agent-based-dependency-analysis"></a>Mi área de trabajo de Log Analytics no aparece al intentar configurar el área de trabajo en Azure Migrate para el análisis de dependencias basado en agentes
Actualmente, Azure Migrate admite la creación de un área de trabajo de OMS en las regiones Este de EE. UU., Sudeste de Asia y Oeste de Europa. Si el área de trabajo se crea fuera de Azure Migrate en cualquier otra región, actualmente no se puede asociar a un proyecto.

## <a name="agent-based-dependency-visualization-in-azure-government"></a>Visualización de dependencias basada en agente en Azure Government

El análisis de dependencias basado en agente no está disponible en Azure Government. Utilice el análisis de dependencias sin agente, que _solo está disponible para servidores de VMware_.

## <a name="agent-based-dependencies-dont-show-after-agent-installation"></a>No se muestran las dependencias basadas en agente después de la instalación del agente

Después de instalar los agentes de visualización de dependencias en las máquinas virtuales locales, Azure Migrate suele tardar entre 15 y 30 minutos en mostrar las dependencias en el portal. Si ha esperado durante más de 30 minutos, asegúrese de que Microsoft Monitoring Agent (MMA) puede conectarse al área de trabajo de Log Analytics.

Para las máquinas virtuales de Windows:
1. En el Panel de control, inicie MMA.
1. En **Propiedades de Microsoft Monitoring Agent** > **Azure Log Analytics (OMS)** , asegúrese de que el **estado** del área de trabajo esté en color verde.
1. Si el estado no está en verde, intente quitar el área de trabajo y vuelva a agregarla a MMA.

    ![Captura de pantalla que muestra el estado de MMA.](./media/troubleshoot-assessment/mma-properties.png)

Para las máquinas virtuales Linux, asegúrese de que se hayan completado correctamente los comandos de instalación de MMA y el agente de dependencias. Consulte otra guía de solución de problemas en [este sitio web](../azure-monitor/vm/service-map.md#post-installation-issues).

## <a name="supported-operating-systems-for-agent-based-dependency-analysis"></a>Sistemas operativos admitidos para el análisis de dependencias basado en agentes

- **Agente de MMS**: sistemas operativos [Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems) y [Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems) admitidos.
- **Agente de dependencias**: sistemas operativos [Windows y Linux](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) admitidos.

## <a name="visualize-dependencies-for-1-hour-with-agent-based-dependency-analysis"></a>Visualización de dependencias durante más de una hora con el análisis de dependencias basado en agentes

Con el análisis de dependencias basado en agentes, aunque Azure Migrate le permita volver atrás a una fecha determinada en el último mes, la duración máxima para la que se pueden visualizar las dependencias es de una hora. Por ejemplo, puede usar la característica de duración de tiempo en el mapa de dependencias para ver las dependencias de ayer, pero solo puede verlas durante un período de una hora. Puede usar los registros de Azure Monitor para [consultar los datos de dependencias](./how-to-create-group-machine-dependencies.md) durante un período más largo.

## <a name="visualize-dependencies-for-10-servers-with-agent-based-dependency-analysis"></a>Visualización de dependencias para más de 10 servidores con el análisis de dependencias basado en agentes

En Azure Migrate, puede [visualizar las dependencias para grupos](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) que tengan un máximo de 10 máquinas virtuales con un análisis de dependencia basado en agentes. En el caso de grupos más grandes, divida las máquinas virtuales en grupos más pequeños para visualizar las dependencias.

## <a name="servers-show-install-agent-for-agent-based-dependency-analysis"></a>Los servidores muestran "Instalar agente" para el análisis de dependencias basado en agentes

Después de migrar los servidores con la visualización de dependencias habilitada en Azure, es posible que los servidores muestren la acción **Instalar agente** en lugar de **Ver dependencias** debido al comportamiento siguiente:

- Después de la migración a Azure, los servidores locales están desconectados y las máquinas virtuales equivalentes trabajan en Azure. Estos servidores adquieren una dirección MAC diferente.
- Los servidores también pueden adquirir una dirección IP distinta en función de si se elige conservar o no la dirección IP local.
- Si las direcciones IP y MAC difieren de las locales, Azure Migrate no asocia los servidores locales con ningún dato de dependencias de Service Map. En este caso, se muestra la opción de instalar el agente en lugar de ver las dependencias.
- Después de una migración de prueba a Azure, los servidores locales permanecen encendidos según lo previsto. Los servidores equivalentes que se activan en Azure adquieren una dirección MAC distinta y pueden adquirir una dirección IP diferente. A menos que bloquee el tráfico saliente del registro de Azure Monitor procedente de estos servidores, Azure Migrate no asociará los servidores locales con ningún dato de dependencias de Service Map. En este caso, muestra la opción de instalar agentes en lugar de ver las dependencias.

## <a name="capture-network-traffic"></a>Captura del tráfico de red

Para recopilar registros del tráfico de red:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione F12 para iniciar las Herramientas de desarrollo. Si es necesario, desactive la opción **Borrar entradas de navegación**.
1. Seleccione la pestaña **Red** para empezar a capturar el tráfico de red:
   - En Chrome, seleccione **Preserve log** (Conservar registro). La grabación debe iniciarse automáticamente. Un círculo rojo indica que la captura del tráfico está en curso. Si no aparece el círculo rojo, seleccione el círculo negro para comenzar.
   - En Microsoft Edge e Internet Explorer, la grabación se debe iniciar automáticamente. Si no es así, seleccione el botón de reproducción de color verde.
1. Pruebe a reproducir el error.
1. Una vez que haya encontrado el error durante la grabación, detenga la grabación y guarde una copia de la actividad grabada:
   - En Chrome, haga clic con el botón derecho y seleccione **Guardar como HAR con contenido**. Esta acción comprime y exporta los registros como un archivo HTTP (har).
   - En Microsoft Edge o Internet Explorer, seleccione la opción **Exportar tráfico capturado**. Esta acción comprime y exporta el registro.
1. Seleccione la pestaña **Consola** para ver si hay alguna advertencia o error. Para guardar el registro de la consola:
   - En Chrome, haga clic con el botón derecho en cualquier lugar en el registro de la consola. Seleccione **Guardar como** para exportar y comprimir el registro.
   - En Microsoft Edge o Internet Explorer, haga clic con el botón derecho sobre los errores y seleccione **Copiar todo**.
1. Cierre las Herramientas de desarrollo.


## <a name="next-steps"></a>Pasos siguientes

[Crear](how-to-create-assessment.md) o [personalizar](how-to-modify-assessment.md) una evaluación.
