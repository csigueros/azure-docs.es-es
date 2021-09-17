---
title: Creación de una directiva de configuración de invitado a partir de una directiva de grupo
description: Obtenga información sobre cómo convertir una directiva de grupo en una definición de directiva.
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: 12bd1c905c254f16da170cde4a4426a2aa0cb263
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772299"
---
# <a name="how-to-create-a-guest-configuration-policy-from-group-policy"></a>Creación de una directiva de configuración de invitado a partir de una directiva de grupo

Antes de comenzar, es aconsejable leer la página de información general de la [configuración de invitado](../concepts/guest-configuration.md) y los detalles sobre los efectos de la directiva de configuración de invitado en [Opciones de corrección para la configuración de invitado](../concepts/guest-configuration-policy-effects.md).

> [!IMPORTANT]
> La conversión de una directiva de grupo en una configuración de invitado se encuentra **en versión preliminar**. No todos los tipos de configuración de directiva de grupo tienen los recursos de DSC correspondientes disponibles para PowerShell 7.
>
> Todos los comandos de esta página deben ejecutarse en **Windows PowerShell 5.1**.
> Los archivos MOF de salida resultantes se deben empaquetar usando el módulo `GuestConfiguration` de PowerShell 7.1.3 o una versión posterior.
> 
> Las definiciones de directiva de configuración de invitado personalizadas que usan **AuditIfNotExists** están disponibles con carácter general, pero las definiciones que usan **DeployIfNotExists** con la configuración de invitado están en **versión preliminar**.
> 
> Se requiere la extensión de configuración de invitado para máquinas virtuales de Azure. Para implementar la extensión a gran escala en todas las máquinas, asigne la siguiente iniciativa de directiva: `Deploy prerequisites to enable guest configuration policies on
> virtual machines`
>
> No use secretos ni información confidencial en paquetes de contenido personalizado.

La comunidad de código abierto ha publicado el módulo [BaselineManagement](https://github.com/microsoft/BaselineManagement) para convertir las plantillas exportadas de [directiva de grupo](/support/windows-server/group-policy/group-policy-overview) al formato DSC de PowerShell. Junto con el módulo `GuestConfiguration`, puede crear un paquete de configuración de invitado para Windows a partir de objetos de directiva de grupo exportados. Posteriormente, el paquete de configuración de invitado se puede usar para auditar o configurar servidores usando una directiva local, aunque no estén unidos a un dominio.

En esta guía se recorre el proceso de creación de un paquete de configuración de invitado de Azure Policy a partir de un objeto de directiva de grupo (GPO).

## <a name="download-required-powershell-modules"></a>Descarga de los módulos de PowerShell necesarios

Para instalar todos los módulos necesarios en PowerShell:

```powershell
Install-Module guestconfiguration
Install-Module baselinemanagement
```

Para realizar una copia de seguridad de objetos de directiva de grupo (GPO) desde un entorno de Active Directory, necesita los comandos de PowerShell disponibles en las Herramientas de instalación remota del servidor (RSAT).

Con el fin de habilitar las RSAT para la Consola de administración de directivas de grupo en Windows 10:

```powerShell
Add-WindowsCapability -Online -Name 'Rsat.GroupPolicy.Management.Tools~~~~0.0.1.0'
Add-WindowsCapability -Online -Name 'Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0'
```

## <a name="export-and-convert-group-policy-to-guest-configuration"></a>Exportación de una directiva de grupo y conversión en una configuración de invitado

Hay tres opciones para exportar archivos de directiva de grupo y convertirlos a DSC con el fin de usarlos en una configuración de invitado:

- Exportar un único objeto de directiva de grupo.
- Exportar los objetos de directiva de grupo combinados para una UO.
- Exportar los objetos de directiva de grupo combinados desde dentro de una máquina.

### <a name="single-group-policy-object"></a>Un único objeto de directiva de grupo

Identifique el GUID del objeto de directiva de grupo que se exportará usando los comandos del módulo `Group Policy`. En un entorno de gran tamaño, considere la posibilidad de canalizar la salida a `where-object` y filtrar por el nombre.

Ejecute cada uno de los siguientes en un entorno **Windows PowerShell 5.1**, en una máquina Windows **unida a un dominio**:

```powershell
# List all Group Policy Objects
Get-GPO -all
```

Realice una copia de seguridad de la directiva de grupo en archivos. El comando también acepta un parámetro "Name", pero el GUID de la directiva es menos propenso a errores cuando se usa.

```powershell
Backup-GPO -Guid 'f0cf623e-ae29-4768-9bb4-406cce1f3cff' -Path C:\gpobackup\
```

```

The output of the command returns the details of the files.

ConfigurationScript                   Configuration                   Name
-------------------                   -------------                   ----
C:\convertfromgpo\myCustomPolicy1.ps1 C:\convertfromgpo\localhost.mof myCustomPolicy1
```

Revise el script exportado de PowerShell para asegurarse de que se hayan rellenado todos los valores y no se hayan escrito mensajes de error. Cree un paquete de configuración mediante el archivo MOF siguiendo las instrucciones de la página [Creación de artefactos de paquetes de configuración de invitado personalizados](./guest-configuration-create.md).
Los pasos para crear y probar el paquete de configuración de invitado deben ejecutarse en un entorno PowerShell 7.

### <a name="merged-group-policy-objects-for-an-ou"></a>Objetos de directiva de grupo combinados para una UO

Exporte la combinación combinada de objetos de directiva de grupo (similar a un conjunto resultante de directivas) en una unidad organizativa especificada. La operación de combinación tiene en cuenta el estado del vínculo, el cumplimiento y el acceso, pero no los filtros WMI.

```powershell
Merge-GPOsFromOU -Path C:\mergedfromou\ -OUDistinguishedName 'OU=mySubOU,OU=myOU,DC=mydomain,DC=local' -OutputConfigurationScript
```

La salida del comando devuelve los detalles de los archivos.

```powershell
Configuration                                Name    ConfigurationScript
-------------                                ----    -------------------
C:\mergedfromou\mySubOU\output\localhost.mof mySubOU C:\mergedfromou\mySubOU\output\mySubOU.ps1
```

### <a name="merged-group-policy-objects-from-within-a-machine"></a>Objetos de directiva de grupo combinados desde dentro de una máquina

También puede combinar las directivas aplicadas a una máquina específica ejecutando el comando `Merge-GPOs` desde Windows PowerShell. Los filtros WMI solo se evalúan si se combinan desde dentro de una máquina.

```powershell
Merge-GPOs -OutputConfigurationScript -Path c:\mergedgpo
```

La salida del comando devuelve los detalles de los archivos.

```powershell
Configuration              Name                  ConfigurationScript                    PolicyDetails
-------------              ----                  -------------------                    -------------
C:\mergedgpo\localhost.mof MergedGroupPolicy_ws1 C:\mergedgpo\MergedGroupPolicy_ws1.ps1 {@{Name=myEnforcedPolicy; Ap...
```

## <a name="optional-download-sample-group-policy-files-for-testing"></a>OPCIONAL: Descarga de archivos de directiva de grupo de ejemplo para pruebas

Si todavía no desea exportar archivos de directiva de grupo desde un entorno Active Directory, puede descargar la línea de base de seguridad de Windows Server desde el kit de herramientas de cumplimiento de seguridad de Windows.

Cree un directorio y descargue la línea base de seguridad de Windows Server 2019 desde el kit de herramientas de cumplimiento de seguridad de Windows.

```azurepowershell-interactive
# Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
```

Desbloquee y expanda la línea de base de la instancia de Server 2019 descargada.

```azurepowershell-interactive
Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
```

Valide el contenido de línea de base de Server 2019 mediante **MapGuidsToGpoNames.ps1**.

```azurepowershell-interactive
# Show content details of downloaded GPOs
C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
```

## <a name="next-steps"></a>Pasos siguientes

- [Cree un artefacto de paquete](./guest-configuration-create.md) para la configuración de invitado.
- [Pruebe el artefacto de paquete](./guest-configuration-create-test.md) desde el entorno de desarrollo.
- [Publique el artefacto del paquete](./guest-configuration-create-publish.md) para que puedan acceder a él sus máquinas.
- Use el módulo `GuestConfiguration` con el fin de [crear una definición de Azure Policy](./guest-configuration-create-definition.md) para la administración a gran escala de su entorno.
- [Asigne su definición de directiva personalizada](../assign-policy-portal.md) mediante Azure Portal.
- Obtenga información sobre cómo ver los [detalles de cumplimiento de asignaciones de directivas de configuración de invitado](./determine-non-compliance.md#compliance-details-for-guest-configuration).
