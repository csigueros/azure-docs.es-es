---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 05/19/2021
ms.topic: include
ms.openlocfilehash: 103a23a478a3629eb913c3d1672b3665bc9cba9c
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113559384"
---
## <a name="availability"></a>Disponibilidad

|Aspecto|Detalles|
|----|:----|
|Estado de la versión:|Disponible con carácter general|
|Precios:|**Azure Defender para registros de contenedor** se factura como se muestra en la [página de precios](../articles/security-center/security-center-pricing.md).|
|Registros e imágenes compatibles:|Imágenes de Linux en registros de ACR accesibles desde la red pública de Internet con acceso al shell|
|Registros e imágenes no compatibles:|Imágenes de Windows<br>Registros "privados"<br>Imágenes excesivamente minimalistas, como las imágenes [base de Docker](https://hub.docker.com/_/scratch/) o imágenes "sin distribución" que solo contienen una aplicación y sus dependencias en tiempo de ejecución sin un administrador de paquetes, shell o sistema operativo.<br>Imágenes con la [especificación del formato de imagen Open Container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md)|
|Roles y permisos necesarios:|**Lector de seguridad** y [roles y permisos de Azure Container Registry](../articles/container-registry/container-registry-roles.md)|
|Nubes:|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: Nubes comerciales<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: US Gov y China Gov: actualmente solo se admite el examen en la característica de inserción. Más información en [¿Cuándo se examinan las imágenes?](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)|
|||
