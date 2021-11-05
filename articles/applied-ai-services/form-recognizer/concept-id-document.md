---
title: Modelo de documentos de identificación de Form Recognizer
titleSuffix: Azure Applied AI Services
description: Conceptos que abarcan la extracción y el análisis de datos mediante el modelo de documentos de identificación precompilado
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: d598c3af52f8b62b23b49f9d661a79a2979574a0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027311"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-id-document-model"></a>Modelo de documentos de identificación de Form Recognizer

El modelo de documentos de identificación combina eficaces funcionalidades de reconocimiento óptico de caracteres (OCR) con modelos de aprendizaje profundo para analizar y extraer información clave de permisos de conducir de EE. UU. (los 50 estados y el distrito de Columbia) y las páginas de información personal de los pasaportes internacionales (excepto los visados y otros documentos de viajes). La API analiza los documentos de identidad, extrae información clave como nombre, apellidos, dirección y fecha de nacimiento, y devuelve una representación de datos JSON estructurada.

***Permiso de conducir de EE. UU. de ejemplo procesado con Form Recognizer Studio***

:::image type="content" source="media/studio/drivers-license.png" alt-text="tarjeta de identidad de ejemplo" lightbox="media/overview-id.jpg":::

## <a name="development-options"></a>Opciones de desarrollo

Los siguientes recursos son compatibles con Form Recognizer v2.1:

| Característica | Recursos |
|----------|-------------------------|
|**Modelo de documentación de id.**| <ul><li>[**Herramienta de etiquetado Form Recognizer**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**API DE REST**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-identity-id-documents)</li><li>[**SDK de biblioteca cliente**](quickstarts/try-sdk-rest-api.md)</li><li>[**Contenedor de Docker de Form Recognizer**](containers/form-recognizer-container-install-run.md?tabs=id-document#run-the-container-with-the-docker-compose-up-command)</li></ul>|

Los siguientes recursos son compatibles con Form Recognizer v3.0:

| Característica | Recursos | Id. de modelo |
|----------|-------------|-----------|
|**Modelo de documentación de id.**|<ul><li> [**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**API DE REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**SDK DE C#**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**SDK de Python**](quickstarts/try-v3-python-sdk.md)</li><li>[**SDK de Java**](quickstarts/try-v3-java-sdk.md)</li><li>[**SDK de JavaScript**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|**prebuilt-idDocument**|

### <a name="try-form-recognizer"></a>Probar Form Recognizer

Consulte cómo se extraen datos, incluidos el nombre, la fecha de nacimiento, el área legible por máquina y la fecha de expiración, de los documentos de identificación mediante Form Recognizer Studio o nuestra herramienta de etiquetado de ejemplo. Necesitará lo siguiente:

* Una suscripción a Azure: puede [crear una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/)

* Una [instancia de Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) en Azure Portal. Puede usar el plan de tarifa gratuito (`F0`) para probar el servicio. Después de implementar el recurso, seleccione **Ir al recurso** para obtener la clave de API y el punto de conexión.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

#### <a name="form-recognizer-studio-preview"></a>Form Recognizer Studio (versión preliminar)

> [!NOTE]
> Form Recognizer Studio está disponible con la API de versión preliminar (v3.0).

1. En la página principal de Form Recognizer Studio, seleccione **Facturas**.

1. Puede analizar la factura de ejemplo o seleccionar el botón **+ Agregar** para cargar un ejemplo propio.

1. Seleccione el botón **Analizar**:

    :::image type="content" source="media/studio/id-document-analyze.png" alt-text="Captura de pantalla: menú para analizar el documento de identificación.":::

    > [!div class="nextstepaction"]
    > [Prueba de Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=idDocument)

#### <a name="sample-labeling-tool"></a>Herramienta de etiquetado de muestras

Necesitará un documento de identificación. Puede usar nuestro [documento de identificación de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png).

1. En la página principal de la herramienta de etiquetado de ejemplo, seleccione **Use prebuilt model to get data** (Usar modelo precompilado para obtener datos).

1. Seleccione **Identity documents** (Documentos de identidad) en el menú desplegable **Tipo de formulario**:

    :::image type="content" source="media/try-id-document.png" alt-text="Captura de pantalla: menú desplegable de selección del modelo precompilado de la herramienta de etiquetado de ejemplo.":::

   > [!div class="nextstepaction"]
   > [Pruebe la herramienta de etiquetado de ejemplo](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

## <a name="input-requirements"></a>Requisitos de entrada

* Para obtener unos resultados óptimos, proporcione una foto clara o una digitalización de alta calidad por documento.
* Formatos de archivo admitidos: JPEG, PNG, BMP, TIFF y PDF (texto insertado o digitalizado). Los PDF insertados de texto son mejores para eliminar la posibilidad de error en la extracción de caracteres y en la ubicación.
* En el caso de PDF y TIFF, se pueden procesar hasta 2000 páginas (con una suscripción de nivel gratuito, solo se procesan las dos primeras páginas).
* El tamaño del archivo debe ser inferior a 50 MB.
* Las imágenes deben tener unas dimensiones entre 50 x 50 píxeles y 10 000 x 10 000 píxeles.
* Los archivos PDF tienen unas dimensiones de hasta 17 x 17 pulgadas, lo que corresponde a los tamaños de papel Legal o A3, o más pequeños.
* El tamaño total de los datos de entrenamiento es de 500 páginas o menos.
* Si los archivos PDF están bloqueados con contraseña, debe desbloquearlos antes de enviarlos.
* Para un aprendizaje sin supervisión (sin datos etiquetados):
  * Los datos deben contener claves y valores.
  * Las claves deben aparecer por encima o a la izquierda de los valores; no pueden aparecer por debajo ni a la derecha.

> [!NOTE]
> La [herramienta de etiquetado de ejemplo](https://fott-2-1.azurewebsites.net/) no admite el formato de archivo BMP. Se trata de una limitación de la herramienta, no del servicio Form Recognizer.

## <a name="supported-languages-and-locales-v21"></a>Idiomas y configuraciones regionales compatibles v2.1

| Modelo | Idioma: código de configuración local | Valor predeterminado |
|--------|:----------------------|:---------|
|Documento de identificación| <ul><li>Inglés (Estados Unidos): en-US (permiso de conducir)</li><li>Páginas de información personal de los pasaportes internacionales</br> (excepto los visados y otros documentos de viajes)</li></ul></br>|Inglés (Estados Unidos): en-US|

## <a name="field-extraction"></a>Extracción de campos

|Nombre| Tipo | Descripción | Salida estandarizada|
|:-----|:----|:----|:----|
|  CountryRegion | countryRegion | Código de país o región que se ajusta a la norma ISO 3166 estándar. |  |
|  DateOfBirth | Fecha | DOB | aaaa-mm-dd |
|  DateOfExpiration | Fecha | Fecha de expiración FDN | aaaa-mm-dd |
|  DocumentNumber | String | Número de pasaporte, número de permiso de conducir, etc. pertinente. |  |
|  FirstName | String | Nombre extraído e inicial del segundo nombre, si procede. |  |
|  LastName | String | Apellido extraído |  |
|  Nacionalidad | countryRegion | Código de país o región que se ajusta a la norma ISO 3166 estándar (solo pasaporte). |  |
|  Sex | String | Los valores extraídos posibles son "M", "F" y "X". | |
|  MachineReadableZone | Object | Zona de lectura automática del pasaporte extraída, incluidas dos líneas de 44 caracteres cada una. | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816" |
|  DocumentType | String | Tipo de documento, por ejemplo, pasaporte o permiso de conducir. | "passport" |
|  Dirección | String | Dirección extraída (solo permiso de conducir) ||
|  Region | String | Valor extraído de región, estado, provincia, etc. (solo permiso de conducir) |  |

## <a name="form-recognizer-preview-v30"></a>Versión preliminar v3.0 de Form Recognizer

 La versión preliminar de Form Recognizer presenta varias características y funcionalidades nuevas:

* El modelo de **documentos de identificación (v3.0)** admite aprobaciones, restricciones y la extracción de clasificación de vehículos de permisos de conducir de EE. UU.

### <a name="id-document-preview-field-extraction"></a>Extracción de campos de la versión preliminar del documento de identificación

|Nombre| Tipo | Descripción | Salida estandarizada|
|:-----|:----|:----|:----|
| 🆕 Aprobaciones | String | Privilegios de conducción adicionales concedidos a un conductor, como una moto o un autobús escolar.  | |
| 🆕 Restricciones | String | Privilegios de conducción restringidos aplicables a licencias suspendidas o revocadas.| |
| 🆕VehicleClassification | String | Tipos de vehículos que puede conducir un conductor. ||
|  CountryRegion | countryRegion | Código de país o región que se ajusta a la norma ISO 3166 estándar. |  |
|  DateOfBirth | Fecha | DOB | aaaa-mm-dd |
|  DateOfExpiration | Fecha | Fecha de expiración FDN | aaaa-mm-dd |
|  DocumentNumber | String | Número de pasaporte, número de permiso de conducir, etc. pertinente. |  |
|  FirstName | String | Nombre extraído e inicial del segundo nombre, si procede. |  |
|  LastName | String | Apellido extraído |  |
|  Nacionalidad | countryRegion | Código de país o región que se ajusta a la norma ISO 3166 estándar (solo pasaporte). |  |
|  Sex | String | Los valores extraídos posibles son "M", "F" y "X". | |
|  MachineReadableZone | Object | Zona de lectura automática del pasaporte extraída, incluidas dos líneas de 44 caracteres cada una. | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816" |
|  DocumentType | String | Tipo de documento, por ejemplo, pasaporte o permiso de conducir. | "passport" |
|  Dirección | String | Dirección extraída (solo permiso de conducir) ||
|  Region | String | Valor extraído de región, estado, provincia, etc. (solo permiso de conducir) |  |

### <a name="migration-guide-and-rest-api-v30"></a>Guía de migración y API REST v3.0

* Siga nuestra [**guía de migración de Form Recognizer v3.0**](v3-migration-guide.md) para obtener información sobre cómo usar la versión preliminar en las aplicaciones y flujos de trabajo.

* Explore nuestra [**API de REST (versión preliminar)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) para obtener más información sobre la versión preliminar y las nuevas funcionalidades.

## <a name="next-steps"></a>Pasos siguientes

* Completar un inicio rápido de Form Recognizer:

  > [!div class="nextstepaction"]
  > [Inicio rápido: Uso de la biblioteca cliente de Form Recognizer](quickstarts/try-sdk-rest-api.md)

* Explorar nuestra API de REST:

    > [!div class="nextstepaction"]
    > [API v2.1 de Form Recognizer](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707)
