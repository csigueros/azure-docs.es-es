---
title: Etiquetas de imagen y notas de la versión de Form Recognizer
titleSuffix: Azure Applied AI Services
description: Una lista de todas las etiquetas de imagen de contenedor de Form Recognizer.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: reference
ms.date: 09/02/2021
ms.author: lajanuar
keywords: Docker, contenedor, imágenes
ms.openlocfilehash: d38fd74324246463e9f74ff8d53c67ccb2ccd039
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481573"
---
# <a name="form-recognizer-container-image-tags-and-release-notes"></a>Etiquetas de imagen de contenedor y notas de la versión de Form Recognizer

> [!IMPORTANT]
>
> * **Los contenedores de Form Recognizer v2.1** están en versión preliminar validada. Para usarlos, debe enviar una [solicitud en línea](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu) y recibir la aprobación. 
>
> * El formulario de solicitud en línea requiere que proporcione una dirección de correo electrónico válida que pertenezca a la organización propietaria del identificador de suscripción de Azure y que tenga o se le haya concedido acceso a esa suscripción.

## <a name="feature-containers"></a>Contenedores de características

Las características de Form Recognizer se admiten en siete contenedores:

| Nombre del contenedor | Nombre completo de la imagen |
|---|---|
| **Diseño** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout |
| **Tarjeta de presentación** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/businesscard |
| **Documento de identificación** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/id-document |
| **Recibo** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/receipt |
| **Factura** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/invoice |
| **API personalizada** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-api |
| **Supervisado personalizado** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-supervised |

## <a name="microsoft-container-registry"></a>Registro de contenedor de Microsoft

Las imágenes de contenedor de Form Recognizer se pueden encontrar en Microsoft Container Registry **mcr.microsoft.** **<span></span>com** container registry syndicate, el registro principal de todas las imágenes de Docker publicadas por Microsoft.

* La experiencia de detección de MCR se proporciona por medio de [docker hub](https://hub.docker.com/publishers/microsoftowner).

* También puede consultar [la lista de repositorios dentro de mcr](https://mcr.microsoft.com/v2/_catalog).

## <a name="form-recognizer-tags"></a>Etiquetas de Form Recognizer

Las siguientes etiquetas están disponibles para Form Recognizer:

### <a name="latest-version"></a>[La versión más reciente](#tab/current)

Notas de la versión de `v2.1` (versión preliminar validada):

| Contenedor | Etiquetas |
|------------|:------|
| **Diseño**| &bullet; `latest` </br> &bullet; `2.1-preview` </br> &bullet; `2.1.0.016140001-08108749-amd64-preview`|
| **Tarjeta de presentación** | &bullet; `latest` </br> &bullet; `2.1-preview` </br> &bullet; `2.1.016190001-amd64-preview`  </br> &bullet; `2.1.016320001-amd64-preview`  |
| **Documento de identificación** | &bullet; `latest` </br> &bullet; `2.1-preview`</br>&bullet; `2.1.016190001-amd64-preview`</br>&bullet; `2.1.016320001-amd64-preview` |
| **Recibo**| &bullet; `latest` </br> &bullet; `2.1-preview`</br>&bullet; `2.1.016190001-amd64-preview`</br>&bullet; `2.1.016320001-amd64-preview` |
| **Factura**| &bullet; `latest` </br> &bullet; `2.1-preview`</br>&bullet; `2.1.016190001-amd64-preview`</br>&bullet; `2.1.016320001-amd64-preview` |
| **API personalizada** | &bullet; `latest` </br> &bullet;`2.1-distroless-20210622013115034-0cc5fcf6`</br>&bullet; `2.1-preview`|
| **Supervisado personalizado**| &bullet; `latest` </br> &bullet; `2.1-distroless-20210622013149174-0cc5fcf6`</br>&bullet; `2.1-preview`|

### <a name="previous-versions"></a>[Versiones anteriores](#tab/previous)

> [!IMPORTANT]
> El contenedor de Form Recognizer v1.0 se ha retirado.

---

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Instalación y ejecución de contenedores de Form Recognizer](form-recognizer-container-install-run.md)
>