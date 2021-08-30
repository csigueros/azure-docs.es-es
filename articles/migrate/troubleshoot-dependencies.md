---
title: Solución de problemas con el análisis de dependencias tanto sin agente como basado en agentes
description: Obtenga ayuda para visualizar las dependencias en Azure Migrate.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: 7e618052a71f73c5559860fa09b495e9be2a14d9
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114290970"
---
# <a name="troubleshoot-dependency-visualization"></a>Solución de problemas de visualización de dependencias

Este artículo lo ayuda a solucionar problemas relacionados con el análisis de dependencias tanto sin agente como basado en agentes _(solo disponible para servidores de VMware)_ . [Obtenga más información](concepts-dependency-visualization.md) sobre los tipos de visualización de dependencia admitidos en Azure Migrate.


## <a name="visualize-dependencies-for--1-hour-with-agentless-dependency-analysis"></a>Visualización de dependencias durante más de una hora con análisis de dependencias sin agente

Con el análisis de dependencia sin agente, puede visualizar dependencias o exportarlas en un mapa para una duración de hasta 30 días.

## <a name="visualized-dependencies-for--10-servers-with-agentless-dependency-analysis"></a>Dependencias visualizadas para más de 10 servidores con análisis de dependencias sin agente

Azure Migrate ofrece una plantilla de Power BI que puede usar para visualizar las conexiones de red de muchos servidores a la vez, así como para filtrar por proceso y servidor. [Obtenga más información](how-to-create-group-machine-dependencies-agentless.md#visualize-network-connections-in-power-bi) sobre cómo visualizar las dependencias de muchos servidores juntos.

## <a name="dependencies-export-csv-shows-unknown-process-with-agentless-dependency-analysis"></a>El archivo de exportación de dependencias muestra "Proceso desconocido con el análisis de dependencias sin agente
En el análisis de dependencias sin agente, los nombres de los procesos se capturan de la mejor manera posible. En ciertos escenarios, aunque los nombres de los servidores de origen y destino y el puerto de destino se capturen, no es factible determinar los nombres de los procesos en ambos extremos de la dependencia. En tales casos, el proceso se marca como "_Proceso desconocido_".

## <a name="common-agentless-dependency-analysis-errors"></a>Errores comunes en el análisis de dependencias sin agente

Azure Migrate admite el análisis de dependencias sin agente mediante Azure Migrate: Detección y evaluación. Actualmente, el análisis de dependencias sin agente solo se admite para VMware. [Obtenga más información](how-to-create-group-machine-dependencies-agentless.md) sobre los requisitos para el análisis de dependencias sin agente.

En la tabla siguiente se resume la lista de errores en el análisis de dependencias sin agente.

> [!Note]
> También se pueden encontrar los mismos errores con el inventario de software, ya que sigue la misma metodología que el análisis de dependencias sin agente para recopilar los datos necesarios.

| **Error** | **Causa** | **Acción** |
|--|--|--|
| **9000:** No se puede detectar el estado de las herramientas de VMware en el servidor | Es posible que las herramientas de VMware no estén instaladas en el servidor o que la versión instalada esté dañada. | Asegúrese de que las herramientas de VMware posteriores a la versión 10.2.1 estén instaladas y en ejecución en el servidor. |
| **9001:** Las herramientas de VMware no están instaladas en el servidor. | Es posible que las herramientas de VMware no estén instaladas en el servidor o que la versión instalada esté dañada. | Asegúrese de que las herramientas de VMware posteriores a la versión 10.2.1 estén instaladas y en ejecución en el servidor. |
| **9002:** Las herramientas de VMware no se ejecutan en el servidor. | Es posible que las herramientas de VMware no estén instaladas en el servidor o que la versión instalada esté dañada. | Asegúrese de que las herramientas de VMware posteriores a la versión 10.2.0 estén instaladas y en ejecución en el servidor. |
| **9003:** No se admite el tipo de sistema operativo que se ejecuta en el servidor. | El sistema operativo que se ejecuta en el servidor no es Windows ni Linux. | Solo se admiten los tipos de SO Windows y Linux. Si el servidor es Windows o Linux, compruebe el tipo de sistema operativo especificado en vCenter Server. |
| **9004:** El servidor no está en ejecución. | El servidor está apagado. | Asegúrese de que el servidor esté en ejecución. |
| **9005:** No se admite el tipo de sistema operativo que se ejecuta en el servidor. | El sistema operativo que se ejecuta en el servidor no es Windows ni Linux. | Solo se admiten los tipos de SO Windows y Linux. Actualmente no se admite el sistema operativo \<FetchedParameter>. |
| **9006:** La dirección URL necesaria para descargar el archivo de metadatos de detección del servidor está vacía. | Este podría ser un problema transitorio debido a que el agente de detección en el dispositivo no funciona según lo previsto. | El problema se debe resolver automáticamente en el ciclo siguiente en un plazo de 24 horas. Si el problema persiste, envíe un caso de soporte técnico de Microsoft. |
| **9007:** El proceso que ejecuta el script para recopilar los metadatos no se encuentra en el servidor. | Este podría ser un problema transitorio debido a que el agente de detección en el dispositivo no funciona según lo previsto. | El problema se debe resolver automáticamente en el ciclo siguiente en un plazo de 24 horas. Si el problema persiste, envíe un caso de soporte técnico de Microsoft. |
| **9008:** No se puede recuperar el estado del proceso que se ejecuta en el servidor para recopilar los metadatos. | Este podría ser un problema transitorio debido a un error interno. | El problema se debe resolver automáticamente en el ciclo siguiente en un plazo de 24 horas. Si el problema persiste, envíe un caso de soporte técnico de Microsoft. |
| **9009:** El Control de cuentas de usuario (UAC) de Windows impide la ejecución de operaciones de detección en el servidor. | La configuración del Control de cuentas de usuario (UAC) de Windows restringe la detección de las aplicaciones instaladas desde el servidor. | En el servidor afectado, disminuya el nivel de la configuración de "Control de cuentas de usuario" en el Panel de control. |
| **9010:** El servidor está apagado. | El servidor está apagado. | Asegúrese de que el servidor esté encendido. |
| **9011:** El archivo que contiene los metadatos detectados no se encuentra en el servidor. | Este podría ser un problema transitorio debido a un error interno. | El problema se debe resolver automáticamente en el ciclo siguiente en un plazo de 24 horas. Si el problema persiste, envíe un caso de soporte técnico de Microsoft. |
| **9012:** El archivo que contiene los metadatos detectados en el servidor está vacío. | Este podría ser un problema transitorio debido a un error interno. | El problema se debe resolver automáticamente en el ciclo siguiente en un plazo de 24 horas. Si el problema persiste, envíe un caso de soporte técnico de Microsoft. |
| **9013:** Cada vez que se inicia sesión en el servidor, se crea un perfil de usuario temporal nuevo. | Cada vez que se inicia sesión en el servidor, se crea un perfil de usuario temporal nuevo. | Envíe un caso de soporte técnico de Microsoft para recibir ayuda para solucionar este problema. |
| **9014:** No se puede recuperar el archivo que contiene los metadatos detectados debido a un error detectado en el host ESXi.  Código de error: %ErrorCode; detalles: %ErrorMessage | Se encontró un error en el host ESXi \<HostName>.  Código de error: %ErrorCode; detalles: %ErrorMessage | Asegúrese de que el puerto 443 esté abierto en el host ESXi en el que se ejecuta el servidor.<br/><br/> [Obtenga más información](troubleshoot-dependencies.md#error-9014-httpgetrequesttoretrievefilefailed) sobre cómo corregir el problema.|
| **9015:** La cuenta de usuario de VCenter Server que se proporcionó para la detección de servidores no tiene habilitados los privilegios de operaciones de invitado. | Los privilegios necesarios para las operaciones de invitado no están habilitados en la cuenta de usuario de VCenter Server. | Asegúrese de que la cuenta de usuario de vCenter Server tenga habilitados los privilegios para Máquinas virtuales > Operaciones de invitado a fin de interactuar con el servidor y extraer los datos necesarios. <br/><br/> [Obtenga más información](tutorial-discover-vmware.md#prepare-vmware) sobre cómo configurar la cuenta de vCenter Server con los privilegios necesarios. |
| **9016:** No se pueden detectar los metadatos porque el agente de operaciones de invitado en el servidor no está actualizado. | Las herramientas de VMware no están instaladas en el servidor o la versión instalada no está actualizada. | Asegúrese de que las herramientas de VMware estén instaladas, actualizadas y en ejecución en el servidor. La versión de las herramientas de VMware debe ser la versión 10.2.1 o posterior. |
| **9017:** El archivo que contiene los metadatos detectados no se encuentra en el servidor. | Este podría ser un problema transitorio debido a un error interno. | Envíe un caso de soporte técnico de Microsoft para recibir ayuda para solucionar este problema. |
| **9018:** PowerShell no está instalado en el servidor. | PowerShell no se encuentra en el servidor. | Asegúrese de que PowerShell 2.0 o posterior esté instalado en el servidor. <br/><br/> [Obtenga más información](troubleshoot-dependencies.md#error-9018-powershellnotfound) sobre cómo corregir el problema.|
| **9019**: No se pueden detectar los metadatos porque hay errores de operaciones de invitado en el servidor. | Error en las operaciones de invitado de VMware en el servidor. El problema se encontró al probar las credenciales siguientes en el servidor: <FriendlyNameOfCredentials>. | Asegúrese de que las credenciales del servidor proporcionadas en el dispositivo sean válidas y que el nombre de usuario proporcionado en las credenciales esté en formato UPN. (Busque en las posibles causas el nombre descriptivo de las credenciales que Azure Migrate probó) |
| **9020:** No se puede crear el archivo necesario para contener los metadatos detectados en el servidor. | El rol asociado a las credenciales proporcionadas en el dispositivo o una directiva de grupo local restringe la creación del archivo en la carpeta necesaria. El problema se encontró al probar las credenciales siguientes en el servidor: <FriendlyNameOfCredentials>. | 1. Compruebe si las credenciales proporcionadas en el dispositivo tienen permiso de creación de archivos en la carpeta \<folder path/folder name> del servidor. <br/>2. Si las credenciales proporcionadas en el dispositivo no tienen los permisos necesarios, proporcione otro conjunto de credenciales o edite uno existente. (Busque en las posibles causas el nombre descriptivo de las credenciales que Azure Migrate probó) |
| **9021:** No se puede crear el archivo necesario para contener los metadatos detectados en la ruta de acceso correcta en el servidor. | Las herramientas de VMware informan de una ruta de acceso de archivo incorrecta para crear el archivo. | Asegúrese de que las herramientas de VMware posteriores a la versión 10.2.0 estén instaladas y en ejecución en el servidor. |
| **9022:** Se deniega el acceso para ejecutar el cmdlet Get-WmiObject en el servidor. | El rol asociado a las credenciales proporcionadas en el dispositivo o una directiva de grupo local restringe el acceso al objeto WMI. El problema se encontró al probar las credenciales siguientes en el servidor: \<FriendlyNameOfCredentials>. | 1. Compruebe si las credenciales proporcionadas en el dispositivo tienen privilegios de administrador para crear archivos y tiene WMI habilitado. <br/> 2. Si las credenciales proporcionadas en el dispositivo no tienen los permisos necesarios, proporcione otro conjunto de credenciales o edite uno existente. (Busque en las posibles causas el nombre descriptivo de las credenciales que Azure Migrate probó).<br/><br/> [Obtenga más información](troubleshoot-dependencies.md#error-9022-getwmiobjectaccessdenied) sobre cómo corregir el problema.|
| **9023:** No se puede ejecutar PowerShell ya que valor de la variable de entorno %SystemRoot% está vacío. | El valor de la variable de entorno %SystemRoot% está vacío para el servidor. | 1. Compruebe si la variable de entorno devuelve un valor vacío mediante la ejecución del comando echo %systemroot% en el servidor afectado. <br/> 2.- Si el problema persiste, envíe un caso de soporte técnico de Microsoft. |
| **9024:** No se puede realizar la detección porque el valor de la variable de entorno %TEMP% está vacío. | El valor de la variable de entorno %TEMP% está vacío para el servidor. | 1. Compruebe si la variable de entorno devuelve un valor vacío mediante la ejecución del comando echo %temp% en el servidor afectado. <br/> 2.- Si el problema persiste, envíe un caso de soporte técnico de Microsoft. |
| **9025:** No se puede realizar la detección: PowerShell está dañado en el servidor. | PowerShell está dañado en el servidor. | Vuelva a instalar PowerShell y compruebe que está en ejecución en el servidor afectado. |
| **9026:** No se pueden ejecutar operaciones de invitado en el servidor. | El estado actual del servidor no permite que se ejecuten las operaciones de invitado. | 1. Asegúrese de que el servidor afectado esté en ejecución.<br/> 2.- Si el problema persiste, envíe un caso de soporte técnico de Microsoft. |
| **9027:** No se pueden detectar los metadatos porque el agente de operaciones de invitado no está en ejecución en el servidor. | No es posible establecer contacto con el agente de operaciones de invitado en el servidor. | Asegúrese de que las herramientas de VMware posteriores a la versión 10.2.0 estén instaladas y en ejecución en el servidor. |
| **9028:** No se puede crear el archivo necesario para contener los metadatos detectados debido a almacenamiento insuficiente en el servidor. | No hay espacio de almacenamiento suficiente en el disco del servidor. | Asegúrese de que haya espacio disponible suficiente en el almacenamiento en disco del servidor afectado. |
| **9029:** Las credenciales proporcionadas en el dispositivo no tienen permisos de acceso para ejecutar PowerShell. | Las credenciales proporcionadas en el dispositivo no tienen permisos de acceso para ejecutar PowerShell. El problema se encontró al probar las credenciales siguientes en el servidor: \<FriendlyNameOfCredentials>. | 1. Asegúrese de que las credenciales proporcionadas en el dispositivo puedan acceder a PowerShell en el servidor.<br/> 2. Si las credenciales proporcionadas en el dispositivo no tienen el acceso necesario, proporcione otro conjunto de credenciales o edite uno existente. (Busque en las posibles causas el nombre descriptivo de las credenciales que Azure Migrate probó) |
| **9030:** No se pueden recopilar los metadatos detectados porque el host ESXi en el que se hospeda el servidor está desconectado. | El host ESXi en el que reside el servidor está desconectado. | Asegúrese de que el host ESXi que ejecuta servidor esté conectado. |
| **9031:** No se pueden recopilar los metadatos detectados porque el host ESXi en el que se hospeda el servidor no responde. | El host ESXi en el que reside el servidor tiene un estado no válido. | Asegúrese de que el host ESXi que ejecuta el servidor esté conectado y en ejecución. |
| **9032:** No se puede detectar debido a un error interno. | El problema que se encontró se debe a un error interno. | Siga [estos pasos](troubleshoot-dependencies.md#error-9032-invalidrequest) para corregir el problema. Si el problema persiste, abra un caso de soporte técnico de Microsoft. |
| **9033:** No se puede detectar porque el nombre de usuario de las credenciales proporcionadas en el dispositivo para el servidor tiene caracteres que no son válidos. | Las credenciales proporcionadas en el dispositivo contienen caracteres no válidos en el nombre de usuario. El problema se encontró al probar las credenciales siguientes en el servidor: \<FriendlyNameOfCredentials>. | Asegúrese de que las credenciales proporcionadas en el dispositivo no tengan caracteres no válidos en el nombre de usuario. Puede volver al administrador de configuración del dispositivo para editar las credenciales. (Busque en las posibles causas el nombre descriptivo de las credenciales que Azure Migrate probó). |
| **9034:** No se puede detectar porque el nombre de usuario de las credenciales proporcionadas en el dispositivo para el servidor no está en formato UPN. | Las credenciales proporcionadas en el dispositivo no tienen el nombre de usuario en formato UPN. El problema se encontró al probar las credenciales siguientes en el servidor: \<FriendlyNameOfCredentials>. | Asegúrese de que las credenciales proporcionadas en el dispositivo tengan el nombre de usuario en el formato de nombre principal de usuario (UPN). Puede volver al administrador de configuración del dispositivo para editar las credenciales. (Busque en las posibles causas el nombre descriptivo de las credenciales que Azure Migrate probó). |
| **9035:** No se puede detectar porque el modo de lenguaje de PowerShell no está correctamente establecido. | El modo de lenguaje de PowerShell no está establecido en "Lenguaje completo". | Asegúrese de que el modo de lenguaje de PowerShell se estableció en 'Lenguaje completo'. |
| **9036:** No se puede detectar porque el nombre de usuario de las credenciales proporcionadas en el dispositivo para el servidor no está en formato UPN. | Las credenciales proporcionadas en el dispositivo no tienen el nombre de usuario en formato UPN. El problema se encontró al probar las credenciales siguientes en el servidor: \<FriendlyNameOfCredentials>. | Asegúrese de que las credenciales proporcionadas en el dispositivo tengan el nombre de usuario en el formato de nombre principal de usuario (UPN). Puede volver al administrador de configuración del dispositivo para editar las credenciales. (Busque en las posibles causas el nombre descriptivo de las credenciales que Azure Migrate probó). |
| **9037:** La colección de metadatos está temporalmente pausada debido al tiempo de respuesta elevado del servidor. | El servidor está tardando demasiado tiempo en responder. | El problema se debe resolver automáticamente en el ciclo siguiente en un plazo de 24 horas. Si el problema persiste, envíe un caso de soporte técnico de Microsoft. |
| **10000:** No se admite el tipo de sistema operativo que se ejecuta en el servidor. | El sistema operativo que se ejecuta en el servidor no es Windows ni Linux. | Solo se admiten los tipos de SO Windows y Linux. Actualmente no se admite el sistema operativo \<GuestOSName>. |
| **10001:** El script necesario para recopilar metadatos de detección no se encuentra en el servidor. | Es posible que el script necesario para realizar la detección se haya eliminado o quitado de la ubicación esperada. | Envíe un caso de soporte técnico de Microsoft para recibir ayuda para solucionar este problema. |
| **10002:** Se agotó el tiempo de espera de las operaciones de detección en el servidor. | Este podría ser un problema transitorio debido a que el agente de detección en el dispositivo no funciona según lo previsto. | El problema se debe resolver automáticamente en el ciclo siguiente en un plazo de 24 horas. Si el problema no se solucionó, siga [estos pasos](troubleshoot-dependencies.md#error-10002-scriptexecutiontimedoutonvm) para hacerlo. Si el problema persiste, abra un caso de soporte técnico de Microsoft.|
| **10003:** El proceso que ejecuta las operaciones de detección se cerró con un error. | El proceso que ejecuta las operaciones de detección se cerró de manera abrupta debido a un error.| El problema se debe resolver automáticamente en el ciclo siguiente en un plazo de 24 horas. Si el problema persiste, envíe un caso de soporte técnico de Microsoft. |
| **10004:** No se proporcionaron credenciales en el dispositivo para el tipo de SO del servidor. | Las credenciales para el tipo de SO del servidor no se agregaron en el dispositivo. | 1. Asegúrese de agregar las credenciales para el tipo de SO del servidor afectado en el dispositivo.<br/> 2. Ahora puede agregar varias credenciales de servidor en el dispositivo. |
| **10005**: Las credenciales proporcionadas en el dispositivo para el servidor no son válidas. | Las credenciales proporcionadas en el dispositivo no son válidas. El problema se encontró al probar las credenciales siguientes en el servidor: \<FriendlyNameOfCredentials>. | 1. Asegúrese de que las credenciales proporcionadas en el dispositivo sean válidas y de que se pueda acceder al servidor con las credenciales.<br/> 2. Ahora puede agregar varias credenciales de servidor en el dispositivo.<br/> 3. Vuelva al administrador de configuración del dispositivo para proporcionar otro conjunto de credenciales o editar uno existente. (Busque en las posibles causas el nombre descriptivo de las credenciales que Azure Migrate probó). <br/><br/> [Obtenga más información](troubleshoot-dependencies.md#error-10005-guestcredentialnotvalid) sobre cómo corregir el problema.|
| **10006:** No se admite el tipo de sistema operativo que se ejecuta en el servidor. | El sistema operativo que se ejecuta en el servidor no es Windows ni Linux. | Solo se admiten los tipos de SO Windows y Linux. Actualmente no se admite el sistema operativo \<GuestOSName>. |
| **10007:** No se pueden procesar los metadatos detectados desde el servidor. | Se detectó un error al analizar el contenido del archivo que contiene los metadatos detectados. | Envíe un caso de soporte técnico de Microsoft para recibir ayuda para solucionar este problema. |
| **10008:** No se puede crear el archivo necesario para contener los metadatos detectados en el servidor. | El rol asociado a las credenciales proporcionadas en el dispositivo o una directiva de grupo local restringe la creación del archivo en la carpeta necesaria. El problema se encontró al probar las credenciales siguientes en el servidor: <FriendlyNameOfCredentials>. | 1. Compruebe si las credenciales proporcionadas en el dispositivo tienen permiso de creación de archivos en la carpeta \<folder path/folder name> del servidor.<br/> 2. Si las credenciales proporcionadas en el dispositivo no tienen los permisos necesarios, proporcione otro conjunto de credenciales o edite uno existente. (Busque en las posibles causas el nombre descriptivo de las credenciales que Azure Migrate probó) |
| **10009**: No se pueden escribir los metadatos detectados en el archivo en el servidor. | El rol asociado a las credenciales proporcionadas en el dispositivo o una directiva de grupo local restringe la escritura en el archivo en el servidor. El problema se encontró al probar las credenciales siguientes en el servidor: \<FriendlyNameOfCredentials>. | 1. Compruebe si las credenciales proporcionadas en el dispositivo tienen permiso de escritura en el archivo en la carpeta <ruta de acceso a la carpeta/nombre de la carpeta> en el servidor.<br/> 2. Si las credenciales proporcionadas en el dispositivo no tienen los permisos necesarios, proporcione otro conjunto de credenciales o edite uno existente. (Busque en las posibles causas el nombre descriptivo de las credenciales que Azure Migrate probó) |
| **10010:** No se puede detectar porque en el servidor falta el comando %CommandName; necesario para recopilar algunos metadatos. | El paquete que contiene el comando %CommandName; no está instalado en el servidor. | Asegúrese de que el paquete que contiene el comando %CommandName; esté instalado en el servidor. |
| **10011:** Las credenciales proporcionadas en el dispositivo se usaron para iniciar y cerrar una sesión interactiva. | El inicio y cierre de una sesión interactiva fuerza la descarga de las clave del Registro en el perfil de la cuenta que se utiliza. Esta condición hace que las claves no estén disponibles para usarlas en el futuro. | Utilice los métodos de resolución que se documentan [aquí](/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error#resolutionus/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error). |
| **10012:** No se han proporcionado credenciales en el dispositivo para el servidor. | No se ha proporcionado ninguna credencial para el servidor o se han proporcionado credenciales de dominio con un nombre de dominio incorrecto en el dispositivo. [Obtenga más información](troubleshoot-dependencies.md#error-10012-credentialnotprovided) sobre la causa de este error. | 1. Asegúrese de que se proporcionaron credenciales en el dispositivo para el servidor y de que se pueda acceder al servidor con las credenciales. <br/> 2. Ahora puede agregar varias credenciales en el dispositivo para los servidores. Vuelva al administrador de configuración del dispositivo para proporcionar las credenciales del servidor.|


## <a name="error-970-dependencymapinsufficientprivilegesexception"></a>Error 970: DependencyMapInsufficientPrivilegesException

### <a name="cause"></a>Causa
El error suele aparecer para los servidores Linux cuando no se han proporcionado credenciales con los privilegios necesarios en el dispositivo.

### <a name="remediation"></a>Corrección
- Asegúrese de haber proporcionado una cuenta de usuario raíz O BIEN
- Una cuenta con estos permisos en los archivos /bin/netstat y /bin/ls:
   - CAP_DAC_READ_SEARCH
   - CAP_SYS_PTRACE
- Para comprobar si la cuenta de usuario proporcionada en el dispositivo tiene los privilegios necesarios, realice estos pasos:
1. Inicie sesión en el servidor en el que encontró este error con la misma cuenta de usuario que se mencionó en el mensaje de error.
2. Ejecute los comandos siguientes en Shell. Si no tiene los privilegios necesarios para el análisis de dependencias sin agente, recibirá errores:

   ````
   ps -o pid,cmd | grep -v ]$
   netstat -atnp | awk '{print $4,$5,$7}'
   ````
3. Establezca los permisos necesarios en los archivos /bin/netstat y /bin/ls mediante la ejecución de estos comandos:

   ````
   sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls
   sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat
   ````
4. Puede validar si los comandos anteriores asignaron o no los permisos necesarios a la cuenta de usuario:

   ````
   getcap /usr/bin/ls
   getcap /usr/bin/netstat
   ````
5. Vuelva a ejecutar los comandos proporcionados en el paso 2 para obtener una salida correcta.


## <a name="error-9014-httpgetrequesttoretrievefilefailed"></a>Error 9014: HTTPGetRequestToRetrieveFileFailed

### <a name="cause"></a>Causa
El problema se produce cuando el agente de detección de VMware del dispositivo intenta descargar el archivo de salida que contiene los datos de dependencia del sistema de archivos del servidor a través del host ESXi en el que se hospeda el servidor.

### <a name="remediation"></a>Corrección
- Puede probar la conectividad TCP con el host ESXi _(nombre proporcionado en el mensaje de error)_ en el puerto 443 (debe estar abierto en hosts ESXi para extraer datos de dependencia) desde el dispositivo. Para ello, abra PowerShell en el servidor del dispositivo y ejecute el comando siguiente:
    ````
    Test -NetConnection -ComputeName <Ip address of the ESXi host> -Port 443
    ````
- Si el comando devuelve una conectividad correcta, puede ir al proyecto de Azure Migrate > Detección y evaluación > Información general > Administrar > Dispositivos, seleccione el nombre del dispositivo y, luego, **Actualizar servicios**.

## <a name="error-9018-powershellnotfound"></a>Error 9018: PowerShellNotFound

### <a name="cause"></a>Causa
El error suele aparecer para los servidores que ejecutan Windows Server 2008 o una versión inferior.

### <a name="remediation"></a>Corrección
Debe instalar la versión de PowerShell necesaria (2.0 o posterior) en esta ubicación del servidor: ($SYSTEMROOT)\System32\WindowsPowershell\v1.0\powershell.exe. [Obtenga más información](/powershell/scripting/windows-powershell/install/installing-windows-powershell) sobre cómo instalar PowerShell en Windows Server.

Después de instalar la versión de PowerShell necesaria, puede comprobar si se resolvió el error. Para ello, siga los pasos que se indican [aquí](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli).

## <a name="error-9022-getwmiobjectaccessdenied"></a>Error 9022: GetWMIObjectAccessDenied

### <a name="remediation"></a>Corrección
Asegúrese de que la cuenta de usuario proporcionada en el dispositivo tenga acceso al espacio de nombres WMI y a los espacios de subnombres. Siga estos pasos para establecer el acceso:
1.  Vaya al servidor que informa de este error.
2.  Busque y seleccione "Ejecutar" en el menú Inicio. En el cuadro de diálogo "Ejecutar", escriba wmimgmt.msc en el campo de texto "Abrir:" y presione ENTRAR.
3.  Se abrirá la consola wmimgmt y verá "Control WMI (local)" en el panel izquierdo. Haga clic con el botón derecho y seleccione "Propiedades" en el menú.
4.  En el cuadro de diálogo "Propiedades: Control WMI (local) ", seleccione la pestaña "Seguridad".
5.  En la pestaña Segurdad, seleccione el botón "Seguridad". Se abrirá el cuadro de diálogo "Seguridad para Root".
7.  Seleccione el botón "Opciones avanzadas" para abrir el cuadro de diálogo "Configuración de seguridad avanzada para Root". 
8.  Seleccione el botón "Agregar" y se abrirá el cuadro de diálogo "Entrada de permiso para Root".
9.  Haga clic en "Seleccionar una entidad de seguridad" para abrir el cuadro de diálogo "Seleccionar usuarios, equipos, cuentas de servicio o grupos".
10. Seleccione los nombres de usuario o grupos a los que desea conceder acceso a WMI y haga clic en "Aceptar".
11. Asegúrese de conceder permisos de ejecución y seleccione "Este espacio de nombres y espacios de subnombres" en la lista desplegable "Se aplica a:".
12. Seleccione el botón "Aplicar" para guardar la configuración y cerrar todos los cuadros de diálogo.

Después de obtener el acceso necesario, puede comprobar si se resolvió el error. Para ello, siga los pasos que se indican [aquí](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli).

## <a name="error-9032-invalidrequest"></a>Error 9032: InvalidRequest

### <a name="cause"></a>Causa
Este problema puede tener varios motivos; uno de ellos es cuando el nombre de usuario proporcionado (credenciales del servidor) en el administrador de configuración del dispositivo tiene caracteres XML no válidos, lo que genera un error al analizar la solicitud SOAP.

### <a name="remediation"></a>Corrección
- Asegúrese de que el nombre de usuario de las credenciales del servidor no tiene caracteres XML no válidos y está en formato username@domain.com, conocido popularmente como formato UPN.
- Después de editar las credenciales en el dispositivo, puede comprobar si se resolvió el error. Para ello, siga los pasos que se indican [aquí](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli).


## <a name="error-10002-scriptexecutiontimedoutonvm"></a>Error 10002: ScriptExecutionTimedOutOnVm

### <a name="cause"></a>Causa
- Este error se produce cuando el servidor es lento o no responde y se empieza a agotar el tiempo de espera del script ejecutado para extraer los datos de dependencia.
- Una vez que el agente de detección encuentra este error en el servidor, el dispositivo deja de intentar realizar el análisis de dependencias sin agente en el servidor a fin de evitar sobrecargar el servidor que no responde.
- Por lo tanto, seguirá viendo el error hasta que compruebe el problema con el servidor y reinicie el servicio de detección.

### <a name="remediation"></a>Corrección
1. Inicie sesión en el servidor en el que se encontró este error.
1. Ejecute los comandos siguientes en PowerShell:
   ````
   Get-WMIObject win32_operatingsystem;
   Get-WindowsFeature  | Where-Object {$_.InstallState -eq 'Installed' -or ($_.InstallState -eq $null -and $_.Installed -eq 'True')};
   Get-WmiObject Win32_Process;
   netstat -ano -p tcp | select -Skip 4;
   ````
3. Si los comandos generan el resultado en unos segundos, puede ir al proyecto de Azure Migrate > Detección y evaluación > Información general > Administrar > Dispositivos, seleccione el nombre del dispositivo y, luego, **Actualizar servicios** para reiniciar el servicio de detección.
4. Si se está agotando el tiempo de espera de los comandos sin generar ninguna salida: 
- Debe averiguar qué proceso consume una gran cantidad de memoria o CPU en el servidor.
- Puede intentar proporcionar más núcleos o memoria a ese servidor y volver a ejecutar los comandos.

## <a name="error-10005-guestcredentialnotvalid"></a>Error 10005: GuestCredentialNotValid

### <a name="remediation"></a>Corrección
- A fin de garantizar la validez de las credenciales _(el nombre descriptivo que se proporciona en el error)_ , haga clic en "Volver a validar las credenciales" en el administrador de configuración del dispositivo.
- Asegúrese de poder iniciar sesión en el servidor afectado con la misma credencial proporcionada en el dispositivo.
- Puede intentar usar otra cuenta de usuario (para el mismo dominio, en caso de que el servidor esté unido a un dominio) para ese servidor en lugar de la cuenta de administrador.
- El problema puede producirse cuando la comunicación entre el catálogo global y el controlador de dominio está rota. Para comprobarlo, cree una cuenta de usuario en el controlador de dominio y proporciónela en el dispositivo. Esto también puede requerir reiniciar el controlador de dominio.
- Después de realizar los pasos de corrección, puede comprobar si se resolvió el error. Para ello, siga los pasos que se indican [aquí](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli).

## <a name="error-10012-credentialnotprovided"></a>Error 10012: CredentialNotProvided

### <a name="cause"></a>Causa
Este error se produce cuando se ha proporcionado una credencial de dominio con un nombre de dominio incorrecto en el administrador de configuración del dispositivo. Por ejemplo, si ha proporcionado una credencial de dominio con nombre de usuario user@abc.com, pero proporcionó un nombre de dominio como def.com, esas credenciales no se utilizarán si el servidor está conectado a def.com y recibirá este mensaje de error.

### <a name="remediation"></a>Corrección
- Vaya al administrador de configuración del dispositivo para agregar una credencial de servidor o editar una existente, tal como se explica en la causa.
- Después de realizar los pasos de corrección, puede comprobar si se resolvió el error. Para ello, siga los pasos que se indican [aquí](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli).

## <a name="mitigation-verification-using-vmware-powercli"></a>Comprobación de la mitigación con VMware PowerCLI

Después de usar los pasos de mitigación en los errores anteriormente indicados, puede comprobar si funcionó la mitigación. Para ello, ejecute algunos comandos de PowerCLI desde el servidor del dispositivo. Si los comandos se completan correctamente significa que se resolvió el problema. Si no es así, debe volver a comprobar y seguir los pasos de corrección.

1. Ejecute los comandos siguientes para configurar PowerCLI en el servidor del dispositivo:
   ````
   Install-Module -Name VMware.PowerCLI -AllowClobber
   Set-PowerCLIConfiguration -InvalidCertificateAction Ignore
   ````
2. Conéctese a vCenter Server desde el dispositivo; para ello, proporcione la dirección IP de vCenter Server en el comando y las credenciales en el símbolo del sistema:
   ````
   Connect-VIServer -Server <IPAddress of vCenter Server>
   ````
3. Conéctese al servidor de destino desde el dispositivo; para ello, proporcione el nombre y las credenciales del servidor (tal como se proporcionan en el dispositivo):
   ````
   $vm = get-VM <VMName>
   $credential = Get-Credential
   ````
4. Para el análisis de dependencias sin agente, ejecute estos comandos para ver si obtiene una salida correcta:

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
5. Después de comprobar que la mitigación funcionó, puede ir al proyecto de Azure Migrate > Detección y evaluación > Información general > Administrar > Dispositivos, seleccione el nombre del dispositivo y, luego, **Actualizar servicios** para iniciar un ciclo de detección nuevo.


## <a name="my-log-analytics-workspace-is-not-listed-when-trying-to-configure-the-workspace-in-azure-migrate-for-agent-based-dependency-analysis"></a>Mi área de trabajo de Log Analytics no aparece al intentar configurar el área de trabajo en Azure Migrate para el análisis de dependencias basado en agentes
Actualmente, Azure Migrate admite la creación de un área de trabajo de OMS en las regiones Este de EE. UU., Sudeste de Asia y Oeste de Europa. Si el área de trabajo se crea fuera de Azure Migrate en cualquier otra región, actualmente no se puede asociar a un proyecto.

## <a name="agent-based-dependency-visualization-in-azure-government"></a>Visualización de dependencias basada en agente en Azure Government

El análisis de dependencias basado en agente no está disponible en Azure Government. Utilice el análisis de dependencias sin agente _(solo disponible para servidores de VMware)_ .

## <a name="agent-based-dependencies-dont-show-after-agent-install"></a>Las dependencias basadas en agente no se muestran después de la instalación del agente

Después de instalar los agentes de visualización de dependencias en las máquinas virtuales locales, Azure Migrate suele tardar entre 15 y 30 minutos en mostrar las dependencias en el portal. Si ha esperado durante más de 30 minutos, asegúrese de que Microsoft Monitoring Agent (MMA) puede conectarse al área de trabajo de Log Analytics.

Para las máquinas virtuales de Windows:
1. En el Panel de control, inicie MMA.
2. En **Propiedades de Microsoft Monitoring Agent** > **Azure Log Analytics (OMS)** , asegúrese de que el **estado** del área de trabajo esté en verde.
3. Si el estado no está en verde, intente quitar el área de trabajo y vuelva a agregarla a MMA.

    ![Estado de MMA](./media/troubleshoot-assessment/mma-properties.png)

Para las máquinas virtuales Linux, asegúrese de que se hayan completado correctamente los comandos de instalación de MMA y el agente de dependencias. Consulte la guía de solución de problemas [aquí](../azure-monitor/vm/service-map.md#post-installation-issues).

## <a name="supported-operating-systems-for-agent-based-dependency-analysis"></a>Sistemas operativos admitidos para el análisis de dependencias basado en agentes

- **Agente de MMS**: Revise los sistemas operativos [Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems) y [Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems) admitidos.
- **Agente de dependencias**: sistemas operativos [Windows y Linux](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) admitidos.

## <a name="visualize-dependencies-for--1-hour-with-agent-based-dependency-analysis"></a>Visualización de dependencias durante más de una hora con análisis de dependencias basado en agentes

Con el análisis de dependencias basado en agentes, aunque Azure Migrate le permita volver atrás a una fecha determinada en el último mes, el tiempo máximo durante el que se pueden visualizar las dependencias es de una hora. Por ejemplo, puede usar la característica de duración de tiempo en el mapa de dependencias para ver las dependencias de ayer, pero solo puede verlas durante un período de una hora. Sin embargo, puede usar los registros de Azure Monitor para [consultar los datos de dependencia](./how-to-create-group-machine-dependencies.md) durante un período más largo.

## <a name="visualized-dependencies-for--10-servers-with-agent-based-dependency-analysis"></a>Dependencias visualizadas para más de 10 servidores con análisis de dependencias basado en agentes

En Azure Migrate, puede [visualizar las dependencias para grupos](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) que tengan un máximo de 10 máquinas virtuales con un análisis de dependencia basado en agentes. En el caso de los grupos más grandes, se recomienda dividir las máquinas virtuales en grupos más pequeños para visualizar las dependencias.

## <a name="servers-show-install-agent-for-agent-based-dependency-analysis"></a>Los servidores muestran "Instalar agente" para el análisis de dependencias basado en agentes

Después de migrar los servidores con la visualización de dependencias habilitada en Azure, es posible que los servidores muestren la acción "Instalar agente" en lugar de "Ver dependencias" debido al comportamiento siguiente:

- Después de la migración a Azure, los servidores locales están desconectados y las máquinas virtuales equivalentes trabajan en Azure. Estos servidores adquieren una dirección MAC diferente.
- Los servidores también pueden adquirir una dirección IP distinta en función de si se elige conservar o no la dirección IP local.
- Si las direcciones IP y MAC difieren de las locales, Azure Migrate no asocia los servidores locales con ningún dato de dependencias de Service Map. En este caso, se mostrará la opción para instalar el agente en lugar de ver las dependencias.
- Después de una migración de prueba a Azure, los servidores locales permanecen encendidos según lo previsto. Los servidores equivalentes que se activan en Azure adquieren una dirección MAC distinta y pueden adquirir una dirección IP diferente. A menos que bloquee el tráfico saliente del registro de Azure Monitor procedente de estos servidores, Azure Migrate no asociará los servidores locales con ningún dato de dependencias de Service Map y, por tanto, mostrará la opción de instalar los agentes en lugar de visualizar las dependencias.

## <a name="capture-network-traffic"></a>Captura del tráfico de red

Recopile registros de tráfico de red como se indica a continuación:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Presione F12 para iniciar las Herramientas de desarrollo. Si es necesario, desactive la opción **Borrar entradas de navegación**.
3. Seleccione la pestaña **Red** para empezar a capturar el tráfico de red:
   - En Chrome, seleccione **Preserve log** (Conservar registro). La grabación debe iniciarse automáticamente. Un círculo rojo indica que la captura del tráfico está en curso. Si no aparece el círculo rojo, seleccione el círculo negro para comenzar.
   - En Microsoft Edge e Internet Explorer, la grabación debe iniciarse automáticamente. Si no es así, seleccione el botón de reproducción de color verde.
4. Pruebe a reproducir el error.
5. Una vez haya encontrado el error durante la grabación, detenga la grabación y guarde una copia de la actividad grabada:
   - En Chrome, haga clic con el botón derecho y seleccione **Guardar como HAR con contenido**. Esta acción comprime y exporta los registros como un archivo HTTP (har).
   - En Microsoft Edge o Internet Explorer, seleccione la opción **Exportar tráfico capturado**. Esta acción comprime y exporta el registro.
6. Seleccione la pestaña **Consola** para ver si hay alguna advertencia o error. Para guardar el registro de la consola:
   - En Chrome, haga clic con el botón derecho en cualquier lugar en el registro de la consola. Seleccione **Save as** (Guardar como) para exportar y comprimir el registro.
   - En Microsoft Edge o Internet Explorer, haga clic con el botón derecho sobre los errores y seleccione **Copiar todo**.
7. Cierre las Herramientas de desarrollo.


## <a name="next-steps"></a>Pasos siguientes

[Crear](how-to-create-assessment.md) o [personalizar](how-to-modify-assessment.md) una evaluación.