---
title: Configuración de Palo Alto para Azure Spring Cloud
description: Configuración de Palo Alto para Azure Spring Cloud
author: karlerickson
ms.author: vaangadi
ms.topic: how-to
ms.service: spring-cloud
ms.date: 09/17/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: a65a6ea1fb2070b6a1879521b257c2738930bf3a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367834"
---
# <a name="how-to-configure-palo-alto-for-azure-spring-cloud"></a>Configuración de Palo Alto para Azure Spring Cloud

**Este artículo se aplica a:** ✔️ Java ✔️ C#

En este artículo se describe cómo usar Azure Spring Cloud con un firewall de Palo Alto.

Por ejemplo, la [arquitectura de referencia de Azure Spring Cloud](/azure/spring-cloud/reference-architecture) incluye una instancia de Azure Firewall para proteger las aplicaciones. Sin embargo, si las implementaciones actuales incluyen un firewall de Palo Alto, puede omitir Azure Firewall de la implementación de Azure Spring Cloud y usar Palo Alto en su lugar, como se describe en este artículo.

Debe conservar la información de configuración, como las reglas y los caracteres comodín de dirección, en archivos CSV en un repositorio Git. En este artículo se muestra cómo usar la automatización para aplicar estos archivos a Palo Alto. Para comprender la configuración que se va a aplicar a Palo Alto, consulte [Responsabilidades del cliente para ejecutar Azure Spring Cloud en una red virtual](/azure/spring-cloud/vnet-customer-responsibilities). 

> [!Note]
> Al describir el uso de las API de REST, en este artículo se usa la sintaxis de variables de PowerShell para indicar los nombres y valores que quedan a su entera discreción. Asegúrese de usar los mismos valores en todos los pasos.
>
> Después de configurar el certificado TLS/SSL en Palo Alto, quite el argumento `-SkipCertificateCheck` de todas las llamadas API de REST de Palo Alto en los ejemplos siguientes.
>
> No debe usar este artículo como referencia para las API de REST de Palo Alto. Todos los ejemplos tienen como único fin realizar una demostración. Para los detalles sobre la API autoritativa, consulte [API de REST de PAN-OS](https://docs.paloaltonetworks.com/pan-os/9-1/pan-os-panorama-api/get-started-with-the-pan-os-rest-api/pan-os-rest-api.html) en la documentación de Palo Alto.

## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. Si no tiene una suscripción, cree [una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
* Una implementación de Palo Alto. Si no tiene una implementación, puede aprovisionar [Palo Alto desde Azure Marketplace](https://ms.portal.azure.com/#create/paloaltonetworks.vmseries-ngfwbundle2).
* [PowerShell](/powershell/scripting/install/installing-powershell)
* [CLI de Azure](/cli/azure/install-azure-cli)

## <a name="configure-palo-alto"></a>Configuración de Palo Alto

En primer lugar, configure el firewall de la serie VM de Palo Alto. Para obtener instrucciones detalladas, vea [Implementación del firewall de la serie VM desde Azure Marketplace (plantilla de solución)](https://docs.paloaltonetworks.com/vm-series/9-1/vm-series-deployment/set-up-the-vm-series-firewall-on-azure/deploy-the-vm-series-firewall-on-azure-solution-template.html). Estas instrucciones le ayudarán a aprovisionar un firewall de la serie VM y configurar las subredes `Trust` y `UnTrust`, así como las tarjetas de interfaz de red asociadas. Para mantener la coherencia, debe crear este firewall en el espacio de direcciones de la red virtual `Hub` en la arquitectura de referencia.

En la [Guía de arquitectura de referencia de Azure](https://www.paloaltonetworks.com/resources/guides/azure-architecture-guide) se exploran varios modelos de diseño técnico para implementar el firewall en Azure.

En el resto de este artículo se da por supuesto que tiene las dos zonas de red preconfiguradas siguientes:

* `Trust`, que contiene la interfaz conectada a una red virtual emparejada con la red virtual de Azure Spring Cloud.
* `UnTrust`, que contiene la interfaz para la red pública de Internet creada anteriormente en la guía de implementación de la serie VM.

## <a name="prepare-csv-files"></a>Preparación de archivos CSV

A continuación, cree tres archivos CSV.

Asigne al primer archivo el nombre *AzureSpringCloudServices.csv*. Este archivo debe contener puertos de entrada para Azure Spring Cloud. Los valores del ejemplo siguiente solo son con fines de demostración. Para ver todos los valores necesarios, consulte la sección [Requisitos de la red de Azure Spring Cloud](/azure/spring-cloud/vnet-customer-responsibilities#azure-spring-cloud-network-requirements) de [Responsabilidades del cliente para ejecutar Azure Spring Cloud en una red virtual](/azure/spring-cloud/vnet-customer-responsibilities).

```CSV
name,protocol,port,tag
ASC_1194,udp,1194,AzureSpringCloud
ASC_443,tcp,443,AzureSpringCloud
ASC_9000,tcp,9000,AzureSpringCloud
ASC_445,tcp,445,AzureSpringCloud
ASC_123,udp,123,AzureSpringCloud
```

Asigne al segundo archivo el nombre *AzureSpringCloudUrlCategories.csv*. Este archivo debe contener las direcciones (con caracteres comodín) que deben estar disponibles para la salida de Azure Spring Cloud. Los valores del ejemplo siguiente solo son con fines de demostración. Para los valores actualizados, consulte [Requisitos y reglas de aplicación del FQDN de Azure Spring Cloud](/azure/spring-cloud/vnet-customer-responsibilities#azure-spring-cloud-fqdn-requirementsapplication-rules).

```CSV
name,description
*.azmk8s.io,
mcr.microsoft.com,
*.cdn.mscr.io,
*.data.mcr.microsoft.com,
management.azure.com,
*.microsoftonline.com,
*.microsoft.com,
packages.microsoft.com,
acs-mirror.azureedge.net,
mscrl.microsoft.com,
crl.microsoft.com,
crl3.digicert.com
```

Asigne al tercer archivo el nombre *AzureMonitorAddresses.csv*. Este archivo debe contener todas las direcciones e intervalos IP que estarán disponibles para las métricas y la supervisión a través de Azure Monitor, si usa Azure Monitor. Los valores del ejemplo siguiente solo son con fines de demostración. Para los valores actualizados, vea [Direcciones IP usadas por Azure Monitor](/azure/azure-monitor/app/ip-addresses).

```CSV
name,type,address,tag
40.114.241.141,ip-netmask,40.114.241.141/32,AzureMonitor
104.45.136.42,ip-netmask,104.45.136.42/32,AzureMonitor
40.84.189.107,ip-netmask,40.84.189.107/32,AzureMonitor
168.63.242.221,ip-netmask,168.63.242.221/32,AzureMonitor
52.167.221.184,ip-netmask,52.167.221.184/32,AzureMonitor
live.applicationinsights.azure.com,fqdn,live.applicationinsights.azure.com,AzureMonitor
rt.applicationinsights.microsoft.com,fqdn,rt.applicationinsights.microsoft.com,AzureMonitor
rt.services.visualstudio.com,fqdn,rt.services.visualstudio.com,AzureMonitor

```

## <a name="authenticate-into-palo-alto"></a>Autenticación en Palo Alto

A continuación, deberá autenticarse en Palo Alto y obtener una clave de API. Para más información, consulte [Obtención de la clave de API](https://docs.paloaltonetworks.com/pan-os/9-1/pan-os-panorama-api/get-started-with-the-pan-os-xml-api/get-your-api-key.html) en la documentación de Palo Alto.

En el ejemplo siguiente se usa PowerShell para autenticar y generar encabezados de solicitud que se usarán más adelante en este artículo:

```powershell
$username=<username for PaloAlto>
$password=<password for PaloAlto>
$authResponse = irm "https://${PaloAltoIpAddress}/api/?type=keygen&user=${username}&password=${password}" -SkipCertificateCheck
$paloAltoHeaders = @{'X-PAN-KEY' = $authResponse.response.result.key; 'Content-Type' = 'application/json' }
```

## <a name="delete-existing-service-group"></a>Eliminación de un grupo de servicio existente.

Si ha realizado intentos de configuración anteriores, debe restablecer estas configuraciones y eliminar cualquier regla de seguridad y grupo de servicios.

Elimine la regla de seguridad mediante la [API de REST de regla de seguridad](https://docs.paloaltonetworks.com/pan-os/9-1/pan-os-panorama-api/get-started-with-the-pan-os-rest-api/create-security-policy-rule-rest-api.html), como se muestra en el ejemplo siguiente:

```powershell
$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Policies/SecurityRules?location=vsys&vsys=vsys1&name=${paloAltoSecurityPolicyName}"
Invoke-RestMethod -Method Delete -Uri $url -Headers $paloAltoHeaders -SkipCertificateCheck
```

Elimine el grupo de servicios como se muestra en el ejemplo siguiente:

```powershell
$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/ServiceGroups?location=vsys&vsys=vsys1&name=${paloAltoServiceGroupName}"
Invoke-RestMethod -Method Delete -Uri $url -Headers $paloAltoHeaders -SkipCertificateCheck
```

Elimine cada servicio de Palo Alto (tal como se define *AzureSpringCloudServices.csv*) como se muestra en el ejemplo siguiente:

```powershell
Get-Content .\AzureSpringCloudServices.csv | ConvertFrom-Csv | select name | ForEach-Object {
    $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/Services?location=vsys&vsys=vsys1&name=${_}"
    Invoke-RestMethod -Method Delete -Uri $url -Headers $paloAltoHeaders -SkipCertificateCheck
}
```

## <a name="create-a-service-and-service-group"></a>Creación de un servicio y un grupo de servicios

Para automatizar la creación de servicios en función del archivo *AzureSpringCloudServices.csv* que creó anteriormente, use el ejemplo siguiente.

```powershell
# Define a function to create and submit a Palo Alto service creation request
function New-PaloAltoService {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true, ValueFromPipeline = $true)]
        [PSCustomObject]
        $ServiceObject
    )
    PROCESS {
        $requestBody = @{
            'entry' = @{
                '@name'    = $ServiceObject.name
                'protocol' = @{
                    $ServiceObject.protocol = @{
                        'port'     = $ServiceObject.port
                        'override' = @{
                            'no' = @{}
                        }

                    }
                }
                'tag'      = @{
                    'member' = @($ServiceObject.tag)
                }
            }
        }

        # Some rules in the CSV may need to conain source ports or descriptions. If these are present, populate them in the request
        if ($ServiceObject.description) {
            $requestBody.entry.description = $ServiceObject.description
        }
        if ($ServiceObject.'source-port') {
            $requestBody.entry.protocol."$($ServiceObject.protocol)".'source-port' = $ServiceObject.'source-port'
        }

        # Send the request
        $name = $requestBody.entry.'@name'
        $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/Services?location=vsys&vsys=vsys1&name=${name}"
         Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body (ConvertTo-Json -WarningAction Ignore $requestBody -Depth 9) -Verbose
    }
}

# Now invoke that function for every row in AzureSpringCloudServices.csv
Get-Content ./AzureSpringCloudServices.csv | ConvertFrom-Csv | New-PaloAltoService
```

A continuación, cree un grupo de servicios para estos servicios, como se muestra en el ejemplo siguiente:

```powershell
# Create a function to consume service definitions and submit a service group creation request
function New-PaloAltoServiceGroup {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true, ValueFromPipeline = $true)]
        [PSCustomObject[]]
        $RuleData,

        [Parameter(Mandatory = $true)]
        [string]
        $ServiceGroupName
    )
    begin {
        [array] $names = @()
    }

    process {
        $names += $RuleData.name
    }

    end {
        $requestBody = @{ 'entry' = [ordered] @{
                '@name'   = $ServiceGroupName
                'members' = @{ 'member' = $names }
                'tag'     = @{ 'member' = 'AzureSpringCloud' }
            }
        }

        $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/ServiceGroups?location=vsys&vsys=vsys1&name=${ServiceGroupName}"

        Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body (ConvertTo-Json $requestBody) -Verbose
    }
}

# Run that function for all services in AzureSpringCloudServices.csv.
Get-Content ./AzureSpringCloudServices.csv | ConvertFrom-Csv | New-PaloAltoServiceGroup -ServiceGroupName 'AzureSpringCloud_SG'
```

## <a name="create-custom-url-categories"></a>Creación de categorías de direcciones URL personalizadas

A continuación, defina categorías de direcciones URL personalizadas para que el grupo de servicios habilite la salida Azure Spring Cloud, como se muestra en el ejemplo siguiente.

```powershell
# Read Service entries from CSV to enter into Palo Alto
$csvImport = Get-Content ${PSScriptRoot}/AzureSpringCloudUrls.csv | ConvertFrom-Csv

# Convert name column of CSV to add to the Custom URL Group in Palo Alto
$requestBody = @{ 'entry' = [ordered] @{
        '@name' = 'AzureSpringCloud_SG'
        'list'  = @{ 'member' = $csvImport.name }
        'type'  = 'URL List'
    }
} | ConvertTo-Json -Depth 9

$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/CustomURLCategories?location=vsys&vsys=vsys1&name=AzureSpringCloud_SG"

try {
    $existingObject = Invoke-RestMethod -Method Get -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
    Invoke-RestMethod -Method Delete -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
}
catch {
}

Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body $requestBody -Verbose
```

## <a name="create-a-security-rule"></a>Creación de una regla de seguridad

A continuación, cree un archivo JSON para que contenga una regla de seguridad. Asigne al archivo el nombre *SecurityRule.json* y agregue el contenido siguiente. Los nombres de las dos zonas `Trust` y `UnTrust` coinciden con los nombres de zona descritos anteriormente en la sección [Configuración de Palo Alto](#configure-palo-alto). La entrada `service/member` contiene el nombre del grupo de servicios creado en los pasos anteriores.

```json
{
    "entry": [
        {
            "@name": "azureSpringCloudRule",
            "@location": "vsys",
            "@vsys": "vsys1",
            "to": {
                "member": [
                    "UnTrust"
                ]
            },
            "from": {
                "member": [
                    "Trust"
                ]
            },
            "source-user": {
                "member": [
                    "any"
                ]
            },
            "application": {
                "member": [
                    "any"
                ]
            },
            "service": {
                "member": [
                    "AzureSpringCloud_SG"
                ]
            },
            "hip-profiles": {
                "member": [
                    "any"
                ]
            },
            "action": "allow",
            "category": {
                "member": [
                    "any"
                ]
            },
            "source": {
                "member": [
                    "any"
                ]
            },
            "destination": {
                "member": [
                    "any"
                ]
            }
        }
    ]
}
```

Ahora, aplique esta regla a Palo Alto, como se muestra en el ejemplo siguiente.

```powershell
$url = "https://${PaloAltoIpAddress}/restapi/v9.1/Policies/SecurityRules?location=vsys&vsys=vsys1&name=azureSpringCloudRule"

# Delete the rule if it already exists
try {
    $getResult = Invoke-RestMethod -Headers $paloAltoHeaders -Method Get -SkipCertificateCheck -Uri $url -Verbose
    if ($getResult.'@status' -eq 'success') {
        Invoke-RestMethod -Method Delete  -Headers $paloAltoHeaders -SkipCertificateCheck -Uri $url
    }
}
catch {}

# Create the rule from the JSON file
Invoke-WebRequest -Uri $url -Method Post -Headers $paloAltoHeaders -Body (Get-Content SecurityRule.json) -SkipCertificateCheck
```

## <a name="create-azure-monitor-addresses"></a>Creación de direcciones de Azure Monitor

A continuación, use el archivo *AzureMonitorAddresses.csv* para definir objetos de dirección en Palo Alto. En el código de ejemplo siguiente se muestra cómo automatizar esta tarea.

```powershell
Get-Content ./AzureMonitorAddresses.csv | ConvertFrom-Csv | ForEach-Object {
    $requestBody = @{ 'entry' = [ordered]@{
            '@name' = $_.name
            $_.type = $_.address
            'tag'   = @{ 'member' = @($_.tag) }
        }
    }

    $name = $requestBody.entry.'@name'
    $url = "https://${PaloAltoIpAddress}/restapi/v9.1/Objects/Addresses?location=vsys&vsys=vsys1&name=${name}"

    # Delete the address if it already exists
    try {
        Invoke-RestMethod -Method Delete -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
    }
    catch {
    }

    # Create the address
    Invoke-RestMethod -Method Post -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders -Body (ConvertTo-Json -WarningAction Ignore $requestBody -Depth 3) -Verbose
}
```

## <a name="commit-changes-to-palo-alto"></a>Confirmación de cambios en Palo Alto

Debe confirmar algunos de los cambios anteriores para que se activen. Puede hacerlo con la siguiente llamada API de REST.

```powershell
$url = "https://${PaloAltoIpAddress}/api/?type=commit&cmd=<commit></commit>"
Invoke-RestMethod -Method Get -Uri $url  -SkipCertificateCheck -Headers $paloAltoHeaders
```

## <a name="configure-the-security-rules-for-azure-spring-cloud-subnets"></a>Configuración de las reglas de seguridad para subredes de Azure Spring Cloud

A continuación, agregue reglas de seguridad de red para permitir que el tráfico de Palo Alto acceda a Azure Spring Cloud. En los ejemplos siguientes se hace referencia a los grupos de seguridad de red (NSG) de radio creados por la arquitectura de referencia: `nsg-spokeapp` y `nsg-spokeruntime`.

Ejecute los siguientes comandos de la CLI de Azure en una ventana de PowerShell para crear la regla de seguridad de red necesaria para cada uno de estos NSG, donde `$PaloAltoAddressPrefix` es la dirección de enrutamiento interdominios sin clases (CIDR) de las direcciones IP privadas de Palo Alto.

```azurecli
az network nsg rule create `
    --resource-group $ResourceGroupName `
    --name 'allow-palo-alto' `
    --nsg-name 'nsg-spokeapp' `
    --access Allow `
    --source-address-prefixes $PaloAltoAddressPrefix `
    --priority 1000
az network nsg rule create `
    --resource-group $ResourceGroupName `
    --name 'allow-palo-alto' `
    --nsg-name 'nsg-spokeruntime' `
    --access Allow `
    --source-address-prefixes $PaloAltoAddressPrefix `
    --priority 1000
```

## <a name="configure-the-next-hop"></a>Configuración del próximo salto

Después de configurar Palo Alto, configure Azure Spring Cloud para que Palo Alto sea su próximo salto para el acceso saliente a Internet. Puede usar los siguientes comandos de la CLI de Azure en una ventana de PowerShell para esta configuración. Asegúrese de proporcionar valores para las siguientes variables:

* `$AppResourceGroupName`: nombre del grupo de recursos que contiene Azure Spring Cloud.
* `$AzureSpringCloudServiceSubnetRouteTableName`: nombre de la tabla de enrutamiento de subred en el runtime o servicio de Azure Spring Cloud. En la arquitectura de referencia, se establece en `rt-spokeruntime`.
* `$AzureSpringCloudAppSubnetRouteTableName`: nombre de la tabla de enrutamiento de subred de aplicación de Azure Spring Cloud. En la arquitectura de referencia, se establece en `rt-spokeapp`.

```azurecli
az network route-table route create `
    --resource-group ${AppResourceGroupName} `
    --name default `
    --route-table-name ${AzureSpringCloudServiceSubnetRouteTableName} `
    --address-prefix 0.0.0.0/0 `
    --next-hop-type VirtualAppliance `
    --next-hop-ip-address ${PaloAltoIpAddress} `
    --verbose

az network route-table route create `
    --resource-group ${AppResourceGroupName} `
    --name default `
    --route-table-name ${AzureSpringCloudAppSubnetRouteTableName} `
    --address-prefix 0.0.0.0/0 `
    --next-hop-type VirtualAppliance `
    --next-hop-ip-address ${PaloAltoIpAddress} `
    --verbose
```

Se ha completado la configuración.

## <a name="next-steps"></a>Pasos siguientes

* [Streaming de registros de aplicaciones de Azure Spring Cloud en tiempo real](/azure/spring-cloud/how-to-log-streaming)
* [Agente In-Process de Java de Application Insights en Azure Spring Cloud](/azure/spring-cloud/how-to-application-insights)
* [Automatización de implementaciones de aplicaciones en Azure Spring Cloud](/azure/spring-cloud/how-to-cicd)
