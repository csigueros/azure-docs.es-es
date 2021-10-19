---
title: Modelo de tarjetas de presentación de Form Recognizer
titleSuffix: Azure Applied AI Services
description: Conceptos que comprenden la extracción y el análisis de datos mediante modelos de tarjetas de presentación precompilados
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/05/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: b9517dcab3f748283c7dace99c0e49796dae7f7f
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716308"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-business-card-model"></a>Modelo de tarjetas de presentación de Form Recognizer

## <a name="overview"></a>Información general

El modelo de tarjeta de presentación combina funcionalidades de reconocimiento óptico de caracteres (OCR) eficaces con modelos de aprendizaje profundo para analizar y extraer información clave de imágenes de tarjetas de presentación. La API analiza tarjetas de presentación impresas; extrae información clave, como el nombre, los apellidos, el nombre de la empresa, la dirección de correo electrónico y el número de teléfono; y devuelve una representación de datos JSON estructurada.

***Tarjeta de presentación de ejemplo procesada con la [herramienta de etiquetado de ejemplo de Form Recognizer](https://fott-2-1.azurewebsites.net/):***

:::image type="content" source="./media/overview-business-card.jpg" alt-text="tarjeta de presentación de ejemplo" lightbox="./media/overview-business-card.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>Probar Form Recognizer Studio (versión preliminar)

* Form Recognizer Studio está disponible con la API de versión preliminar (v3.0).

* Extraiga el nombre, el puesto, la dirección, el correo electrónico, el nombre de la empresa y mucho más con nuestra característica de tarjetas de presentación de Form Recognizer Studio:

> [!div class="nextstepaction"]
> [Probar Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=businessCard)

## <a name="try-it-sample-labeling-tool"></a>Prueba: herramienta de etiquetado de ejemplo

Para ver cómo se extraen los datos de las tarjetas de presentación, pruebe nuestra herramienta de etiquetado de ejemplo. Necesitará lo siguiente:

* Una suscripción a Azure: puede [crear una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/).

* Una [instancia de Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) en Azure Portal. Puede usar el plan de tarifa gratuito (`F0`) para probar el servicio. Después de implementar el recurso, haga clic en **Ir al recurso** para obtener la clave de API y el punto de conexión.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

* Un documento de tarjeta de presentación. Puede usar nuestro [documento de tarjeta de presentación de ejemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/businessCard.png).

> [!div class="nextstepaction"]
  > [Probarlo](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

  En la interfaz de usuario de Form Recognizer:

  1. Seleccione **Uso del modelo precompilado para obtener datos**.
  1. Seleccione **Tarjeta de presentación** en el menú desplegable **Tipo de formulario**:

  :::image type="content" source="media/try-business-card.png" alt-text="Captura de pantalla: menú de selección de modelos precompilados desplegable de la herramienta de etiquetado de ejemplo.":::

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

## <a name="supported-languages-and-locales"></a>Idiomas y configuraciones regionales compatibles

>[!NOTE]
 > No es necesario especificar una configuración regional. Se trata de un parámetro opcional. La tecnología de aprendizaje profundo de Form Recognizer detectará automáticamente el idioma del texto de la imagen.

| Modelo | Idioma: código de configuración local | Valor predeterminado |
|--------|:----------------------|:---------|
|Tarjeta de presentación| <ul><li>Inglés (Estados Unidos): en-US</li><li> Inglés (Australia): en-AU</li><li>Inglés (Canadá): en-CA</li><li>Inglés (Reino Unido): en-GB</li><li>Inglés (India): en-IN</li></ul>  | Detección automática |

## <a name="key-value-pair-extraction"></a>Extracción de pares clave-valor

|Nombre| Tipo | Descripción |Salida estandarizada |
|:-----|:----|:----|:----:|
| ContactNames | matriz de objetos | Nombre de contacto |  |
| FirstName | string | Nombre (de nacimiento) del contacto |  |
| LastName | string | Apellidos del contacto |  |
| CompanyNames | Matriz de cadenas | Nombre de la empresa|  |
| Departments | Matriz de cadenas | Departamento u organización del contacto |  |
| JobTitles | Matriz de cadenas | Puesto mostrado del contacto |  |
| Correos electrónicos | Matriz de cadenas | Dirección de correo electrónico de contacto |  |
| Websites | Matriz de cadenas | Sitio web de la empresa |  |
| Direcciones | Matriz de cadenas | Dirección extraída de la tarjeta de presentación | |
| MobilePhones | matriz de números de teléfono | Número de teléfono móvil de la tarjeta de presentación |+1 xxx xxx xxxx |
| Faxes | matriz de números de teléfono | Número de fax de la tarjeta de presentación | +1 xxx xxx xxxx |
| WorkPhones | matriz de números de teléfono | Número de teléfono del trabajo de la tarjeta de presentación | +1 xxx xxx xxxx | 
| OtherPhones     | matriz de números de teléfono | Otros números de teléfono de la tarjeta de presentación | +1 xxx xxx xxxx |

## <a name="form-recognizer-preview-v30"></a>Versión preliminar de Form Recognizer v3.0

 La versión preliminar de Form Recognizer presenta varias características y funcionalidades nuevas.

* Siga nuestra [**guía de migración de Form Recognizer v3.0**](v3-migration-guide.md) para obtener información sobre cómo usar la versión preliminar en las aplicaciones y flujos de trabajo.

* Explore nuestra [**API de REST (versión preliminar)** ](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument) para obtener más información sobre la versión preliminar y las nuevas funcionalidades.

## <a name="next-steps"></a>Pasos siguientes

* Completar un inicio rápido de Form Recognizer:

  > [!div class="nextstepaction"]
  > [Inicio rápido: Uso de la biblioteca cliente de Form Recognizer](quickstarts/try-sdk-rest-api.md)

* Explorar nuestra API de REST:

    > [!div class="nextstepaction"]
    > [API v2.1 de Form Recognizer](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync)
