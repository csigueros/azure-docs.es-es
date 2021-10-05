---
title: Procedimientos para probar paquetes de configuración de invitado personalizados
description: La experiencia de creación y realización de pruebas de paquetes que auditan o aplican configuraciones a las máquinas.
ms.date: 07/20/2021
ms.topic: how-to
ms.openlocfilehash: 216cd207033b3bddd4960b85d8943e3842f8041f
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080658"
---
# <a name="how-to-test-guest-configuration-package-artifacts"></a>Procedimientos para probar paquetes de configuración de invitado personalizados

El módulo de PowerShell `GuestConfiguration` incluye herramientas para automatizar las pruebas de un paquete de configuración fuera de Azure. Use estas herramientas para buscar problemas e iterar rápidamente antes de pasar a la prueba en un entorno conectado a Azure o Arc.

Antes de comenzar las pruebas, siga todos los pasos de la página [Configuración de un entorno de creación de configuración de invitado](./guest-configuration-create-setup.md) y, a continuación, [Creación de artefactos de paquetes de configuración de invitado personalizados](./guest-configuration-create.md) para crear y publicar un paquete de configuración de invitado personalizado.

> [!IMPORTANT]
> Los paquetes personalizados que auditan el estado de un entorno están disponibles con carácter general, pero los paquetes que aplican configuraciones están **en versión preliminar**. **Se aplican las siguientes limitaciones:**
> 
> Para usar paquetes de configuración de invitado que aplican configuraciones, se requiere la versión de la extensión de configuración de invitado de máquina virtual de Azure **1.29.24** o posterior, o el agente de Arc **1.10.0** o posterior.
> 
> Para probar la creación y aplicación de configuraciones en Linux, el módulo `GuestConfiguration` solo está disponible en Ubuntu 18, pero el paquete y las directivas que genera el módulo se pueden usar en cualquier distribución o versión de Linux compatible con Azure o Arc.
>
> Los paquetes de prueba en MacOS no están disponibles.

Puede probar el paquete desde la estación de trabajo o el entorno de integración continua e implementación continua (CI/CD).  El módulo `GuestConfiguration` incluye el mismo agente para el entorno de desarrollo que se usa en máquinas habilitadas para Azure o Arc. El agente incluye una instancia independiente de PowerShell 7.1.3 para Windows y 7.2.0-preview.7 para Linux, por lo que el entorno de script donde se prueba el paquete será coherente con las máquinas que administre mediante la configuración de invitado.

El servicio de agente en máquinas habilitadas para Azure y Arc se ejecuta como la cuenta "LocalSystem" en Windows y "Root" en Linux. Ejecute los comandos siguientes en el contexto de seguridad con privilegios para obtener los mejores resultados.

Para ejecutar PowerShell como "LocalSystem" en Windows, use la herramienta SysInternals [PSExec](/sysinternals/downloads/psexec).

Para ejecutar PowerShell como "Root" en Linux, use el [comando su](https://manpages.ubuntu.com/manpages/man1/su.1.html).

## <a name="validate-the-configuration-package-meets-requirements"></a>Validación de que el paquete de configuración cumple los requisitos

En primer lugar, compruebe que el paquete de configuración cumple los requisitos básicos mediante `Get-GuestConfigurationPacakgeComplianceStatus `. El comando comprueba los siguientes requisitos de paquete.

- MOF está presente y es válido en la ubicación correcta.
- Los módulos o dependencias necesarios están presentes con la versión correcta, sin duplicados.
- Validación de que el paquete está firmado (opcional).
- Comprobación de que `Test` y `Get` devuelven información sobre el estado de cumplimiento.

Parámetros del cmdlet `Get-GuestConfigurationPackageComplianceStatus `:

- **Ruta de acceso**: ruta de acceso o URI del archivo del paquete de configuración de invitado.
- **Parámetro**: Parámetros de directiva proporcionados en formato de tabla hash.

Cuando este comando se ejecuta por primera vez, el agente de configuración de invitado se instala en el equipo de prueba en la ruta de acceso `c:\programdata\GuestConfig\bin` en Windows y `/var/lib/GuestConfig/bin` en Linux. Esta ruta de acceso no es accesible para una cuenta de usuario, por lo que el comando requiere elevación.

Ejecute el siguiente comando para comprobar el paquete:

En Windows, desde una sesión de PowerShell 7 con privilegios elevados.

```powershell
# Get the current compliance results for the local machine
Get-GuestConfigurationPackageComplianceStatus -Path ./MyConfig.zip
```

En Linux, mediante la ejecución de PowerShell con sudo.

```bash
# Get the current compliance results for the local machine
sudo pwsh -command 'Get-GuestConfigurationPackageComplianceStatus -Path ./MyConfig.zip'
```

El comando genera un objeto que contiene el estado de cumplimiento y los detalles por recurso.

```powershell
  complianceStatus  resources
  ----------------  ---------
  True              @{BuiltInAccount=localSystem; ConfigurationName=MyConfig; Credential=; Dependencies=System.Obje…
```

#### <a name="test-the-configuration-package-can-apply-a-configuration"></a>Comprobación de que el paquete de configuración puede aplicar una configuración

Por último, si el modo de paquete de configuración es `AuditandSet`, puede probar que el método `Set` puede aplicar la configuración a un equipo local mediante el comando `Start-GuestConfigurationPackageRemediation`.

> [!IMPORTANT]
> Este comando intenta realizar cambios en el entorno local donde se ejecuta.

Parámetros del cmdlet `Start-GuestConfigurationPackageRemediation`:

- **Path**: ruta de acceso completa del paquete de configuración de invitados.

En Windows, desde una sesión de PowerShell 7 con privilegios elevados.

```powershell
# Test applying the configuration to local machine
Start-GuestConfigurationPackageRemediation -Path ./MyConfig.zip
```

En Linux, mediante la ejecución de PowerShell con sudo.

```bash
# Test applying the configuration to local machine
sudo pwsh -command 'Start-GuestConfigurationPackageRemediation -Path ./MyConfig.zip'
```

El comando no devolverá la salida a menos que se produzcan errores. Para solucionar problemas relacionados con los eventos que se producen durante `Set`, use el parámetro `-verbose`.

Después de ejecutar el comando `Start-GuestConfigurationPackageRemediation`, puede volver a ejecutar el comando `Get-GuestConfigurationComplianceStatus` para confirmar que la máquina está ahora en el estado correcto.

## <a name="next-steps"></a>Pasos siguientes

- [Publique el artefacto del paquete](./guest-configuration-create-publish.md) para que sea accesible para las máquinas.
- Use el módulo `GuestConfiguration` a fin de [crear una definición de Azure Policy](./guest-configuration-create-definition.md) para la administración a gran escala del entorno.
- [Asigne la definición de directiva personalizada](../assign-policy-portal.md) mediante Azure Portal.
- Obtenga información sobre cómo ver los [detalles de cumplimiento para las asignaciones de directivas de configuración de invitado](./determine-non-compliance.md#compliance-details-for-guest-configuration).
