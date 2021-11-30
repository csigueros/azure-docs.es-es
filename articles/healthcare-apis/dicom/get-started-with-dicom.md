---
title: 'Introducción al servicio DICOM: API de Azure Healthcare'
description: En este documento se describe cómo empezar a trabajar con el servicio DICOM en las API de Azure Healthcare.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/24/2021
ms.author: zxue
ms.openlocfilehash: bc3c691d7a990e47b6afe624854d9085169fe0b2
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2021
ms.locfileid: "133273257"
---
# <a name="get-started-with-the-dicom-service"></a>Introducción al servicio DICOM

En este artículo se describen los pasos básicos para empezar a trabajar con el servicio DICOM en las [API de Azure Healthcare](../healthcare-apis-overview.md). 

Como requisito previo, necesitará una suscripción de Azure y se le han concedido los permisos adecuados para crear grupos de recursos de Azure e implementar recursos de Azure. Puede seguir todos los pasos u omitir algunos si tiene un entorno existente. Además, puede combinar todos los pasos y completarlos en PowerShell, CLI de Azure y scripts de API REST.

[![Introducción con DICOM](media/get-started-with-dicom.png)](media/get-started-with-dicom.png#lightbox)

## <a name="create-a-workspace-in-your-azure-subscription"></a>Creación de un área de trabajo en la suscripción de Azure

Puede crear un área de trabajo desde la [Azure Portal](../healthcare-apis-quickstart.md) o mediante PowerShell, CLI de Azure api rest. Puede encontrar scripts en los ejemplos [de LAS API de atención sanitaria.](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)

> [!NOTE]
> Hay límites en el número de áreas de trabajo y el número de instancias de servicio DICOM que puede crear en cada suscripción de Azure.

## <a name="create-a-dicom-service-in-the-workspace"></a>Creación de un servicio DICOM en el área de trabajo

Puede crear una instancia de servicio DICOM desde la Azure Portal [o](deploy-dicom-services-in-azure.md) mediante PowerShell, CLI de Azure api rest. Puede encontrar scripts en los ejemplos [de LAS API de atención sanitaria.](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)

Opcionalmente, puede crear un servicio [FHIR y](../fhir/fhir-portal-quickstart.md) un conector [de IoT en](../iot/deploy-iot-connector-in-azure.md) el área de trabajo.

## <a name="access-the-dicom-service"></a>Acceso al servicio DICOM

El servicio DICOM está protegido por Azure Active Directory (Azure AD) que no se pueden deshabilitar. Para acceder a la API de servicio, debe crear una aplicación cliente a la que también se hace referencia como entidad de servicio en Azure AD y concederle los permisos adecuados.

### <a name="register-a-client-application"></a>Registro de una aplicación cliente

Puede crear o registrar una aplicación cliente desde el Azure Portal [o](../register-application.md)mediante PowerShell y CLI de Azure scripts. Esta aplicación cliente se puede usar para una o varias instancias de servicio DICOM. También se puede usar para otros servicios en las API de Azure Healthcare.

Si la aplicación cliente se crea con un certificado o un secreto de cliente, asegúrese de renovar el certificado o secreto de cliente antes de que expire y reemplace las credenciales de cliente en las aplicaciones.

Puede eliminar una aplicación cliente. Antes de hacerlo, asegúrese de que no se usa en entornos de producción, desarrollo, pruebas o control de calidad (QA).

### <a name="grant-access-permissions"></a>Concesión de permisos de acceso

Puede conceder permisos de acceso o asignar roles desde el Azure Portal [o](../configure-azure-rbac.md)mediante PowerShell y CLI de Azure scripts.

### <a name="perform-create-read-update-and-delete-crud-transactions"></a>Realización de transacciones de creación, lectura, actualización y eliminación (CRUD)

Puede realizar transacciones de creación, lectura (búsqueda), actualización y eliminación (CRUD) en el servicio DICOM en las aplicaciones o mediante herramientas como Postman, el cliente REST, cURL y Python. Dado que el servicio DICOM está protegido de forma predeterminada, debe obtener un token de acceso e incluirlo en la solicitud de transacción.

#### <a name="get-an-access-token"></a>Obtención de un token de acceso

Puede obtener un token de Azure AD mediante PowerShell, CLI de Azure, la CLI de REST o el SDK de .NET.  Para más información, consulte Get [access token (Obtener token de acceso).](../get-access-token.md)

#### <a name="access-using-existing-tools"></a>Acceso mediante herramientas existentes

- [Postman](../use-postman.md)
- [Cliente REST](../using-rest-client.md)
- [.NET C #](dicomweb-standard-apis-c-sharp.md)
- [cURL](dicomweb-standard-apis-curl.md)
- [Python](dicomweb-standard-apis-python.md)

### <a name="dicomweb-standard-apis-and-change-feed"></a>API estándar de DICOMweb y fuente de cambios

Puede encontrar más detalles sobre las API estándar de DICOMweb y la fuente de cambios en la [documentación del servicio DICOM.](dicom-services-overview.md)

#### <a name="dicomcast"></a>DICOMCast

Puede usar el proyecto [DICOMCast de](https://github.com/microsoft/dicom-server/tree/main/converter/dicom-cast) código abierto para trabajar con datos de FHIR. En el futuro, esta funcionalidad estará disponible en el servicio administrado.

## <a name="next-steps"></a>Pasos siguientes

En este artículo se describen los pasos básicos para empezar a usar el servicio DICOM. Para obtener información sobre cómo implementar el servicio DICOM en el área de trabajo, consulte

>[!div class="nextstepaction"]
>[Implementación del servicio DICOM mediante el Azure Portal](deploy-dicom-services-in-azure.md)