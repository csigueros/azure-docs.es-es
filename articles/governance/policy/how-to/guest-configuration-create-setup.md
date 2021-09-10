---
title: Instalación del módulo de creación de configuración de invitado
description: Obtenga información sobre cómo instalar el módulo de PowerShell para crear y probar asignaciones y definiciones de directivas de configuración de invitado.
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: c32c405cffb71527e61b68f7fb532487d695743f
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868618"
---
# <a name="how-to-setup-a-guest-configuration-authoring-environment"></a>Configuración de un entorno de creación de configuración de invitado

El módulo `GuestConfiguration` de PowerShell automatiza el proceso de creación de contenido personalizado, que incluye lo siguiente:

- Creación de un artefacto de contenido de configuración de invitado (.zip)
- Validación del cumplimiento de los requisitos del paquete
- Instalación del agente de configuración de invitado localmente para pruebas
- Validación del uso del paquete para auditar la configuración de una máquina
- Validación del uso del paquete para configurar una máquina
- Publicación del paquete en Azure Storage
- Creación de una definición de directiva
- Publicación de la directiva

El soporte técnico para aplicar configuraciones a través de la configuración de invitado se introduce en la versión `3.4.2`.

> [!IMPORTANT]
> Los paquetes personalizados que auditan el estado de un entorno están disponibles con carácter general, pero los paquetes que aplican configuraciones están **en versión preliminar**. **Se aplican las siguientes limitaciones:**
> 
> Para probar la creación y aplicación de configuraciones en Linux, el módulo `GuestConfiguration` solo está disponible en Ubuntu 18, pero el paquete y las definiciones de directiva que ha generado el módulo se pueden usar en cualquier distribución o versión de Linux compatible con Azure o Arc.
>
> La prueba de paquetes en MacOS no está disponibles.

### <a name="base-requirements"></a>Requisitos básicos

Sistemas operativos donde se puede instalar el módulo:

- Ubuntu 18
- Windows

El módulo se puede instalar en una máquina que ejecute PowerShell 7. Instale las versiones de PowerShell que se enumeran a continuación.

| SO | Versión de PowerShell |
|-|-|
|Windows|[PowerShell 7.1.3](https://github.com/PowerShell/PowerShell/releases/tag/v7.1.3)|
|Ubuntu 18|[PowerShell 7.2.0-preview.6](https://github.com/PowerShell/PowerShell/releases/tag/v7.2.0-preview.6)|

El módulo `GuestConfiguration` requiere el software siguiente:

- Azure PowerShell 5.9.0 o posterior. Los módulos Az necesarios se instalan automáticamente con el módulo `GuestConfiguration`, aunque también puede seguir estas [instrucciones](/powershell/azure/install-az-ps).
  - Solo se requieren los módulos Az "Az.Accounts", "Az.Resources" y "Az.Storage".
- Módulo `PSDesiredStateConfiguration`.

### <a name="install-the-module-from-the-powershell-gallery"></a>Instalación del módulo desde la Galería de PowerShell

Para instalar el módulo `GuestConfiguration` en Windows o Linux, ejecute el siguiente comando en PowerShell 7.

```powershell
# Install the guest configuration DSC resource module from PowerShell Gallery
Install-Module -Name GuestConfiguration
```

Asegúrese de que el módulo se haya importado:

```powershell
# Get a list of commands for the imported GuestConfiguration module
Get-Command -Module 'GuestConfiguration'
```

## <a name="update-and-import-the-psdesiredstateconfiguration-module-on-linux"></a>Actualización e importación del módulo PSDesiredStateConfiguration en Linux

A partir de PowerShell 7.2 Preview 6, DSC se lanza independientemente de PowerShell como un módulo en el Galería de PowerShell. Para instalar DSC, versión 3, en el entorno de PowerShell en Linux, ejecute el comando siguiente.

```powershell
# Install the DSC module before compiling using the Configuration keyword
Install-Module PSDesiredStateConfiguration -AllowPreRelease -Force
```

## <a name="next-steps"></a>Pasos siguientes

- [Cree un artefacto de paquete](./guest-configuration-create.md) para la configuración de invitado.
- [Pruebe el artefacto de paquete](./guest-configuration-create-test.md) desde el entorno de desarrollo.
- Use el módulo `GuestConfiguration` a fin de [crear una definición de Azure Policy](./guest-configuration-create-definition.md) para la administración a gran escala del entorno.
- [Asigne la definición de directiva personalizada](../assign-policy-portal.md) mediante Azure Portal.
- Obtenga información sobre cómo ver los [detalles de cumplimiento de las asignaciones de directivas de configuración de invitado](./determine-non-compliance.md#compliance-details-for-guest-configuration).
