---
title: Creación de runbooks modulares en Azure Automation
description: En este artículo se explica cómo crear un runbook al que llama otro runbook.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 11bd439b511f6ae60078f5006a1ff4571b5908e9
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129617271"
---
# <a name="create-modular-runbooks-in-automation"></a>Creación de runbooks modulares en Automation

Un procedimiento recomendado en Azure Automation es escribir runbooks que sean reutilizables y modulares con una función independiente a la que puedan llamar otros runbooks. Con frecuencia, un runbook primario llama a uno o varios runbooks secundarios para realizar la funcionalidad necesaria. 

Hay dos maneras de llamar a un runbook secundario: en modo insertado o a través de un cmdlet. En la tabla siguiente se resumen las diferencias que le ayudarán a decidir cuál es mejor para sus escenarios.

|  | En línea | Cmdlet |
|:--- |:--- |:--- |
| **Trabajo** |Los runbooks secundarios se ejecutan en el mismo trabajo que el primario. |Se crea un trabajo independiente para el runbook secundario. |
| **Ejecución** |El runbook primario espera a que el runbook secundario se complete antes de continuar. |El runbook primario continúa inmediatamente después de que se inicie el runbook secundario, *o* el runbook primario espera a que finalice el trabajo secundario. |
| **Salida** |El runbook primario puede obtener la salida directamente del runbook secundario. |El runbook primario debe obtener la salida del trabajo de runbook secundario, *o* el runbook primario puede obtener la salida directamente del runbook secundario. |
| **Parámetros** |Los valores de los parámetros del runbook secundario se especifican por separado y pueden usar cualquier tipo de datos. |Los valores de los parámetros del runbook secundario deben combinarse en una sola tabla hash. Esta tabla hash solo puede incluir tipos de datos simples, de matriz y de objeto que usen la serialización JSON. |
| **Cuenta de Automation** |El runbook primario solo puede usar un runbook secundario de la misma cuenta de Automation. |Los runbooks primarios pueden usar un runbook secundario desde cualquier cuenta de Automation, desde la misma suscripción de Azure e incluso desde otra suscripción con la que se tenga conexión. |
| **Publicación** |Un runbook secundario debe publicarse antes de publicar el runbook primario. |Un runbook secundario se publica en cualquier momento antes de iniciar el runbook primario. |

## <a name="call-a-child-runbook-by-using-inline-execution"></a>Invocación de un runbook secundario mediante la ejecución insertada

Para llamar a un runbook en modo insertado desde otro runbook, use el nombre del runbook y proporcione valores para sus parámetros, igual a como usaría una actividad o un cmdlet. De esta manera, todos los runbooks de la misma cuenta de Automation estarán disponibles para que todos los demás los usen. El runbook primario espera a que el runbook secundario se complete antes de pasar a la siguiente línea y cualquier salida se devuelve directamente al primario.

Al invocar un runbook en modo insertado, este se ejecuta en el mismo trabajo que el runbook primario. No hay ninguna indicación en el historial de trabajos del runbook secundario. Todas las excepciones y salidas de flujo pertenecientes al runbook secundario se asocian al primario. Este comportamiento da lugar a que se creen menos trabajos y facilita el seguimiento y la solución de problemas.

Cuando se publica un runbook, cualquier runbook secundario al que llame deberá estar ya estar publicado. Esto se debe a que Azure Automation crea una asociación con los runbooks secundarios cuando compila un runbook. Si aún no se han publicado los runbooks secundarios, el runbook primario parece publicarse correctamente, pero genera una excepción cuando se inicia. 

Si obtiene una excepción, puede volver a publicar el runbook primario para que haga referencia correctamente a los runbooks secundarios. No es necesario que vuelva a publicar el runbook primario si cambia algún runbook secundario, porque ya se habrá creado la asociación.

Los parámetros de un runbook secundario al que se llama de modo insertado pueden ser de cualquier tipo de datos, incluidos objetos complejos. No hay ninguna [serialización JSON](start-runbooks.md#work-with-runbook-parameters), como sucede cuando se inicia el runbook mediante Azure Portal o con el cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook).

### <a name="runbook-types"></a>Tipos de runbook

Solo determinados tipos de runbook pueden llamarse entre sí:

* Un [runbook de PowerShell](automation-runbook-types.md#powershell-runbooks) y un [runbook gráfico](automation-runbook-types.md#graphical-runbooks) pueden llamarse entre sí en modo insertado, ya que ambos se basan en PowerShell.
* Un [runbook de flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) y un runbook gráfico de flujo de trabajo de PowerShell pueden llamarse entre sí en modo insertado, ya que ambos tipos se basan en el flujo de trabajo de PowerShell.
* Los tipos de PowerShell y los tipos de flujo de trabajo de PowerShell no pueden llamarse entre sí en modo insertado. Deben usar `Start-AzAutomationRunbook`.

El orden de publicación de los runbooks solo es importante para los runbooks de flujo de trabajo de PowerShell y los runbooks gráficos de flujo de trabajo de PowerShell.

Cuando el runbook llama a un runbook secundario gráfico o de flujo de trabajo de PowerShell con la ejecución insertada, usa el nombre del runbook. El nombre debe comenzar por `.\\` para especificar que el script se encuentra en el directorio local.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se inicia un runbook secundario de prueba que acepta un objeto complejo, un valor entero y un valor booleano. Los resultados del runbook secundario se asignan a una variable. En este caso, el runbook secundario es un runbook de flujo de trabajo de PowerShell.

```powershell
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = PSWF-ChildRunbook -VM $vm -RepeatCount 2 -Restart $true
```

A continuación se muestra el mismo ejemplo con un runbook de PowerShell como secundario.

```powershell
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = .\PS-ChildRunbook.ps1 -VM $vm -RepeatCount 2 -Restart $true
```

## <a name="start-a-child-runbook-by-using-a-cmdlet"></a>Inicio de un runbook secundario mediante un cmdlet

> [!IMPORTANT]
> Si el runbook llama a un runbook secundario con el cmdlet `Start-AzAutomationRunbook` y el parámetro `Wait`, y el runbook secundario genera un resultado de objeto, es posible que se produzca un error en la operación. Para resolver el error, consulte el tema sobre [Runbooks secundarios con salida de objeto](troubleshoot/runbooks.md#child-runbook-object). En este artículo se muestra cómo implementar la lógica para sondear los resultados mediante el cmdlet [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord).

Puede utilizar `Start-AzAutomationRunbook` para iniciar un runbook, tal como se describe en [Inicio de un runbook con Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Existen dos modos de usar este cmdlet:

- El cmdlet devuelve el identificador del trabajo cuando se crea el trabajo para el runbook secundario. 
- El cmdlet espera a que finalice el trabajo secundario y devuelve la salida del runbook secundario. Para habilitar este modo, el script especifica el parámetro `Wait`.

El trabajo de un runbook secundario que se inició con un cmdlet se ejecuta de forma independiente al trabajo del runbook primario. Como resultado de este comportamiento, habrá más trabajos que cuando se inicia el runbook en modo insertado y será más difícil realizar un seguimiento de los trabajos. Asimismo, el primario puede iniciar varios runbooks secundarios de forma asincrónica sin tener que esperar a que se complete cada uno de ellos. Para esta ejecución en paralelo que llama a los runbooks secundarios en modo insertado, el runbook primario debe usar la [palabra clave parallel](automation-powershell-workflow.md#use-parallel-processing).

La salida del runbook secundario no se devuelve al runbook primario de forma confiable debido al tiempo. Además, las variables `$VerbosePreference` y `$WarningPreference`, entre otras, podrían no propagarse a los runbooks secundarios. Para evitar estos problemas, puede iniciar los runbooks secundarios como trabajos de Automation independientes mediante el cmdlet `Start-AzAutomationRunbook` con el parámetro `Wait`. Esta técnica bloquea el runbook primario hasta que se completa el runbook secundario.

Si no quiere bloquear el runbook primario durante la espera, puede iniciar el runbook secundario mediante el cmdlet `Start-AzAutomationRunbook` sin el parámetro `Wait`. En este caso, el runbook debe usar [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) para esperar la finalización del trabajo. También debe usar [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) y [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) para recuperar los resultados.

Los parámetros de un runbook secundario iniciado con un cmdlet se proporcionan como una tabla hash, como se describe en [Parámetros de runbook](start-runbooks.md#work-with-runbook-parameters). Solo puede usar tipos de datos simples. Si el runbook tiene un parámetro con un tipo de datos complejo, debe llamarse en modo insertado.

Es posible que se pierda el contexto de la suscripción cuando inicia runbooks secundarios como trabajos individuales. Para que el runbook secundario ejecute los cmdlets del módulo Az en una suscripción de Azure específica, el secundario debe autenticarse en esta suscripción de forma independiente al runbook primario.

Si los trabajos de una misma cuenta de Automation funcionan con varias suscripciones, la selección de una suscripción en uno de los trabajos puede cambiar el contexto de suscripción actualmente seleccionado para otros trabajos. Para evitar esta situación, use `Disable-AzContextAutosave -Scope Process` al principio de cada runbook. Esta acción solo guarda el contexto de esa ejecución de runbook.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se inicia un runbook secundario con parámetros y se espera a que finalice mediante el cmdlet `Start-AzAutomationRunbook` con el parámetro `Wait`. Una vez que finaliza el runbook secundario, el ejemplo recopila la salida del cmdlet del runbook secundario. Para usar `Start-AzAutomationRunbook`, el script debe autenticarse en su suscripción de Azure.

```powershell
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    -AutomationAccountName 'MyAutomationAccount' `
    -Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -DefaultProfile $AzureContext `
    -Parameters $params -Wait
```

Si desea que el runbook se ejecute con la identidad administrada asignada por el sistema, deje el código tal y como está. Si prefiere usar una identidad administrada asignada por el usuario, haga lo siguiente:
1. En la línea 5, quite `$AzureContext = (Connect-AzAccount -Identity).context`.
1. Reemplace el valor por `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`.
1. Escriba el id. de cliente.

## <a name="next-steps"></a>Pasos siguientes

* Para ejecutar el runbook, consulte [Inicio de un runbook en Azure Automation](start-runbooks.md).
* Para supervisar la operación del runbook, consulte [Salida y mensajes del runbook en Azure Automation](automation-runbook-output-and-messages.md).
