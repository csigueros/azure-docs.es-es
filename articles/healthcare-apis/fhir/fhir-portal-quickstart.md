---
title: Implementación de un servicio FHIR en las API de Azure Healthcare
description: En este artículo se enseña a los usuarios a implementar un servicio FHIR en el Azure Portal.
author: stevewohl
ms.service: healthcare-apis
ms.topic: quickstart
ms.date: 09/10/2021
ms.author: ginle
ms.custom: mode-other
ms.openlocfilehash: 659fb82e35cd25622158d05a88d85a16e736c770
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/24/2021
ms.locfileid: "133069700"
---
# <a name="deploy-a-fhir-service-within-azure-healthcare-apis---using-portal"></a>Implementación de un servicio FHIR en las API de Azure Healthcare mediante el portal

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo, aprenderá a implementar el servicio FHIR dentro de las API de Azure Healthcare (por lo que se denomina el servicio FHIR) mediante el Azure Portal.

## <a name="prerequisite"></a>Requisito previo

Antes de empezar, ya debe haber implementado las API de Azure Healthcare. Para más información sobre la implementación de las API de Azure Healthcare, consulte Implementación del área de [trabajo en Azure Portal](../healthcare-apis-quickstart.md).

## <a name="create-a-new-fhir-service"></a>Creación de un nuevo servicio FHIR

En el área de trabajo, **seleccione Deploy FHIR Services (Implementar servicios de FHIR).**

[![Implementación del servicio FHIR ](media/fhir-service/deploy-fhir-services.png) ](media/fhir-service/deploy-fhir-services.png#lightbox)

Seleccione **+ Agregar servicio FHIR**.

[![Adición del servicio FHIR ](media/fhir-service/add-fhir-service.png) ](media/fhir-service/add-fhir-service.png#lightbox)

Escriba un **nombre de cuenta** para el servicio FHIR. Seleccione la **versión de FHIR** **(STU3** o **R4)** y, a continuación, **seleccione Revisar y crear**.

[![Creación del servicio FHIR ](media/fhir-service/create-fhir-service.png) ](media/fhir-service/create-fhir-service.png#lightbox)

Antes de seleccionar **Crear**, revise las propiedades de los valores **Básico** y **Adicional** del servicio FHIR. Si necesita volver atrás y realizar cambios, seleccione **Anterior.** Confirme que aparece **el mensaje Validación** correcta. 

[![Validación del servicio FHIR ](media/fhir-service/validation-fhir-service.png) ](media/fhir-service/validation-fhir-service.png#lightbox)

## <a name="additional-settings-optional"></a>Opciones de configuración adicionales (opcional)

También puede seleccionar la pestaña **Configuración adicional para** ver la configuración de autenticación. La configuración predeterminada de Azure API for FHIR es **usar RBAC de Azure para asignar los roles de plano de datos**. Cuando se configura en este modo, la "entidad" del servicio FHIR se establecerá en el Azure Active Directory inquilino de la suscripción.

[![Configuración adicional del servicio FHIR ](media/fhir-service/additional-settings-tab.png) ](media/fhir-service/additional-settings-tab.png#lightbox)

Observe que el cuadro para escribir los **IDs** de objeto permitidos está atenuado. Esto se debe a que usamos RBAC de Azure para configurar asignaciones de roles en este caso.

Si desea configurar el servicio FHIR para que use un inquilino de Azure Active Directory externo o secundario, puede cambiar la autoridad y especificar los identificadores de objeto para los usuarios y grupos a los que se deba permitir el acceso al servidor.

## <a name="fetch-fhir-api-capability-statement"></a>Captura de la instrucción de funcionalidad de la API de FHIR

Para validar que se aprovisiona la nueva cuenta de FHIR API, busque una instrucción de funcionalidad. Para ello, vaya a `https://<WORKSPACE NAME>-<ACCOUNT-NAME>.fhir.azurehealthcareapis.com/metadata` .

## <a name="next-steps"></a>Pasos siguientes

>[!div class="nextstepaction"]
>[Acceso al servicio FHIR mediante Postman](../use-postman.md)
