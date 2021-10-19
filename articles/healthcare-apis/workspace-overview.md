---
title: ¿Qué es el área de trabajo? - API de Azure Healthcare
description: En este artículo se describe una introducción al área de trabajo de Azure Healthcare API.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 07/12/2021
ms.author: ginle
ms.openlocfilehash: 6eb5fa5186b7f28954724f27186b972f7efb08b0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787455"
---
# <a name="what-is-healthcare-apis-preview-workspace"></a>¿Qué es el área de trabajo de las API de atención sanitaria (versión preliminar)?

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

El área de trabajo de Azure Healthcare API es un contenedor lógico para todas las instancias del servicio de atención sanitaria, como los servicios de Recursos Rápidos de Interoperabilidad en Salud (FHIR®), los servicios digitales de imágenes y comunicaciones en el sector sanitario (DICOM®) y los conectores de Internet de las cosas (IoT). El área de trabajo también crea un límite de cumplimiento (HIPAA, HITRUST) dentro del cual puede desplazarse la información sanitaria protegida.

Puede aprovisionar varios servicios de datos dentro de un área de trabajo y, por diseño, funcionan sin problemas entre sí. Con el área de trabajo, puede organizar todas las instancias de Healthcare API y administrar determinadas opciones de configuración que se comparten entre todos los conjuntos de datos y servicios subyacentes donde sea aplicable.

## <a name="workspace-provisioning-process"></a>Proceso de aprovisionamiento del área de trabajo
 
Se pueden crear una o varias áreas de trabajo en un grupo de recursos desde el Azure Portal o mediante scripts de implementación. Un área de trabajo de las API de atención sanitaria, como elemento primario en el árbol jerárquico de servicios, debe crearse primero antes de que se puedan crear una o varias instancias de servicio secundarias.   
 
No se puede eliminar un área de trabajo a menos que se hayan eliminado todas las instancias de servicio secundarias dentro del área de trabajo. Esta característica ayuda a evitar cualquier eliminación accidental de instancias de servicio. Sin embargo, cuando se elimina un grupo de recursos de área de trabajo, se eliminan todas las áreas de trabajo y las instancias de servicio secundarias dentro del grupo de recursos del área de trabajo. 

## <a name="workspace-and-azure-region-selection"></a>Selección del área de trabajo y la región de Azure 
 
Al crear un área de trabajo, debe configurarse para una región de Azure, que puede ser igual o diferente del grupo de recursos. No se puede cambiar la región después de crear el área de trabajo. Dentro de cada área de trabajo, todos los servicios de healthcare APIs (servicio FHIR, servicio DICOM y servicio IoT Connector) deben crearse en la región del área de trabajo y no se pueden mover a otra área de trabajo. 

## <a name="workspace-and-azure-healthcare-apis-service-instances"></a>Instancias de servicio del área de trabajo y de las API de Azure Healthcare 

Una vez que se crea el área de trabajo de Azure Healthcare API, ya está listo para crear una o varias instancias de servicio a partir del Azure Portal. Puede crear varias instancias de servicio del mismo tipo o tipos diferentes en un área de trabajo. Dentro del área de trabajo, puede aplicar opciones de configuración compartidas a las instancias de servicio secundarias, que se tratan en la sección configuración y área de trabajo.

[![Grupo de recursos de ](media/azure-resource-group.png) Azure ](media/azure-resource-group.png#lightbox)

Además, las áreas de trabajo se pueden crear mediante Azure Resource Manager de implementación, un proceso conocido normalmente como infraestructura como código (IaC). Esta opción ofrece la capacidad de personalizar las plantillas de ARM y completar la creación del área de trabajo y la creación de instancias de servicio en un paso combinado. 

Puede usar PowerShell, la CLI, los scripts de Terraform o el SDK de .NET para implementar el servicio healthcare API. Para crear una instancia de servicio en el área de trabajo, seleccione Crear **(servicio** FHIR, servicio DICOM o conectores de IoT) y, a continuación, escriba los detalles de la cuenta para esa instancia de servicio que se va a crear.


## <a name="fhir-service"></a>Servicio FHIR

El servicio FHIR incluye las API y los puntos de conexión de FHIR en Azure para el acceso a datos y el almacenamiento en formato de datos de FHIR. Un servicio FHIR administra la información sanitaria protegida (PHI) en un entorno de nube seguro y compatible. La implementación de un servicio FHIR le permite reunir datos de salud clínicos de varios sistemas en la nube de Azure según el estándar de datos interoperables [de FHIR](https://www.hl7.org/fhir/index.html) publicado por HL7. Para obtener más información, vea [Acerca del servicio FHIR.](./fhir/overview.md)

## <a name="dicom-service"></a>Servicio DICOM

Implemente un servicio DICOM para traer datos de imágenes médicas a la nube desde cualquier sistema habilitado para DICOMwebTM. Para obtener más información, vea [Información general del servicio DICOM.](dicom/dicom-services-overview.md)

## <a name="iot-connector"></a>Conector de IoT

El IoT Connector permite ingerir datos de dispositivo IoT de alta frecuencia en el servicio FHIR de una manera escalable, segura y compatible. Para más información, consulte la [página de documentación del conector de IoT](./iot/index.yml).
 
## <a name="workspace-configuration-settings"></a>Opciones de configuración del área de trabajo

Algunas características se configuran en el nivel de área de trabajo y se aplican a todos los servicios secundarios dentro de esa área de trabajo.

### <a name="application-monitoring"></a>Supervisión de aplicaciones

Azure Monitor ayuda a maximizar la disponibilidad y el rendimiento de las aplicaciones y los servicios. Ofrece una solución completa para recopilar, analizar y actuar en la telemetría desde los entornos local y en la nube. Esta información proporciona información sobre el rendimiento de las aplicaciones y le permite identificar y resolver de forma proactiva los problemas que les afectan y los recursos de los que dependen. Para obtener información sobre Azure Monitor, consulte la [documentación Azure Monitor información](../azure-monitor/index.yml) general.

### <a name="role-based-access-control-rbac"></a>Control de acceso basado en roles (RBAC)

El control de acceso basado en rol de Azure (Azure RBAC) es un sistema que proporciona una administración avanzada del acceso a los recursos de Azure. Con Azure RBAC, puede repartir las tareas entre el equipo y conceder a los usuarios únicamente el nivel de acceso que necesitan para realizar su trabajo. Además, le ayuda a administrar quién tiene acceso a los recursos de Azure, qué puede hacer con esos recursos y a qué áreas tiene acceso. Para más información, consulte la [documentación de RBAC de Azure.](../role-based-access-control/index.yml)


## <a name="next-steps"></a>Pasos siguientes

Para empezar a trabajar con las API de Azure Healthcare, siga el inicio rápido de 5 minutos para implementar un área de trabajo.

>[!div class="nextstepaction"]
>[Implementación del área de trabajo en la Azure Portal](healthcare-apis-quickstart.md)