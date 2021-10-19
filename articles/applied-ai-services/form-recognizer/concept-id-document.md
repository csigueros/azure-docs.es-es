---
title: Modelo de documentos de identificación de Form Recognizer
titleSuffix: Azure Applied AI Services
description: Conceptos que abarcan la extracción y el análisis de datos mediante el modelo de documentos de identificación precompilado
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 4b73526ad245d9364b0ada997fc014e0dd16df40
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716313"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-id-document-model"></a>Modelo de documentos de identificación de Form Recognizer

El modelo de documentos de identificación combina eficaces funcionalidades de reconocimiento óptico de caracteres (OCR) con modelos de aprendizaje profundo para analizar y extraer información clave de permisos de conducir de EE. UU. (los 50 estados y el distrito de Columbia) y las páginas de información personal de los pasaportes internacionales (excepto los visados y otros documentos de viajes). La API analiza los documentos de identidad, extrae información clave como nombre, apellidos, dirección y fecha de nacimiento, y devuelve una representación de datos JSON estructurada.

***Permiso de conducir de EE. UU. de ejemplo procesado con la [herramienta de etiquetado de ejemplo de Form Recognizer](https://fott-2-1.azurewebsites.net/):***

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="tarjeta de identidad de ejemplo" lightbox="./media/overview-id.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>Pruebe Form Recognizer Studio (versión preliminar)

* Form Recognizer Studio está disponible con la API de versión preliminar (v3.0).

* Extraiga el nombre, la zona de lectura automática y la fecha de expiración con nuestra característica de documentos de identificación de Form Recognizer Studio:

> [!div class="nextstepaction"]
> [Pruebe Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=idDocument)

## <a name="try-it-sample-labeling-tool"></a>Pruébelo: herramienta de etiquetado de ejemplo

Para ver cómo se extraen los datos de documentos de identificación, pruebe nuestra herramienta de etiquetado de ejemplo. Necesitará lo siguiente:

* Una suscripción a Azure: puede [crear una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/)

* Una [instancia de Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) ) en Azure Portal. Puede usar el plan de tarifa gratuito (`F0`) para probar el servicio. Después de implementar el recurso, haga clic en **Ir al recurso** para obtener la clave de API y el punto de conexión.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

* Un documento de identificación. Puede usar nuestro [documento de identificación de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/id-license.jpg).

> [!div class="nextstepaction"]
  > [Probarlo](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

En la interfaz de usuario de Form Recognizer:

  1. Seleccione **Uso del modelo precompilado para obtener datos**.
  1. Seleccione **Recibo** en el menú desplegable **Tipo de formulario**:

  :::image type="content" source="media/try-id-document.png" alt-text="Captura de pantalla: menú de selección de modelos precompilados desplegable de la herramienta de etiquetado de ejemplo.":::

## <a name="input-requirements"></a>Requisitos de entrada

* Para obtener los mejores resultados, proporcione una foto clara o un escaneo de alta calidad por documento.
* Formatos de archivo admitidos: JPEG, PNG, BMP, TIFF y PDF (texto insertado o digitalizado). Los PDF insertados de texto son mejores para eliminar la posibilidad de error en la extracción de caracteres y en la ubicación.
* En el caso de PDF y TIFF, se pueden procesar hasta 2000 páginas (con una suscripción de nivel gratuito, solo se procesan las dos primeras páginas).
* El tamaño del archivo debe ser inferior a 50 MB.
* Las imágenes deben tener unas dimensiones entre 50 x 50 píxeles y 10 000 x 10 000 píxeles.
* Los archivos PDF tienen unas dimensiones de hasta 17 x 17 pulgadas, lo que se corresponde con los tamaños de papel Legal o A3, o más pequeños.
* El tamaño total de los datos de aprendizaje es de 500 páginas o menos.
* Si los archivos PDF están bloqueados con contraseña, debe quitar el bloqueo antes de enviarlos.
* Para un aprendizaje sin supervisión (sin datos etiquetados):
  * los datos deben contener claves y valores.
  * las claves deben aparecer arriba o a la izquierda de los valores, pero no pueden aparecer abajo o a la derecha.

> [!NOTE]
> La [herramienta de etiquetado de ejemplo](https://fott-2-1.azurewebsites.net/) no admite el formato de archivo BMP. Se trata de una limitación de la herramienta, no del servicio Form Recognizer.

## <a name="supported-languages-and-locales-v21"></a>Idiomas y configuraciones regionales compatibles v2.1

| Modelo | Idioma: código de configuración local | Valor predeterminado |
|--------|:----------------------|:---------|
|Documento de identificación| <ul><li>Inglés (Estados Unidos): en-US (permiso de conducir)</li><li>Páginas de información personal de los pasaportes internacionales</br> (excepto los visados y otros documentos de viajes)</li></ul></br>|Inglés (Estados Unidos): en-US|

## <a name="key-value-pair-extraction"></a>Extracción de pares clave-valor

|Nombre| Tipo | Descripción | Salida estandarizada|
|:-----|:----|:----|:----|
|  CountryRegion | countryRegion | Código de país o región que se ajusta a la norma ISO 3166 estándar. |  |
|  DateOfBirth | date | DOB | aaaa-mm-dd |
|  DateOfExpiration | date | Fecha de expiración FDN | aaaa-mm-dd |
|  DocumentNumber | string | Número de pasaporte, número de permiso de conducir, etc. pertinente. |  |
|  FirstName | string | Nombre extraído e inicial del segundo nombre, si procede. |  |
|  LastName | string | Apellido extraído |  |
|  Nacionalidad | countryRegion | Código de país o región que se ajusta a la norma ISO 3166 estándar (solo pasaporte). |  |
|  Sex | string | Los valores extraídos posibles son "M", "F" y "X". | |
|  MachineReadableZone | objeto | Zona de lectura automática del pasaporte extraída, incluidas dos líneas de 44 caracteres cada una. | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816" |
|  DocumentType | string | Tipo de documento, por ejemplo, pasaporte o permiso de conducir. | "passport" |
|  Dirección | string | Dirección extraída (solo permiso de conducir) ||
|  Region | string | Valor extraído de región, estado, provincia, etc. (solo permiso de conducir) |  |

## <a name="form-recognizer-preview-v30"></a>Versión preliminar v3.0 de Form Recognizer

 La versión preliminar de Form Recognizer presenta varias características y funcionalidades nuevas:

* El modelo de **documentos de identificación (v3.0)** admite aprobaciones, restricciones y la extracción de clasificación de vehículos de permisos de conducir de EE. UU.

    ### <a name="id-document-preview-key-value-pair-extraction"></a>Extracción de pares clave-valor de la vista previa de documentos de identificación

    |Nombre| Tipo | Descripción | Salida estandarizada|
    |:-----|:----|:----|:----|
    | 🆕 Aprobaciones | string | Privilegios de conducción adicionales concedidos a un conductor, como una moto o un autobús escolar.  | |
    | 🆕 Restricciones | string | Privilegios de conducción restringidos aplicables a licencias suspendidas o revocadas.| |
    | 🆕VehicleClassification | string | Tipos de vehículos que puede conducir un conductor. ||
    |  CountryRegion | countryRegion | Código de país o región que se ajusta a la norma ISO 3166 estándar. |  |
    |  DateOfBirth | date | DOB | aaaa-mm-dd |
    |  DateOfExpiration | date | Fecha de expiración FDN | aaaa-mm-dd |
    |  DocumentNumber | string | Número de pasaporte, número de permiso de conducir, etc. pertinente. |  |
    |  FirstName | string | Nombre extraído e inicial del segundo nombre, si procede. |  |
    |  LastName | string | Apellido extraído |  |
    |  Nacionalidad | countryRegion | Código de país o región que se ajusta a la norma ISO 3166 estándar (solo pasaporte). |  |
    |  Sex | string | Los valores extraídos posibles son "M", "F" y "X". | |
    |  MachineReadableZone | objeto | Zona de lectura automática del pasaporte extraída, incluidas dos líneas de 44 caracteres cada una. | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816" |
    |  DocumentType | string | Tipo de documento, por ejemplo, pasaporte o permiso de conducir. | "passport" |
    |  Dirección | string | Dirección extraída (solo permiso de conducir) ||
    |  Region | string | Valor extraído de región, estado, provincia, etc. (solo permiso de conducir) |  |

* Siga nuestra [**guía de migración de Form Recognizer v3.0**](v3-migration-guide.md) para obtener información sobre cómo usar la versión preliminar en las aplicaciones y flujos de trabajo.

* Explore nuestra [**API REST (versión preliminar)** ](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) para obtener más información sobre la versión preliminar y las nuevas funcionalidades.

## <a name="next-steps"></a>Pasos siguientes

* Completar un inicio rápido de Form Recognizer:

  > [!div class="nextstepaction"]
  > [Inicio rápido: Uso de la biblioteca cliente de Form Recognizer](quickstarts/try-sdk-rest-api.md)

* Explorar nuestra API REST:

    > [!div class="nextstepaction"]
    > [API v2.1 de Form Recognizer](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707)