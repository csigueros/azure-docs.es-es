---
title: Uso de Azure Automation para iniciar un trabajo en StorSimple Data Manager
description: Aprenda a usar Azure Automation para desencadenar trabajos de StorSimple Data Manager
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 99824c80b041af284d3e71f720943b4425b78a75
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730276"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Uso de Azure Automation para desencadenar un trabajo

En este artículo se explica cómo se usa la característica de transformación de datos en StorSimple Data Manager para transformar los datos de los dispositivos StorSimple. Puede iniciar un trabajo de transformación de datos de dos maneras: 

 - Uso del SDK de .NET
 - Uso de un runbook de Azure Automation
 
En este artículo se detalla cómo crear un runbook de Azure Automation y, a continuación, usarlo para iniciar un trabajo de transformación de datos. Para más información sobre cómo iniciar la transformación de datos mediante el SDK de .NET, vaya a [Uso del SDK de .NET para desencadenar trabajos de transformación de datos](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Prerequisites

Antes de comenzar, asegúrese de que dispone de:

*   Azure PowerShell instalado en el equipo cliente. [Descarga de Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
*   Una definición de trabajo configurada correctamente en un servicio StorSimple Data Manager dentro de un grupo de recursos.
*   Descargue el archivo [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) del repositorio de GitHub. 
*   Descargue el script [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) del repositorio de GitHub.

## <a name="step-by-step-procedure"></a>Procedimiento paso a paso

### <a name="set-up-the-automation-account"></a>Configuración de la cuenta de Automation

1. Cree una cuenta de automatización de ejecución de Azure en Azure Portal. Para ello, vaya a **Azure Marketplace > Todo** y, después, busque **Automation**. Seleccione **Cuentas de Automation**.

    ![Creación de una cuenta de ejecución de Automation](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Para agregar una nueva cuenta de automatización, haga clic en **+ Agregar**.

    ![Creación de una cuenta de ejecución de Automation 2](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. En **Agregar automatización**:

   1. Suministre el **nombre** de la cuenta de Automation.
   2. Seleccione la **suscripción** vinculada al servicio StorSimple Data Manager.
   3. Cree un nuevo grupo de recursos o seleccione uno del grupo de recursos existente.
   4. Seleccione una **ubicación**.
   5. Deje la opción predeterminada **Crear cuenta de ejecución** seleccionada.
   6. Para obtener un vínculo para acceso rápido al panel, consulte **Anclar al panel**. Haga clic en **Crear**.

      ![Creación de una cuenta de ejecución de Automation 3](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Recibirá una notificación cuando la cuenta de automatización se cree correctamente.
    
      ![Notificación de la implementación de la cuenta de automatización](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      Para más información, vaya a [Creación de una cuenta de ejecución](../automation/manage-runas-account.md).

3. En la cuenta recién creada, vaya a **Recursos compartidos > Módulos** y haga clic en **+ Agregar módulo**.

    ![Importación del módulo 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Vaya a la ubicación del archivo `DataTransformationApp.zip` en el equipo local y seleccione y abra el módulo. Haga clic en **Aceptar** para importar el módulo.

    ![Importación del módulo 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Cuando Azure Automation importa un módulo a su cuenta, extrae los metadatos sobre el módulo. Esta operación puede demorar unos minutos.

   ![Importación del módulo 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Recibe una notificación que le indica que el módulo se está implementado y otra notificación cuando se complete el proceso.  El estado en **Módulos** cambia a **Disponible**.

    ![Importación del módulo 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Importar, publicar y ejecutar el runbook de Automation

Realice los pasos siguientes para importar, publicar y ejecutar el runbook para desencadenar la definición del trabajo.

1. En Azure Portal, abra su cuenta de Automation. Vaya a **Automatización de procesos > Runbooks** y haga clic en **+ Agregar un runbook**.

    ![Incorporación del runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. En **Agregar runbook**, haga clic en **Importar un runbook existente**.

3. Seleccione el archivo de script de Azure PowerShell `Trigger-DataTransformation-Job.ps1` para **Archivo de runbook**. El tipo de runbook se selecciona automáticamente. Proporcione un nombre y una descripción opcional del runbook. Haga clic en **Crear**.

    ![Incorporación del runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. El runbook nuevo aparece en la lista de runbooks de la cuenta de Automation. Seleccione y haga clic en este runbook.

    ![Incorporación del runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Edite el runbook y haga clic en **Probar**.

    ![Incorporación del runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Proporcione los parámetros, como el nombre del servicio StorSimple Data Manager, el grupo de recursos asociado y el nombre de la definición de trabajo. **Inicie** la prueba. Una vez completada la ejecución, se genera el informe. Para más información, vaya a cómo [probar un runbook](../automation/learn/powershell-runbook-managed-identity.md).

    ![Incorporación del runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Revise el resultado del runbook en el panel de prueba. Si le parece correcto, cierre el panel. Haga clic en **Publicar** y, cuando se le solicite una confirmación, confirme y publique el runbook.

    ![Incorporación del runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Vuelva a **Runbooks** y seleccione el runbook recién creado.

    ![Incorporación del runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Inicie** el runbook. En **Iniciar runbook**, escriba todos los parámetros. Haga clic en **Aceptar** para enviar e iniciar el trabajo de transformación de datos.

10. Para supervisar el progreso del trabajo en Azure Portal, vaya a **Trabajos** en el servicio StorSimple Data Manager. Seleccione y haga clic en el trabajo para ver los detalles del trabajo.

    ![Incorporación del runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Pasos siguientes

[Manage using the StorSimple Data Manager service UI](storsimple-data-manager-ui.md) (Administración del uso de la interfaz de usuario del servicio StorSimple Data Manager).