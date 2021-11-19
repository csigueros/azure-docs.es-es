---
title: Obtener el token de acceso mediante CLI de Azure o Azure PowerShell
description: En este artículo se explica cómo obtener un token de acceso para las API de atención sanitaria mediante CLI de Azure o Azure PowerShell.
services: healthcare-apis
author: SteveWohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/17/2021
ms.author: zxue
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d350669abca87156cc5e735151f4a905d98745a8
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814781"
---
# <a name="get-access-token-using-azure-cli-or-azure-powershell"></a>Obtener el token de acceso mediante CLI de Azure o Azure PowerShell

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo, aprenderá a obtener un token de acceso para el servicio FHIR y el servicio DICOM mediante PowerShell y la CLI de Azure. Para más información sobre cómo empezar a trabajar con las API de atención sanitaria, consulte Introducción a [FHIR]() o Introducción [a DICOM.]() 

---
## <a name="obtain-a-token-for-the-fhir-service"></a>Obtención de un token para el servicio FHIR

El servicio FHIR usa o con un URI igual al `resource`  URI del servidor de `Audience` FHIR `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` . Puede obtener un token y almacenarlo en una variable (llamada `$token`) con el siguiente comando:

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$token = (Get-AzAccessToken -ResourceUrl 'https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com').Token
```

---

## <a name="use-the-token-with-the-fhir-service"></a>Uso del token con el servicio FHIR

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$headers = @{Authorization="Bearer $token"}
Invoke-WebRequest -Method GET -Headers $headers -Uri 'https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient'
```

## <a name="obtain-a-token-for-the-dicom-service"></a>Obtención de un token para el servicio DICOM

El servicio DICOM usa `resource` o `Audience` con un URI equivalente al del servidor de DICOM `https://dicom.healthcareapis.azure.com`. Puede obtener un token y almacenarlo en una variable (llamada `$token`) con el siguiente comando:


```Azure CLICopy
$token=$(az account get-access-token --resource=https://dicom.healthcareapis.azure.com --query accessToken --output tsv)
```

## <a name="use-the-tokenb-with-the-dicom-service"></a>Uso del tokenb con el servicio DICOM

```Azure CLICopy
curl -X GET --header "Authorization: Bearer $token"  https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com/v<version of REST API>/changefeed
```

## <a name="next-steps"></a>Pasos siguientes

- [Acceso a FHIR mediante Postman](use-postman.md)
- [Acceso a FHIR sing Rest Client](using-rest-client.md)
- [Acceso a DICOM mediante cUrl](dicom/dicomweb-standard-apis-curl.md)

