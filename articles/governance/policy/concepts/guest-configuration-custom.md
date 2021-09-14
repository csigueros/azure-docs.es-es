---
title: Cambios en el comportamiento de la configuración de invitados en PowerShell Desired State Configuration
description: En este artículo se proporciona información general sobre la plataforma que se usa para entregar cambios de configuración a las máquinas a través Azure Policy.
ms.date: 05/31/2021
ms.topic: how-to
ms.openlocfilehash: ee5165ea9e8a80fc31863389df018548859e9b20
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257169"
---
# <a name="changes-to-behavior-in-powershell-desired-state-configuration-for-guest-configuration"></a>Cambios en el comportamiento de la configuración de invitados en PowerShell Desired State Configuration

Antes de comenzar, es una buena idea leer la información general de la [configuración de invitado](./guest-configuration.md).

[Hay disponible un tutorial de vídeo de este documento](https://youtu.be/nYd55FiKpgs).

La configuración de invitado usa la versión 3 de [Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) para auditar y configurar máquinas. La configuración de DSC define el estado en que debe encontrarse la máquina. Hay muchas diferencias importantes en el modo de implementación de DSC en la configuración de invitado.

## <a name="guest-configuration-uses-powershell-7-cross-platform"></a>La configuración de invitado usa PowerShell 7 multiplataforma

La configuración de invitado está diseñada para que la experiencia de administración de Windows y Linux pueda ser coherente. En ambos entornos de sistema operativo, alguien con conocimientos de DSC de PowerShell puede crear y publicar configuraciones mediante aptitudes de scripting.

La configuración de invitado solo usa la versión 3 de DSC de PowerShell y no se basa en la implementación anterior de [DSC para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) ni en los proveedores de "nx" incluidos en ese repositorio.

La configuración de invitado funciona en PowerShell 7.1.3 para Windows y PowerShell 7.2, versión preliminar 6, para Linux. A partir de la versión 7.2, el módulo `PSDesiredStateConfiguration` ha dejado de formar parte de la instalación de PowerShell y, en su lugar, se ha instalado como un [módulo de la Galería de PowerShell](https://www.powershellgallery.com/packages/PSDesiredStateConfiguration).

## <a name="multiple-configurations"></a>Varias configuraciones

La configuración de invitado admite la asignación de varias configuraciones en la misma máquina. No se requieren pasos especiales en el sistema operativo de la extensión de configuración de invitado. No es necesario establecer [configuraciones parciales](/powershell/scripting/dsc/pull-server/partialConfigs).

## <a name="configuration-mode-is-set-in-the-package-artifact"></a>El modo de configuración se establece en el artefacto del paquete

Al crear el paquete de configuración, el modo se establece mediante las siguientes opciones:

- _Audit_: comprueba el cumplimiento de una máquina. No se realiza ningún cambio.
- _AuditandSet_: comprueba y corrige el estado de cumplimiento de la máquina.
  Se realizan cambios si la máquina no es compatible.

El modo se establece en el paquete en lugar de establecerse en el servicio [Configuration Manager local](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings) ya que puede ser diferente según la configuración cuando se asignan varias configuraciones.

## <a name="parameter-support-through-azure-resource-manager"></a>Compatibilidad con parámetros a través de Azure Resource Manager

Los parámetros establecidos por la matriz de propiedades `configurationParameter` en las [asignaciones de configuración de invitado](guest-configuration-assignments.md) sobrescriben el texto estático en un archivo MOF de configuración cuando el archivo se almacena en una máquina. Los parámetros permiten que un operador de la API de servicio controle la personalización y los cambios sin necesidad de ejecutar comandos en la máquina.

Los parámetros de Azure Policy que pasan valores a las asignaciones de configuración de invitado deben ser de tipo _cadena_. No es posible pasar matrices mediante parámetros, aunque el recurso de DSC admita matrices.

## <a name="sequence-of-events"></a>Secuencia de eventos

Cuando la configuración de invitado audita o configura una máquina, se usa la misma secuencia de eventos para Windows y Linux. El cambio de comportamiento importante es que el servicio llama al método `Get` para devolver detalles sobre el estado de la máquina.

1. El agente primero ejecuta `Test` para determinar si la configuración se encuentra en el estado correcto.
1. Si el paquete se establece en `Audit`, el valor booleano devuelto por la función determina si el estado de Azure Resource Manager para Asignación de invitado debe ser Compatible o No compatible.
1. Si el paquete se establece en `AuditandSet`, el valor booleano determina si se debe corregir la máquina aplicando la configuración mediante el método `Set`.
   Si el método `Test` devuelve False, se ejecuta `Set`. Si `Test` devuelve True, no se ejecuta `Set`.
1. Por último, el proveedor ejecuta `Get` para devolver el estado actual de cada configuración, de modo que haya detalles disponibles tanto sobre el motivo por el que una máquina no es compatible como para confirmar que el estado actual es compatible.

## <a name="trigger-set-from-outside-machine"></a>Conjunto de desencadenadores ajeno a la máquina

Un desafío de versiones anteriores de DSC era corregir el desfase a gran escala sin una gran cantidad de código personalizado y la dependencia de conexiones remotas de WinRM. La configuración de invitado soluciona este problema. Los usuarios de la configuración de invitado controlan la corrección de desfase a través de la [corrección a petición](./guest-configuration-policy-effects.md#remediation-on-demand-applyandmonitor).

## <a name="maximum-size-of-custom-configuration-package"></a>Tamaño máximo del paquete de configuración personalizado

En la configuración del estado de Azure Automation, las configuraciones de DSC tenían [un tamaño limitado](../../../automation/automation-dsc-compile.md#compile-your-dsc-configuration-in-windows-powershell).
La configuración de invitado admite un tamaño total de paquete de 100 MB (antes de la compresión). No hay ningún límite específico en el tamaño del archivo MOF dentro del paquete.

## <a name="special-requirements-for-get"></a>Requisitos especiales para Get

El método de función `Get` tiene requisitos especiales para la configuración de invitado de Azure Policy, que no eran necesarios para Windows PowerShell Desired State Configuration.

- La tabla hash que se devuelve debe incluir una propiedad denominada **Reasons**.
- La propiedad Reasons debe ser una matriz.
- Cada elemento de la matriz debe ser una tabla hash con claves denominadas **Code** y **Phrase**.

El servicio utiliza la propiedad Reasons para estandarizar el modo en que se presenta la información sobre el cumplimiento. Puede pensar en cada elemento de Reasons como un "motivo" por el que el recurso es o no es compatible. La propiedad es una matriz porque un recurso podría no cumplir los requisitos por más de un motivo.

El servicio espera las propiedades **Code** y **Phrase**. Al crear un recurso personalizado, establezca el texto (normalmente stdout) que le gustaría mostrar como el motivo por el que el recurso no es compatible como el valor de **Phrase**.
**Code** tiene requisitos de formato específicos, por lo que los informes pueden mostrar claramente información sobre el recurso usado para realizar la auditoría. Con esta solución, la configuración de invitado es extensible. Se podría ejecutar cualquier comando, siempre que la salida se pueda devolver como un valor de cadena para la propiedad **Phrase**.

- **Code** (cadena): el nombre del recurso, repetido, y luego un nombre corto sin espacios como identificador del motivo. Estos tres valores deben estar delimitados por signos de dos puntos sin espacios.
  - Un ejemplo sería `registry:registry:keynotpresent`
- **Phrase** (cadena): texto legible para explicar el motivo por el que la configuración no es compatible.
  - Un ejemplo sería `The registry key $key isn't present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

### <a name="the-reasons-property-embedded-class"></a>Clase incrustada de la propiedad Reasons

En los recursos basados en scripts (solo Windows), la clase Reasons se incluye en el archivo MOF del esquema como se muestra a continuación.

```mof
[ClassVersion("1.0.0.0")]
class Reason
{
  [Read] String Phrase;
  [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
  [Key, Description("Example description")] String Example;
  [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

En los recursos basados en clases (Windows y Linux), la clase `Reason` se incluye en el módulo de PowerShell como se muestra a continuación. Linux distingue mayúsculas de minúsculas, por lo que la "C" de Código y la "P" de Phrase deben estar en mayúsculas.

```powershell
enum ensure {
  Absent
  Present
}

class Reason {
  [DscProperty()]
  [string] $Code

  [DscProperty()]
  [string] $Phrase
}

[DscResource()]
class Example {

  [DscProperty(Key)]
  [ensure] $ensure

  [DscProperty()]
  [Reason[]] $Reasons

  [Example] Get() {
    # return current current state
  }

  [void] Set() {
    # set the state
  }

  [bool] Test() {
    # check whether state is correct
  }
}

```

Si el recurso tiene las propiedades necesarias, `Get` debe devolver esas propiedades en paralelo con la clase `Reason`. Si no se incluye `Reason`, el servicio incluye un comportamiento "comodín" que compara los valores de entrada con `Get` y los valores devueltos por `Get` y proporciona una comparación detallada como `Reason`.

## <a name="configuration-names"></a>Nombres de la configuración

El nombre de la configuración personalizada debe ser coherente en todas partes. El nombre del archivo `.zip` para el paquete de contenido, el nombre de la configuración en el archivo MOF y el nombre de la asignación de invitado en la plantilla de Azure Resource Manager deben ser el mismo.

## <a name="common-dsc-features-not-available-during-guest-configuration-public-preview"></a>Características comunes de DSC no disponibles durante la versión preliminar pública de la configuración de invitado

Durante la versión preliminar pública, la configuración de invitado no admite la [especificación de dependencias entre máquinas](/powershell/scripting/dsc/configurations/crossnodedependencies) mediante recursos "WaitFor*". No es posible que una máquina supervise otra máquina y espere a que esta alcance un estado antes de progresar.

El [control del reinicio](/powershell/scripting/dsc/configurations/reboot-a-node) no está disponible en la versión preliminar pública de la configuración de invitado; `$global:DSCMachineStatus` tampoco está disponible. Las configuraciones no pueden reiniciar un nodo durante la configuración ni cuando esta finaliza.

## <a name="known-compatibility-issues-with-supported-modules"></a>Problemas conocidos de compatibilidad con los módulos admitidos

El módulo `PsDscResources` de la Galería de PowerShell y el módulo `PSDesiredStateConfiguration` que se suministra con Windows son compatibles con Microsoft y han sido un conjunto de recursos de uso frecuente para DSC. Hasta que el módulo `PSDscResources` se actualice para DSCv3, tenga en cuenta los siguientes problemas de compatibilidad conocidos.

- No use recursos del módulo `PSDesiredStateConfiguration` que se incluye con Windows. En su lugar, cambie a `PSDscResources`.
- No use los recursos `WindowsFeature` y `WindowsFeatureSet` en `PsDscResources`. En su lugar, cambie a los recursos `WindowsOptionalFeature` y `WindowsOptionalFeatureSet`.

## <a name="coexistance-with-dsc-version-3-and-previous-versions"></a>Coexistencia con la versión 3 y anteriores de DSC

La versión 3 de DSC de la configuración de invitado puede coexistir con versiones anteriores instaladas en [Windows](/powershell/scripting/dsc/getting-started/wingettingstarted) y [Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).
Las implementaciones son independientes. Sin embargo, no hay ninguna detección de conflictos en las versiones de DSC, por lo que no intente administrar la misma configuración.

## <a name="next-steps"></a>Pasos siguientes

- Lea la [introducción a la configuración de invitado](./guest-configuration.md).
- Configure un [entorno de desarrollo](../how-to/guest-configuration-create-setup.md) personalizado de paquetes de configuración de invitado.
- [Cree un artefacto de paquete](../how-to/guest-configuration-create.md) para la configuración de invitado.
- [Pruebe el artefacto de paquete](../how-to/guest-configuration-create-test.md) desde el entorno de desarrollo.
- Use el módulo `GuestConfiguration` a fin de [crear una definición de Azure Policy](../how-to/guest-configuration-create-definition.md) para la administración a gran escala del entorno.
- [Asigne la definición de directiva personalizada](../assign-policy-portal.md) mediante Azure Portal.
- Obtenga información sobre cómo ver los [detalles de cumplimiento para las asignaciones de directivas de configuración de invitado](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration).
