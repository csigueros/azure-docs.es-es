---
title: 'Uso de DICOMweb: API estándar con el servicio DICOM de las API de Azure Healthcare'
description: En este tutorial se describe cómo usar las API estándar de DICOMweb con el servicio DICOM.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 08/23/2021
ms.author: aersoy
ms.openlocfilehash: b219c4d624c5823804371bba156911aaa94eeccb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631994"
---
# <a name="using-dicomwebtradestandard-apis-with-dicom-services"></a>Uso de las API estándar de DICOMweb &trade; con servicios DICOM

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este tutorial se proporciona información general sobre cómo usar las API estándar de DICOMweb &trade; con el servicio DICOM.

El servicio DICOM admite un subconjunto del estándar DICOMweb &trade; que incluye lo siguiente:

* Almacenamiento (STOW-RS)
* Recuperación (WADO-RS)
* Búsqueda (QIDO-RS)

Además, se admiten las API no estándar siguientes:

* Eliminar
* Fuente de cambios

Para obtener más información sobre nuestra compatibilidad con las API de DICOM Web Standard, consulte el documento de referencia de la instrucción [de conformidad de DICOM.](dicom-services-conformance-statement.md)

## <a name="prerequisites"></a>Requisitos previos

Para usar las API estándar de DICOMweb, debe tener implementada una instancia de &trade; los servicios DICOM. Si aún no ha implementado una instancia del servicio DICOM, consulte [Deploy DICOM service using the Azure Portal](deploy-dicom-services-in-azure.md).

Una vez completada la implementación, puede usar el Azure Portal para ir al servicio DICOM recién creado para ver los detalles, incluida la dirección URL del servicio. La dirección URL del servicio para acceder al servicio DICOM será: ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` . Asegúrese de especificar la versión como parte de la dirección URL al realizar solicitudes. Para obtener más información, consulte [Control de versiones de API para el servicio DICOM](api-versioning-dicom-service.md).

## <a name="overview-of-various-methods-to-use-with-dicom-service"></a>Información general de varios métodos para usar con el servicio DICOM

Dado que el servicio DICOM se expone como una API REST, puede acceder a él mediante cualquier lenguaje de desarrollo moderno. Para obtener información independiente del lenguaje sobre cómo trabajar con el servicio, vea [DICOM Conformance Statement](dicom-services-conformance-statement.md).

Para ver ejemplos específicos del lenguaje, consulte los ejemplos siguientes. Puede ver ejemplos de colecciones de Postman en varios idiomas, entre los que se incluyen:

* Go 
* Java 
* JavaScript 
* C# 
* PHP 
* C 
* NodeJS
* Objective-C
* OCaml
* PowerShell
* Python
* Ruby 
* Veloz.

### <a name="c"></a>C#

Consulte el tutorial [Uso de DICOMweb™ API](dicomweb-standard-apis-c-sharp.md) estándar con C# para obtener información sobre cómo usar C# con el servicio DICOM.

### <a name="curl"></a>cURL

cURL es una herramienta de línea de comandos común para llamar a puntos de conexión web que está disponible para casi cualquier sistema operativo. [Descargue cURL](https://curl.haxx.se/download.html) para empezar.

Para obtener información sobre cómo usar cURL con el servicio DICOM, consulte el tutorial Uso de [DICOMWeb™ API estándar con cURL.](dicomweb-standard-apis-curl.md)

### <a name="python"></a>Python

Consulte el tutorial [Uso de DICOMWeb™ API estándar](dicomweb-standard-apis-python.md) con Python para aprender a usar Python con el servicio DICOM.

### <a name="postman"></a>postman

Postman es una excelente herramienta para diseñar, compilar y probar las API REST. [Descargue Postman](https://www.postman.com/downloads/) para empezar. Puede aprender a usar De forma eficaz Postman en el [sitio de aprendizaje de Postman.](https://learning.postman.com/)

Una advertencia importante con Postman y el estándar DICOMweb es que Postman solo puede admitir la carga de archivos DICOM mediante la carga de una sola parte definida en el estándar &trade; DICOM. Este motivo se debe a que Postman no puede admitir separadores personalizados en una solicitud POST de varias partes o relacionada. Para obtener más información, [vea Multipart POST not working for me # 576](https://github.com/postmanlabs/postman-app-support/issues/576). Por lo tanto, todos los ejemplos de la colección de Postman para cargar documentos DICOM mediante una solicitud de varias partes tienen como prefijo [no funcionará - vea la descripción]. Los ejemplos para cargar mediante una solicitud de una sola parte se incluyen en la colección y tienen como prefijo "Store-Single-Instance".

Para usar la colección de Postman, deberá descargarla localmente e importarla a través de Postman. Para obtener acceso a esta colección, vea [Ejemplos de la colección de Postman](https://github.com/microsoft/dicom-server/blob/main/docs/resources/Conformance-as-Postman.postman_collection.json).

## <a name="summary"></a>Resumen

En este tutorial se proporciona información general sobre las API compatibles con el servicio DICOM. Introducción al uso de estas API con las siguientes herramientas:

- [Uso de DICOMweb™ API estándar con C #](dicomweb-standard-apis-c-sharp.md)
- [Uso de DICOMWeb™ API estándar con cURL](dicomweb-standard-apis-curl.md)
- [Uso de DICOMWeb™ API estándar con Python](dicomweb-standard-apis-python.md)
- [Uso de LAS API web estándar de DICOM con la colección de ejemplos de Postman](https://github.com/microsoft/dicom-server/blob/main/docs/resources/Conformance-as-Postman.postman_collection.json)

### <a name="next-steps"></a>Pasos siguientes

Para obtener más información, vea

>[!div class="nextstepaction"]
>[Introducción al servicio DICOM](dicom-services-overview.md)
