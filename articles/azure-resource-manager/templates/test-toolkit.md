---
title: Kit de herramientas para pruebas de plantillas de ARM
description: Describe cómo ejecutar el kit de herramientas para pruebas de la plantilla de Azure Resource Manager en su plantilla. El kit de herramientas le permite ver si ha implementado los procedimientos recomendados.
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 5c53ede7df0fd8ba24ef82e7de5a793a4e55f204
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393283"
---
# <a name="use-arm-template-test-toolkit"></a>Uso del kit de herramientas para pruebas de plantillas de ARM

El [kit de herramientas para pruebas de la plantilla de Resource Manager](https://aka.ms/arm-ttk) comprueba si la plantilla usa los procedimientos recomendados. Cuando la plantilla no es compatible con los procedimientos recomendados, devuelve una lista de advertencias con los cambios sugeridos. Con el kit de herramientas para pruebas, puede obtener información sobre cómo evitar los problemas comunes en el desarrollo de plantillas. En este artículo se describe cómo ejecutar el kit de herramientas para pruebas y cómo agregar o eliminar pruebas. Para más información sobre cómo ejecutar pruebas o sobre la ejecución de una prueba específica, consulte [Parámetros de prueba](#test-parameters).

El kit de herramientas es un conjunto de scripts de PowerShell que se puede ejecutar desde un comando de PowerShell o la CLI. Estas pruebas son recomendaciones, pero no requisitos. Puede decidir qué pruebas son adecuadas para sus objetivos y personalizar qué pruebas se ejecutan.

El kit de herramientas contiene cuatro conjuntos de pruebas:

- [Casos de prueba para plantillas de ARM](template-test-cases.md)
- [Casos de prueba para archivos de parámetros](parameter-file-test-cases.md)
- [Casos de prueba para createUiDefinition.json](createUiDefinition-test-cases.md)
- [Casos de prueba de todos los archivos](all-files-test-cases.md)

## <a name="install-on-windows"></a>Instalar en Windows

1. Si aún no tiene PowerShell, [instale PowerShell en Windows](/powershell/scripting/install/installing-powershell-core-on-windows).

1. [Descargue el archivo ZIP más reciente](https://aka.ms/arm-ttk-latest) para el kit de herramientas de pruebas y extráigalo.

1. Inicie PowerShell.

1. Navegue hasta la carpeta en la que extrajo el kit de herramientas de pruebas. Dentro de esa carpeta, vaya a la carpeta _arm-ttk_.

1. Si la [directiva de ejecución](/powershell/module/microsoft.powershell.core/about/about_execution_policies) bloquea los scripts de Internet, debe desbloquear los archivos de script. Asegúrese de que se encuentra en la carpeta _arm-ttk_.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Importe el módulo.

   ```powershell
   Import-Module .\arm-ttk.psd1
   ```

1. Para ejecutar las pruebas, use el siguiente comando:

   ```powershell
   Test-AzTemplate -TemplatePath \path\to\template
   ```

## <a name="install-on-linux"></a>Instalar en Linux

1. Si aún no tiene PowerShell, [instale PowerShell en Linux](/powershell/scripting/install/installing-powershell-core-on-linux).

1. [Descargue el archivo ZIP más reciente](https://aka.ms/arm-ttk-latest) para el kit de herramientas de pruebas y extráigalo.

1. Inicie PowerShell.

   ```bash
   pwsh
   ```

1. Navegue hasta la carpeta en la que extrajo el kit de herramientas de pruebas. Dentro de esa carpeta, vaya a la carpeta _arm-ttk_.

1. Si la [directiva de ejecución](/powershell/module/microsoft.powershell.core/about/about_execution_policies) bloquea los scripts de Internet, debe desbloquear los archivos de script. Asegúrese de que se encuentra en la carpeta _arm-ttk_.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Importe el módulo.

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. Para ejecutar las pruebas, use el siguiente comando:

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="install-on-macos"></a>Instalación en macOS

1. Si aún no tiene PowerShell, [instale PowerShell en macOS](/powershell/scripting/install/installing-powershell-core-on-macos).

1. Instale `coreutils`:

   ```bash
   brew install coreutils
   ```

1. [Descargue el archivo ZIP más reciente](https://aka.ms/arm-ttk-latest) para el kit de herramientas de pruebas y extráigalo.

1. Inicie PowerShell.

   ```bash
   pwsh
   ```

1. Navegue hasta la carpeta en la que extrajo el kit de herramientas de pruebas. Dentro de esa carpeta, vaya a la carpeta _arm-ttk_.

1. Si la [directiva de ejecución](/powershell/module/microsoft.powershell.core/about/about_execution_policies) bloquea los scripts de Internet, debe desbloquear los archivos de script. Asegúrese de que se encuentra en la carpeta _arm-ttk_.

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Importe el módulo.

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. Para ejecutar las pruebas, use el siguiente comando:

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="result-format"></a>Formato de resultado

Las pruebas que se superan se muestran en color **verde** y precedidas de `[+]` .

Las pruebas con errores se muestran en color **rojo** y precedidas de `[-]` .

Las pruebas con una advertencia se muestran en color **amarillo** y precedidas de `[?]`.

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="Vista de resultados de la prueba.":::

Los resultados de texto son:

```powershell
deploymentTemplate
[+] adminUsername Should Not Be A Literal (6 ms)
[+] apiVersions Should Be Recent In Reference Functions (9 ms)
[-] apiVersions Should Be Recent (6 ms)
    Api versions must be the latest or under 2 years old (730 days) - API version 2019-06-01 of
    Microsoft.Storage/storageAccounts is 760 days old
    Valid Api Versions:
    2021-04-01
    2021-02-01
    2021-01-01
    2020-08-01-preview

[+] artifacts parameter (4 ms)
[+] CommandToExecute Must Use ProtectedSettings For Secrets (9 ms)
[+] DependsOn Best Practices (5 ms)
[+] Deployment Resources Must Not Be Debug (6 ms)
[+] DeploymentTemplate Must Not Contain Hardcoded Uri (4 ms)
[?] DeploymentTemplate Schema Is Correct (6 ms)
    Template is using schema version '2015-01-01' which has been deprecated and is no longer
    maintained.
```

## <a name="test-parameters"></a>Parámetros de prueba

Cuando se proporciona el parámetro `-TemplatePath`, el kit de herramientas busca en esa carpeta una plantilla con el nombre _azuredeploy.json_ o _maintemplate.json_. Primero se prueba esta plantilla y, a continuación, se prueban todas las demás plantillas de la carpeta y sus subcarpetas. Las otras plantillas se prueban como plantillas vinculadas. Si la ruta de acceso incluye un archivo llamado [createUiDefinition.json](../managed-applications/create-uidefinition-overview.md), se ejecutan las pruebas que corresponden a la definición de la interfaz de usuario. Las pruebas también se ejecutan para los archivos de parámetros y todos los archivos JSON de la carpeta.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

Para probar un archivo de esa carpeta, agregue el parámetro `-File`. Sin embargo, la carpeta debe tener una plantilla principal llamada _azuredeploy.json_ o _maintemplate.json_.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

De forma predeterminada, se ejecutan todas las pruebas. Para especificar pruebas individuales que se van a ejecutar, use el parámetro `-Test` y proporcione el nombre de la prueba. Para los nombres de prueba, consulte las [plantillas de ARM](template-test-cases.md), los [archivos de parámetros](parameter-file-test-cases.md), [createUiDefinition.json](createUiDefinition-test-cases.md) y [todos los archivos](all-files-test-cases.md).

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>Personalización de las pruebas

Puede personalizar las pruebas predeterminadas o crear sus propias pruebas. Si quiere eliminar permanentemente una prueba, elimine el archivo _.test.ps1_ de la carpeta.

El kit de herramientas tiene cuatro carpetas que contienen las pruebas predeterminadas que se ejecutan para tipos de archivo específicos:

- Plantillas de ARM: _\arm-ttk\testcases\deploymentTemplate_
- Archivos de parámetros: _\arm-ttk\testcases\deploymentParameters_
- [createUiDefinition.json](../managed-applications/create-uidefinition-overview.md): _\arm-ttk\testcases\CreateUIDefinition_
- Todos los archivos: _\arm-ttk\testcases\AllFiles_

### <a name="add-a-custom-test"></a>Adición de una prueba personalizada

Para agregar su propia prueba, cree un archivo con la convención de nomenclatura: _Your-Custom-Test-Name.test.ps1_.

La prueba puede obtener la plantilla como un parámetro de objeto o un parámetro de cadena. Normalmente se usa uno u otro, pero se pueden usar ambos.

Use el parámetro de objeto cuando necesite obtener una sección de la plantilla y recorrer en iteración sus propiedades. Una prueba que usa el parámetro de objeto tiene el siguiente formato:

```powershell
param(
  [Parameter(Mandatory=$true,Position=0)]
  [PSObject]
  $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

El objeto de la plantilla tiene las siguientes propiedades:

- `$schema`
- `contentVersion`
- `parameters`
- `variables`
- `resources`
- `outputs`

Por ejemplo, puede obtener la colección de parámetros con `$TemplateObject.parameters`.

Use el parámetro de cadena cuando necesite realizar una operación de cadena en toda la plantilla. Una prueba que usa el parámetro de cadena tiene el siguiente formato:

```powershell
param(
  [Parameter(Mandatory)]
  [string]
  $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

Por ejemplo, puede ejecutar una expresión regular del parámetro de cadena para ver si se utiliza una sintaxis específica.

Para más información sobre la implementación de la prueba, examine las otras pruebas de esa carpeta.

## <a name="integrate-with-azure-pipelines"></a>Integración con Azure Pipelines

Puede agregar el kit de herramientas para pruebas a su canalización de Azure. Con una canalización, puede ejecutar la prueba cada vez que se actualiza la plantilla o ejecutarla como parte del proceso de implementación.

La forma más fácil de agregar el kit de herramientas para pruebas a la canalización es con extensiones de terceros. Están disponibles las dos extensiones siguientes:

- [Ejecutar pruebas de ARM TTK](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
- [Comprobación de plantillas de ARM](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

O bien, puede implementar sus propias tareas. En el ejemplo siguiente se muestra cómo descargar el kit de herramientas para pruebas.

```json
{
  "environment": {},
  "enabled": true,
  "continueOnError": false,
  "alwaysRun": false,
  "displayName": "Download TTK",
  "timeoutInMinutes": 0,
  "condition": "succeeded()",
  "task": {
    "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
    "versionSpec": "2.*",
    "definitionType": "task"
  },
  "inputs": {
    "targetType": "inline",
    "filePath": "",
    "arguments": "",
    "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
    "errorActionPreference": "stop",
    "failOnStderr": "false",
    "ignoreLASTEXITCODE": "false",
    "pwsh": "true",
    "workingDirectory": ""
  }
}
```

En el ejemplo siguiente se muestra cómo ejecutar las pruebas.

```json
{
  "environment": {},
  "enabled": true,
  "continueOnError": true,
  "alwaysRun": false,
  "displayName": "Run Best Practices Tests",
  "timeoutInMinutes": 0,
  "condition": "succeeded()",
  "task": {
    "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
    "versionSpec": "2.*",
    "definitionType": "task"
  },
  "inputs": {
    "targetType": "inline",
    "filePath": "",
    "arguments": "",
    "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
    "errorActionPreference": "continue",
    "failOnStderr": "true",
    "ignoreLASTEXITCODE": "false",
    "pwsh": "true",
    "workingDirectory": ""
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre las pruebas de plantillas, consulte [Casos de prueba para plantillas de ARM](template-test-cases.md).
- Para probar los archivos de parámetros, consulte [Casos de prueba para archivos de parámetros](parameters.md).
- Para las pruebas de createUiDefinition, consulte [Casos de prueba para createUiDefinition.json](createUiDefinition-test-cases.md).
- Para información sobre las pruebas de todos los archivos, vea [Casos de prueba para todos los archivos](all-files-test-cases.md).
- Para un módulo de Microsoft Learn que abarque el uso del kit de herramientas de pruebas, consulte [Validación de recursos de Azure mediante el kit de herramientas para pruebas de plantillas de ARM](/learn/modules/arm-template-test/).
