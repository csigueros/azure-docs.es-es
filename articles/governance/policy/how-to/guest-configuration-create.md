---
title: Cómo crear artefactos de paquete de configuración de invitado personalizado
description: Obtenga información sobre cómo crear un archivo de paquete de configuración de invitado.
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: 12767e40ef99cf218666b6dc540a5ae1c2e2bffa
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772542"
---
# <a name="how-to-create-custom-guest-configuration-package-artifacts"></a>Cómo crear artefactos de paquete de configuración de invitado personalizado

Antes de comenzar, es aconsejable leer la página de información general de la [configuración de invitado](../concepts/guest-configuration.md).

Durante la auditoría o configuración de Windows y Linux, la configuración de invitado usa [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC). La configuración de DSC define la condición en la que debe estar la máquina.

> [!IMPORTANT]
> Los paquetes personalizados que auditan el estado de un entorno están disponibles con carácter general, pero los que aplican configuraciones están **en versión preliminar**. **Se aplican las siguientes limitaciones:**
> 
> Para usar paquetes de configuración de invitado que aplican configuraciones, se requiere la versión de la extensión de configuración de invitado de máquina virtual de Azure **1.29.24** o posterior, o el agente de Arc **1.10.0** o posterior.
> 
> Para probar la creación y aplicación de configuraciones en Linux, el módulo `GuestConfiguration` solo está disponible en Ubuntu 18, pero el paquete y las directivas que genera el módulo se pueden usar en cualquier distribución o versión de Linux compatible con Azure o Arc.
>
> No hay disponibles paquetes de prueba en MacOS.
> 
> No use secretos ni información confidencial en paquetes de contenido personalizado.

Use los siguientes pasos para crear su propia configuración con el fin de administrar el estado de una máquina, sea de Azure o no.

## <a name="install-powershell-7-and-required-powershell-modules"></a>Instalación de PowerShell 7 y los módulos de PowerShell necesarios

En primer lugar, asegúrese de haber seguido todos los pasos de la página [Configuración de un entorno de creación de configuración de invitado](./guest-configuration-create-setup.md) para instalar la versión de PowerShell necesaria para su sistema operativo, el módulo `GuestConfiguration` y, en caso necesario, el `PSDesiredStateConfiguration`.

## <a name="author-a-configuration"></a>Creación de una configuración

Antes de crear un paquete de configuración, cree y compile una configuración DSC.
Si es necesario, hay configuraciones de ejemplo disponibles para Windows y Linux.

> [!IMPORTANT]
> Al compilar configuraciones para Windows, use la versión `2.0.5` (versión estable) de `PSDesiredStateConfiguration`. Al compilar configuraciones para Linux, instale la versión preliminar `3.0.0`.

En el [documento de introducción](/powershell/scripting/dsc/getting-started/wingettingstarted#define-a-configuration-and-generate-the-configuration-document) de DSC para Windows se proporciona un ejemplo.

Para Linux, deberá crear un módulo de recursos de DSC personalizado usando [clases de PowerShell](/powershell/scripting/dsc/resources/authoringResourceClass).
En la página [Escritura de un recurso de DSC personalizado con clases de PowerShell](/powershell/scripting/dsc/resources/authoringResourceClass) de la documentación de la solución hay disponible un ejemplo completo de un recurso personalizado y una configuración (probado con la configuración de invitado).

## <a name="create-a-configuration-package-artifact"></a>Creación de un artefacto de paquete de configuración

Una vez compilado el MOF, los archivos auxiliares deben empaquetarse juntos.
La configuración de invitado usa el paquete completado para crear las definiciones de Azure Policy.

El cmdlet `New-GuestConfigurationPackage` crea el paquete. Los módulos necesarios para la configuración deben estar disponibles en `$Env:PSModulePath` para el entorno de desarrollo, de modo que los comandos del módulo puedan agregarlos al paquete.

Parámetros del cmdlet `New-GuestConfigurationPackage` al crear el contenido de Windows:

- **Name**: nombre del paquete de configuración de invitado.
- **Configuración**: ruta de acceso completa del documento de configuración de DSC compilado.
- **Ruta de acceso**: ruta de acceso de la carpeta de salida. Este parámetro es opcional. Si no se especifica, el paquete se crea en el directorio actual.
- **Type**: (Audit o AuditandSet) determina si la configuración solo debe auditar o si se debe aplicar la configuración y cambiar el estado de la máquina. El valor predeterminado es "Audit".

Este paso no requiere elevación. El cmdlet Force se usa para sobrescribir los paquetes existentes en caso de que ejecute el comando más de una vez.

Los siguientes comandos crean artefactos de paquete:

```powershell
# Create a package that will only audit compliance
New-GuestConfigurationPackage `
  -Name 'MyConfig' `
  -Configuration './Config/MyConfig.mof' `
  -Type Audit `
  -Force
```

```powershell
# Create a package that will audit and apply the configuration (Set)
New-GuestConfigurationPackage `
  -Name 'MyConfig' `
  -Configuration './Config/MyConfig.mof' `
  -Type AuditAndSet `
  -Force
```

Se devuelve un objeto con el nombre y la ruta de acceso del paquete creado.

```
Name      Path                                                    
----      ----                                                    
MyConfig  /Users/.../MyConfig/MyConfig.zip
```

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Contenido esperado de un artefacto de configuración de invitado

La configuración de invitado usa el paquete completado para crear las definiciones de Azure Policy. El paquete consta de:

- La configuración de DSC compilada como MOF
- Carpeta Modules
  - Módulo GuestConfiguration
  - Módulo DscNativeResources
  - Módulos de recursos de DSC que requiere el MOF
- Archivo de metaconfiguración que almacena el paquete `type` y `version`

El cmdlet de PowerShell crea el archivo .zip del paquete. No se requiere ninguna carpeta de nivel raíz ni carpeta de versiones. El formato del paquete debe ser un archivo .zip y no puede superar el tamaño total de 100 MB cuando se descomprima.

## <a name="extending-guest-configuration-with-third-party-tools"></a>Ampliación de la configuración de invitado con herramientas de terceros

Los paquetes de artefacto para la configuración de invitado se pueden ampliar para que incluyan herramientas de terceros. La ampliación de la configuración de invitado requiere el desarrollo de dos componentes.

- Un recurso de Desired State Configuration que controla toda la actividad relacionada con la administración de la herramienta de terceros.
  - Instalar
  - Invocar
  - Convertir salida
- Contenido en el formato correcto para que la herramienta consuma de forma nativa.

El recurso de DSC requiere un desarrollo personalizado si aún no existe una solución de la comunidad. Las soluciones de la comunidad se pueden descubrir buscando en Galería de PowerShell por la etiqueta [GuestConfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22).

> [!NOTE]
> La extensibilidad de la configuración de invitado es un escenario de "traiga su propia licencia". Asegúrese de que ha cumplido los términos y condiciones de las herramientas de terceros antes de usarlas.

Una vez instalado el recurso de DSC en el entorno de desarrollo, use el parámetro **FilesToInclude** para que `New-GuestConfigurationPackage` incluya el contenido de la plataforma de terceros en el artefacto de contenido.

## <a name="next-steps"></a>Pasos siguientes

- [Pruebe el artefacto de paquete](./guest-configuration-create-test.md) desde el entorno de desarrollo.
- [Publique el artefacto del paquete](./guest-configuration-create-publish.md) para que sea accesible para los equipos.
- Use el módulo `GuestConfiguration` a fin de [crear una definición de Azure Policy](./guest-configuration-create-definition.md) para la administración a gran escala del entorno.
- [Asigne la definición de directiva personalizada](../assign-policy-portal.md) mediante Azure Portal.
- Obtenga información sobre cómo ver los [detalles de cumplimiento para las asignaciones de directivas de configuración de invitado](./determine-non-compliance.md#compliance-details-for-guest-configuration).
