---
title: Acceso a las API de Azure Healthcare con cURL
description: En este artículo se explica cómo acceder a las API de atención sanitaria con cURL
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 07/19/2021
ms.author: ginle
ms.openlocfilehash: ddc7b291ba6ecd86e4915523345c40054794a993
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787496"
---
# <a name="access-the-healthcare-apis-preview-with-curl"></a>Acceso a las API de atención sanitaria (versión preliminar) con cURL 

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo, aprenderá a acceder a las API de Azure Healthcare con cURL.

## <a name="prerequisites"></a>Requisitos previos

### <a name="powershell"></a>PowerShell

* Una cuenta de Azure con una suscripción activa. [cree una de forma gratuita](https://azure.microsoft.com/free/).
* Si desea ejecutar el código localmente, instale [PowerShell](/powershell/module/powershellget/) y [Azure Az PowerShell.](/powershell/azure/install-az-ps)
* Opcionalmente, puede ejecutar los scripts en Visual Studio Code con la extensión de cliente rest. Para obtener más información, vea [Crear un vínculo al documento del cliente rest](using-rest-client.md).
* Descargue e instale [cURL](https://curl.se/download.html).

### <a name="cli"></a>CLI

* Una cuenta de Azure con una suscripción activa. [cree una de forma gratuita](https://azure.microsoft.com/free/).
* Si desea ejecutar el código localmente, instale [CLI de Azure](/cli/azure/install-azure-cli). 
* Opcionalmente, instale un shell de Bash, como Git Bash, que se incluye en [Git para Windows](https://gitforwindows.org/).
* Opcionalmente, ejecute los scripts en Visual Studio Code con la extensión de cliente rest. Para obtener más información, vea [Crear un vínculo al documento del cliente rest](using-rest-client.md).
* Descargue e instale [cURL](https://curl.se/download.html).

## <a name="obtain-azure-access-token"></a>Obtención del token de acceso de Azure

Antes de acceder a las API de atención sanitaria, debe conceder al usuario o a la aplicación cliente los permisos adecuados. Para obtener más información sobre cómo conceder permisos, vea [Autorización de las API de atención sanitaria.](authentication-authorization.md)

Hay varias maneras diferentes de obtener un token de acceso de Azure para las API de atención sanitaria. 

> [!NOTE]
> Asegúrese de que ha iniciado sesión en Azure y de que se encuentra en la suscripción e inquilino de Azure donde ha implementado la instancia de Healthcare API.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell-interactive
### check Azure environment and PowerShell versions
Get-AzContext 
Set-AzContext -Subscription <subscriptionid>
$PSVersionTable.PSVersion
Get-InstalledModule -Name Az -AllVersions
curl --version

### get access token for the FHIR service
$fhirservice="https://<fhirservice>.fhir.azurehealthcareapis.com"
$token=(Get-AzAccessToken -ResourceUrl $fhirservice).Token

### Get access token for the DICOM service
$dicomtokenurl= "https://dicom.healthcareapis.azure.com/"
$token=$( Get-AzAccessToken -ResourceUrl $dicomtokenurl).Token
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
### check Azure environment and CLI versions
az account show --output table
az account set --subscription <subscriptionid>
cli –version
curl --version

### get access token for the FHIR service
$fhirservice=https://<fhirservice>.fhir.azurehealthcareapis.com
token=$(az account get-access-token --resource=$fhirservice --query accessToken --output tsv)

### get access token for the DICOM service
dicomtokenurl= https://dicom.healthcareapis.azure.com/
token=$(az account get-access-token --resource=$dicomtokenurl --query accessToken --output tsv)
```

---

## <a name="access-data-in-the-fhir-service"></a>Acceso a datos en el servicio FHIR

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell-interactive
$fhirservice="https://<fhirservice>.fhir.azurehealthcareapis.com"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
fhirservice="https://<fhirservice>.fhir.azurehealthcareapis.com"
```

---

`curl -X GET --header "Authorization: Bearer $token" $fhirservice/Patient`

[![Acceso a datos en el servicio FHIR con script curl. ](media/curl-fhir.png) ](media/curl-fhir.png#lightbox)

## <a name="access-data-in-the-dicom-service"></a>Acceso a datos en el servicio DICOM

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell-interactive
$dicomservice="https://<dicomservice>.dicom.azurehealthcareapis.com"
```
# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
dicomservice="https://<dicomservice>.dicom.azurehealthcareapis.com"
```
---

`curl -X GET --header "Authorization: Bearer $token" $dicomservice/changefeed?includemetadata=false`

[![Acceso a datos en el servicio DICOM con script curl. ](media/curl-dicom.png) ](media/curl-dicom.png#lightbox)

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a acceder a los datos de las API de atención sanitaria mediante cURL.

Para obtener información sobre cómo acceder a los datos de las API de atención sanitaria mediante la extensión de cliente REST Visual Studio Code, consulte 

>[!div class="nextstepaction"]
>[Acceso a las API de atención sanitaria mediante el cliente REST](using-rest-client.md)