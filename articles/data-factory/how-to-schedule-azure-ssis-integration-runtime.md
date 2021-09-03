---
title: Programación de Azure-SSIS Integration Runtime
description: En este artículo se describe cómo programar el inicio y la detención de una instancia de Azure-SSIS Integration Runtime mediante Azure Data Factory.
ms.service: data-factory
ms.subservice: integration-services
ms.devlang: powershell
ms.topic: conceptual
ms.date: 06/04/2021
author: swinarko
ms.author: sawinark
ms.openlocfilehash: dec61a586c744fd8a5f537bf5a593b7409a1636e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735341"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Inicio y detención de Azure-SSIS Integration Runtime mediante una programación

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

En este artículo se describe cómo programar el inicio y la detención de Azure-SSIS Integration Runtime (IR) mediante Azure Data Factory (ADF). Azure-SSIS IR es un recurso de proceso de ADF dedicado a la ejecución de paquetes de SQL Server Integration Services (SSIS). La ejecución de Azure-SSIS IR lleva asociado un costo. Por lo tanto, normalmente es preferible ejecutar la instancia de IR solo cuando haya que ejecutar paquetes SSIS en Azure y detenerla cuando ya no se necesite. Puede usar la interfaz de usuario o la aplicación de ADF, o Azure PowerShell, para [iniciar o detener manualmente la instancia de IR](manage-azure-ssis-integration-runtime.md).

Como alternativa, puede crear actividades web en las canalizaciones de ADF para iniciar o detener la instancia de IR según la programación (por ejemplo, iniciarla por la mañana antes de ejecutar las cargas de trabajo de ETL diarias y detenerla por la tarde después de que hayan terminado).  También puede encadenar una actividad Ejecutar paquete SSIS entre dos actividades web que inician y detienen la instancia de IR, por lo que esta se iniciará y detendrá a petición, justo a tiempo antes o después de la ejecución del paquete. Para más información sobre la actividad Ejecutar paquete SSIS, consulte el artículo [Ejecución de un paquete de SSIS mediante una actividad Ejecutar paquete de SSIS de Azure Data Factory](how-to-invoke-ssis-package-ssis-activity.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Requisitos previos
Si todavía no ha aprovisionado Azure-SSIS IR, hágalo ahora según las instrucciones del [tutorial](./tutorial-deploy-ssis-packages-azure.md). 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Creación y programación de canalizaciones de ADF que inician o detienen Azure-SSIS IR
En esta sección se muestra cómo usar las actividades web en las canalizaciones de ADF para iniciar o detener Azure-SSIS IR al programar o iniciar y detener a petición. Se le guiará para que cree tres canalizaciones: 

1. La primera canalización contiene una actividad web que inicia Azure-SSIS IR. 
2. La segunda canalización contiene una actividad web que detiene Azure-SSIS IR.
3. La tercera canalización contiene una actividad Ejecutar paquete SSIS encadenada entre dos actividades web que inician y detienen Azure-SSIS IR. 

Después de crear y probar esas canalizaciones, cree un desencadenador de programación y asócielo a una de ellas. El desencadenador de programación define una programación para la ejecución de la canalización asociada. 

Por ejemplo, puede crear dos desencadenadores, el primero se programa para ejecutarse diariamente a las 6 a. m. y se asocia a la primera canalización, mientras que el otro se programa para ejecutarse diariamente a las 6 p. m. y se asocia a la segunda canalización.  De este modo dispone de un período entre las 6 a. m. y las 6 p. m. todos los días en el que la instancia de IR se ejecuta, lista para ejecutar sus cargas de trabajo diarias de ETL.  

Si crea un tercer desencadenador que se programe para ejecutarse diariamente a medianoche y se asocie a la tercera canalización, esa canalización se ejecutará a medianoche todos los días, iniciará el entorno de ejecución de integración (IR) inmediatamente antes de la ejecución del paquete, ejecutará el paquete y detendrá inmediatamente la instancia de IR tras la ejecución, por lo que la instancia de IR no se ejecutará inútilmente.

### <a name="create-your-adf"></a>Creación de una instancia de ADF

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/).    
2. En el menú de la izquierda, haga clic en **Nuevo**, **Datos y análisis** y **Factoría de datos**. 
   
   ![New->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. En la página **Nueva factoría de datos**, escriba **MyAzureSsisDataFactory** en **Nombre**. 
      
   ![Página New data factory (Nueva factoría de datos)](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   La instancia de ADF debe tener un nombre único global. Si recibe el siguiente error, cambie el nombre de la instancia de ADF (por ejemplo, suNombreMyAzureSsisDataFactory) e intente crearla de nuevo. Consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md) para conocer las reglas de nomenclatura de los artefactos de ADF.
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Seleccione la **suscripción** de Azure en la que quiere crear una instancia de ADF. 
5. Para el **grupo de recursos**, realice uno de los siguientes pasos:
     
   - Seleccione en primer lugar **Usar existente** y después un grupo de recursos de la lista desplegable. 
   - Seleccione **Crear nuevo** y escriba el nombre del nuevo grupo de recursos.   
         
   Para más información sobre los grupos de recursos, consulte el artículo [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/management/overview.md).
   
6. En **Versión**, seleccione **V2**.
7. En la lista desplegable **Ubicación**, seleccione una de las ubicaciones compatibles con la creación de ADF.
8. Seleccione **Anclar al panel**.     
9. Haga clic en **Crear**.
10. En el panel de Azure, verá el icono siguiente con el estado: **Deploying Data Factory** (Implementando Data Factory). 

    ![icono implementando factoría de datos](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. Una vez completada la creación, puede ver la página de ADF tal y como se muestra a continuación.
   
    ![Página principal Factoría de datos](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Haga clic en **Crear y supervisar** para iniciar la interfaz de usuario o la aplicación de ADF en una pestaña independiente.

### <a name="create-your-pipelines"></a>Creación de las canalizaciones

1. En la página principal, seleccione **Organizar**. 

   ![Captura de pantalla que muestra la página principal de ADF.](./media/doc-common-process/get-started-page.png)
   
2. En el cuadro de herramientas **Actividades**, expanda el menú **General**, arrastre una actividad **web** y colóquela en la superficie del diseñador de canalizaciones. En la pestaña **General** de la ventana de propiedades, cambie el nombre de la actividad a **startMyIR**. Cambie a la pestaña **Configuración** y realice los pasos siguientes.

    1. En **Dirección URL**, escriba la siguiente dirección URL de API REST que inicia Azure-SSIS IR y reemplace `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}` y `{integrationRuntimeName}` por los valores reales de la instancia de IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` También puede copiar y pegar el identificador de recurso de la instancia de IR de su página de supervisión en la interfaz de usuario o la aplicación de ADF para remplazar el siguiente elemento de la anterior dirección URL: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![Identificador de recurso del entorno de ejecución para la integración de SSIS en ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. En **Method** (Método) seleccione **POST**. 
    3. En **Body** (Cuerpo), especifique `{"message":"Start my IR"}`. 
    4. En **Authentication** (Autenticación), seleccione **MSI** para usar la identidad administrada de su ADF. Para más información, consulte el artículo [Identidad administrada de Data Factory](./data-factory-service-identity.md).
    5. En **Recurso**, escriba `https://management.azure.com/`.
    
       ![IR de SSIS de programación de actividades web en ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Clone la primera canalización para crear otra, cambie el nombre de actividad a **stopMyIR** y reemplace las propiedades siguientes.

    1. En **Dirección URL**, escriba la siguiente dirección URL de API REST que detiene Azure-SSIS IR y reemplace `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}` y `{integrationRuntimeName}` por los valores reales de la instancia de IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. En **Body** (Cuerpo), especifique `{"message":"Stop my IR"}`. 

4. Cree una tercera canalización, arrastre una actividad **Ejecutar paquete SSIS** del cuadro de herramientas **Actividades** y colóquela en la superficie del diseñador de canalizaciones, y configúrela según las instrucciones del artículo [Ejecución de un paquete de SSIS mediante una actividad Ejecutar paquete de SSIS de Azure Data Factory](how-to-invoke-ssis-package-ssis-activity.md).  También puede usar una actividad **Procedimiento almacenado** en su lugar y configurarla según las instrucciones del artículo [Ejecución de un paquete de SSIS mediante una actividad de procedimiento almacenado de Azure Data Factory](how-to-invoke-ssis-package-stored-procedure-activity.md).  Luego, encadene la actividad Ejecutar paquete SSIS o Procedimiento almacenado entre dos actividades web que inicien y detengan la instancia de IR, de forma similar a las actividades web de la primera y segunda canalización.

   ![IR de SSIS a petición de actividades web en ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Asigne a la identidad administrada para la instancia de ADF un rol de **colaborador** para sí misma, de manera que las actividades web de las canalizaciones puedan llamar a la API REST para iniciar o detener las instancias de Azure-SSIS IR aprovisionadas en ella.  En su página de ADF en Azure Portal, haga clic en **Control de acceso (IAM)** y en **+ Add role assignment** (+ Agregar asignación de rol) y luego realice las siguientes acciones en la hoja **Agregar asignación de rol**.

    1. En **Rol**, seleccione **Colaborador**. 
    2. En **Asignar acceso a**, seleccione **Usuario, grupo o entidad de servicio de Azure AD**. 
    3. En **Seleccionar**, busque el nombre de su ADF y selecciónelo. 
    4. Haga clic en **Save**(Guardar).
    
   ![Asignaciones de roles de identidad administrada de ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Valide la instancia de ADF y todos los valores de canalización, haga clic en **Validate all/Validate** (Validar todo/Validar) en la barra de herramientas de la fábrica o la canalización. Para cerrar **Factory/Pipeline Validation Output** (Resultado de validación de la fábrica/canalización), haga clic en el botón **>>** .  

   ![Comprobar la canalización](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>Serie de pruebas de las canalizaciones

1. Seleccione **Serie de pruebas** en la barra de herramientas de cada canalización y vea la ventana **Salida** en el panel inferior. 

   ![Serie de pruebas](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. Para probar la tercera canalización, inicie SQL Server Management Studio (SSMS). En la ventana **Conectar al servidor**, realice las acciones siguientes. 

    1. En **Nombre del servidor**, escriba **&lt;el nombre del servidor&gt;.database.windows.net**.
    2. Seleccione **Opciones >>** .
    3. En **Conectar una base de datos**, seleccione **SSISDB**.
    4. Seleccione **Conectar**. 
    5. Expanda **Catálogos de Integration Services** -> **SSISDB** -> Su carpeta -> **Proyectos** -> Su proyecto de SSIS -> **Paquetes** . 
    6. Haga clic con el botón derecho en el paquete SSIS especificado para ejecutarlo y luego seleccione **Informes** -> **Informes estándar** -> **Todas las ejecuciones**. 
    7. Compruebe que se ejecutó. 

   ![Comprobación de la ejecución del paquete de SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>Programación de las canalizaciones

Ahora que las canalizaciones funcionan tal como esperaba, puede crear desencadenadores para ejecutarlas a las cadencias especificadas. Para más información sobre la asociación de desencadenadores y canalizaciones, consulte el artículo [Desencadenamiento de la canalización de forma programada](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

1. En la barra de herramientas de la canalización, seleccione **Desencadenador** y luego, **Nuevo/Editar**. 

   ![Captura de pantalla en la que se resalta la opción de menú Desencadenar-> Nuevo/editar.](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. En el panel **Add Triggers** (Agregar desencadenadores), seleccione **+ Nuevo**.

   ![Agregar desencadenadores: nuevo](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. En **Nuevo desencadenador**, lleve a cabo las siguientes acciones: 

    1. En **Nombre**, escriba el nombre del desencadenador. En el ejemplo siguiente, **Ejecutar diariamente** es el nombre del desencadenador. 
    2. En **Tipo**, seleccione **Programación**. 
    3. En **Fecha de inicio (UTC)** , seleccione una fecha y hora de inicio en UTC. 
    4. En **Periodicidad**, especifique la cadencia para el desencadenador. En el ejemplo siguiente, es **A diario** una vez. 
    5. En **Fin**, seleccione **Sin fin** o escriba una fecha y hora de finalización después de seleccionar **El día**. 
    6. Seleccione **Activado** para activar el desencadenador inmediatamente después de publicar la configuración completa de ADF. 
    7. Seleccione **Next** (Siguiente).

   ![Desencadenador -> Nuevo/Editar](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. En la página **Trigger Run Parameters** (Parámetros de ejecución de desencadenador), revise si hay alguna advertencia y seleccione **Finalizar**. 
5. Publique la configuración completa de ADF seleccionando **Publicar todo** en la barra de herramientas de fábrica. 

   ![Publicar todo](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Supervisión de las canalizaciones y los desencadenadores en Azure Portal

1. Para supervisar las ejecuciones de los desencadenadores y las ejecuciones de las canalizaciones, utilice la pestaña **Supervisar** de la izquierda de la interfaz de usuario o la aplicación de ADF. Para ver el procedimiento detallado, consulte el artículo sobre [supervisión de la canalización](quickstart-create-data-factory-portal.md#monitor-the-pipeline).

   ![Ejecuciones de la canalización](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. Para ver las ejecuciones de actividad asociadas a una ejecución de canalización, seleccione el primer vínculo (**View Activity Runs** [Ver ejecuciones de actividad]) de la columna **Actions** (Acciones). En el caso de la tercera canalización, verá tres ejecuciones de actividad, una por cada actividad encadenada en la canalización (actividad web para iniciar la instancia de IR, actividad de procedimiento almacenado para ejecutar el paquete y actividad web para detener la instancia de IR). Para ver de nuevo las ejecuciones de canalización, seleccione el vínculo **Pipelines** (Canalizaciones) de la parte superior.

   ![Ejecuciones de actividad](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Para ver las ejecuciones de desencadenador, seleccione **Trigger Runs** (Ejecuciones de desencadenador) en la lista desplegable **Pipeline Runs** (Ejecuciones de canalización) de la parte superior. 

   ![Ejecuciones de desencadenador](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Supervisión de las canalizaciones y los desencadenadores con PowerShell

Utilice scripts como los de los ejemplos siguientes para supervisar las canalizaciones y los desencadenadores.

1. Obtenga el estado de la ejecución de una canalización.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Obtenga información sobre un desencadenador.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Obtenga el estado de la ejecución de un desencadenador.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Creación y programación de un runbook de Azure Automation que inicia o detiene Azure-SSIS IR

En esta sección, aprenderá a crear un runbook de Azure Automation que ejecute el script de PowerShell, para iniciar o detener Azure-SSIS IR mediante una programación.  Esto resulta útil cuando se quieren ejecutar otros scripts antes o después de iniciar y detener la instancia de IR para su procesamiento anterior o posterior.

### <a name="create-your-azure-automation-account"></a>Creación de la cuenta de Azure Automation

Si aún no tiene una cuenta de Azure Automation, cree una según las instrucciones de este paso. Para ver el procedimiento detallado, consulte el artículo [Creación de una cuenta de Azure Automation](../automation/automation-quickstart-create-account.md). Como parte de este paso, va a crear una cuenta **de ejecución de Azure** (una entidad de servicio en su instancia de Azure Active Directory) y le va a asignar el rol **Colaborador** en su suscripción a Azure. Asegúrese de que se trata de la misma suscripción que contiene la instancia de ADF con el entorno de ejecución para la integración de SSIS en Azure. Azure Automation utilizará esta cuenta para autenticarse en Azure Resource Manager y trabajar con sus recursos. 

1. Inicie el explorador web **Microsoft Edge** o **Google Chrome**. Actualmente, la interfaz de usuario o la aplicación de ADF solo se admite en los exploradores web Microsoft Edge y Google Chrome.
2. Inicie sesión en el [portal de Azure](https://portal.azure.com/).    
3. Seleccione **Nuevo** en el menú de la izquierda, seleccione **Supervisión y administración** y seleccione **Automation**. 

   ![Captura de pantalla en la que se resalta la opción Supervisión y administración > Automation.](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. En el panel **Agregar cuenta de Automation**, lleve a cabo las siguientes acciones.

    1. En **Nombre**, escriba un nombre para la cuenta de Azure Automation. 
    2. En **Suscripción**, seleccione la suscripción que tiene la instancia de ADF con Azure-SSIS IR. 
    3. En **Grupo de recursos**, seleccione **Crear nuevo** para crear un grupo de recursos o **Usar existente** para seleccionar un grupo de recursos existente. 
    4. En **Ubicación**, seleccione la ubicación para la cuenta de Azure Automation. 
    5. Seleccione **Sí** para confirmar la **creación de la cuenta de Azure**. Se creará una entidad de servicio en la instancia de Azure Active Directory y se le asignará un rol **Colaborador** en la suscripción de Azure.
    6. Seleccione **Anclar al panel** para que se muestre permanentemente en el panel de Azure. 
    7. Seleccione **Crear**. 

   ![Nuevo -> Supervisión y administración -> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Verá el estado de implementación de la cuenta de Azure Automation en el panel de Azure y las notificaciones. 
    
   ![Implementando automatización](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Cuando la haya creado, verá la página principal de su cuenta de Azure Automation. 

   ![Página principal de Automation](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>Importación de módulos ADF

1. Seleccione **Módulos** en la sección **RECURSOS COMPARTIDOS** del menú izquierdo y compruebe si tiene **Az.DataFactory** + **Az.Profile** en la lista de módulos.

   ![Comprobación de los módulos necesarios](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Si no tiene **Az.DataFactory**, vaya al módulo [Az.DataFactory](https://www.powershellgallery.com/packages/Az.DataFactory/) en la Galería de PowerShell, elija **Implementar en Azure Automation**, seleccione su cuenta de Azure Automation y luego, **Aceptar**. Vuelva a la vista **Módulos** en la sección **RECURSOS COMPARTIDOS** del menú izquierdo y espere hasta que vea que el **ESTADO** del módulo **Az.DataFactory** cambia a **Disponible**.

    ![Comprobación del módulo de factoría de datos](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Si no tiene **Az.Profile**, vaya al módulo [Az.Profile](https://www.powershellgallery.com/packages/Az.profile/) en la Galería de PowerShell, elija **Implementar en Azure Automation**, seleccione su cuenta de Azure Automation y luego, **Aceptar**. Vuelva a la vista **Módulos** en la sección **RECURSOS COMPARTIDOS** del menú izquierdo y espere hasta que vea que el **ESTADO** del módulo **Az.Profile** cambia a **Disponible**.

    ![Comprobación del módulo Perfil](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>Creación del runbook de PowerShell

En la siguiente sección se ofrecen los pasos para la creación de un runbook de PowerShell. El script asociado al runbook inicia o detiene Azure-SSIS IR en función del comando que se especifique para el parámetro **OPERATION**. En esta sección no se ofrecen todos los detalles para crear un runbook. Para más información, consulte el artículo [Creación de un runbook de Azure Automation](../automation/automation-quickstart-create-runbook.md).

1. Cambie a la pestaña **Runbooks** y seleccione **+ Agregar un runbook** en la barra de herramientas. 

   ![Captura de pantalla en la que se resalta el botón + Agregar un runbook.](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Seleccione **Crear un runbook nuevo** y realice los pasos siguientes: 

    1. En **Nombre**, escriba **StartStopAzureSsisRuntime**.
    2. En **Tipo de runbook**, seleccione **PowerShell**.
    3. Seleccione **Crear**.
    
   ![Botón Agregar un runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Copie y pegue el siguiente script de PowerShell en la ventana de script del runbook. Guarde y luego publique el runbook con los botones **Guardar** y **Publicar** de la barra de herramientas. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   ![Edición de un runbook de PowerShell](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. Pruebe el runbook seleccionando el botón **Iniciar** en la barra de herramientas. 

   ![Botón Iniciar runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. En el panel **Iniciar runbook**, haga lo siguiente: 

    1. En **NOMBRE DEL GRUPO DE RECURSOS**, escriba el nombre del grupo de recursos que tiene la instancia de ADF con Azure-SSIS IR. 
    2. En **NOMBRE DE LA FACTORÍA DE DATOS**, escriba el nombre de la instancia de ADF con Azure-SSIS IR. 
    3. En **AZURESSISNAME** (Nombre de SSIS en Azure), escriba el nombre de la instancia de Azure-SSIS IR. 
    4. En **OPERATION** (Operación), escriba **START** (Iniciar). 
    5. Seleccione **Aceptar**.  

   ![Ventana Iniciar runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. En la ventana de trabajo, seleccione el icono **Salida**. En la ventana de salida, espere hasta ver el mensaje **### Completed ###** (Completado) después de ver **### Starting ###** (Iniciando). Se tardan 20 minutos aproximadamente en iniciar Azure-SSIS IR. Cierre la ventana **Trabajo** y vuelva a la ventana **Runbook**.

   ![Captura de pantalla en la que se resalta el icono Salida.](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Repita los dos pasos anteriores, pero utilice **STOP** como valor de **OPERATION**. Inicie de nuevo el runbook seleccionando el botón **Iniciar** en la barra de herramientas. Escriba los nombres del grupo de recursos, la instancia de ADF y la instancia de Azure-SSIS IR. En **OPERATION** (Operación), escriba **STOP** (Detener). En la ventana de salida, espere hasta ver el mensaje **### Completed ###** (Completado) después de ver **### Stopping###** (Deteniendo). No se tarda tanto en detener Azure-SSIS IR como en iniciarlo. Cierre la ventana **Trabajo** y vuelva a la ventana **Runbook**.

8. También puede desencadenar el runbook a través de un webhook que se puede crear seleccionando el elemento de menú **Webhooks** o según una programación que se pueda crear seleccionando el elemento de menú **Programaciones** tal como se especifica a continuación.  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Creación de programaciones para que el runbook inicie o detenga Azure-SSIS IR

En la sección anterior, ha creado un runbook de Azure Automation que puede iniciar o detener Azure-SSIS IR. En esta sección, va a crear dos programaciones para el runbook. Al configurar la primera programación, especifique **START** para **OPERATION**. De igual forma, al configurar la segunda programación, especifique **STOP** para **OPERATION**. Para ver el procedimiento detallado para crear programaciones, consulte el artículo [Creación de una programación](../automation/shared-resources/schedules.md#create-a-schedule).

1. En la ventana **Runbook**, seleccione **Programaciones** y seleccione **+ Agregar una programación** en la barra de herramientas. 

   ![IR de SSIS en Azure: iniciado](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. En el panel **Programar Runbook**, lleve a cabo las acciones siguientes: 

    1. Seleccione **Vincular una programación a su Runbook**. 
    2. Seleccione **Crear una programación nueva**.
    3. En el panel **Nueva programación**, escriba **Iniciar IR a diario** como **Nombre**. 
    4. En **Se inicia el**, especifique una hora que se unos minutos posterior a la hora actual. 
    5. En **Periodicidad**, seleccione **Periódico**. 
    6. En **Repetir cada**, escriba **1** y seleccione **Día**. 
    7. Seleccione **Crear**. 

   ![Programación para el inicio de una instancia de IR de SSIS en Azure](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Cambie a la pestaña **Configuración de ejecución y parámetros**. Especifique los nombres del grupo de recursos, la instancia de ADF y la instancia de Azure-SSIS IR. Para **OPERATION**, especifique **START** y seleccione **Aceptar**. Vuelva a seleccionar **Aceptar** para ver la programación en la página **Programaciones** del runbook. 

   ![Captura de pantalla en la que se resalta el campo Operación.](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Repita los dos pasos anteriores para crear una programación denominada **Detener IR a diario**. Especifique una hora que sea al menos 30 minutos posterior a la hora que ha especificado en la programación **Iniciar IR a diario**. Para **OPERATION**, especifique **STOP** y seleccione **Aceptar**. Vuelva a seleccionar **Aceptar** para ver la programación en la página **Programaciones** del runbook. 

5. En la ventana **Runbook**, seleccione **Trabajos** en el menú de la izquierda. Debería ver los trabajos creados por las programaciones en las horas especificadas, con sus estados. Puede ver detalles del trabajo, como su salida, similares a los que ha visto después de probar el runbook. 

   ![Programación para el inicio de la instancia de IR de SSIS en Azure](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. Cuando haya terminado las pruebas, edite las programaciones para deshabilitarlas. Seleccione **Programaciones** en el menú de la izquierda, elija **Iniciar IR a diario o Detener IR a diario** y seleccione **No** para **Habilitado**. 

## <a name="next-steps"></a>Pasos siguientes
Vea la siguiente entrada de blog:
-   [Modernize and extend your ETL/ELT workflows with SSIS activities in ADF pipelines](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370) (Modernización y ampliación de los flujos de trabajo ETL/ETL con actividades de SSIS en las canalizaciones de ADF)

Consulte los siguientes artículos de la documentación de SSIS: 

- [Deploy, run, and monitor an SSIS package on Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) (Implementación, ejecución y supervisión de un paquete SSIS en Azure)   
- [Connect to SSIS catalog on Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) (Conexión al catálogo de SSIS en Azure)
- [Schedule package execution on Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) (Programación de la ejecución de un paquete en Azure)
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Conexión a orígenes de datos locales con autenticación de Windows)