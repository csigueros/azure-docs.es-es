---
title: Creación de un runbook gráfico en Azure Automation
description: Este artículo le enseña a crear, probar y publicar un runbook gráfico en Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 07/16/2021
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e782fd046af23beb5c84c8e419cd5925db7230ce
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449078"
---
# <a name="tutorial-create-a-graphical-runbook"></a>Tutorial: Creación de un runbook gráfico

Este tutorial le guiará por la creación de un [runbook gráfico](../automation-runbook-types.md#graphical-runbooks) en Azure Automation. Puede crear y editar runbooks gráficos y runbooks de flujo de trabajo de PowerShell mediante el editor gráfico de Azure Portal.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un runbook gráfico simple
> * Prueba y publicación del runbook
> * Ejecutar y seguir el estado del trabajo de runbook
> * Actualización del runbook para iniciar una máquina virtual de Azure, con parámetros de runbook y vínculos condicionales

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

* [Cuenta de Automation](../index.yml) con una [cuenta de ejecución de Azure](../create-run-as-account.md) para contener el runbook y autenticarse en recursos de Azure. Esta cuenta debe tener permiso para iniciar y detener la máquina virtual.
* Módulos **Az.Accounts** y **Az.Compute** de PowerShell para la cuenta de Automation. Para más información, consulte [Administración de módulos en Azure Automation](../shared-resources/modules.md).
* Una [máquina virtual de Azure](../../virtual-machines/windows/quick-create-portal.md). Dado que va a detener e iniciar esta máquina, no debería ser una máquina virtual de producción. Comience con la máquina virtual **detenida**.

## <a name="create-runbook"></a>Creación de runbook

Empiece por crear un runbook simple que genere el texto `Hello World`.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. En Azure Portal, vaya a su cuenta de Automation.

1. En **Automatización de procesos**, seleccione **Runbooks** para abrir la página de **runbooks**.

1. Seleccione **Crear un Runbook** para abrir la página **Crear un runbook**.

1. Asigne al runbook el nombre `MyFirstRunbook-Graphical`.

1. En el menú desplegable **Tipo de Runbook**, seleccione **Gráfico**.

   :::image type="content" source="../media/automation-tutorial-runbook-graphical/create-graphical-runbook.png" alt-text="Cree una página de entrada de runbook.":::

1. Seleccione **Crear** para crear el runbook y abrir el editor gráfico, la página **Editar Runbook gráfica**.

## <a name="add-activities"></a>Adición de actividades

El lado izquierdo del editor es el **control Biblioteca**. El centro es el **lienzo**. El lado derecho es el **control Configuración**. El **control Biblioteca** permite seleccionar las actividades que se van a agregar al runbook. Va a agregar un cmdlet `Write-Output` para generar el texto desde el runbook.

1. En el campo de búsqueda del **control Biblioteca**, escriba `Write-Output`.

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

1. Desplácese hasta la parte inferior de la lista. Haga clic con el botón derecho en **Write-Output** y seleccione **Agregar al lienzo**. También puede seleccionar los puntos suspensivos (...) junto al nombre del cmdlet y seleccionar **Agregar al lienzo**.

1. En **Lienzo**, seleccione la actividad **Write-Output**. Esta acción rellena la página **Control de configuración**, que le permite configurar la actividad.

1. En el **control Configuración**, el campo **Etiqueta** tiene como valor predeterminado el nombre del cmdlet, pero puede cambiarlo a algo más descriptivo. Cámbielo a `Write Hello World to output`.

1. Seleccione **Parámetros** para especificar los valores de los parámetros del cmdlet.

   Algunos cmdlets tienen varios conjuntos de parámetros; debe seleccionar el que usará. En este caso, `Write-Output` solo tiene un conjunto de parámetros.

1. En la página **Configuración de parámetros de la actividad**, seleccione el parámetro `INPUTOBJECT` para abrir la página **Valor de parámetro**. Puede usar este parámetro para especificar el texto que se va a enviar al flujo de salida.

1. El menú desplegable **Origen de datos** proporciona orígenes que se usan para rellenar un valor de parámetro. En este menú, seleccione **Expresión de PowerShell**.

   Puede usar la salida de esos orígenes como otra actividad, un recurso de Automation o una expresión de PowerShell. En este caso, la salida es simplemente `Hello World`. Puede usar una expresión de PowerShell y especificar una cadena.

1. En el cuadro de texto **Expresión**, escriba `"Hello World"` y seleccione **Aceptar** dos veces para volver al editor gráfico.

1. Seleccione **Guardar** para guardar el runbook.

## <a name="test-the-runbook"></a>Probar el runbook

Antes de publicar el runbook para que esté disponible en producción, debe probarlo para asegurarse de que funciona correctamente. La prueba de un runbook ejecuta su versión de borrador y permite ver la salida de forma interactiva.

1. En el editor gráfico, seleccione **Panel de prueba** para abrir el panel **Prueba**.

1. Seleccione **Iniciar** para iniciar la prueba.

   Se crea un [trabajo de runbook](../automation-runbook-execution.md) y su estado se muestra en el panel. El estado del trabajo se inicia como `Queued`, que indica que el trabajo está esperando a que haya disponible un trabajo de runbook en la nube. El estado cambia a `Starting` cuando un rol de trabajo solicita el trabajo. Por último, el estado pasa a `Running` cuando el runbook comienza a ejecutarse.

   Cuando se completa el trabajo del runbook, el panel de prueba muestra su salida. En este caso, verá `Hello World`.

   :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-test-results.png" alt-text="Salida de runbook Hola mundo.":::

1. Seleccione **X** en la esquina superior derecha para cerrar el panel **Prueba** y volver al editor gráfico.

## <a name="publish-and-start-the-runbook"></a>Publicar e iniciar el runbook

El runbook que ha creado todavía está en modo borrador y debe publicarse para poder ejecutarlo en producción. Al publicar un runbook, se sobrescribe la versión publicada existente con la versión de borrador.

1. En el editor gráfico, seleccione **Publicar** para publicar el runbook y **Sí** cuando se le solicite. Se le devolverá a la página de información general del **runbook**.

1. En la página de información general del **runbook**, el valor de **Estado** es **Publicado**.

1. Para iniciar el runbook, seleccione **Iniciar** y **Sí** cuando se le solicite, y abra la página **Trabajo**.

   Las opciones que se encuentran en la parte superior permiten iniciar el runbook ahora, programar una hora de inicio futura o crear un [webhook](../automation-webhooks.md) para que el runbook pueda iniciarse mediante una llamada HTTP.

   :::image type="content" source="../media/automation-tutorial-runbook-graphical/published-status.png" alt-text="Página de información general y estado publicado.":::

1. En la página **Trabajo**, compruebe que el campo **Estado** aparece como **Completado**.

1. Seleccione **Salida** para que aparezca `Hello World`.

1. Seleccione **Todos los registros** para ver los flujos de trabajo del runbook y seleccione la única entrada para abrir la página **Detalles del flujo de trabajo**. Solo debería ver `Hello World`.

    En **Todos los registros** se pueden mostrar otros flujos de trabajo del runbook, como Error y Modo detallado, si el runbook escribe en ellos.

1. Cierre la página **Detalles del flujo de trabajo** y, a continuación, la página **Trabajo** para volver a la página de información general del **runbook**.

1. En **Recursos**, seleccione **Trabajos** para ver todos los trabajos del runbook. La página Trabajos muestra todos los trabajos creados por el runbook. Solo debería ver un trabajo en la lista, ya que solo ejecutó el trabajo una vez.

1. Seleccione el trabajo para abrir la misma página **Trabajo** que vio cuando se inició el runbook.

1. Cierre la página **Trabajo** y, a continuación, en el menú de la izquierda, seleccione **Información general**.

## <a name="create-variable-assets"></a>Creación de recursos de variables

Ha probado y publicado el runbook, pero hasta ahora no resulta útil para gestionar recursos de Azure. Antes de configurar el runbook para la autenticación, debe crear una variable que contenga el identificador de suscripción, configurar una actividad para la autenticación y, después, hacer referencia a la variable. Incluir una referencia al contexto de suscripción permite trabajar fácilmente con varias suscripciones.

1. En **Información general**, seleccione el icono **Copiar al Portapapeles** junto a **Id. de suscripción**.

1. Cierre la página **Runbook** para volver a la página **Cuenta de Automation**.

1. En **Recursos compartidos**, seleccione **Variables**.

1. Seleccione **Agregar una variable** para abrir la página **Nueva variable**.

1. En la página **Nueva variable**, establezca los valores siguientes:

    | Campo| Value|
    |---|---|
    |Value|Presione <kbd>CTRL+V</kbd> para pegar en el identificador de la suscripción.|
    |Nombre |Escriba `AzureSubscriptionId`.|
    |Tipo|Mantenga el valor predeterminado **Cadena**.|
    |Cifrado|Mantenga el valor predeterminado **No**.|

1. Seleccione **Crear** para crear la variable y volver a la página **Variables**.

1. En **Automatización de procesos**, seleccione **Runbooks** y, a continuación, su runbook, **MyFirstRunbook-Graphical**.

## <a name="add-authentication"></a>Agregar autenticación

Ahora que tiene una variable para incluir el identificador de suscripción, puede configurar el runbook para que se autentique con las credenciales de ejecución de la suscripción. Para configurar la autenticación, agregue la conexión de ejecución de Azure como un recurso. También debe agregar los cmdlets [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) y [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) al lienzo.

> [!NOTE]
> En el caso de los runbooks de PowerShell, `Add-AzAccount` y `Add-AzureRMAccount` son alias de `Connect-AzAccount`. Estos alias no están disponibles para los runbooks gráficos. Un runbook gráfico solo puede usar `Connect-AzAccount`.

1. En la página **Runbook**, seleccione **Editar** para volver al editor gráfico.

1. Ya no necesita la actividad `Write Hello World to output`. Seleccione la actividad y aparecerán puntos suspensivos en la esquina superior derecha de la misma. Los puntos suspensivos pueden resultar difíciles de ver. Seleccione los puntos suspensivos y, a continuación, seleccione **Eliminar**.

1. En el **control Biblioteca**, vaya a **RECURSOS** > **Conexiones** > **AzureRunAsConnection**. Seleccione los puntos suspensivos y, a continuación, seleccione **Agregar al lienzo**.

1. En el **control Configuración**, cambie el valor de **Etiqueta** de `Get-AutomationConnection` a `Get Run As Connection`.

1. En el campo de búsqueda del **control Biblioteca**, escriba `Connect-AzAccount`.

1. Agregue `Connect-AzAccount` al lienzo y arrastre la actividad debajo de `Get Run As Connection`.

1. Mantenga el puntero sobre `Get Run As Connection` hasta que aparezca un círculo en la parte inferior de la forma. Mantenga presionado el círculo y aparecerá una flecha. Arrastre la flecha hasta `Connect-AzAccount` para crear un vínculo. El runbook se inicia con `Get Run As Connection` y, después, ejecuta `Connect-AzAccount`.

    ![Crear vínculo entre actividades](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

1. En el **lienzo**, seleccione `Connect-AzAccount`.

1. En el **control Configuración**, cambie el valor de **Etiqueta** de `Connect-AzAccount` a `Login to Azure`.

1. Seleccione **Parámetros** para abrir la página **Configuración de parámetros de la actividad**.

1. El cmdlet `Connect-AzAccount` tiene varios conjuntos de parámetros; deberá seleccionar uno antes de proporcionar los valores de los parámetros. Seleccione **Conjunto de parámetros** y, después, **ServicePrincipalCertificateWithSubscriptionId**. Tenga cuidado de no seleccionar **ServicePrincipalCertificateFileWithSubscriptionId**, ya que los nombres son similares.

   Los parámetros de este conjunto se muestran en la página **Configuración de parámetros de la actividad**.

    ![Agregar parámetros de cuenta de Azure](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

1. Seleccione **CERTIFICATETHUMBPRINT** para abrir la página **Valor de parámetro**.
    1. En el menú desplegable **Origen de datos**, seleccione **Resultado de la actividad**.
    1. En **Seleccionar datos** seleccione **Get Run As Connection** (Obtener conexión de ejecución).
    1. En el cuadro de texto **Ruta de acceso de campo**, escriba `CertificateThumbprint`.
    1. Seleccione **Aceptar** para volver a la página **Configuración de parámetros de la actividad**.

1. Seleccione **SERVICEPRINCIPAL** para abrir la página **Valor de parámetro**.
    1. En el menú desplegable **Origen de datos**, seleccione **Valor constante**.
    1. Seleccione la opción **Verdadero**.
    1. Seleccione **Aceptar** para volver a la página **Configuración de parámetros de la actividad**.

1. Seleccione **TENANT** para abrir la página **Valor de parámetro**.
    1. En el menú desplegable **Origen de datos**, seleccione **Resultado de la actividad**.
    1. En **Seleccionar datos** seleccione **Get Run As Connection** (Obtener conexión de ejecución).
    1. En el cuadro de texto **Ruta de acceso de campo**, escriba `TenantId`.
    1. Seleccione **Aceptar** para volver a la página **Configuración de parámetros de la actividad**.

1. Seleccione **APPLICATIONID** para abrir la página **Valor de parámetro**.
    1. En el menú desplegable **Origen de datos**, seleccione **Resultado de la actividad**.
    1. En **Seleccionar datos** seleccione **Get Run As Connection** (Obtener conexión de ejecución).
    1. En el cuadro de texto **Ruta de acceso de campo**, escriba `ApplicationId`.
    1. Seleccione **Aceptar** para volver a la página **Configuración de parámetros de la actividad**.

1. Seleccione **Aceptar** para volver al editor gráfico.

1. En el campo de búsqueda del **control Biblioteca**, escriba `Set-AzContext`.

1. Agregue `Set-AzContext` al lienzo y arrastre la actividad debajo de `Login to Azure`.

1. En el **control Configuración**, cambie el valor de **Etiqueta** de `Set-AzContext` a `Specify Subscription Id`.

1. Seleccione **Parámetros** para abrir la página **Configuración de parámetros de la actividad**.

1. El cmdlet `Set-AzContext` tiene varios conjuntos de parámetros; deberá seleccionar uno antes de proporcionar los valores de los parámetros. Seleccione **Conjunto de parámetros** y seleccione **Subscription**. Los parámetros de este conjunto se muestran en la página **Configuración de parámetros de la actividad**.

1. Seleccione **SUBSCRIPTION** para abrir la página **Valor de parámetro**.
    1. En el menú desplegable **Origen de datos**, seleccione **Activo de variable**.
    1. En la lista de variables, seleccione **AzureSubscriptionId**.
    1. Seleccione **Aceptar** para volver a la página **Configuración de parámetros de la actividad**.

1. Seleccione **Aceptar** para volver al editor gráfico.

1. Forme un vínculo entre `Login to Azure` y `Specify Subscription Id`. En este punto, el runbook debe tener el siguiente aspecto.

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="Captura de pantalla del runbook después de arrastrar la flecha a &quot;Specify Subscription ID&quot; (&quot;Especificar id. de suscripción&quot;).":::

## <a name="add-activity-to-start-a-virtual-machine"></a>Adición de una actividad para iniciar una máquina virtual

Ahora debe agregar una actividad `Start-AzVM` para iniciar una máquina virtual. Puede seleccionar cualquier máquina virtual de su suscripción de Azure. Por ahora, codificará de forma rígida ese nombre en el cmdlet [Start-AzVM](/powershell/module/az.compute/start-azvm).

1. En el campo de búsqueda del **control Biblioteca**, escriba `Start-AzVM`.

1. Agregue `Start-AzVM` al lienzo y arrastre la actividad debajo de `Specify Subscription Id`.

1. En el **control Configuración**, seleccione **Parámetros** para abrir la página **Configuración de parámetros de la actividad**.

1. Seleccione **Conjunto de parámetros** y, después, **ResourceGroupNameParameterSetName**. Los parámetros de este conjunto se muestran en la página **Configuración de parámetros de la actividad**. Los parámetros **RESOURCEGROUPNAME** y **NAME** tienen signos de exclamación junto a ellos para indicar que son parámetros obligatorios. Ambos campos esperan valores de cadena.

1. Seleccione **RESOURCEGROUPNAME** para abrir la página **Valor de parámetro**.
    1. En el menú desplegable **Origen de datos**, seleccione **Expresión de PowerShell**.
    1. En el cuadro de texto **Expresión**, escriba el nombre del grupo de recursos entre comillas dobles.
    1. Seleccione **Aceptar** para volver a la página **Configuración de parámetros de la actividad**.

1. Seleccione **NAME** para abrir la página **Valor de parámetro**.
    1. En el menú desplegable **Origen de datos**, seleccione **Expresión de PowerShell**.
    1. En el cuadro de texto **Expresión**, escriba el nombre de la máquina virtual entre comillas dobles.
    1. Seleccione **Aceptar** para volver a la página **Configuración de parámetros de la actividad**.

1. Seleccione **Aceptar** para volver al editor gráfico.

1. Forme un vínculo entre `Specify Subscription Id` y `Start-AzVM`. En este punto, el runbook debe tener el siguiente aspecto.

    ![Salida de runbook Start-AzVM](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

1. Seleccione **Panel de prueba** para poder probar el runbook.

1. Seleccione **Iniciar** para iniciar la prueba.

1. Una vez que se complete, asegúrese de que la máquina virtual se ha iniciado. A continuación, detenga la máquina virtual para los pasos posteriores.

1. Vuelva al editor gráfico del runbook.

## <a name="add-input-parameters"></a>Adición de parámetros de entrada

Actualmente, el runbook inicia la máquina virtual en el grupo de recursos que especificó para el cmdlet `Start-AzVM`. El runbook será más útil si especifica el nombre y el grupo de recursos cuando se inicie el runbook. Agreguemos parámetros de entrada al runbook para incluir esta funcionalidad.

1. En la barra de menús superior del editor gráfico, seleccione **Entrada y salida**.

1. Seleccione **Agregar entrada** para abrir la página **Parámetros de entrada de Runbook**.

1. En la página **Parámetros de entrada de runbook**, establezca los valores siguientes:

    | Campo| Value|
    |---|---|
    |Nombre| Escriba `VMName`.|
    |Tipo|Mantenga el valor predeterminado **Cadena**.|
    |Mandatory|Cambie el valor a **Sí**.|

1. Seleccione **Aceptar** para volver a la página **Entrada y salida**.

1. Seleccione **Agregar entrada** para volver a abrir la página **Parámetros de entrada de Runbook**.

1. En la página **Parámetros de entrada de runbook**, establezca los valores siguientes:

    | Campo| Value|
    |---|---|
    |Nombre| Escriba `ResourceGroupName`.|
    |Tipo|Mantenga el valor predeterminado **Cadena**.|
    |Mandatory|Cambie el valor a **Sí**.|

1. Seleccione **Aceptar** para volver a la página **Entrada y salida**. La página será parecida a la siguiente:

    ![Parámetros de entrada de runbook](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

1. Seleccione **Aceptar** para volver al editor gráfico.

1. Es posible que las nuevas entradas no estén disponibles inmediatamente. Seleccione **Guardar**, cierre el editor gráfico y, a continuación, vuelva a abrirlo. Las nuevas entradas deberían estar ya disponibles.

1. Seleccione la actividad `Start-AzVM` y, a continuación, seleccione **Parámetros** para abrir la página **Configuración de parámetros de la actividad**.

1. Para el parámetro configurado anteriormente, **RESOURCEGROUPNAME**, cambie el **Origen de datos** a **Entrada de Runbook** y, a continuación, seleccione **ResourceGroupName**. Seleccione **Aceptar**.

1. Para el parámetro configurado anteriormente, **NAME**, cambie el **Origen de datos** a **Entrada de Runbook** y, a continuación, seleccione **VMName**. Seleccione **Aceptar**. La página será parecida a la siguiente:

    ![Parámetros de Start-AzVM](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

1. Seleccione **Aceptar** para volver al editor gráfico.

1. Seleccione **Guardar** y, luego, **Panel de prueba**. Observe que ahora puede proporcionar valores para las dos variables de entrada que ha creado.

1. Cierre la página **Probar** para volver al editor gráfico.

1. Seleccione **Publicar** y, después, **Sí** cuando se le pida que publique la nueva versión del runbook. Se le devolverá a la página de información general del **runbook**.

1. Seleccione **Iniciar** para abrir la página **Iniciar runbook**.

1. Escriba los valores adecuados para los parámetros `VMNAME` y `RESOURCEGROUPNAME`. Después, seleccione **Aceptar**. A continuación, se abre la página **Trabajo**.

1. Supervise el trabajo y compruebe que la máquina virtual se inició después de que el **estado** se convirtiera en **Completo**. A continuación, detenga la máquina virtual para los pasos posteriores.

1. Vuelva al editor gráfico del runbook.

## <a name="create-a-conditional-link"></a>Creación de un vínculo condicional

Ahora puede modificar el runbook para que solo intente iniciar la máquina virtual si aún no se ha iniciado. Para ello, agregará un cmdlet [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) que recupere el estado de nivel de instancia de la máquina virtual. Tras ello, podrá agregar un módulo de código de flujo de trabajo de PowerShell denominado `Get Status` con un fragmento de código de PowerShell, para determinar si la máquina virtual está en ejecución o detenida. Un vínculo condicional desde el módulo `Get Status` solo ejecutará `Start-AzVM` si el estado de ejecución actual es detenido. Al final de este procedimiento, el runbook usará el cmdlet `Write-Output` para generar un mensaje que le informará de si la máquina virtual se ha iniciado correctamente.

1. En el editor gráfico, haga clic con el botón derecho en el vínculo entre `Specify Subscription Id` y `Start-AzVM`, y seleccione **Eliminar**.

1. En el campo de búsqueda del **control Biblioteca**, escriba `Get-AzVM`.

1. Agregue `Get-AzVM` al lienzo y arrastre la actividad debajo de `Specify Subscription Id`.

1. En el **control Configuración**, seleccione **Parámetros** para abrir la página **Configuración de parámetros de la actividad**.

   Seleccione **Conjunto de parámetros** y, a continuación, seleccione **GetVirtualMachineInResourceGroupParamSet**. Los parámetros de este conjunto se muestran en la página **Configuración de parámetros de la actividad**. Los parámetros **RESOURCEGROUPNAME** y **NAME** tienen signos de exclamación junto a ellos para indicar que son parámetros obligatorios. Ambos campos esperan valores de cadena.

1. Seleccione **RESOURCEGROUPNAME** para abrir la página **Valor de parámetro**.
    1. En el menú desplegable **Origen de datos**, seleccione **Entrada de Runbook**.
    1. Seleccione el parámetro **ResourceGroupName**.
    1. Seleccione **Aceptar** para volver a la página **Configuración de parámetros de la actividad**.

1. Seleccione **NAME** para abrir la página **Valor de parámetro**.
    1. En el menú desplegable **Origen de datos**, seleccione **Entrada de Runbook**.
    1. Seleccione el parámetro **VMName**.
    1. Seleccione **Aceptar** para volver a la página **Configuración de parámetros de la actividad**.

1. Seleccione **STATUS** para abrir la página **Valor de parámetro**.
    1. En el menú desplegable **Origen de datos**, seleccione **Valor constante**.
    1. Seleccione la opción **Verdadero**.
    1. Seleccione **Aceptar** para volver a la página **Configuración de parámetros de la actividad**.

1. Seleccione **Aceptar** para volver al editor gráfico.

1. Forme un vínculo entre `Specify Subscription Id` y `Get-AzVM`.

1. Borre el campo de búsqueda del **control Biblioteca** y, a continuación, vaya a **CONTROL DE RUNBOOK** > **Código**. Seleccione los puntos suspensivos y, a continuación, **Agregar al lienzo**. Arrastre la actividad debajo de `Get-AzVM`.

1. En el **control Configuración**, cambie el valor de **Etiqueta** de `Code` a `Get Status`.

1. En el **control Configuración**, seleccione **Código** para abrir la página **Editor de código**.

1. Pegue el siguiente fragmento de código en el cuadro de texto **Código de PowerShell**.

    ```powershell
    $Statuses = $ActivityOutput['Get-AzVM'].Statuses
    $StatusOut = ""
    foreach ($Status in $Statuses) {
      if($Status.Code -eq "Powerstate/running")
        {$StatusOut = "running"}
      elseif ($Status.Code -eq "Powerstate/deallocated")
        {$StatusOut = "stopped"}
    }
    $StatusOut
    ```

1. Seleccione **Aceptar** para volver al editor gráfico.

1. Forme un vínculo entre `Get-AzVM` y `Get Status`.

1. Forme un vínculo entre `Get Status` y `Start-AzVM`. En este punto, el runbook debe tener el siguiente aspecto.

    ![Runbook con el módulo de código](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

1. Seleccione el vínculo entre `Get Status` y `Start-AzVM`.

1. En el **control Configuración**, cambie **Aplicar condición** a **Sí**. El vínculo se convierte en una línea discontinua, lo que indica que la actividad de destino solo se ejecuta si la condición se resuelve como verdadera.  

1. En **Expresión de condición**, escriba `$ActivityOutput['Get Status'] -eq "Stopped"`. Ahora, `Start-AzVM` solo se ejecuta si la máquina virtual está detenida.

1. En el campo de búsqueda del **control Biblioteca**, escriba `Write-Output`.

1. Agregue `Write-Output` al lienzo y arrastre la actividad debajo de `Start-AzVM`.

1. Seleccione los puntos suspensivos de actividad y seleccione **Duplicar**. Arrastre la actividad duplicada a la derecha de la primera actividad.

1. Seleccione la primera actividad `Write-Output`.
    1. En el **control Configuración**, cambie el valor de **Etiqueta** de `Write-Output` a `Notify VM Started`.
    1. Seleccione **Parámetros** para abrir la página **Configuración de parámetros de la actividad**.
    1. Seleccione **INPUTOBJECT** para abrir la página **Valor de parámetro**.
    1. En el menú desplegable **Origen de datos**, seleccione **Expresión de PowerShell**.
    1. En el cuadro de texto **Expresión**, escriba `"$VMName successfully started."`.
    1. Seleccione **Aceptar** para volver a la página **Configuración de parámetros de la actividad**.
    1. Seleccione **Aceptar** para volver al editor gráfico.

1. Seleccione la primera actividad `Write-Output1`.
    1. En el **control Configuración**, cambie el valor de **Etiqueta** de `Write-Output1` a `Notify VM Start Failed`.
    1. Seleccione **Parámetros** para abrir la página **Configuración de parámetros de la actividad**.
    1. Seleccione **INPUTOBJECT** para abrir la página **Valor de parámetro**.
    1. En el menú desplegable **Origen de datos**, seleccione **Expresión de PowerShell**.
    1. En el cuadro de texto **Expresión**, escriba `"$VMName could not start."`.
    1. Seleccione **Aceptar** para volver a la página **Configuración de parámetros de la actividad**.
    1. Seleccione **Aceptar** para volver al editor gráfico.

1. Forme un vínculo entre `Start-AzVM` y `Notify VM Started`.

1. Forme un vínculo entre `Start-AzVM` y `Notify VM Start Failed`.

1. Seleccione el vínculo a `Notify VM Started` y cambie **Aplicar condición** a **Sí**.

1. En **Expresión de condición**, escriba `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`. Este control `Write-Output` ahora solo se ejecuta si la máquina virtual se inicia correctamente.

1. Seleccione el vínculo a `Notify VM Start Failed`.

1. En la **página Control**, en **Aplicar condición**, seleccione **Sí**.

1. En el cuadro de texto **Expresión de condición**, escriba `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`. Ahora, este control `Write-Output` solo se ejecuta si la máquina virtual no se ha iniciado correctamente. El runbook debe tener un aspecto similar al de la siguiente imagen.

    ![Runbook con Write-Output](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

1. Guarde el runbook y abra el panel Prueba.

1. Inicie el runbook con la máquina virtual detenida; la máquina debe iniciarse.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre la creación de runbooks gráficos, consulte [Creación de un runbook gráfico en Azure Automation](../automation-graphical-authoring-intro.md).
* Para empezar a trabajar con Runbooks de PowerShell, consulte [Creación de un runbook de PowerShell](automation-tutorial-runbook-textual-powershell.md).
* Para empezar a trabajar con runbooks de flujo de trabajo de PowerShell, consulte [Creación de un runbook de flujo de trabajo de PowerShell](automation-tutorial-runbook-textual.md).
* Para ver una referencia de los cmdlets de PowerShell, consulte [Az.Automation](/powershell/module/az.automation).
