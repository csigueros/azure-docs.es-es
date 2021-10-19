---
title: 'Obtener el token de acceso mediante CLI de Azure o Azure PowerShell: servicio FHIR'
description: En este artículo se explica cómo obtener un token de acceso para el servicio FHIR mediante CLI de Azure o Azure PowerShell.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: zxue
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 672089ac061430121916efff67280c08c1c6943f
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619457"
---
# <a name="get-access-token-for-fhir-service-using-azure-cli-or-azure-powershell"></a>Obtenga el token de acceso para el servicio FHIR mediante CLI de Azure o Azure PowerShell

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

En este artículo, obtendrá información sobre cómo obtener un token de acceso para el servicio FHIR en las API de Azure Healthcare (aquí denominado servicio FHIR) mediante el CLI de Azure. Al [aprovisionar el servicio FHIR,](fhir-portal-quickstart.md)se configura un conjunto de usuarios o entidades de servicio que tienen acceso al servicio. Si el identificador del objeto de usuario está en la lista de identificadores de objeto permitidos, puede acceder al servicio mediante un token obtenido con la CLI de Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

En este artículo, obtendrá información sobre cómo obtener un token de acceso para el servicio FHIR en las API de Azure Healthcare (que aquí se denomina servicio FHIR) mediante Azure PowerShell. Al [aprovisionar el servicio FHIR,](fhir-portal-quickstart.md)se configura un conjunto de usuarios o entidades de servicio que tienen acceso al servicio. Si el identificador de objeto de usuario está en la lista de identificadores de objeto permitidos, puede acceder al servicio mediante un token obtenido mediante Azure PowerShell.

[!INCLUDE [azure-powershell-requirements.md](../../../includes/azure-powershell-requirements.md)]

---
## <a name="obtain-a-token"></a>Obtención de un token

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

## <a name="use-with-fhir-service"></a>Uso con el servicio FHIR

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$headers = @{Authorization="Bearer $token"}
Invoke-WebRequest -Method GET -Headers $headers -Uri 'https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient'
```

---

>[!div class="nextstepaction"]
>[Acceso a la API de FHIR con Postman](../use-postman.md)
