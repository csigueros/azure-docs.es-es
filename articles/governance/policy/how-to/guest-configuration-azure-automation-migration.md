---
title: Planeamiento de la migración de State Configuration de Azure Automation a la configuración de invitado
description: En este artículo se proporcionan instrucciones técnicas y de procesos para los clientes interesados en pasar de la versión 2 de DSC de Azure Automation a la versión 3 de Azure Policy.
ms.date: 07/1/2021
ms.topic: how-to
ms.openlocfilehash: ae387e59abe5cbff335b43cdd78030f32f6e6f8c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868622"
---
# <a name="azure-automation-state-configuration-to-guest-configuration-migration-planning"></a>Planeamiento de la migración de la configuración de invitado de Azure Automation a la configuración de invitado

La configuración de invitado es la implementación más reciente de la funcionalidad proporcionada por Azure Automation State Configuration (también conocida como Azure Automation Desired State Configuration o AADSC).
Cuando sea posible, debe planear el traslado de su contenido y sus máquinas al nuevo servicio.
En este artículo se proporcionan instrucciones sobre cómo desarrollar una estrategia de migración de Azure Automation a la configuración de invitado.

Las nuevas características de la configuración de invitado abordan las principales peticiones de clientes:

- Mayor límite de tamaño para las configuraciones (100 MB)
- Informes avanzados a través de Azure Resource Graph, incluidos el Id. y el estado de los recursos
- Administración de varias configuraciones para la misma máquina
- Control de cuándo se produce la corrección en aquellos casos en los que la máquinas se desvían del estado deseado
- Consumo de recursos de DCS basados en PowerShell por parte de Linux y Windows

Antes de empezar, es aconsejable leer la información conceptual general que está disponible en la página [Configuración de invitado de Azure Policy](../concepts/guest-configuration.md).

## <a name="understand-migration"></a>Descripción de la migración

El mejor método de migración es, primero, volver a implementar el contenido y, después, migrar las máquinas. A continuación figuran los pasos esperados de la migración:

- Exportación de configuraciones desde Azure Automation
- Detección de los requisitos de módulo y carga en el entorno
- Compilación de configuraciones
- Creación y publicación de paquetes de configuración de invitado
- Prueba de los paquetes de configuración de invitado
- Incorporación de máquinas híbridas a Azure Arc
- Anulación del registro de servidores de Azure Automation State Configuration
- Asignación de configuraciones a servidores mediante la configuración de invitado

La configuración de invitado usa la versión 3 de DSC con la versión 7 de PowerShell. La versión 3 de DSC puede coexistir con versiones anteriores en [Windows](/powershell/scripting/dsc/getting-started/wingettingstarted) y [Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).
Las implementaciones son independientes. Sin embargo, no se detectan posibles conflictos.

La configuración de invitado no requiere la publicación de módulos o configuraciones en un servicio ni la compilación en este. En su lugar, el contenido se desarrolla y prueba mediante herramientas creadas específicamente y se publica en cualquier lugar al que la máquina pueda acceder a través de HTTPS (normalmente Azure Blob Storage).

Si decide que el plan adecuado para la migración es tener máquinas en ambos servicios durante un período de tiempo, no existen barreras técnicas, aunque la administración podría resultar confusa. Ambos servicios son independientes.

## <a name="export-content-from-azure-automation"></a>Exportación de contenido desde Azure Automation

Empiece detectando y exportando contenido desde Azure Automation State Configuration a un entorno de desarrollo donde cree, pruebe y publique paquetes de contenido para la configuración de invitado.

### <a name="configurations"></a>Configuraciones

Solo se pueden exportar scripts de configuración desde Azure Automation. No es posible exportar configuraciones de nodo ni archivos MOF compilados.
Si ha publicado archivos MOF directamente en la cuenta de Automation y ya no tiene acceso al archivo original, debe volver a compilar la configuración a partir de los scripts privados o, posiblemente, volver a crearla si no se encuentra el original.

Para exportar scripts de configuración desde Azure Automation, primero identifique la cuenta del servicio que contiene las configuraciones y el nombre del grupo de recursos donde está implementada.

Instale el módulo de PowerShell "Az.Automation".

```powershell
Install-Module Az.Automation
```

A continuación, use el comando "Get-AzAutomationAccount" para identificar las cuentas de Automation y el grupo de recursos donde están implementadas.
Las propiedades "ResourceGroupName" y "AutomationAccountName" son importantes para los siguientes pasos.

```powershell
Get-AzAutomationAccount

SubscriptionId        : <your subscription id>
ResourceGroupName     : <your resource group name>
AutomationAccountName : <your automation account name>
Location              : centralus
State                 :
Plan                  :
CreationTime          : 6/30/2021 11:56:17 AM -05:00
LastModifiedTime      : 6/30/2021 11:56:17 AM -05:00
LastModifiedBy        :
Tags                  : {}
```

Detecte las configuraciones en su cuenta de Automation. La salida contiene una entrada por configuración. Si tiene muchas, almacene la información como una variable para que sea más fácil trabajar con ella.

```powershell
Get-AzAutomationDscConfiguration -ResourceGroupName <your resource group name> -AutomationAccountName <your automation account name>

ResourceGroupName     : <your resource group name>
AutomationAccountName : <your automation account name>
Location              : centralus
State                 : Published
Name                  : <your configuration name>
Tags                  : {}
CreationTime          : 6/30/2021 12:18:26 PM -05:00
LastModifiedTime      : 6/30/2021 12:18:26 PM -05:00
Description           :
Parameters            : {}
LogVerbose            : False
```

Por último, exporte cada configuración a un archivo de script local mediante el comando "Export-AzAutomationDscConfiguration". El nombre de archivo resultante usa el patrón `\ConfigurationName.ps1`.

```powershell
Export-AzAutomationDscConfiguration -OutputFolder /<location on your machine> -ResourceGroupName <your resource group name> -AutomationAccountName <your automation account name> -name <your configuration name>

UnixMode   User             Group                 LastWriteTime           Size Name
--------   ----             -----                 -------------           ---- ----
                                               12/31/1600 18:09
```

#### <a name="export-configurations-using-the-powershell-pipeline"></a>Exportación de configuraciones mediante la canalización de PowerShell

Una vez que haya detectado sus cuentas y el número de configuraciones, es posible que quiera exportar todas las configuraciones a una carpeta local de su máquina.
Para automatizar este proceso, canalice la salida de cada comando anterior al siguiente.

En el ejemplo se exportan cinco configuraciones. El patrón de salida es la única indicación de éxito:

```powershell
Get-AzAutomationAccount | Get-AzAutomationDscConfiguration | Export-AzAutomationDSCConfiguration -OutputFolder /<location on your machine>

UnixMode   User             Group                 LastWriteTime           Size Name
--------   ----             -----                 -------------           ---- ----
                                               12/31/1600 18:09
                                               12/31/1600 18:09
                                               12/31/1600 18:09
                                               12/31/1600 18:09
                                               12/31/1600 18:09
```

#### <a name="consider-decomposing-complex-configuration-files"></a>Posibilidad de descomponer archivos de configuración complejos

La configuración de invitado puede administrar varias configuraciones por máquina.
Muchas configuraciones escritas para Azure Automation State Configuration daban por sentada la limitación de administrar una única configuración por máquina. Para aprovechar las funcionalidades ampliadas que ofrece la configuración de invitado, los archivos de configuración de gran tamaño se pueden dividir en muchas configuraciones más pequeñas, donde cada una se encarga de un escenario específico.

No hay ninguna orquestación en la configuración de invitado para controlar el criterio de ordenación de las configuraciones, por lo que mantenga los pasos de una configuración juntos en un paquete si es necesario que se den de forma secuencial.

### <a name="modules"></a>Módulos

No es posible exportar módulos desde Azure Automation o correlacionar automáticamente qué configuraciones requieren qué módulo o versión. Debe tener los módulos en su entorno local para crear un paquete de configuración de invitado. Si desea crear una lista de los módulos que necesita para la migración, use PowerShell para consultar a Azure Automation el nombre y la versión de los módulos.

Si usa módulos personalizados de creación propia que solo existen en su entorno de desarrollo privado, no es posible exportarlos desde Azure Automation.

Si se necesita un módulo personalizado para una configuración y está en la cuenta, pero no se encuentra en su entorno, no podrá compilar la configuración y, por tanto, tampoco migrarla.

#### <a name="list-modules-imported-in-azure-automation"></a>Enumeración de los módulos importados en Azure Automation

Para recuperar una lista de todos los módulos instalados en su cuenta de Automation, use el comando `Get-AzAutomationModule`. La propiedad "IsGlobal" le indica si el módulo está integrado siempre en Azure Automation o si se ha publicado en la cuenta.

Por ejemplo, para crear una lista de todos los módulos publicados en cualquiera de sus cuentas:

```powershell
Get-AzAutomationAccount | Get-AzAutomationModule | ? IsGlobal -eq $false
```

También puede usar la Galería de PowerShell como ayuda para encontrar detalles sobre los módulos que están disponibles públicamente. En el siguiente ejemplo se genera la lista de módulos integrados en nuevas cuentas de Automation y que contienen recursos de DSC:

```powershell
Get-AzAutomationAccount | Get-AzAutomationModule | ? IsGlobal -eq $true | Find-Module -erroraction silentlycontinue | ? {'' -ne $_.Includes.DscResource} | Select Name, Version -Unique | format-table -AutoSize

Name                       Version
----                       -------
AuditPolicyDsc             1.4.0
ComputerManagementDsc      8.4.0
PSDscResources             2.12.0
SecurityPolicyDsc          2.10.0
xDSCDomainjoin             1.2.23
xPowerShellExecutionPolicy 3.1.0.0
xRemoteDesktopAdmin        1.1.0.0
```

#### <a name="download-modules-from-powershell-gallery-or-powershellget-repository"></a>Descarga de módulos desde la Galería de PowerShell o el repositorio PowerShellGet

Si los módulos se han importado desde la Galería de PowerShell, puede canalizar la salida desde `Find-Module` directamente en `Install-Module`. La canalización de la salida entre comandos proporciona una solución para cargar un entorno de desarrollador con todos los módulos que se encuentran en una cuenta de Automation y están disponibles públicamente en la Galería de PowerShell.

Se podría usar el mismo método para extraer módulos de una fuente de NuGet personalizada si la fuente está registrada en su entorno local como [repositorio de PowerShellGet](/powershell/scripting/gallery/how-to/working-with-local-psrepositories).

El comando `Find-Module` del ejemplo no suprime los errores, por lo que los módulos que no se encuentren en la galería devolverán un mensaje de error.

```powershell
Get-AzAutomationAccount | Get-AzAutomationModule | ? IsGlobal -eq $false | Find-Module | ? {'' -ne $_.Includes.DscResource} | Install-Module

  Installing package xWebAdministration'

    [                                                                                        ]
```

#### <a name="inspecting-configuration-scripts-for-module-requirements"></a>Inspección de scripts de configuración para comprobar si cumplen los requisitos de módulo

Si ha exportado scripts de configuración desde Azure Automation, también puede revisar el contenido para obtener detalles sobre los módulos que deben compilar cada configuración en un archivo MOF. Este enfoque solo sería necesario si encuentra configuraciones en sus cuentas de Automation donde se hayan eliminado los módulos.
En ese caso, las configuraciones ya no serían útiles para las máquinas, pero puede que sí en la cuenta.

Hacia la parte superior de cada archivo, busque una línea que incluya "Import-DscResource".
Este comando solo es aplicable dentro de una configuración y se usa para cargar módulos en el momento de la compilación.

En el siguiente ejemplo es la configuración "WindowsIISServerConfig" de la Galería de PowerShell la que contiene las líneas:

```powershell
configuration WindowsIISServerConfig
{

Import-DscResource -ModuleName @{ModuleName = 'xWebAdministration';ModuleVersion = '1.19.0.0'}
Import-DscResource -ModuleName 'PSDesiredStateConfiguration'
```

La configuración requiere que tenga la versión "1.19.0.0" del módulo "xWebAdministration" y el módulo "PSDesiredStateConfiguration".

### <a name="test-content-in-azure-guest-configuration"></a>Prueba del contenido en la configuración de invitado de Azure

La mejor manera de evaluar si su contenido de Azure Automation State Configuration se puede usar con la configuración de invitado es seguir el tutorial paso a paso de la página [Creación de artefactos de paquetes de configuración de invitado personalizados](./guest-configuration-create.md).

Cuando llegue al paso [Creación de una configuración](./guest-configuration-create.md#author-a-configuration), el script de configuración que genera un archivo MOF debe ser uno de los scripts que haya exportado desde Azure Automation State Configuration. Debe tener instalados en su entorno los módulos de PowerShell necesarios para poder compilar la configuración en un archivo MOF y crear un paquete de configuración de invitado.

#### <a name="what-if-a-module-does-not-work-with-guest-configuration"></a>¿Qué ocurre si un módulo no funciona con la configuración de invitado?

Algunos módulos pueden tener problemas de compatibilidad con la configuración de invitado. Los problemas más comunes guardan relación con el uso de .NET Framework o .NET Core. Dispone de información técnica detallada en la página [Diferencias entre Windows PowerShell 5.1 y PowerShell (core) 7.x](/powershell/scripting/whats-new/differences-from-windows-powershell).

Una opción para resolver problemas de compatibilidad es ejecutar comandos en Windows PowerShell desde dentro de un módulo importado en PowerShell 7, ejecutando `powershell.exe`.
Puede revisar un módulo de ejemplo en el que se usa esta técnica en el repositorio de Azure-Policy, donde se usa para auditar el estado de la [configuración DSC de Windows](https://github.com/Azure/azure-policy/blob/bbfc60104c2c5b7fa6dd5b784b5d4713ddd55218/samples/GuestConfiguration/package-samples/resource-modules/WindowsDscConfiguration/DscResources/WindowsDscConfiguration/WindowsDscConfiguration.psm1#L97).

En el ejemplo también se ilustra una pequeña prueba de concepto:

```powershell
# example function that could be loaded from module
function New-TaskResolvedInPWSH7 {
  # runs the fictitious command 'Get-myNotCompatibleCommand' in Windows PowerShell
  $compatObject = & powershell.exe -noprofile -NonInteractive -command { Get-myNotCompatibleCommand }
  # resulting object can be used in PowerShell 7
  return $compatObject
}
```

#### <a name="will-i-have-to-add-reasons-property-to-get-targetresource-in-all-modules-i-migrate"></a>¿Tengo que agregar la propiedad "Reasons" a Get-TargetResource todos los módulos que migre?

La implementación de la [propiedad "Reasons"](../concepts/guest-configuration-custom.md#special-requirements-for-get) proporciona una mejor experiencia al ver los resultados de una asignación de configuración desde Azure Portal. Si el método `Get` de un módulo no incluye "Reasons", se devuelve una salida genérica con detalles de las propiedades devueltas por el método `Get`. Por lo tanto, es opcional para la migración.

## <a name="machines"></a>Machines

Cuando haya terminado de probar el contenido de Azure Automation State Configuration en la configuración de invitado, desarrolle un plan para migrar las máquinas.

Azure Automation State Configuration está disponible para máquinas virtuales ubicadas tanto dentro como fuera de Azure. Debe planear cada uno de estos escenarios aplicando distintos pasos.

### <a name="azure-vms"></a>Máquinas virtuales de Azure

Las máquinas virtuales de Azure ya tienen un [recurso](../../../azure-resource-manager/management/overview.md#terminology) en Azure, por lo que están listas para asignaciones de configuración de invitado que las asocien a una configuración. Las tareas de nivel superior para migrar máquinas virtuales de Azure las eliminan de Azure Automation State Configuration y, a continuación, asignan configuraciones usando la de invitado.

Para eliminar una máquina de Azure Automation State Configuration, siga los pasos de la página [Eliminación de una configuración y un nodo de Automation State Configuration](../../../automation/state-configuration/remove-node-and-configuration-package.md).

Para asignar configuraciones usando la de invitado, siga los pasos de los inicios rápidos de Azure Policy, como [Inicio rápido: Creación de una asignación de directiva para identificar recursos no compatibles](../assign-policy-portal.md).
En el paso 6, al seleccionar una definición de directiva, elija la que aplique una configuración que haya migrado desde Azure Automation State Configuration.

### <a name="hybrid-machines"></a>Máquinas híbridas

Las máquinas situadas fuera de Azure [se pueden registrar en Azure Automation State Configuration](../../../automation/automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines), pero no tienen un recurso de máquina en Azure. La conexión a Azure Automation se controla mediante el servicio de administrador de configuración local dentro de la máquina. Por su parte, el registro del nodo se administra como un recurso en el tipo de proveedor de Azure Automation.

Antes de eliminar una máquina de Azure Automation State Configuration, incorpore cada nodo como [servidor habilitado para Azure Arc](../../../azure-arc/servers/overview.md).
Al incorporarse a Azure Arc, se crea un recurso de máquina en Azure para que la máquina pueda administrarse mediante Azure Policy. Aunque la máquina se puede incorporar a Azure Arc en cualquier momento, puede usar Azure Automation State Configuration para automatizar el proceso.

Puede registrar una máquina en servidores habilitados para Azure Arc mediante DSC de PowerShell.
Para obtener más información, consulte la página [Instalación del agente de Connected Machine mediante DSC de Windows PowerShell](../../../azure-arc/servers/onboard-dsc.md).
Sin embargo, recuerde que Azure Automation State Configuration solo puede administrar una configuración por máquina y cuenta de Automation. Esto significa que tiene la opción de exportar, probar y preparar su contenido para la configuración de invitado y, a continuación, "cambiar" la configuración de nodo en Azure Automation para incorporarlo a Azure Arc. Como último paso, elimine el registro de nodo de Azure Automation State Configuration y pase a administrar el estado de la máquina solo a través de la configuración de invitado.

## <a name="troubleshooting-issues-when-exporting-content"></a>Solución de problemas al exportar contenido

A continuación se proporcionan detalles sobre problemas conocidos.

### <a name="exporting-configurations-results-in--character-in-file-name"></a>La exportación de configuraciones provoca que aparezca el carácter "\\" en el nombre de archivo

Al usar PowerShell en MacOS/Linux, se producen problemas relacionados con los nombres de archivo que genera `Export-AzAutomationDSCConfiguration`.

Como solución alternativa, se ha publicado en la Galería de PowerShell un módulo denominado [AADSCConfigContent](https://www.powershellgallery.com/packages/AADSCConfigContent/).
El módulo tiene un solo comando, que exporta el contenido de una configuración almacenada en Azure Automation realizando una solicitud REST al servicio.

## <a name="next-steps"></a>Pasos siguientes

- [Cree un artefacto de paquete](./guest-configuration-create.md) para la configuración de invitado.
- [Pruebe el artefacto de paquete](./guest-configuration-create-test.md) desde el entorno de desarrollo.
- [Publique el artefacto de paquete](./guest-configuration-create-publish.md) para que puedan acceder a él sus máquinas.
- Use el módulo `GuestConfiguration` con el fin de [crear una definición de Azure Policy](./guest-configuration-create-definition.md) para la administración a gran escala de su entorno.
- [Asigne su definición de directiva personalizada](../assign-policy-portal.md) mediante Azure Portal.
- Obtenga información sobre cómo ver los [detalles de cumplimiento de asignaciones de directivas de configuración de invitado](./determine-non-compliance.md#compliance-details-for-guest-configuration).
