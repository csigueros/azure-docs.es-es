---
ms.topic: include
ms.date: 09/03/2021
ms.author: lagayhar
author: lgayhardt
services: azure-monitor
ms.subservice: application-insights
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b88ee4d53f137f98aa82ee736043943f844f36ca
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154607"
---
### <a name="app-service-application-settings-with-azure-resource-manager"></a>Configuración de la aplicación de App Service con Azure Resource Manager

La configuración de la aplicación de App Service se puede administrar y configurar con [plantillas de Azure Resource Manager](../articles/azure-resource-manager/templates/syntax.md). Este método puede usarse al implementar nuevos recursos de App Service con la automatización de Azure Resource Manager o para modificar la configuración de los recursos existentes.

La estructura básica de JSON de la configuración de la aplicación para App Service se muestra a continuación:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

Para obtener un ejemplo de una plantilla de Azure Resource Manager con la configuración de la aplicación establecida para Application Insights, esta [plantilla](https://github.com/Andrew-MSFT/BasicImageGallery) puede resultar útil, sobre todo en la sección que empieza a partir de la [línea 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatización de la creación de un recurso de Application Insights y vínculo a la instancia de App Service recién creada

Para crear una plantilla de Azure Resource Manager con toda la configuración predeterminada de Application Insights establecida, comience el proceso como si fuese a crear una nueva aplicación web con Application Insights habilitado. 

1. Cree un nuevo recurso de App Service con la información de la aplicación web deseada. Habilite Application Insights en la pestaña **Supervisión**.

2. Seleccione **Revisar y crear** y, a continuación, **Descargar una plantilla para la automatización** en la parte inferior.

    :::image type="content" source="../articles/azure-monitor/app/media/azure-web-apps/create-web-app.png" alt-text="Captura de pantalla del menú de creación de una aplicación web de App Service." :::

    Esta opción genera la plantilla de Azure Resource Manager más reciente con toda la configuración necesaria establecida.
    
    :::image type="content" source="../articles/azure-monitor/app/media/azure-web-apps/arm-template.png" alt-text="Captura de pantalla de la plantilla de una aplicación web de App Service." border="false":::
    

A continuación, se muestra un ejemplo; reemplace todas las instancias de `AppMonitoredSite` por el nombre del sitio:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enable-through-powershell"></a>Habilitación mediante Powershell

Para habilitar la supervisión de la aplicación a través de PowerShell, solo se debe cambiar la configuración de la aplicación subyacente. A continuación, se muestra un ejemplo que habilita la supervisión de la aplicación para un sitio web denominado "AppMonitoredSite" en el grupo de recursos "AppMonitoredRG" y configura los datos para que se envíen a la clave de instrumentación "012345678-abcd-ef01-2345-6789abcd".

[!INCLUDE [updated-for-az](updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```