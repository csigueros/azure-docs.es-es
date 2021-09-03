---
title: 'Obtención del token de acceso mediante la CLI de Azure: Azure Healthcare APIs para el servicio DICOM'
description: En este artículo se explica cómo obtener un token de acceso para el servicio DICOM mediante la CLI de Azure.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: b48a36a5f9549fef2d484dd79b938c1a8e833e8f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780519"
---
# <a name="get-access-token-for-the-dicom-service-using-azure-cli"></a>Obtención del token de acceso para el servicio DICOM mediante la CLI de Azure

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en versión preliminar. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo, aprenderá a obtener un token de acceso para el servicio DICOM mediante la CLI de Azure. Al [implementar el servicio DICOM](deploy-dicom-services-in-azure.md), se configura un conjunto de usuarios o entidades de servicio que tienen acceso al servicio. Si el identificador del objeto de usuario está en la lista de identificadores de objeto permitidos, puede acceder al servicio mediante un token obtenido con la CLI de Azure.

## <a name="prerequisites"></a>Requisitos previos

Use el entorno de Bash en Azure Cloud Shell.


[ ![Inicio de Azure Cloud Shell.](media/launch-cloud-shell.png) ](media/launch-cloud-shell.png#lightbox)

Si lo prefiere, [instale](/cli/azure/install-azure-cli) la CLI de Azure para ejecutar sus comandos de referencia.

* Si usa una instalación local, inicie sesión en la CLI de Azure mediante el comando [az login](/cli/azure/reference-index#az_login). Siga los pasos que se muestran en el terminal para completar el proceso de autenticación. Para ver otras opciones de inicio de sesión, consulte [Inicio de sesión con la CLI de Azure](/cli/azure/authenticate-azure-cli).
* Cuando se le solicite, instale las extensiones de la CLI de Azure la primera vez que la use. Para más información sobre las extensiones, consulte [Uso de extensiones con la CLI de Azure](/cli/azure/azure-cli-extensions-overview).
* Ejecute [az version](/cli/azure/reference-index#az_version) para buscar cuál es la versión y las bibliotecas dependientes que están instaladas. Para realizar la actualización a la versión más reciente, ejecute [az upgrade](/cli/azure/reference-index#az_upgrade).

## <a name="obtain-a-token"></a>Obtención de un token

El servicio DICOM usa `resource` o `Audience` con un URI equivalente al del servidor de DICOM `https://dicom.healthcareapis.azure.com`. Puede obtener un token y almacenarlo en una variable (llamada `$token`) con el siguiente comando:


```Azure CLICopy
Try It
$token=$(az account get-access-token --resource=https://dicom.healthcareapis.azure.com --query accessToken --output tsv)
```

## <a name="use-with-the-dicom-service"></a>Uso con el servicio DICOM

```Azure CLICopy
Try It
curl -X GET --header "Authorization: Bearer $token"  https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com/v<version of REST API>/changefeed
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a obtener un token de acceso para el servicio DICOM mediante la CLI de Azure. 

>[!div class="nextstepaction"]
>[Información general del servicio DICOM](dicom-services-overview.md)