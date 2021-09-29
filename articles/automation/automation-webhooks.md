---
title: Inicio de un runbook de Azure Automation en un webhook
description: En este artículo se indica cómo usar un webhook para iniciar un runbook en Azure Automation desde una llamada HTTP.
services: automation
ms.subservice: process-automation
ms.date: 07/21/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3ccdbb04942e946d251008187693ee948b960178
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124836936"
---
# <a name="start-a-runbook-from-a-webhook"></a>Inicio de un runbook desde un webhook

Un webhook permite que un servicio externo inicie un runbook determinado en Azure Automation mediante una sola solicitud HTTP. Entre los servicios externos se incluyen Azure DevOps Services, GitHub, registros de Azure Monitor y aplicaciones personalizadas. Estos servicios pueden usar un webhook para iniciar un runbook sin tener que implementar la API completa de Azure Automation. Puede comparar los webhooks con otros métodos para iniciar un runbook en [Inicio de un runbook en Azure Automation](./start-runbooks.md).

> [!NOTE]
> No se admite el uso de un webhook para iniciar un runbook de Python.

![Información general sobre webhooks](media/automation-webhooks/webhook-overview-image.png)

Para comprender los requisitos de cliente para TLS 1.2 con webhooks, consulte [TLS 1.2 para Azure Automation](automation-managing-data.md#tls-12-for-azure-automation).

## <a name="webhook-properties"></a>Propiedades de un webhook

En la tabla siguiente se describen las propiedades que debe configurar para un webhook.

| Propiedad | Descripción |
|:--- |:--- |
| Nombre |Nombre del webhook. Puede indicar cualquier nombre que desee, ya que no se expone al cliente. Solo se utiliza para que identifique el runbook en Azure Automation. Como práctica recomendada, debe proporcionar al webhook un nombre relacionado con el cliente que lo usa. |
| URL |Dirección URL del webhook. Es la dirección única a la que llama un cliente con una solicitud HTTP POST para iniciar el runbook vinculado al webhook. Se genera automáticamente al crear el webhook. No se puede especificar una dirección URL personalizada. <br> <br> La dirección URL contiene un token de seguridad que permite que un sistema de terceros invoque el runbook sin autenticación adicional, por lo que debe tratarla como una contraseña. Por motivos de seguridad, solo puede ver la dirección URL en Azure Portal cuando se crea el webhook. Anote la dirección URL en una ubicación segura para su uso futuro. |
| Fecha de expiración | Fecha de expiración del webhook, tras la cual ya no se puede usar. Esta fecha se puede modificar una vez creado el webhook, siempre y cuando no haya expirado. |
| habilitado | Este valor indica si el webhook se habilita de forma predeterminada cuando se crea. Si establece esta propiedad en Deshabilitado, ningún cliente puede usar el webhook. Puede establecer esta propiedad al crear el webhook o en cualquier otro momento después de su creación. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parámetros usados cuando el webhook inicia un runbook

Un webhook puede definir valores para parámetros de runbook que se usan cuando se inicia el runbook. El webhook debe incluir los valores de los parámetros obligatorios del runbook y puede incluir valores para los parámetros opcionales. Los valores de parámetro configurados en un webhook se pueden modificar incluso después de crear el webhook. Varios webhooks vinculados a un único runbook puede utilizar distintos valores de parámetros de runbook. Cuando un cliente inicia un runbook mediante un webhook, no puede reemplazar los valores de los parámetros definidos en el webhook.

Para recibir datos del cliente, el runbook admite un único parámetro llamado `WebhookData`. Este parámetro define un objeto que contiene datos que el cliente incluye en una solicitud POST.

![Propiedades de WebhookData](media/automation-webhooks/webhook-data-properties.png)

El parámetro `WebhookData` tiene las siguientes propiedades:

| Propiedad | Descripción |
|:--- |:--- |
| WebhookName | Nombre del webhook. |
| RequestHeader | Tabla hash que contiene los encabezados de la solicitud POST entrante. |
| RequestBody | Cuerpo de la solicitud POST entrante. Este cuerpo conserva el formato de los datos, como cadena, JSON, XML o datos codificados por formulario. El runbook debe escribirse para que trabaje con el formato de datos que se espera. |

No hay ninguna configuración del webhook obligatoria para admitir el parámetro `WebhookData`, y el runbook no tiene que aceptarlo. Si el runbook no define el parámetro, se ignoran los detalles de la solicitud enviada desde el cliente.

> [!NOTE]
> Al llamar a un webhook, el cliente siempre debe almacenar los valores de los parámetros, por si se produce un error en la llamada. Si se produce un problema de conexión o de interrupción de la red, la aplicación no podrá recuperar las llamadas de webhook con errores.

Si especifica un valor para `WebhookData` durante la creación del webhook, se invalidará cuando el webhook inicie el runbook con los datos de la solicitud POST del cliente. Esto sucederá incluso si la aplicación no contiene ningún dato en el cuerpo de la solicitud.

Si inicia un runbook que define `WebhookData` mediante un mecanismo que no sea un webhook, puede proporcionar un valor para `WebhookData` que el runbook reconozca. Este valor debe ser un objeto con las mismas [propiedades](#webhook-properties) que el parámetro `WebhookData`, para que el runbook pueda funcionar correctamente con él, del mismo modo que si trabajara con objetos `WebhookData` reales pasados por un webhook.

Por ejemplo, si va a iniciar el siguiente runbook desde Azure Portal y desea pasar algunos datos de webhook de ejemplo para las pruebas, debe pasarlos como JSON en la interfaz de usuario.

![Parámetro WebhookData de la interfaz de usuario](media/automation-webhooks/WebhookData-parameter-from-UI.png)

En el siguiente ejemplo de runbook, vamos a definir las siguientes propiedades para `WebhookData`:

* **WebhookName**: MyWebhook
* **RequestBody**: `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Ahora pasamos el siguiente objeto JSON en la interfaz de usuario para el parámetro `WebhookData`. Este ejemplo, con los retornos de carro y los caracteres de nueva línea, coincide con el formato que se pasa desde un webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Iniciar el parámetro WebhookData de la interfaz de usuario](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Automation registra los valores de todos los parámetros de entrada con el trabajo de runbook. Por tanto, se registra cualquier entrada que proporcione el cliente en la solicitud de webhook, y esta entrada estará disponible para cualquiera con acceso al trabajo de automatización. Por este motivo, debe tener cuidado en cómo incluir información confidencial en las llamadas de webhook.

## <a name="webhook-security"></a>Seguridad del webhook

La seguridad de un webhook se basa en la privacidad de su dirección URL, que contiene un token de seguridad que permite que se invoque el webhook. Azure Automation no realiza ninguna autenticación en una solicitud, siempre que se haga en la dirección URL correcta. Por esta razón, los clientes no deben utilizar webhooks en runbooks que realicen operaciones altamente confidenciales sin utilizar medios alternativos para validar la solicitud.

Considere las estrategias siguientes:

* Puede incluir lógica dentro de un runbook para determinar si se le llama desde un webhook. Establezca que el runbook compruebe la propiedad `WebhookName` del parámetro `WebhookData`. El runbook puede realizar más validaciones buscando información determinada en las propiedades `RequestHeader` y `RequestBody`.

* Haga que el runbook realice alguna validación de una condición externa cuando recibe una solicitud de webhook. Por ejemplo, considere un runbook al que llama GitHub siempre que hay una nueva confirmación en un repositorio de GitHub. El runbook puede conectarse a GitHub para asegurarse de que se ha realizado una nueva confirmación antes de continuar.

* Azure Automation admite etiquetas de servicio de red virtual de Azure, específicamente [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). Puede usar etiquetas de servicio para definir controles de acceso a la red en [grupos de seguridad de red](../virtual-network/network-security-groups-overview.md#security-rules) o [Azure Firewall](../firewall/service-tags.md) y desencadenar webhooks desde la red virtual. Las etiquetas de servicio se pueden usar en lugar de direcciones IP específicas al crear reglas de seguridad. Al especificar el nombre de la etiqueta de servicio **GuestAndHybridManagement** en el campo de origen o destino apropiado de una regla, puede permitir o denegar el tráfico para el servicio Automation. Esta etiqueta de servicio no admite un control más pormenorizado mediante la restricción de los intervalos de IP a una región específica.

## <a name="create-a-webhook"></a>Creación de un webhook

Un webhook requiere un runbook publicado. En este recorrido se usa una versión modificada del runbook creada en [Creación de un runbook de Azure Automation](./learn/powershell-runbook-managed-identity.md). Para continuar, edite el runbook de PowerShell con el código siguiente:

```powershell
param
(
    [Parameter(Mandatory=$false)]
    [object] $WebhookData
)

if ($WebhookData.RequestBody) { 
    $names = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        foreach ($x in $names)
        {
            $name = $x.Name
            Write-Output "Hello $name"
        }
}
else {
    Write-Output "Hello World!"
}
```

A continuación, guarde y publique el runbook revisado. En los ejemplos siguientes se muestra cómo crear un webhook mediante Azure Portal, PowerShell y REST.

### <a name="from-the-portal"></a>Desde el portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

1. En Azure Portal, vaya a su cuenta de Automation.

1. En **Automatización de procesos**, seleccione **Runbooks** para abrir la página de **runbooks**.

1. Seleccione el runbook en la lista para abrir la página **Información general** del runbook.

1. Seleccione **Agregar Webhook** para abrir la página **Agregar Webhook**.

   :::image type="content" source="media/automation-webhooks/add-webhook-icon.png" alt-text="Página de información general del runbook con Agregar Webhook resaltado":::.

1. En la página **Agregar Webhook**, seleccione **Crear nuevo Webhook**.

   :::image type="content" source="media/automation-webhooks/add-webhook-page-create.png" alt-text="Página Agregar Webhook con Crear resaltado.":::

1. Escriba el **Nombre** del webhook. La fecha de expiración del campo **Expira** tiene como valor predeterminado un año a partir de la fecha actual.

1. Haga clic en el icono de copiar y presione <kbd>Ctrl+C</kbd> para copiar la dirección URL del webhook. A continuación, guarde la dirección URL en una ubicación segura.

    :::image type="content" source="media/automation-webhooks/create-new-webhook.png" alt-text="Página Crear webhook con la dirección URL resaltada.":::

    > [!IMPORTANT]
    > Una vez creado el webhook, ya no puede volver a recuperar la dirección URL. Asegúrese de copiarlo y grabarlo como se indicó anteriormente.

1. Seleccione **Aceptar** para volver a la página **Agregar Webhook**.

1. En la página **Agregar Webhook**, seleccione **Configurar parámetros y ejecutar configuraciones** para abrir la página **Parámetros**.

   :::image type="content" source="media/automation-webhooks/add-webhook-page-parameters.png" alt-text="Página Agregar Webhook con los parámetros resaltados.":::

1. Revise la página **Parámetros**. Para el runbook de ejemplo que se usa en este artículo, no se necesita ningún cambio. Seleccione **Aceptar** para volver a la página **Agregar Webhook**.

1. En la página **Agregar Webhook**, seleccione **Crear**. Se crea el webhook y se le devuelve a la página **Información general** del runbook.

### <a name="using-powershell"></a>Usar PowerShell

1. Compruebe que dispone de la versión más reciente del [módulo Az](/powershell/azure/new-azureps-module-az) de PowerShell.

1. Inicie sesión en Azure de forma interactiva con el cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) y siga las instrucciones.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    ```

1. Use el cmdlet [New-AzAutomationWebhook](/powershell/module/az.automation/new-azautomationwebhook) para crear un webhook para un runbook de Automation. Proporcione un valor adecuado para las variables y, a continuación, ejecute el script.

    ```powershell
    # Initialize variables with your relevant values
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    $runbook = "runbookName"
    $psWebhook = "webhookName"
    
    # Create webhook
    $newWebhook = New-AzAutomationWebhook `
        -ResourceGroup $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $psWebhook `
        -RunbookName $runbook `
        -IsEnabled $True `
        -ExpiryTime "12/31/2022" `
        -Force
    
    # Store URL in variable; reveal variable
    $uri = $newWebhook.WebhookURI
    $uri
    ```

   La salida será una dirección URL similar a la siguiente: `https://ad7f1818-7ea9-4567-b43a.webhook.wus.azure-automation.net/webhooks?token=uTi69VZ4RCa42zfKHCeHmJa2W9fd`

1. También puede comprobar el webhook con el cmdlet de PowerShell [Get-AzAutomationWebhook](/powershell/module/az.automation/get-azautomationwebhook).

    ```powershell
    Get-AzAutomationWebhook `
        -ResourceGroup $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $psWebhook
    ```

### <a name="using-rest"></a>Uso de REST

El comando PUT se documenta en [Webhook: Creación o actualización](/rest/api/automation/webhook/create-or-update). En este ejemplo se usa el cmdlet de PowerShell [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) para enviar la solicitud PUT.

1. Cree un archivo denominado `webhook.json` y pegue el código siguiente:

    ```json
    {
      "name": "RestWebhook",
      "properties": {
        "isEnabled": true,
        "expiryTime": "2022-03-29T22:18:13.7002872Z",
        "runbook": {
          "name": "runbookName"
        }
      }
    }
    ```

   Antes de ejecutarlo, modifique el valor de la propiedad **runbook:name** con el nombre real del runbook. Revise [Propiedades de un webhook](#webhook-properties) para más información sobre estas propiedades.

1. Compruebe que dispone de la versión más reciente del [módulo Az](/powershell/azure/new-azureps-module-az) de PowerShell.

1. Inicie sesión en Azure de forma interactiva con el cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) y siga las instrucciones.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    ```

1. Proporcione un valor adecuado para las variables y, a continuación, ejecute el script.

    ```powershell
    # Initialize variables
    $subscription = "subscriptionID"
    $resourceGroup = "resourceGroup"
    $automationAccount = "automationAccount"
    $runbook = "runbookName"
    $restWebhook = "webhookName"
    $file = "path\webhook.json"

    # consume file
    $body = Get-Content $file
    
    # Craft Uri
    $restURI = "https://management.azure.com/subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Automation/automationAccounts/$automationAccount/webhooks/$restWebhook`?api-version=2015-10-31"
    ```

1. Ejecute el siguiente script para obtener un token de acceso. Si el token de acceso ha expirado, debe volver a ejecutar el script.

    ```powershell
    # Obtain access token
    $azContext = Get-AzContext
    $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
    $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
    $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
    $authHeader = @{
        'Content-Type'='application/json'
        'Authorization'='Bearer ' + $token.AccessToken
    }
    ```

1. Ejecute el siguiente script para crear el webhook mediante la API de REST.

    ```powershell
    # Invoke the REST API
    # Store URL in variable; reveal variable
    $response = Invoke-RestMethod -Uri $restURI -Method Put -Headers $authHeader -Body $body
    $webhookURI = $response.properties.uri
    $webhookURI
    ```

   La salida es una dirección URL similar a la siguiente: `https://ad7f1818-7ea9-4567-b43a.webhook.wus.azure-automation.net/webhooks?token=uTi69VZ4RCa42zfKHCeHmJa2W9fd`

1. También puede usar [Webhook: Obtención](/rest/api/automation/webhook/get) para recuperar el webhook identificado por su nombre. Puede ejecutar los siguientes comandos de PowerShell:

    ```powershell
    $response = Invoke-RestMethod -Uri $restURI -Method GET -Headers $authHeader
    $response | ConvertTo-Json
    ```

## <a name="use-a-webhook"></a>Uso de webhooks

En este ejemplo se usa el cmdlet de PowerShell [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) para enviar una solicitud PUT al nuevo webhook.

1. Prepare los valores para pasar al runbook como cuerpo de la llamada de webhook. En el caso de los valores relativamente simples, puede incluir los valores en un script, como se muestra a continuación:

    ```powershell
    $Names  = @(
                @{ Name="Hawaii"},
                @{ Name="Seattle"},
                @{ Name="Florida"}
            )
    
    $body = ConvertTo-Json -InputObject $Names
    ```

1. En el caso de conjuntos más grandes, puede ser aconsejable usar un archivo. Cree un archivo denominado `names.json` y pegue el código siguiente:

    ```json
    [
        { "Name": "Hawaii" },
        { "Name": "Florida" },
        { "Name": "Seattle" }
    ]
    ```

    Cambie el valor de la variable `$file` por la ruta de acceso real al archivo JSON antes de ejecutar los siguientes comandos de PowerShell.

    ```powershell
    # Revise file path with actual path
    $file = "path\names.json"
    $bodyFile = Get-Content -Path $file 
    ```

1. Ejecute los siguientes comandos de PowerShell para llamar al webhook mediante la API de REST.

    ```powershell
    $response = Invoke-WebRequest -Method Post -Uri $webhookURI -Body $body -UseBasicParsing
    $response
    
    $responseFile = Invoke-WebRequest -Method Post -Uri $webhookURI -Body $bodyFile -UseBasicParsing
    $responseFile
    ```

   Con fines ilustrativos, se realizaron dos llamadas para los dos métodos diferentes de generar el cuerpo. Para producción, use solo un método.  La salida debe tener un aspecto similar al siguiente (solo se muestra una salida):

   :::image type="content" source="media/automation-webhooks/webhook-post-output.png" alt-text="Salida de la llamada de webhook.":::

    El cliente recibe uno de los siguientes códigos de retorno de la solicitud `POST`.

    | Código | Texto | Descripción |
    |:--- |:--- |:--- |
    | 202 |Accepted |La solicitud se aceptó y el runbook se puso en cola correctamente. |
    | 400 |Bad Request |No se aceptó la solicitud por uno de los siguientes motivos: <ul> <li>El webhook ha expirado.</li> <li>El webhook está deshabilitado.</li> <li>El token de la dirección URL no es válido.</li>  </ul> |
    | 404 |No encontrado |No se aceptó la solicitud por uno de los siguientes motivos: <ul> <li>No se encontró el webhook.</li> <li>No se encontró el runbook.</li> <li>No se encontró la cuenta.</li>  </ul> |
    | 500 |Internal Server Error |La dirección URL es válida, pero se produjo un error. Vuelva a enviar la solicitud. |

    Si la solicitud es correcta, la respuesta del webhook contiene el identificador de trabajo en formato JSON, como se muestra a continuación. Contiene un solo identificador de trabajo, pero el formato JSON permite realizar potenciales mejoras en el futuro.

    ```json
    {"JobIds":["<JobId>"]}
    ```

1. El cmdlet de PowerShell [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) se usará para obtener la salida. También se puede usar la [API de Azure Automation](/rest/api/automation/job).

    ```powershell
    #isolate job ID
    $jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
    
    # Get output
    Get-AzAutomationJobOutput `
        -AutomationAccountName $automationAccount `
        -Id $jobid `
        -ResourceGroupName $resourceGroup `
        -Stream Output
    ```

   La salida debe tener una apariencia similar a la siguiente:

   :::image type="content" source="media/automation-webhooks/webhook-job-output.png" alt-text="Salida del trabajo de webhook.":::

## <a name="update-a-webhook"></a>Actualización de un webhook

Cuando se crea un webhook, tiene un período de validez de 10 años, tras el cual expira automáticamente. Una vez que un webhook ha expirado, no se puede reactivar. Solo se puede quitar y volver a crear. Puede extender un webhook que no haya alcanzado su fecha de expiración. Para extender un webhook, realice los pasos siguientes.

1. Vaya al runbook que contiene el webhook.
1. En **Recursos**, seleccione **Webhooks** y, a continuación, el webhook que quiera extender.
1. En la página **Webhook**, elija una nueva fecha y hora de expiración y haga clic en **Guardar**.

Revise la llamada API [Webhook: Actualización](/rest/api/automation/webhook/update) y el cmdlet de PowerShell [Set-AzAutomationWebhook](/powershell/module/az.automation/set-azautomationwebhook) para ver otras modificaciones posibles.

## <a name="clean-up-resources"></a>Limpieza de recursos

Estos son ejemplos de cómo quitar un webhook de un runbook de Automation.

- Con PowerShell, se puede usar el cmdlet [Remove-AzAutomationWebhook](/powershell/module/az.automation/remove-azautomationwebhook) como se muestra a continuación. No se devuelve salida.

    ```powershell
    Remove-AzAutomationWebhook `
        -ResourceGroup $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $psWebhook
    ```

- Con REST, se puede usar la API de REST [Webhook: Eliminación](/rest/api/automation/webhook/delete) como se muestra a continuación.

    ```powershell
    Invoke-WebRequest -Method Delete -Uri $restURI -Headers $authHeader
    ```

   Una salida de `StatusCode        : 200` significa una eliminación correcta.

## <a name="create-runbook-and-webhook-with-arm-template"></a>Creación de un runbook y un webhook con una plantilla de ARM

También se pueden crear los webhooks de Automation de forma automática por medio de las plantillas de [Azure Resource Manager](../azure-resource-manager/templates/overview.md). Esta plantilla de ejemplo crea una cuenta de Automation, cuatro runbooks y un webhook para el runbook con nombre.

1. Cree un archivo denominado `webhook_deploy.json` y pegue el código siguiente:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "automationAccountName": {
                "type": "String",
                "metadata": {
                    "description": "Automation account name"
                }
            },
            "webhookName": {
                "type": "String",
                "metadata": {
                    "description": "Webhook Name"
                }
            },
            "runbookName": {
                "type": "String",
                "metadata": {
                    "description": "Runbook Name for which webhook will be created"
                }
            },
            "WebhookExpiryTime": {
                "type": "String",
                "metadata": {
                    "description": "Webhook Expiry time"
                }
            },
            "_artifactsLocation": {
                "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/",
                "type": "String",
                "metadata": {
                    "description": "URI to artifacts location"
                }
            }
        },
        "resources": [
            {
                "type": "Microsoft.Automation/automationAccounts",
                "apiVersion": "2020-01-13-preview",
                "name": "[parameters('automationAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "sku": {
                        "name": "Free"
                    }
                },
                "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('runbookName')]",
                        "location": "[resourceGroup().location]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "Sample Runbook",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), 'scripts/AzureAutomationTutorialPython2.py')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "webhooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('webhookName')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]",
                            "[parameters('runbookName')]"
                        ],
                        "properties": {
                            "isEnabled": true,
                            "expiryTime": "[parameters('WebhookExpiryTime')]",
                            "runbook": {
                                "name": "[parameters('runbookName')]"
                            }
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "webhookUri": {
                "type": "String",
                "value": "[reference(parameters('webhookName')).uri]"
            }
        }
    }
    ```

1. En el siguiente ejemplo de código de PowerShell se implementa la plantilla desde el equipo. Proporcione un valor adecuado para las variables y, a continuación, ejecute el script.

    ```powershell
    $resourceGroup = "resourceGroup"
    $templateFile = "path\webhook_deploy.json"
    $armAutomationAccount = "automationAccount"
    $armRunbook = "ARMrunbookName"
    $armWebhook = "webhookName"
    $webhookExpiryTime = "12-31-2022"
    
    New-AzResourceGroupDeployment `
        -Name "testDeployment" `
        -ResourceGroupName $resourceGroup `
        -TemplateFile $templateFile `
        -automationAccountName $armAutomationAccount `
        -runbookName $armRunbook `
        -webhookName $armWebhook `
        -WebhookExpiryTime $webhookExpiryTime
    ```

   > [!NOTE]
   > Por motivos de seguridad, el URI solo se devuelve la primera vez que se implementa una plantilla.

## <a name="next-steps"></a>Pasos siguientes

* Para desencadenar un runbook a partir de una alerta, consulte [Uso de una alerta para desencadenar un runbook de Azure Automation](automation-create-alert-triggered-runbook.md).