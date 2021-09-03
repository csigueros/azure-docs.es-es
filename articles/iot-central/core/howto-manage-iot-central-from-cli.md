---
title: Administración de IoT Central desde la CLI de Azure o PowerShell | Microsoft Docs
description: En este artículo se describe cómo crear y administrar aplicaciones de IoT Central con la CLI de Azure o PowerShell. Puede ver, modificar y quitar la aplicación con estas herramientas.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.custom:
- devx-track-azurecli
- devx-track-azurepowershell
ms.openlocfilehash: b2ba640fec6f9bf2cd09611456967e15ed8f7ade
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732892"
---
# <a name="manage-iot-central-from-azure-cli-or-powershell"></a>Administración de IoT Central con la CLI de Azure o PowerShell

En lugar de crear y administrar aplicaciones de IoT Central desde el sitio web del [administrador de aplicaciones de Azure IoT Central](https://aka.ms/iotcentral), puede usar la [CLI de Azure](/cli/azure/) o [Azure PowerShell](/powershell/azure/) para administrar las aplicaciones.

Si prefiere usar un lenguaje como JavaScript, Python, C#, Ruby o Go, consulte el repositorio de [ejemplos del SDK de ARM de Azure IoT Central](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/) para obtener ejemplos de código que muestran cómo crear, actualizar, enumerar y eliminar aplicaciones de Azure IoT Central.

## <a name="prerequisites"></a>Requisitos previos

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [azure-powershell-requirements-no-header](../../../includes/azure-powershell-requirements-no-header.md)]

> [!TIP]
> Si tiene que ejecutar los comandos de PowerShell en otra suscripción a Azure, consulte [Cambio de la suscripción activa](/powershell/azure/manage-subscriptions-azureps#change-the-active-subscription).

Ejecute el siguiente comando para comprobar si el [módulo de IoT Central](/powershell/module/az.iotcentral/) está instalado en el entorno de PowerShell:

```powershell
Get-InstalledModule -name Az.I*
```

Si la lista de módulos instalados no incluye **Az.IotCentral**, ejecute el siguiente comando:

```powershell
Install-Module Az.IotCentral
```

---

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

## <a name="create-an-application"></a>Crear una aplicación

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Use el comando [az iot central app create](/cli/azure/iot/central/app#az_iot_central_app_create) para crear una aplicación de IoT Central en su suscripción de Azure. Por ejemplo:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

Estos comandos crean en primer lugar un grupo de recursos en la región Este de EE. UU. para la aplicación. En la siguiente tabla se describen los parámetros utilizados con el comando **az iot central app create**:

| Parámetro         | Descripción |
| ----------------- | ----------- |
| resource-group    | Grupo de recursos que contiene a la aplicación. Este grupo de recursos ya debe existir en la suscripción. |
| ubicación          | Este comando usa de forma predeterminada la ubicación del grupo de recursos. Actualmente, se puede crear una aplicación de IoT Central en las zonas geográficas **Australia**, **Asia Pacífico**, **Europa**, **Estados Unidos**, **Reino Unido** y **Japón**. |
| name              | Nombre de la aplicación en Azure Portal. Evite usar caracteres especiales: en su lugar, use letras minúsculas (a-z), números (0-9) y guiones (-).|
| subdomain         | Subdominio en la dirección URL de la aplicación. En el ejemplo, la dirección URL de la aplicación es `https://mysubdomain.azureiotcentral.com`. |
| sku               | Actualmente, puede usar **ST1** o **ST2**. Consulte [Precios de Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| template          | Plantilla de aplicación que se va a usar. Para más información, vea la tabla siguiente. |
| display-name      | Nombre de la aplicación tal como se muestra en la interfaz de usuario. |

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Use el cmdlet [New-AzIotCentralApp](/powershell/module/az.iotcentral/New-AzIotCentralApp) para crear una aplicación de IoT Central en su suscripción de Azure. Por ejemplo:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview" `
  -DisplayName "My Custom Display Name"
```

El script crea primero un grupo de recursos en la región Este de EE. UU. para la aplicación. En la tabla siguiente se describen los parámetros utilizados con el comando **New-AzIotCentralApp**:

|Parámetro         |Descripción |
|------------------|------------|
|ResourceGroupName |Grupo de recursos que contiene a la aplicación. Este grupo de recursos ya debe existir en la suscripción. |
|Ubicación |De forma predeterminada, este cmdlet usa la ubicación del grupo de recursos. Actualmente, se puede crear una aplicación de IoT Central en las zonas geográficas **Australia**, **Asia Pacífico**, **Europa**, **Estados Unidos**, **Reino Unido** y **Japón**. |
|Nombre              |Nombre de la aplicación en Azure Portal. Evite usar caracteres especiales: en su lugar, use letras minúsculas (a-z), números (0-9) y guiones (-). |
|Subdominio         |Subdominio en la dirección URL de la aplicación. En el ejemplo, la dirección URL de la aplicación es `https://mysubdomain.azureiotcentral.com`. |
|SKU               |Actualmente, puede usar **ST1** o **ST2**. Consulte [Precios de Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Plantilla          | Plantilla de aplicación que se va a usar. Para más información, vea la tabla siguiente. |
|DisplayName       |Nombre de la aplicación tal como se muestra en la interfaz de usuario. |

---

### <a name="application-templates"></a>Plantillas de aplicación

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

Si ha creado su propia plantilla de aplicación, puede usarla para crear una aplicación. Cuando se le pida una plantilla de aplicación, escriba el identificador de la aplicación que se muestra en el vínculo compartible de la dirección URL de la aplicación exportada en la sección [Exportación de plantillas de la aplicación](howto-create-iot-central-application.md#create-and-use-a-custom-application-template).

## <a name="view-applications"></a>Ver las aplicaciones

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Use el comando [az iot central app list](/cli/azure/iot/central/app#az_iot_central_app_list) para enumerar las aplicaciones de IoT Central y ver los metadatos.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Use el cmdlet [Get-AzIotCentralApp](/powershell/module/az.iotcentral/Get-AzIotCentralApp) para enumerar las aplicaciones de IoT Central y ver los metadatos.

---

## <a name="modify-an-application"></a>Modificación de una aplicación

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Use el comando [az iot central app update](/cli/azure/iot/central/app#az_iot_central_app_update) para actualizar los metadatos de una aplicación de IoT Central. Por ejemplo, para cambiar el nombre para mostrar de la aplicación, use:

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Use el cmdlet [Set-AzIotCentralApp](/powershell/module/az.iotcentral/set-aziotcentralapp) para actualizar los metadatos de una aplicación de IoT Central. Por ejemplo, para cambiar el nombre para mostrar de la aplicación, use:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

---

## <a name="delete-an-application"></a>Eliminar una aplicación

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Use el comando [az iot central app delete](/cli/azure/iot/central/app#az_iot_central_app_delete) para eliminar una aplicación de IoT Central. Por ejemplo:

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Use el cmdlet [Remove-AzIotCentralApp](/powershell/module/az.iotcentral/Remove-AzIotCentralApp) para eliminar una aplicación de IoT Central. Por ejemplo:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

---

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a administrar aplicaciones de Azure IoT Central desde la CLI de Azure o PowerShell, le sugerimos el paso siguiente:

> [!div class="nextstepaction"]
> [Administrar su aplicación](howto-administer.md)
