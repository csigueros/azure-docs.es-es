---
title: 'Introducción al servicio FHIR: API de Azure Healthcare'
description: En este documento se describe cómo empezar a trabajar con el servicio FHIR en las API de Azure Healthcare.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/24/2021
ms.author: zxue
ms.openlocfilehash: f679f82531c84b5c05de4bddc2551c9c51c6d09b
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2021
ms.locfileid: "133273270"
---
# <a name="get-started-with-the-fhir-service"></a>Introducción al servicio FHIR

En este artículo se describen los pasos básicos para empezar a trabajar con el servicio FHIR en [las API de Azure Healthcare.](../healthcare-apis-overview.md)

Como requisito previo, necesitará una suscripción de Azure y se le han concedido los permisos adecuados para crear grupos de recursos de Azure e implementar recursos de Azure. Puede seguir todos los pasos u omitir algunos si tiene un entorno existente. Además, puede combinar todos los pasos y completarlos en PowerShell, CLI de Azure y scripts de API REST.

[![Introducción al diagrama de flujo del servicio FHIR.](media/get-started-with-fhir.png)](media/get-started-with-fhir.png#lightbox)

## <a name="create-a-workspace-in-your-azure-subscription"></a>Crear un área de trabajo en la suscripción de Azure

Puede crear un área de trabajo desde [la Azure Portal](../healthcare-apis-quickstart.md), o mediante PowerShell, CLI de Azure y la API REST. Puede encontrar scripts en los ejemplos [de LAS API de atención sanitaria.](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)

> [!NOTE]
> Hay límites en el número de áreas de trabajo y el número de instancias de servicio de FHIR que puede crear en cada suscripción de Azure.

## <a name="create-a-fhir-service-in-the-workspace"></a>Creación de un servicio FHIR en el área de trabajo

Puede crear una instancia de servicio de FHIR desde la [Azure Portal](../fhir/fhir-portal-quickstart.md), o mediante PowerShell, CLI de Azure API rest. Puede encontrar scripts en los ejemplos [de LAS API de atención sanitaria.](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)

Opcionalmente, puede crear un servicio [DICOM y](../dicom/deploy-dicom-services-in-azure.md) un conector [de IoT](../iot/deploy-iot-connector-in-azure.md) en el área de trabajo.

## <a name="access-the-fhir-service"></a>Acceso al servicio FHIR

El servicio FHIR está protegido por Azure Active Directory (Azure AD) que no se pueden deshabilitar. Para acceder a la API de servicio, debe crear una aplicación cliente a la que también se hace referencia como entidad de servicio en Azure AD y concederle los permisos adecuados.

### <a name="register-a-client-application"></a>Registro de una aplicación cliente

Puede crear o registrar una aplicación cliente desde el Azure Portal [o](../register-application.md)mediante PowerShell y CLI de Azure scripts. Esta aplicación cliente se puede usar para una o varias instancias del servicio FHIR. También se puede usar para otros servicios en las API de Azure Healthcare.

Si la aplicación cliente se crea con un certificado o un secreto de cliente, asegúrese de renovar el certificado o secreto de cliente antes de que expire y reemplace las credenciales de cliente en las aplicaciones.

Puede eliminar una aplicación cliente. Antes de eliminar una aplicación cliente, asegúrese de que no se usa en entornos de producción, desarrollo, pruebas o control de calidad (QA).

### <a name="grant-access-permissions"></a>Concesión de permisos de acceso

Puede conceder permisos de acceso o asignar roles desde el Azure Portal [o](../configure-azure-rbac.md)mediante PowerShell y CLI de Azure scripts.

### <a name="perform-create-read-update-and-delete-crud-transactions"></a>Realización de transacciones de creación, lectura, actualización y eliminación (CRUD)

Puede realizar transacciones de creación, lectura (búsqueda), actualización y eliminación (CRUD) en el servicio FHIR en las aplicaciones o mediante herramientas como Postman, el cliente REST y cURL. Dado que el servicio FHIR está protegido de forma predeterminada, debe obtener un token de acceso e incluirlo en la solicitud de transacción.

#### <a name="get-an-access-token"></a>Obtención de un token de acceso

Puede obtener un token de Azure AD mediante PowerShell, CLI de Azure, REST CCI o el SDK de .NET.  Para más información, consulte Get [access token (Obtener token de acceso).](../get-access-token.md)

#### <a name="access-using-existing-tools"></a>Acceso mediante herramientas existentes

- [Postman](../use-postman.md)
- [Cliente rest](../using-rest-client.md)
- [cURL](../using-curl.md)

#### <a name="load-data"></a>Cargar datos

Puede cargar datos directamente mediante el método POST o PUT en el servicio FHIR. Para cargar datos de forma masiva, puede usar una de las herramientas de código abierto que se enumeran a continuación.
 
- [Cargador de FHIR](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/FHIRDL) Se trata de una aplicación de consola de .NET y carga los datos almacenados en Azure Storage en el servicio FHIR. Se trata de una aplicación de un solo subproceso, pero puede ejecutar varias copias localmente o en un contenedor de Docker. 
- [Cargador masivo de FHIR](https://github.com/microsoft/fhir-loader) Esta herramienta es una aplicación de función de Azure (microservicio) y se ejecuta en subprocesos paralelos.
- [Importación masiva](https://github.com/microsoft/fhir-server/blob/main/docs/BulkImport.md) Esta herramienta solo funciona con el servidor FHIR de código abierto. Sin embargo, estará disponible para las API de Azure Healthcare en el futuro.

### <a name="cms-search-profile-validation-and-reindex"></a>CMS, búsqueda, validación de perfiles y reindexación

Puede encontrar más detalles sobre la interoperabilidad y el acceso a los pacientes, la búsqueda, la validación de perfiles y la reindexación en la documentación [del servicio FHIR.](overview.md)

### <a name="export-data"></a>Exportar datos

Opcionalmente, puede exportar ($export) datos [](../data-transformation/export-data.md) a Azure Storage y usarlos en los proyectos de análisis o aprendizaje automático. Puede exportar los datos "tal como están" o [des-id](../data-transformation/de-identified-export.md) en `ndjson` formato. 

También puede exportar datos a [Synapse mediante](../data-transformation/move-to-synapse.md) el proyecto de código abierto. En el futuro, esta característica se integrará en el servicio administrado.

### <a name="converting-data"></a>Convertir datos

Opcionalmente, puede convertir [HL7 v2](../data-transformation/convert-data.md) y otros datos de formato a FHIR.

### <a name="using-fhir-data-in-power-bi-dashboard"></a>Uso de datos de FHIR en Power BI Dashboard

Opcionalmente, puede crear informes Power BI panel con datos de FHIR.

- [Power Query conector para FHIR](https://docs.microsoft.com/power-query/connectors/fhir/fhir)
- [Conector de IoT y Microsoft Power BI](../iot/iot-connector-power-bi.md)

## <a name="next-steps"></a>Pasos siguientes

En este artículo se describen los pasos básicos para empezar a usar el servicio FHIR. Para obtener información sobre cómo implementar el servicio FHIR en el área de trabajo, consulte

>[!div class="nextstepaction"]
>[Implementación de un servicio FHIR en las API de Azure Healthcare](fhir-portal-quickstart.md)
