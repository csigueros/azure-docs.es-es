---
title: Configuración del análisis de dependencias sin agentes en Azure Migrate
description: Configuración del análisis de dependencias sin agentes en Azure Migrate.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: 7f4066f466b6dd9e8cb41bf68592aa88c192c44d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743239"
---
# <a name="analyze-server-dependencies-agentless"></a>Análisis de las dependencias del servidor (sin agente)

En este artículo se describe cómo configurar el análisis de dependencias sin agente en Azure Migrate: Detección y evaluación. El [análisis de dependencias](concepts-dependency-visualization.md) le ayuda a identificar y comprender las dependencias entre servidores para la evaluación y la migración a Azure.

> [!IMPORTANT]
>El análisis de dependencias sin agente se encuentra actualmente disponibles para los servidores que se ejecutan en el entorno de VMware, detectados con la herramienta Azure Migrate: Detección y evaluación.

## <a name="current-limitations"></a>Limitaciones actuales

- En la vista de análisis de dependencias, actualmente no se puede agregar o quitar un servidor de un grupo.
- Actualmente, no existe ningún mapa de dependencias disponible para un grupo de servidores.
- En un proyecto de Azure Migrate, la recopilación de datos de dependencia se puede habilitar simultáneamente para 1000 servidores. Se puede analizar un número mayor de servidores mediante la secuenciación en lotes de 1000 servidores.

## <a name="before-you-start"></a>Antes de comenzar

- Asegúrese de que ha [creado un proyecto](./create-manage-projects.md) con la herramienta Azure Migrate: Detección y evaluación agregada.
- Revise los [requisitos de VMware](migrate-support-matrix-vmware.md#vmware-requirements) para realizar el análisis de dependencias.
- Revise los [requisitos del dispositivo](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) antes de configurarlo.
- [Revise los requisitos del análisis de dependencias](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) antes de habilitarlo en los servidores.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Implementación y configuración del dispositivo de Azure Migrate

1. [Revise](migrate-appliance.md#appliance---vmware) los requisitos para implementar el dispositivo de Azure Migrate.
2. Revise las direcciones URL de Azure a las que el dispositivo necesitará acceder en las nubes [públicas](migrate-appliance.md#public-cloud-urls) y [gubernamentales](migrate-appliance.md#government-cloud-urls).
3. [Revise los datos](migrate-appliance.md#collected-data---vmware) que la aplicación recopila durante la detección y valoración.
4. [Tenga en cuenta](migrate-support-matrix-vmware.md#port-access-requirements) los requisitos de acceso a puertos del dispositivo.
5. [Implemente el dispositivo de Azure Migrate](how-to-set-up-appliance-vmware.md) para iniciar la detección. Para implementar el dispositivo, descargue e importe una plantilla OVA a VMware para crear un servidor que se ejecute en vCenter Server. Después de implementar el dispositivo, debe registrarlo en el proyecto y configurarlo para iniciar la detección.
6. Al configurar el dispositivo, debe especificar lo siguiente en el administrador de configuración del dispositivo:
    - Los detalles de la instancia de vCenter Server a la que se quiere conectar.
    - Las credenciales de vCenter Server en el ámbito para detectar los servidores del entorno de VMware.
    - Las credenciales del servidor, que pueden ser credenciales de dominio, Windows (no de dominio) o Linux (no de dominio). [Obtenga más información](add-server-credentials.md) sobre cómo proporcionar credenciales y cómo administrarlas.

## <a name="verify-permissions"></a>Comprobar los permisos

- Debe [crear una cuenta de vCenter Server de solo lectura](./tutorial-discover-vmware.md#prepare-vmware) para la detección y evaluación. La cuenta de solo lectura necesita tener habilitados los privilegios para **Virtual Machines** > **Guest Operations** (Operaciones de invitado), con el fin de interactuar con los servidores para recopilar los datos de dependencia.
- Necesita una cuenta de usuario para que Azure Migrate pueda acceder al servidor para recopilar los datos de dependencia. [Obtenga información](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) sobre los requisitos de la cuenta para servidores de Windows y Linux.

### <a name="add-credentials-and-initiate-discovery"></a>Adición de credenciales e inicio de la detección

1. Abra el administrador de configuración del dispositivo, complete las comprobaciones de requisitos previos y el registro del dispositivo.
2. Navegue hasta el panel **Administrar credenciales y orígenes de detección**.
1.  En **Step 1: Provide vCenter Server credentials** (Paso 1: Proporcionar las credenciales de vCenter Server), haga clic en **Agregar credenciales** para proporcionar las credenciales de la cuenta de vCenter Server que el dispositivo usará para detectar los servidores que se ejecutan en vCenter Server.
1. En el **Paso 2: Proporcionar los detalles de vCenter Server**, haga clic en **Agregar origen de detección** para seleccionar el nombre descriptivo de las credenciales en la lista desplegable, especifique **Dirección IP/FQDN** de la instancia de vCenter Server. :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Panel 3 del administrador de configuración del dispositivo para los detalles de vCenter Server":::
1. En **Step 3: Provide server credentials to perform software inventory, agentless dependency analysis and discovery of SQL Server instances and databases** (Paso 3: Proporcionar credenciales de servidor para realizar el inventario de software, el análisis de dependencias sin agente y la detección de instancias y bases de datos de SQL Server), haga clic en **Agregar credenciales** para proporcionar varias credenciales de servidor a fin de iniciar el inventario de software.
1. Haga clic en **Iniciar detección** para iniciar la detección de vCenter Server.

 Una vez completada la detección de vCenter Server, el dispositivo inicia la detección de las aplicaciones instaladas, los roles y las características (inventario de software). Durante el inventario de software, las credenciales de los servidores agregados se iterarán en los servidores y se validarán para el análisis de dependencias sin agente. Puede habilitar el análisis de dependencias sin agente para los servidores desde el portal. Solo se pueden seleccionar los servidores en los que la validación se realiza correctamente para habilitar el análisis de dependencias sin agente.

## <a name="start-dependency-discovery"></a>Inicio de la detección de dependencias

Seleccione los servidores en los que quiere habilitar la detección de dependencias.

1. En **Azure Migrate: Detección y evaluación**, haga clic en **Servidores descubiertos**.
2. Elija el **Nombre del dispositivo** cuya detección quiere revisar.
1. Puede ver el estado de validación de los servidores en la columna **Dependencias (sin agente)** .
1. Haga clic en el menú desplegable **Análisis de dependencias**.
1. Haga clic en **Agregar servidores**.
1. En la página **Agregar servidores**, seleccione los servidores en los que desea habilitar el análisis de dependencias. Puede habilitar la asignación de dependencias solo en los servidores en los que se superó la validación. El siguiente ciclo de validación se ejecutará 24 horas después de la marca de tiempo de la última validación.
1. Después de seleccionar los servidores, haga clic en **Agregar servidores**.

:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png" alt-text="Inicio del análisis de dependencias":::

Podrá visualizar las dependencias unas seis horas después de habilitar el análisis de dependencias en los servidores. Si quiere habilitar simultáneamente varios servidores para el análisis de dependencias, puede usar [PowerShell](#start-or-stop-dependency-analysis-using-powershell) para hacerlo.

## <a name="visualize-dependencies"></a>Visualización de dependencias

1. En **Azure Migrate: Detección y evaluación**, haga clic en **Servidores descubiertos**.
1. Elija el **Nombre del dispositivo** cuya detección quiere revisar.
1. Busque el servidor cuyas dependencias quiera revisar.
1. En la columna **Dependencias (sin agente)** , haga clic en **Ver dependencias**.
1. Cambie el período de tiempo del que quiere ver el mapa mediante el menú desplegable **Duración**.
1. Expanda el grupo **Cliente** para enumerar los servidores que tienen una dependencia en el servidor seleccionado.
1. Expanda el grupo **Puerto** para mostrar las máquinas que tienen una dependencia del servidor seleccionado.
1. Para navegar a la vista de mapa de cualquiera de los servidores dependientes, haga clic en el nombre del servidor > **Cargar mapa del servidor**
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png" alt-text="Expansión del grupo de puertos del servidor y carga del mapa del servidor":::
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png" alt-text="Expansión del grupo de clientes":::

8. Expanda el servidor seleccionado para ver los detalles de nivel de proceso de cada dependencia.
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png" alt-text="Expansión del servidor para mostrar los procesos":::

> [!NOTE]
> La información del proceso para una dependencia no siempre está disponible. Si no está disponible, la dependencia se representa con el proceso marcado como "Proceso desconocido".

## <a name="export-dependency-data"></a>Exportar datos de dependencia

1. En **Azure Migrate: Detección y evaluación**, haga clic en **Servidores descubiertos**.
2. Haga clic en el menú desplegable **Análisis de dependencias**.
3. Haga clic en **Exportar dependencias de aplicaciones**.
4. En la página **Exportar dependencias de aplicaciones**, elija el nombre del dispositivo que detecta los servidores deseados.
5. Seleccione la hora de inicio y la hora de finalización. Tenga en cuenta que solo puede descargar los datos de los últimos 30 días.
6. Haga clic en **Exportar dependencia**.

Los datos de dependencia se exportan y se descargan en formato CSV. El archivo descargado contiene los datos de dependencia en todos los servidores habilitados para el análisis de dependencias. 
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/export.png" alt-text="Exportar dependencias":::

### <a name="dependency-information"></a>Información de dependencia

Cada fila del CSV exportado corresponde a una dependencia observada en el intervalo de tiempo especificado.

En la tabla siguiente se resumen los campos del CSV exportado. Tenga en cuenta que los campos del nombre del servidor, de la aplicación y del proceso solo se rellenan en los servidores que tienen habilitado el análisis de dependencia sin agente.

**Nombre del campo** | **Detalles**
--- | --- 
Intervalo de tiempo | El intervalo de tiempo durante el cual se observó la dependencia. <br/> Los datos de dependencia se capturan en intervalos de 6 horas actualmente.
Nombre de servidor de origen | Nombre del servidor de origen. 
Aplicación de origen | Nombre de la aplicación en el servidor de origen.  
Proceso de origen | Nombre del proceso en el servidor de origen.  
Nombre del servidor de destino | Nombre del servidor de destino.
IP de destino | Dirección IP del servidor de destino.
Aplicación de destino | Nombre de la aplicación en el servidor de destino.
Proceso de destino | Nombre del proceso en el servidor de destino.  
Puerto de destino | Número de puerto en el servidor de destino.

## <a name="stop-dependency-discovery"></a>Detención de la detección de dependencias

Seleccione los servidores en los que quiere detener la detección de dependencias.

1. En **Azure Migrate: Detección y evaluación**, haga clic en **Servidores descubiertos**.
1. Elija el **Nombre del dispositivo** cuya detección quiere revisar.
1. Haga clic en el menú desplegable **Análisis de dependencias**.
1. Haga clic en **Quitar servidores**.
1. En la página **Quitar servidores**, seleccione el servidor para el que quiere detener el análisis de dependencias.
1. Después de seleccionar los servidores, haga clic en **Quitar servidores**.

Si quiere detener simultáneamente la dependencia en varios servidores, puede usar [PowerShell](#start-or-stop-dependency-analysis-using-powershell) para hacerlo.

## <a name="start-or-stop-dependency-analysis-using-powershell"></a>Inicio o detención del análisis de dependencias con PowerShell

Descargue el módulo de PowerShell del repositorio de [ejemplos de Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) en GitHub.

### <a name="log-in-to-azure"></a>Inicio de sesión en Azure

1. Inicie sesión en su suscripción de Azure con el cmdlet Connect-AzAccount.

    ```PowerShell
    Connect-AzAccount
    ```
    Si usa Azure Government, utilice el siguiente comando.
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Seleccione la suscripción en la que creó el proyecto 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importe el módulo de PowerShell AzMig_Dependencies descargado.

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>Habilitación o deshabilitación de la recopilación de datos de dependencia

1. Obtenga la lista de servidores detectados en el proyecto con los siguientes comandos. En el ejemplo siguiente, el nombre del proyecto es FabrikamDemoProject y el grupo de recursos al que pertenece es FabrikamDemoRG. La lista de servidores se guardará en FabrikamDemo_VMs.csv.

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    En el archivo, puede ver el nombre para mostrar del servidor, el estado actual de la recopilación de dependencias y el id. de ARM de todos los servidores detectados. 

2. Para habilitar o deshabilitar las dependencias, cree un archivo CSV de entrada. El archivo debe tener una columna con el encabezado "ARM ID". Se omitirán los demás encabezados que pueda haber en el archivo CSV. Puede crear el CSV con el archivo generado en el paso anterior. Cree una copia del archivo que conserve los servidores en los que quiere habilitar o deshabilitar las dependencias. 

    En el ejemplo siguiente, se habilita el análisis de dependencias en la lista de servidores del archivo de entrada FabrikamDemo_VMs_Enable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    En el ejemplo siguiente, se deshabilita el análisis de dependencias en la lista de servidores del archivo de entrada FabrikamDemo_VMs_Disable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Visualización de las conexiones de red en Power BI

Azure Migrate ofrece una plantilla de Power BI que puede usar para visualizar las conexiones de red de muchos servidores a la vez, así como para filtrar por proceso y servidor. Para visualizar las conexiones, cargue la plantilla de Power BI con los datos de dependencia según las instrucciones siguientes.

1. Descargue el módulo de PowerShell y la plantilla de Power BI del repositorio de [ejemplos de Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) en GitHub.

2. Inicie sesión en Azure con las siguientes instrucciones: 
    - Inicie sesión en su suscripción de Azure con el cmdlet Connect-AzAccount.

        ```PowerShell
        Connect-AzAccount
        ```

    - Si usa Azure Government, utilice el siguiente comando.

        ```PowerShell
        Connect-AzAccount -EnvironmentName AzureUSGovernment
        ```

    - Seleccione la suscripción en la que creó el proyecto

        ```PowerShell
        select-azsubscription -subscription "Fabrikam Demo Subscription"
        ```

3. Importe el módulo de PowerShell AzMig_Dependencies descargado.

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Ejecute el siguiente comando: Este comando descarga los datos de dependencias en un CSV y los procesa para generar una lista de dependencias únicas que se puede usar para la visualización en Power BI. En el ejemplo siguiente, el nombre del proyecto es FabrikamDemoProject y el grupo de recursos al que pertenece es FabrikamDemoRG. Se descargarán las dependencias de los servidores detectados por FabrikamAppliance. Las dependencias únicas se guardarán en FabrikamDemo_Dependencies.csv.

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. Abra la plantilla de Power BI descargada.

6. Cargue los datos de dependencia descargados en Power BI.
    - Abra la plantilla en Power BI.
    - Haga clic en **Obtener datos** en la barra de herramientas. 
    - Elija **Texto/CSV** en Orígenes de datos comunes.
    - Elija el archivo de dependencias descargado.
    - Haga clic en **Cargar**.
    - Verá que se importa una tabla con el nombre del archivo CSV. Puede ver la tabla en la barra de campos de la derecha. Cambie su nombre a AzMig_Dependencies.
    - Haga clic en Actualizar en la barra de herramientas.

    Se destacarán el gráfico de conexiones de red y las segmentaciones de nombre del servidor de origen, nombre del servidor de destino, nombre del proceso de origen y nombre del proceso de destino con los datos importados.

7. Visualice el mapa de conexiones de red filtrando por servidores y procesos. Guarde el archivo.

## <a name="next-steps"></a>Pasos siguientes

[Agrupación de los servidores](how-to-create-a-group.md) para la evaluación.