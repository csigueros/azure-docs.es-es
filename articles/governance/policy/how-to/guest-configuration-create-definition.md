---
title: Creación de definiciones de directivas de configuración de invitado personalizadas
description: Obtenga información sobre cómo crear una directiva de configuración de invitado.
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: 1dd1620d0ef41bf28a276cfe2412ca4bdc09d183
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644911"
---
# <a name="how-to-create-custom-guest-configuration-policy-definitions"></a>Creación de definiciones de directivas de configuración de invitado personalizadas

Antes de comenzar, es una buena idea leer la página de información general de [configuración de invitado](../concepts/guest-configuration.md) y los detalles sobre los efectos de la directiva de configuración de invitado en [Opciones de corrección para la configuración de invitado](../concepts/guest-configuration-policy-effects.md).

> [!IMPORTANT]
> Se requiere la extensión de configuración de invitado para Azure Virtual Machines. Para implementar la extensión a gran escala en todas las máquinas, asigne la iniciativa de directiva siguiente: `Deploy prerequisites to enable guest configuration policies on
> virtual machines`
> 
> Para usar paquetes de configuración de invitado que aplican configuraciones, se requiere la versión de la extensión de configuración de invitado de máquina virtual de Azure **1.29.24** o posterior, o el agente de Arc **1.10.0** o posterior.
>
> Las definiciones de directivas de configuración de invitado personalizadas que usan **AuditIfNotExists** están disponibles con carácter general, pero las definiciones que usan **DeployIfNotExists** con la configuración de invitado están en **versión preliminar**.

Realice los pasos siguientes para crear sus propias directivas de auditoría del cumplimiento o administración del estado de las máquinas de Azure o habilitadas para Arc.

## <a name="install-powershell-7-and-required-powershell-modules"></a>Instalación de PowerShell 7 y módulos de PowerShell necesarios

En primer lugar, asegúrese de que ha seguido todos los pasos de la página [Configuración de un entorno de creación de configuración de invitado](./guest-configuration-create-setup.md) para instalar la versión necesaria de PowerShell para el sistema operativo y el módulo `GuestConfiguration`.

## <a name="create-and-publish-a-guest-configuration-package-artifact"></a>Creación y publicación de un artefacto de paquete de configuración de invitado

Si aún no lo ha hecho, siga todos los pasos de la página [Creación de artefactos de paquetes de configuración de invitados personalizados](./guest-configuration-create.md) para crear y publicar un paquete de configuración de invitado personalizado y [Procedimientos para probar paquetes de configuración de invitado personalizados](./guest-configuration-create-test.md) para validar el paquete de configuración de invitado localmente en el entorno de desarrollo.

## <a name="policy-requirements-for-guest-configuration"></a>Requisitos de directivas para la configuración de invitado

La sección `metadata` de la definición de directiva debe incluir dos propiedades para que el servicio de configuración de invitado automatice el aprovisionamiento y la generación de informes de las asignaciones de la configuración de invitado. La propiedad `category` debe establecerse en "Configuración de invitado" y una sección denominada `guestConfiguration` debe contener información sobre la asignación de la configuración de invitado. El cmdlet `New-GuestConfigurationPolicy` crea este texto automáticamente.

En el ejemplo siguiente se muestra la sección `metadata` que `New-GuestConfigurationPolicy` crea automáticamente.

```json
    "metadata": {
      "category": "Guest Configuration",
      "guestConfiguration": {
        "name": "test",
        "version": "1.0.0",
        "contentType": "Custom",
        "contentUri": "CUSTOM-URI-HERE",
        "contentHash": "CUSTOM-HASH-VALUE-HERE",
        "configurationParameter": {}
      }
    },
```

La propiedad `category` debe establecerse en "Configuración de invitado". Si el efecto de definición se establece en "DeployIfNotExists", la sección `then` debe contener detalles de implementación sobre una asignación de configuración de invitado. El cmdlet `New-GuestConfigurationPolicy` crea este texto automáticamente.

### <a name="create-an-azure-policy-definition"></a>Creación de una definición de Azure Policy

Una vez que se ha creado y cargado un paquete de directivas personalizadas de configuración de invitado, cree la definición de la directiva de configuración de invitado. El cmdlet `New-GuestConfigurationPolicy` toma un paquete de directivas personalizado y crea una definición de directivas.

El parámetro **PolicyId** de `New-GuestConfigurationPolicy` requiere una cadena única. Se recomienda usar un identificador único global (GUID). Para las nuevas definiciones, genere un nuevo GUID mediante el cmdlet `New-GUID`. Al realizar actualizaciones en la definición, use la misma cadena única para **PolicyId** para asegurarse de que se actualiza la definición correcta.

Parámetros del cmdlet `New-GuestConfigurationPolicy`:

- **PolicyId**: GUID u otra cadena única que identifica la definición.
- **ContentUri**: URI de HTTP(S) público del paquete de contenido de configuración de invitado.
- **DisplayName**: Nombre para mostrar de la directiva.
- **Descripción**: Descripción de la directiva.
- **Parámetro**: Parámetros de directiva proporcionados en formato de tabla hash.
- **Versión**: Versión de la directiva.
- **Ruta de acceso**: Ruta de acceso de destino donde se crean las definiciones de directiva.
- **Plataforma**: plataforma de destino (Windows/Linux) para la directiva de configuración de invitado y el paquete de contenido.
- **Modo**: (ApplyAndMonitor, ApplyAndAutoCorrect, Audit) elija si la directiva debe auditar o implementar la configuración. El valor predeterminado es "Auditar".
- **Tag** agrega uno o varios filtros de etiquetas a la definición de directiva.
- **Category** establece el campo de metadatos de categoría en la definición de directiva.

Para obtener más información sobre el parámetro "Mode", consulte la página [Opciones de corrección para la configuración de invitado](../concepts/guest-configuration-policy-effects.md).

Cree una definición de directiva que realice una auditoría mediante un paquete de configuración personalizado en una ruta de acceso especificada:

```powershell
New-GuestConfigurationPolicy `
  -PolicyId 'My GUID' `
  -ContentUri '<paste the ContentUri output from the Publish command>' `
  -DisplayName 'My audit policy.' `
  -Description 'Details about my policy.' `
  -Path './policies' `
  -Platform 'Windows' `
  -Version 1.0.0 `
  -Verbose
```

Cree una definición de directiva que implemente una configuración mediante un paquete de configuración personalizado en una ruta de acceso especificada:

```powershell
New-GuestConfigurationPolicy `
  -PolicyId 'My GUID' `
  -ContentUri '<paste the ContentUri output from the Publish command>' `
  -DisplayName 'My audit policy.' `
  -Description 'Details about my policy.' `
  -Path './policies' `
  -Platform 'Windows' `
  -Version 1.0.0 `
  -Mode 'ApplyAndAutoCorrect' `
  -Verbose
```

La salida del cmdlet devuelve un objeto que contiene el nombre para mostrar de la definición y la ruta de acceso de los archivos de directiva. Los archivos JSON de definición que crean definiciones de directiva de auditoría se denominan **auditIfNotExists.json** y los archivos que crean definiciones de directiva para aplicar configuraciones, **deployIfNotExists.json**.

#### <a name="filtering-guest-configuration-policies-using-tags"></a>Filtrado de directivas de configuración de invitado mediante etiquetas

De forma opcional, las definiciones de directiva creadas por cmdlets en la configuración de invitado pueden incluir un filtro para las etiquetas. El parámetro **Tag** de `New-GuestConfigurationPolicy` admite una matriz de tablas hash que contiene entradas de etiquetas individuales. Las etiquetas se agregan a la sección `If` de la definición de directiva y no se pueden modificar mediante una asignación de directiva.

A continuación se muestra un fragmento de código de ejemplo de una definición de directiva que filtra por etiquetas.

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original guest configuration content
    }
  ]
}
```

#### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>Uso de parámetros en definiciones de directivas de configuración de invitado personalizadas

La configuración de invitado admite la invalidación de propiedades de una configuración en tiempo de ejecución. Esta característica significa que los valores del archivo MOF del paquete no tienen que considerarse estáticos. Los valores de invalidación se proporcionan mediante Azure Policy y no cambian el modo en que se crean o compilan las configuraciones.

Los cmdlets `New-GuestConfigurationPolicy` e `Get-GuestConfigurationPacakgeComplianceStatus ` incluyen un parámetro denominado **Parámetros**. Este parámetro toma una definición de tabla hash que incluye todos los detalles sobre cada parámetro y crea las secciones necesarias de cada archivo que se usa para crear la definición de Azure Policy.

En el ejemplo siguiente se crea una definición de directiva para auditar un servicio que el usuario selecciona de una lista en el momento de la asignación de la directiva.

```powershell
# This DSC Resource text:
Service 'UserSelectedNameExample'
  {
    Name = 'ParameterValue'
    Ensure = 'Present'
    State = 'Running'
  }`

# Would require the following hashtable:
$PolicyParameterInfo = @(
  @{
    Name = 'ServiceName'                                           # Policy parameter name (mandatory)
    DisplayName = 'windows service name.'                          # Policy parameter display name (mandatory)
    Description = 'Name of the windows service to be audited.'     # Policy parameter description (optional)
    ResourceType = 'Service'                                       # DSC configuration resource type (mandatory)
    ResourceId = 'UserSelectedNameExample'                         # DSC configuration resource id (mandatory)
    ResourcePropertyName = 'Name'                                  # DSC configuration resource property name (mandatory)
    DefaultValue = 'winrm'                                         # Policy parameter default value (optional)
    AllowedValues = @('BDESVC','TermService','wuauserv','winrm')   # Policy parameter allowed values (optional)
  }
)

New-GuestConfigurationPolicy `
  -PolicyId 'My GUID' `
  -ContentUri '<paste the ContentUri output from the Publish command>' `
  -DisplayName 'Audit Windows Service.' `
  -Description 'Audit if a Windows Service isn't enabled on Windows machine.' `
  -Path '.\policies' `
  -Parameter $PolicyParameterInfo `
  -Version 1.0.0
```

### <a name="publish-the-azure-policy-definition"></a>Publicación de la definición de Azure Policy

Por último, publique las definiciones de directivas con el cmdlet `Publish-GuestConfigurationPolicy`. El cmdlet solo tiene el parámetro **Path** que apunta a la ubicación de los archivos JSON que creó `New-GuestConfigurationPolicy`.

Para ejecutar el comando Publish, necesita acceso para crear definiciones de directivas en Azure. Los requisitos de autorización específicos se documentan en la página [Información general de Azure Policy](../overview.md). El rol integrado recomendado es **Colaborador de la directiva de recursos**.

```powershell
Publish-GuestConfigurationPolicy -Path '.\policies'
```

Con la definición de directiva creada en Azure, el último paso es asignar la definición. Consulte cómo asignar la definición con el [portal](../assign-policy-portal.md), la [CLI de Azure](../assign-policy-azurecli.md) y [Azure PowerShell](../assign-policy-powershell.md).

### <a name="optional-piping-output-from-each-command-to-the-next"></a>Opcional: canalización de la salida de cada comando al siguiente

Los comandos de la configuración de invitado admiten parámetros de canalización por nombre.
Puede usar el operador `|` para canalizar la salida de cada comando al siguiente.
La canalización es útil en entornos de desarrollador cuando se realiza la iteración rápidamente, ya que no requerirá copiar y pegar la salida de cada comando.

Para ejecutar la secuencia mediante el operador `|`:

```powershell
# End to end flow piping output of each command to the next
$ConfigName         = myConfigName
$ResourceGroupName  = myResourceGroupName
$StorageAccountName = myStorageAccountName
$DisplayName        = 'Configure Linux machine per my scenario.'
$Description        = 'Details about my policy.'
New-GuestConfigurationPackage -Name $ConfigName -Configuration ./$ConfigName.mof -Path ./package/ -Type AuditAndSet -Force |
Publish-GuestConfigurationPackage -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName -Force |
New-GuestConfigurationPolicy -PolicyId 'My GUID' -DisplayName $DisplayName -Description $Description -Path './policies' -Platform 'Linux' -Version 1.0.0 -Mode 'ApplyAndAutoCorrect' |
Publish-GuestConfigurationPolicy
```

## <a name="policy-lifecycle"></a>Ciclo de vida de la directiva

Si desea publicar una actualización de la definición de directiva, realice el cambio en el paquete de configuración de invitado y en los detalles de la definición de Azure Policy.

> [!NOTE]
> La propiedad `version` de la asignación de configuración de invitado solo afecta a los paquetes hospedados por Microsoft. El procedimiento recomendado para el control de versiones del contenido personalizado es incluir la versión en el nombre de archivo.

En primer lugar, al ejecutar `New-GuestConfigurationPackage`, especifique un nombre para el paquete que lo haga único con respecto a las versiones anteriores. Puede incluir un número de versión en el nombre, como `PackageName_1.0.0`. El número de este ejemplo solo se usa para hacer que el paquete sea único, no para especificar que el paquete sea más reciente o más antiguo que otros paquetes.

En segundo lugar, actualice los parámetros que se usan con el cmdlet `New-GuestConfigurationPolicy` después de cada una de las explicaciones siguientes.

- **Versión**: al ejecutar el cmdlet `New-GuestConfigurationPolicy`, debe especificar un número de versión mayor que el que se ha publicado actualmente.
- **contentUri**: al ejecutar el cmdlet `New-GuestConfigurationPolicy`, debe especificar un URI para la ubicación del paquete. La inclusión de una versión del paquete en el nombre de archivo garantiza que el valor de esta propiedad cambie en cada versión.
- **contentHash**: el cmdlet `New-GuestConfigurationPolicy` actualiza automáticamente esta propiedad. Es un valor hash del paquete que creó `New-GuestConfigurationPackage`. La propiedad debe ser correcta para el archivo `.zip` que se publica. Si solo se actualiza la propiedad **contentUri**, la extensión no aceptará el paquete de contenido.

La manera más fácil de publicar un paquete actualizado es repetir el proceso que se describe en este artículo y proporcionar un número de versión actualizado. Este proceso garantiza que todas las propiedades se hayan actualizado correctamente.

## <a name="next-steps"></a>Pasos siguientes

- [Asigne la definición de directiva personalizada](../assign-policy-portal.md) mediante Azure Portal.
- Obtenga información sobre cómo ver los [detalles de cumplimiento para las asignaciones de directivas de configuración de invitado](./determine-non-compliance.md#compliance-details-for-guest-configuration).
