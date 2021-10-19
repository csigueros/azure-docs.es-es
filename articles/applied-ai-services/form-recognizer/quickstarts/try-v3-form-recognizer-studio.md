---
title: 'Inicio rápido: Form Recognizer Studio | Versión preliminar'
titleSuffix: Azure Applied AI Services
description: Procesamiento de formularios y documentos, extracción de datos y análisis mediante Form Recognizer Studio (versión preliminar)
author: sanjeev3
manager: netahw
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 09/14/2021
ms.author: sajagtap
ms.openlocfilehash: 032669d298ef0aeda3663c303f5575e24cd02354
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129809770"
---
# <a name="get-started-form-recognizer-studio--preview"></a>Introducción: Form Recognizer Studio | Versión preliminar

>[!NOTE]
> Form Recognizer Studio está actualmente en versión preliminar pública. Es posible que algunas características no se admitan o que tengan funcionalidades limitadas. 

La [versión preliminar de Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/) es una herramienta en línea para explorar, comprender e integrar visualmente características del servicio Form Recognizer en las aplicaciones. Comience con la exploración de los modelos previamente entrenados con documentos de ejemplo o los suyos propios. Cree proyectos para crear modelos de formulario personalizados y haga referencia a los modelos en las aplicaciones mediante la [versión preliminar del SDK de Python](try-v3-python-sdk.md) y otros inicios rápidos.

## <a name="prerequisites"></a>Prerrequisitos

* Una [**cuenta de Azure**](https://azure.microsoft.com/free/cognitive-services/) activa.  En caso de no tener ninguna, puede crear una [**cuenta gratuita**](https://azure.microsoft.com/free/).
* Un recurso de [**Form Recognizer**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) o de [**servicios múltiples de Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).

## <a name="additional-steps-for-custom-projects"></a>Pasos adicionales para proyectos personalizados

Además de la cuenta de Azure y un recurso de Form Recognizer o Cognitive Services, necesitará:

### <a name="azure-blob-storage-container"></a>Contenedor de Azure Blob Storage

Una [**cuenta de Azure Blob Storage**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) con un **rendimiento estándar**. Creará contenedores para almacenar y organizar los datos de los blobs en la cuenta de almacenamiento. Si no sabe cómo crear una cuenta de almacenamiento de Azure con un contenedor, siga estos inicios rápidos:

  * [**Creación de una cuenta de almacenamiento**](/azure/storage/common/storage-account-create). Al crear la cuenta de almacenamiento, asegúrese de seleccionar rendimiento **Estándar** en el campo **Detalles de instancia → Rendimiento**.
  * [**Creación de un contenedor**](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container). Al crear un contenedor, establezca el campo **Nivel de acceso público** en **Contenedor** (acceso anónimo de lectura para contenedores y blobs) en la ventana **Nuevo contenedor**.

### <a name="sample-documents-set"></a>Conjunto de documentos de ejemplo

1. Vaya a [Azure Portal](https://ms.portal.azure.com/#home) y desplácese de la manera siguiente: **Su cuenta de almacenamiento** → **Almacenamiento de datos** → **Contenedores**

   :::image border="true" type="content" source="../media/sas-tokens/data-storage-menu.png" alt-text="Captura de pantalla: menú Almacenamiento de datos en Azure Portal.":::

1. Seleccione un **contenedor** de la lista.

1. En el menú de la parte superior de la página, seleccione **Cargar**.

    :::image border="true" type="content" source="../media/sas-tokens/container-upload-button.png" alt-text="Captura de pantalla: botón de carga de contenedores en Azure Portal.":::

1. Aparece la ventana **Cargar blob**.

1. Seleccione los archivos que quiere cargar.

    :::image border="true" type="content" source="../media/sas-tokens/upload-blob-window.png" alt-text="Captura de pantalla: botón Cargar blob en Azure Portal.":::

> [!NOTE]
> De manera predeterminada, Studio usará los documentos de formulario que se encuentran en la raíz del contenedor. Sin embargo, puede usar datos organizados en carpetas si se especifica en los pasos de creación del proyecto de formulario personalizado. *Consulte* [**Organización de los datos en subcarpetas (opcional)** ](/azure/applied-ai-services/form-recognizer/build-training-data-set#organize-your-data-in-subfolders-optional).

## <a name="sign-into-the-form-recognizer-studio-preview"></a>Inicio de sesión en la versión preliminar de Form Recognizer Studio

Una vez completados los requisitos previos, vaya a la [versión preliminar de Form Recognizer Studio](https://formrecognizer.appliedai.azure.com).

1. Seleccione una característica del servicio Form Recognizer en la página principal de Studio.

1. Seleccione la suscripción de Azure, el grupo de recursos y el recurso. (los recursos se pueden cambiar en cualquier momento en "Configuración" en el menú superior).

1. Revise y confirme las selecciones.

:::image border="true" type="content" source="../media/quickstarts/form-recognizer-studio-get-started-v2.gif" alt-text="Ejemplo de introducción a Form Recognizer studio":::

## <a name="layout"></a>Layout

En la vista Diseño:

1. Seleccione el comando Analizar para ejecutar el análisis de diseño en el documento de ejemplo o pruebe el documento mediante el comando Agregar.

1. Observe el texto extraído resaltado, los iconos de tabla que muestran las ubicaciones de tabla extraídas y las marcas de selección están resaltadas.

1. Use los controles de la parte inferior de la pantalla para acercar y alejar y girar la vista del documento.

1. Muestre y oculte las capas de texto, tablas y marcas de selección para centrarse en cada una de ellas a la vez.

1. En la pestaña Resultados de la sección de salida, examine la salida JSON para comprender el formato de respuesta del servicio. Copie y descargue para iniciar la integración.

:::image border="true" type="content" source="../media/quickstarts/layout-get-started-v2.gif" alt-text="Ejemplo de diseño de Form Recognizer":::

## <a name="prebuilt-models"></a>Modelos creados previamente

En la vista pregenerada:

1. En la página principal de Studio, seleccione uno de los modelos precompilados. En este ejemplo, se usa el modelo de factura.

1. Seleccione el comando Analizar para ejecutar el análisis en el documento de ejemplo o pruebe la factura mediante el comando Agregar.

1. En la sección de visualización, observe los campos y valores resaltados y los elementos de las líneas de la factura. También se muestran todo el texto y las tablas que se han extraído.

1. En la pestaña Campos de la sección de salida, observe los campos y valores enumerados y seleccione los elementos de línea que desea ver en un formato de tabla.

1. En la pestaña Resultados de la sección de salida, examine la salida JSON para comprender el formato de respuesta del servicio. Copie y descargue para iniciar la integración.

:::image border="true" type="content" source="../media/quickstarts/prebuilt-get-started-v2.gif" alt-text="Ejemplo pregenerado de Form Recognizer":::

## <a name="custom-model-basics"></a>Conceptos básicos del modelo personalizado

### <a name="getting-started"></a>Introducción

Para crear modelos personalizados, empiece por configurar el proyecto:

1. En la página principal de Studio, seleccione el proyecto de formulario personalizado para abrir la página principal del formulario personalizado.

1. Use el comando "Crear un proyecto" para iniciar el asistente para la configuración del nuevo proyecto.

1. Escriba los detalles del proyecto, seleccione la suscripción y el recurso de Azure y el contenedor de Azure Blob Storage que contiene los datos.

1. Revise y envíe los valores para crear el proyecto.

:::image border="true" type="content" source="../media/quickstarts/1-custom-model-get-started-v2.gif" alt-text="Ejemplo de introducción de proyecto personalizado de Form Recognizer":::

### <a name="basic-flow"></a>Flujo básico

Después del paso de creación del proyecto, en la fase del modelo personalizado:

1. En la vista de etiquetado, defina las etiquetas y sus tipos que le interesa extraer.

1. Seleccione el texto del documento y haga clic en la etiqueta en la lista desplegable o en el panel de etiquetas.

1. Etiquete cuatro documentos más para tener al menos cinco documentos etiquetados.

1. Seleccione el comando Entrenar y escriba el nombre y la descripción del modelo para empezar a entrenar el modelo personalizado.

1. Una vez que el modelo esté listo, use el comando Probar para validarlo con los documentos de prueba y observar los resultados.

:::image border="true" type="content" source="../media/quickstarts/2-custom-model-basic-steps-v2.gif" alt-text="Ejemplo de flujo de trabajo básico de proyecto personalizado de Form Recognizer":::

### <a name="other-features"></a>Otras características

Además, puede ver todos los modelos mediante la pestaña Modelos de la izquierda. En la vista de lista, seleccione los modelos para realizar las siguientes acciones:

1. Probar el modelo desde la vista de lista.

1. Usar el comando Eliminar para eliminar modelos que no son necesarios.

1. Descargar los detalles del modelo para su visualización sin conexión.

1. Seleccionar varios modelos y componerlos en un nuevo modelo que se usará en las aplicaciones.

## <a name="labeling-as-tables"></a>Etiquetado como tablas

Al crear los modelos personalizados, es posible que tenga que extraer colecciones de valores de los documentos. Estas colecciones aparecen en varios formatos. Por ejemplo:

* Colección dinámica de valores (filas) para un conjunto determinado de campos (columnas)

* Colección fija de valores agrupados por un segundo conjunto de campos (filas o columnas)

### <a name="label-as-dynamic-table"></a>Etiquetado como tabla dinámica

Para etiquetar filas dinámicas de datos para un conjunto determinado de campos:

1. Agregue una nueva etiqueta de tipo "Tabla", seleccione el tipo "Tabla dinámica" y asigne un nombre a la etiqueta.

1. Agregue el número de columnas (campos) y filas (para datos) que necesita.

1. Seleccione el texto de la página y, a continuación, haga clic en la celda que se asignará al texto. Repita el procedimiento para todas las filas y columnas de todas las páginas de todos los documentos.

:::image border="true" type="content" source="../media/quickstarts/custom-tables-dynamic.gif" alt-text="Ejemplo de etiquetado como tabla dinámica de Form Recognizer":::

### <a name="label-as-fixed-table"></a>Etiqueta como tabla fija

Para etiquetar colecciones fijas de datos agrupadas por dos conjuntos de campos:

1. Cree una nueva etiqueta de tipo "Tabla", seleccione el tipo "Tabla fija" y asigne un nombre a la etiqueta.

1. Agregue el número de columnas y filas que necesita para los dos conjuntos de campos.

1. Seleccione el texto de la página y, a continuación, haga clic en la celda para asignarla al texto. Repita el procedimiento para otros documentos.

:::image border="true" type="content" source="../media/quickstarts/custom-tables-fixed.gif" alt-text="Ejemplo de etiquetado como tabla fija de Form Recognizer":::

## <a name="labeling-for-signature-detection"></a>Etiquetado para la detección de firmas

Para etiquetar para la detección de firmas:

1. En la vista de etiquetado, cree una nueva etiqueta de tipo "Firma" y asigne un nombre a la etiqueta.

1. Use el comando Región para crear una región rectangular en la ubicación esperada de la firma.

1. Seleccione la región dibujada y haga clic en la etiqueta de tipo firma para asignarla a la región dibujada. Repita el procedimiento para otros documentos.

:::image border="true" type="content" source="../media/quickstarts/custom-signature.gif" alt-text="Ejemplo de etiquetado para la detección de firmas de Form Recognizer":::

## <a name="next-steps"></a>Pasos siguientes

[Introducción a Form Recognizer Studio (versión preliminar)](https://formrecognizer.appliedai.azure.com).
