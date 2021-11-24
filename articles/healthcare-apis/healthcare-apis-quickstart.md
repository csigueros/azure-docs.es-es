---
title: 'Implementación del área de trabajo en Azure Portal: API de Azure Healthcare'
description: En este documento se enseña a los usuarios a implementar un área de trabajo en Azure Portal.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 07/12/2021
ms.author: ginle
ms.custom: mode-portal
ms.openlocfilehash: 89be6d8bab9ef544ffa62a85f0b87afb15fab013
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/24/2021
ms.locfileid: "133061571"
---
# <a name="deploy-healthcare-apis-preview-workspace-using-azure-portal"></a>Implementación del área de trabajo de las API de atención sanitaria (versión preliminar) mediante Azure Portal

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo, aprenderá a crear un área de trabajo mediante la implementación de las API de Azure Healthcare a través de Azure Portal. El área de trabajo es un contenedor lógico centralizado para todos los servicios de API de atención sanitaria, como servicios de FHIR, servicios DICOM® y conectores de IoT. Permite organizar y administrar determinadas opciones de configuración que se comparten entre todos los conjuntos de datos y servicios subyacentes, si procede.


## <a name="prerequisite"></a>Requisito previo

Para poder crear un área de trabajo en el Azure Portal, debe tener una suscripción de cuenta. Si no tiene una suscripción a Azure, consulte Creación [de una cuenta gratuita de Azure hoy mismo.](https://azure.microsoft.com/free/search/?OCID=AID2100131_SEM_c4b0772dc7df1f075552174a854fd4bc:G:s&ef_id=c4b0772dc7df1f075552174a854fd4bc:G:s&msclkid=c4b0772dc7df1f075552174a854fd4bc)

## <a name="create-new-azure-service"></a>Creación de un servicio de Azure

En Azure Portal, haga clic en **Crear un recurso**.

[![Creación de un recurso ](media/create-resource.png) ](media/create-resource.png#lightbox)

## <a name="search-for-azure-healthcare-apis"></a>Búsqueda de API de Azure Healthcare

En el cuadro de búsqueda, escriba **Api de Azure Healthcare.**

[![Búsqueda de API de atención sanitaria ](media/search-for-healthcare-apis.png) ](media/search-for-healthcare-apis.png#lightbox)

## <a name="create-azure-healthcare-api-account"></a>Creación de una cuenta de Azure Healthcare API

Seleccione **Crear** para crear una nueva cuenta de Azure Healthcare API.

   [![Creación de la versión preliminar del área de trabajo ](media/create-workspace-preview.png) ](media/create-workspace-preview.png#lightbox)

## <a name="enter-subscription-and-instance-details"></a>Especificación de los detalles de la suscripción y la instancia

1. Seleccione una **suscripción y** un grupo **de recursos** en las listas desplegables o seleccione **Crear nuevo.**

   [![Creación de un área de trabajo nueva ](media/create-healthcare-api-workspace-new.png) ](media/create-healthcare-api-workspace-new.png#lightbox)

2. Escriba un **nombre para** el área de trabajo y, a continuación, seleccione **una región**. El nombre debe tener entre 3 y 24 caracteres alfanuméricos, todo en minúsculas. No use un guion "-" porque es un carácter no válido para el nombre. Para obtener información sobre regiones y zonas de disponibilidad, consulte [Regiones y Availability Zones en Azure](../availability-zones/az-overview.md).

3. (**Opcional)** Seleccione **Siguiente: Etiquetas >**. Escriba un **nombre** y **un valor** y, a continuación, **seleccione Siguiente: Revisar y crear**. 

   [![Etiquetas ](media/tags-new.png) ](media/tags-new.png#lightbox)

   Las etiquetas son pares nombre-valor que se usan para categorizar los recursos. Para más información sobre las etiquetas, consulte [Uso de etiquetas para organizar los recursos de Azure y la jerarquía de administración.](.././azure-resource-manager/management/tag-resources.md)

4. Seleccione **Crear**.

[![Términos del área de trabajo ](media/workspace-terms.png) ](media/workspace-terms.png)


   **Opcional:** puede seleccionar Descargar **una plantilla para automatizar el** área de trabajo recién creada.


## <a name="next-steps"></a>Pasos siguientes

Ahora que se ha creado el área de trabajo, puede hacer lo siguiente:

* Implementación de servicios de FHIR
* Implementación de servicios DICOM
* Implemente un IoT Connector e ingiera datos en el servicio FHIR.
* Transforme los datos en diferentes formatos y uso secundario a través de nuestras API de conversión y des-identificación.


[![Implementación de diferentes servicios ](media/healthcare-apis-deploy-services.png) ](media/healthcare-apis-deploy-services.png)

>[!div class="nextstepaction"]
>[¿Qué es un área de trabajo de Azure Machine Learning?](workspace-overview.md)
