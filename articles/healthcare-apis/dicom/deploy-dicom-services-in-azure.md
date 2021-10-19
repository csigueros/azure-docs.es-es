---
title: 'Implementación del servicio DICOM mediante Azure Portal: API de Azure Healthcare'
description: En este artículo se describe cómo implementar el servicio DICOM en el Azure Portal.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: b07d9bba578aeac026864e40a23236816b510f1e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787245"
---
# <a name="deploy-dicom-service-using-the-azure-portal"></a>Implemente el servicio DICOM mediante el Azure Portal

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este inicio rápido, aprenderá a implementar el servicio DICOM mediante el Azure Portal.

Una vez completada la implementación, puede usar el Azure Portal para ir al servicio DICOM recién creado para ver los detalles, incluida la dirección URL del servicio. La dirección URL del servicio para acceder al servicio DICOM será: ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` . Asegúrese de especificar la versión como parte de la dirección URL al realizar solicitudes. Puede encontrar más información en la documentación del servicio [Control de versiones de API para DICOM](api-versioning-dicom-service.md).

## <a name="prerequisite"></a>Requisito previo

Para implementar el servicio DICOM, debe tener un área de trabajo creada en el Azure Portal. Para obtener más información sobre cómo crear un área de trabajo, vea **Implementar área de trabajo en Azure Portal**.

## <a name="deploying-dicom-service"></a>Implementación del servicio DICOM

1. En la **página Grupo de** recursos de la Azure Portal, seleccione el nombre del área de trabajo de las API de atención **sanitaria.**

   [![Seleccione grupo de recursos del área de trabajo. ](media/select-workspace-resource-group.png) ](media/select-workspace-resource-group.png#lightbox)

2. Seleccione **Deploy DICOM service (Implementar servicio DICOM).**

   [![implementar el servicio dicom. ](media/workspace-deploy-dicom-services.png) ](media/workspace-deploy-dicom-services.png#lightbox)


3. Seleccione **Add DICOM service (Agregar servicio DICOM).**

   [![agregar servicio dicom. ](media/add-dicom-service.png) ](media/add-dicom-service.png#lightbox)


4. Escriba un nombre para el servicio DICOM y, a continuación, **seleccione Revisar y crear**. 

    [![nombre del servicio dicom. ](media/enter-dicom-service-name.png) ](media/enter-dicom-service-name.png#lightbox)


   (**Opcional)** Seleccione **Siguiente: Etiquetas >**.

    Las etiquetas son pares nombre-valor que se usan para categorizar los recursos. Para más información sobre las etiquetas, consulte [Uso de etiquetas para organizar los recursos de Azure y la jerarquía de administración.](../../azure-resource-manager/management/tag-resources.md)

5. Cuando observe la marca de verificación de validación verde, seleccione **Crear para** implementar el servicio DICOM.

6. Cuando se complete el proceso de implementación, seleccione **Ir al recurso**.  

   [![dicom vaya al recurso. ](media/go-to-resource.png) ](media/go-to-resource.png#lightbox)



   A continuación se muestra el resultado del servicio DICOM recién implementado.

   [![Dicom finalizó la implementación. ](media/results-deployed-dicom-service.png) ](media/results-deployed-dicom-service.png#lightbox)



## <a name="next-steps"></a>Pasos siguientes

>[!div class="nextstepaction"]
>[Introducción al servicio DICOM](dicom-services-overview.md)






