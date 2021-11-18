---
title: 'Inicio rápido: Etiquetado de formularios, entrenamiento de un modelo y análisis de formularios mediante la herramienta de etiquetado de ejemplo: Form Recognizer'
titleSuffix: Azure Applied AI Services
description: En este inicio rápido, aprenderá a utilizar la herramienta de etiquetado de ejemplo Form Recognizer para etiquetar manualmente documentos de formularios. A continuación, entrenará un modelo personalizado de procesamiento de documentos con los documentos etiquetados y lo empleará para extraer pares clave-valor.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-may-2021, ignite-fall-2021
keywords: procesamiento de documentos
ms.openlocfilehash: 88bc19e245c3272c0ef7bdf958a29da36d668f7c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132710717"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
<!-- markdownlint-disable MD029 -->
# <a name="get-started-with-the-sample-labeling-tool"></a>Introducción a la herramienta de etiquetado de ejemplo

Azure Form Recognizer es un servicio de Azure Applied AI Services basado en la nube que usa modelos de aprendizaje automático para extraer y analizar campos de formulario, texto y tablas de los documentos. Puede usar Form Recognizer para automatizar el procesamiento de datos en aplicaciones y flujos de trabajo, mejorar las estrategias basadas en datos y enriquecer las funcionalidades de búsqueda de documentos. 

La herramienta de etiquetado de ejemplo de Form Recognizer es una herramienta de código abierto que le permite probar las características más recientes de Azure Form Recognizer y los servicios de reconocimiento óptico de caracteres (OCR):

* [Análisis de documentos con la API Layout](#analyze-layout). Pruebe la API Layout para extraer texto, tablas, marcas de selección y estructura de documentos.

* [Análisis de documentos mediante un modelo precompilado](#analyze-using-a-prebuilt-model). Comience con un modelo precompilado para extraer datos de facturas, recibos, documentos de identidad o tarjetas de presentación.

* [Entrene y analice un formulario personalizado](#train-a-custom-form-model). Use un modelo personalizado para extraer datos de documentos específicos en distintos datos empresariales y casos de uso.

## <a name="prerequisites"></a>Prerrequisitos

Necesitará lo siguiente para empezar:

* Una suscripción a Azure: puede [crear una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/)

* Un recurso de Cognitive Services o Form Recognizer. Una vez que tenga la suscripción de Azure, cree un recurso de Form Recognizer de [servicio único](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) o de [varios servicios](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) en Azure Portal para obtener la clave y el punto de conexión. Puede usar el plan de tarifa gratis (`F0`) para probar el servicio y actualizarlo más adelante a un plan de pago para producción.

    > [!TIP]
    > Cree un recurso de Cognitive Services si tiene previsto acceder a varios servicios de Cognitive Services en un único punto de conexión o clave. Para acceder únicamente a Form Recognizer, cree un recurso de Form Recognizer. Tenga en cuenta que necesitará un recurso de servicio único si tiene previsto usar la [autenticación de Azure Active Directory](../../../active-directory/authentication/overview-authentication.md).

## <a name="create-a-form-recognizer-resource"></a>Creación de un recurso de Form Recognizer

[!INCLUDE [create resource](../includes/create-resource.md)]

 :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Captura de pantalla: claves y ubicación del punto de conexión en Azure Portal.":::

## <a name="analyze-using-a-prebuilt-model"></a>Análisis mediante un modelo precompilado

Form Recognizer ofrece varios modelos precompilados entre los que elegir. Cada modelo tiene su propio conjunto de campos admitidos. El modelo que se va a usar para la operación de análisis depende del tipo de documento que se va a analizar. Estos son los modelos precompilados que actualmente admite el servicio Form Recognizer:

* [**Factura**](../concept-invoice.md): extrae texto, marcas de selección, tablas, pares clave-valor e información importante de las facturas.
* [**Recibo**](../concept-receipt.md): extrae texto e información clave de los recibos.
* [**Documento de identificación**](../concept-id-document.md): extrae texto e información clave de permisos de conducir y pasaportes internacionales.
* [**Tarjeta de presentación**](../concept-business-card.md): extrae texto e información clave de las tarjetas de presentación.

1. Vaya a [Form Recognizer Sample Tool](https://fott-2-1.azurewebsites.net/) (Herramienta de ejemplo de Form Recognizer).

1. En la página principal de la herramienta de ejemplo, seleccione **Use prebuilt model to get data** (Usar un modelo precompilado para obtener datos).

    :::image type="content" source="../media/label-tool/prebuilt-1.jpg" alt-text="Análisis de resultados del diseño de Form Recognizer":::

1. Seleccione el **tipo de formulario** que desea analizar en la ventana desplegable.

1. Elija una dirección URL para el archivo que quiere analizar entre las opciones siguientes:

    * [**Una factura de ejemplo**](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf).
    * [**Un documento de identidad de ejemplo**](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png).
    * [**Una imagen de un recibo de ejemplo**](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg).
    * [**Una imagen de una tarjeta de presentación de ejemplo**](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg).

1. En el campo **Origen: Dirección URL** pegue la dirección URL seleccionada y seleccione el botón **Capturar**.

1. En el campo **Form recognizer service endpoint** (Punto de conexión de servicio de Form Recognizer) pegue el punto de conexión que obtuvo con la suscripción de Form Recognizer.

1. En el campo **Clave de API**, pegue la clave de suscripción que obtuvo del recurso de Form Recognizer.

    :::image type="content" source="../media/fott-select-form-type.png" alt-text="Captura de pantalla: seleccione la ventana desplegable Tipo de formulario.":::

1. Seleccione **Run analysis** (Ejecutar análisis). La herramienta de etiquetado de ejemplo de Form Recognizer llamará a la API de análisis precompilado y analizará el documento.

1. Vea los resultados: vea los pares clave-valor extraídos, los elementos de línea, el texto resaltado extraído y las tablas detectadas.

    :::image type="content" source="../media/label-tool/prebuilt-2.jpg" alt-text="Resultados de análisis del modelo de factura de Form Recognizer":::

1. Descargue el archivo de salida JSON para ver los resultados detallados.

    * El nodo "readResults" contiene cada línea de texto con su posición de cuadro de límite correspondiente en la página.
    * El nodo "selectionMarks" muestra todas las marcas de selección (casilla, botón de opción) y si su estado es "seleccionado" o "no seleccionado".
    * En la sección "pageResults" se incluyen las tablas extraídas. Para cada tabla, se extraen el texto, el índice de filas y columnas, la expansión de filas y columnas, el rectángulo de selección, etc.
    * El campo "documentResults" contiene información de pares clave-valor y de elementos de línea para las partes más importantes del documento.

## <a name="analyze-layout"></a>Análisis de diseño

La API Layout de Azure Form Recognizer extrae texto, tablas, marcas de selección e información de estructura de documentos (PDF, TIFF) e imágenes (JPG, PNG, BMP).

1. Vaya a [Form Recognizer Sample Tool](https://fott-2-1.azurewebsites.net/) (Herramienta de ejemplo de Form Recognizer).

1. En la página principal de la herramienta de ejemplo, seleccione **Use layout to get text, tables and selection marks** (Usar el diseño para obtener texto, tablas y marcas de selección).

     :::image type="content" source="../media/label-tool/layout-1.jpg" alt-text="Configuración de conexión para la herramienta Form Recognizer de diseño.":::

1. En el campo **Form recognizer service endpoint** (Punto de conexión de servicio de Form Recognizer) pegue el punto de conexión que obtuvo con la suscripción de Form Recognizer.

1. En el campo **Clave de API**, pegue la clave de suscripción que obtuvo del recurso de Form Recognizer.

1. En el campo **Origen: Dirección URL** pegue la siguiente dirección URL `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/layout-page-001.jpg` y seleccione el botón **Capturar**.

1. Seleccione **Run Layout** (Ejecutar la API Layout). La herramienta de etiquetado de ejemplo de Form Recognizer llamará a la API Analyze Layout y analizará el documento.

    :::image type="content" source="../media/fott-layout.png" alt-text="Captura de pantalla: Ventana desplegable Layout.":::

1. Vea los resultados: vea el texto resaltado que se ha extraído, así como las marcas de selección y las tablas detectadas.

    :::image type="content" source="../media/label-tool/layout-3.jpg" alt-text="Configuración de conexión para la herramienta Form Recognizer.":::

1. Descargue el archivo de salida JSON para ver los resultados detallados del diseño.
     * El nodo `readResults` contiene cada línea de texto con su correspondiente posición de cuadro de límite en la página.
     * El nodo `selectionMarks` muestra todas las marcas de selección (casilla, botón de opción) y si su estado es `selected` o `unselected`.
     * En la sección `pageResults` se incluyen las tablas extraídas. Para cada tabla, se extraen el texto, el índice de filas y columnas, la expansión de filas y columnas, el rectángulo de selección, etc.

## <a name="train-a-custom-form-model"></a>Entrenar un modelo de formulario personalizado

Entrene un modelo personalizado para analizar y extraer datos de formularios y documentos específicos de la empresa. La API es un programa de aprendizaje automático entrenado para reconocer campos de formulario dentro del contenido distintivo y extraer pares clave-valor y datos de tablas. Necesitará al menos cinco ejemplos del mismo tipo de formulario para empezar y el modelo personalizado se puede entrenar con o sin conjuntos de datos etiquetados.

### <a name="prerequisites-for-training-a-custom-form-model"></a>Requisitos previos para entrenar un modelo de formulario personalizado

* Un contenedor de blobs de Azure Storage que contenga un conjunto de datos de entrenamiento. Asegúrese de que todos los documentos de entrenamiento tienen el mismo formato. Si tiene formularios en varios formatos, organícelos en subcarpetas basadas en un formato común. Para este proyecto puede usar nuestro [conjunto de datos de ejemplo](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample_data_without_labels.zip).

* Configuración de CORS

    [CORS (uso compartido de recursos entre orígenes)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) debe configurarse en la cuenta de almacenamiento de Azure para que sea accesible desde Form Recognizer Studio. Para configurar CORS en Azure Portal, necesitará acceso a la hoja CORS de la cuenta de almacenamiento.

    :::image type="content" source="../media/quickstarts/storage-cors-example.png" alt-text="Captura de pantalla que muestra la configuración de CORS para una cuenta de almacenamiento.":::

    1. Seleccione la hoja CORS de la cuenta de almacenamiento.

    1. Empiece por crear una nueva entrada de CORS en Blob service.

    1. Establezca **Orígenes permitidos** en **https://formrecognizer.appliedai.azure.com** .

    1. Seleccione las ocho opciones disponibles de **Métodos permitidos**.

    1. Apruebe todos los **encabezados permitidos** y los **encabezados expuestos**; para ello, escriba * en cada campo.

    1. Establezca la **antigüedad máxima** en 120 segundos o cualquier valor aceptable.

    1. Haga clic en el botón Guardar de la parte superior de la página para guardar los cambios.

    CORS ahora debe estar configurado para usar la cuenta de almacenamiento de Form Recognizer Studio.

### <a name="use-the-sample-labeling-tool"></a>Uso de la herramienta de etiquetado de ejemplo

1. Vaya a [Form Recognizer Sample Tool](https://fott-2-1.azurewebsites.net/) (Herramienta de ejemplo de Form Recognizer).

1. En la página principal de la herramienta de ejemplo, seleccione **Use custom form to train a model with labels and get key value pairs** (Usar un formulario personalizado para entrenar un modelo con etiquetas y obtener pares clave-valor).

    :::image type="content" source="../media/label-tool/custom-1.jpg" alt-text="Entrene un modelo personalizado.":::

1. Selección de **Nuevo proyecto**

    :::image type="content" source="../media/fott-new-project.png" alt-text="Captura de pantalla: seleccione un nuevo símbolo del sistema del proyecto.":::

#### <a name="create-a-new-project"></a>Creación de un nuevo proyecto

Configure los campos de **Configuración del proyecto** con los valores siguientes:

1. **Nombre para mostrar**. Dé un nombre al proyecto.

1. **Token de seguridad**. Cada proyecto generará automáticamente un token de seguridad que se puede usar para cifrar o descifrar los valores de configuración confidenciales del proyecto. Puede buscar los tokens de seguridad en la configuración de la aplicación. Para ello, seleccione el icono de engranaje situado en la esquina inferior de la barra de navegación izquierda.

1. **Conexión de origen**. La herramienta de etiquetado de ejemplo se conecta a un origen (los formularios originales que cargó) y a un destino (las etiquetas creadas y los datos de salida). Las conexiones se pueden configurar y compartir entre proyectos. Usan un modelo extensible de proveedores, por lo que puede agregar fácilmente nuevos proveedores de origen y destino.

    * Cree una conexión y seleccione el botón **Agregar conexión**. Rellene los campos con los siguientes valores:

    > [!div class="checklist"]
    >
    > * **Nombre para mostrar**. Asigne un nombre a la conexión.
    > * **Descripción**. Agregue una breve descripción.
    > * **Dirección URL de SAS**. Pegue la dirección URL de la firma de acceso compartido (SAS) del contenedor de Azure Blob Storage.

    * Para recuperar la dirección URL de la firma de acceso compartido para los datos de entrenamiento del modelo personalizado, vaya al recurso de almacenamiento en Azure Portal y seleccione la pestaña **Explorador de Storage**. Vaya al contenedor, haga clic con el botón derecho y seleccione **Obtener firma de acceso compartido**. Es importante obtener la firma de acceso compartido para el contenedor, no para la propia cuenta de almacenamiento. Asegúrese de que están seleccionados los permisos de **lectura**, **escritura**, **eliminación** y **enumeración** y haga clic en **Crear**. A continuación, copie el valor de la sección **URL** en una ubicación temporal. Debe tener el formato `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

       :::image type="content" source="../media/quickstarts/get-sas-url.png" alt-text="Ubicación de SAS.":::

1. **Ruta de acceso de la carpeta** (opcional).  Si los formularios de origen se encuentran dentro de una carpeta del contenedor de blobs, especifique el nombre de la carpeta.

1. **URI de servicio del servicio Form Recognizer**: la dirección URL del punto de conexión de Form Recognizer.

1. **Clave de API**. La clave de suscripción de Form Recognizer.

1. **Versión de API** Mantenga el valor v2.1 (valor predeterminado).

1. **Descripción** (opcional). Describa el proyecto.

    :::image type="content" source="../media/label-tool/connections.png"  alt-text="Configuración de conexión":::

#### <a name="label-your-forms"></a>Etiquetado de formularios

  :::image type="content" source="../media/label-tool/new-project.png"  alt-text="Página del nuevo proyecto":::

Al crear o abrir un proyecto, se abrirá la ventana principal del editor de etiquetas. El editor de etiquetas consta de tres partes:

* Un panel de vista previa de tamaño variable que contiene una lista desplazable de formularios de la conexión de origen.
* El panel principal del editor que permite aplicar etiquetas.
* El panel del editor de etiquetas que permite a los usuarios modificar, bloquear, reordenar y eliminar etiquetas.

##### <a name="identify-text-and-tables"></a>Identificación de textos y tablas

Seleccione **Run OCR on all files** (Ejecutar OCR en todos los archivos) en el panel izquierdo para obtener la información de diseño del texto de cada documento. La herramienta de etiquetado dibujará los cuadros de límite alrededor de cada elemento de texto.

También mostrará las tablas que se hayan extraído automáticamente. Seleccione el icono de tabla o cuadrícula en la parte izquierda del documento para ver la tabla extraída. Como el contenido de la tabla se extrae automáticamente, este no se etiquetará, sino que se basará en la extracción automatizada.

  :::image type="content" source="../media/label-tool/table-extraction.png" alt-text="Visualización de tablas mediante la herramienta de etiquetado de ejemplo.":::

##### <a name="apply-labels-to-text"></a>Aplicación de etiquetas a texto

A continuación, creará etiquetas y las aplicará a los elementos de texto que quiere que analice el modelo. Tenga en cuenta que el conjunto de datos de etiquetas de ejemplo incluye campos etiquetados; agregaremos otro campo.

Use el panel del editor de etiquetas para crear una etiqueta que quiera identificar:

1. Seleccione el signo más ( **+** ) para crear una etiqueta.

1. Escriba el nombre de la etiqueta "Total".

1. Seleccione **ENTRAR** para guardar la etiqueta.

1. En el editor principal, seleccione el valor total en los elementos de texto resaltados.

1. Seleccione la etiqueta Total que va a aplicar al valor o presione la tecla correspondiente del teclado. Las teclas numéricas se asignan como teclas de acceso rápido para las diez primeras etiquetas. Puede volver a ordenar las etiquetas con los iconos de flecha arriba y abajo del panel del editor de etiquetas.

    > [!Tip]
    > Tenga en cuenta las siguientes sugerencias cuando vaya a etiquetar los formularios:
    >
    > * Solo se puede aplicar una etiqueta a cada elemento de texto seleccionado.
    >
    > * Cada etiqueta solo se puede aplicar una vez por página. Si un valor aparece varias veces en el mismo formulario, cree etiquetas diferentes para cada instancia. Por ejemplo, "factura n.º 1", "factura n.º 2", etc.
    > * Las etiquetas no pueden abarcar varias páginas.
    > * Etiquete los valores tal como aparecen en el formulario; no intente dividir un valor en dos partes con dos etiquetas diferentes. Por ejemplo, un campo de dirección debe etiquetarse con una sola etiqueta incluso si abarca varias líneas.
    > * No incluya claves en los campos etiquetados, solo los valores.
    > * Los datos de la tabla deben detectarse automáticamente y estarán disponibles en el archivo JSON de salida final, en la sección “pageResults”. Sin embargo, si el modelo no detecta todos los datos de la tabla, también puede etiquetar y entrenar un modelo para detectar tablas. Consulte [Entrenamiento de un modelo personalizado | Etiquetado de los formularios](../label-tool.md#label-your-forms)
    > * Use los botones situados a la derecha de **+** para buscar, reordenar y eliminar las etiquetas, así como cambiarles el nombre.
    > * Para quitar una etiqueta aplicada sin eliminar la etiqueta en sí, seleccione el rectángulo etiquetado en la vista de documento y presione la tecla Supr.
    >

1. Siga los pasos anteriores para etiquetar los cinco formularios del conjunto de datos de ejemplo.

  :::image type="content" source="../media/label-tool/custom-1.jpg" alt-text="Etiquete los ejemplos.":::

#### <a name="train-a-custom-model"></a>Entrenamiento de un modelo personalizado

Elija el icono Train (Entrenar) en el panel izquierdo para abrir la página de entrenamiento. A continuación, seleccione el botón **Train** (Entrenar) para empezar a entrenar el modelo. Una vez completado el proceso de entrenamiento, verá la siguiente información:

* **Id. del modelo**: el identificador del modelo que se ha creado y entrenado. Cada llamada de entrenamiento crea un nuevo modelo con su propio identificador. Copie esta cadena en una ubicación segura; la necesitará si desea realizar llamadas de predicción mediante la [API REST](./try-sdk-rest-api.md?pivots=programming-language-rest-api) o la [biblioteca cliente](./try-sdk-rest-api.md).

* **Precisión media**: el promedio de precisión del modelo. Puede mejorar la precisión del modelo si etiqueta más formularios y vuelve a realizar el entrenamiento para crear otro modelo. Se recomienda empezar por etiquetar cinco formularios y realizar un análisis y pruebas de los resultados; después, si es necesario, agregar más formularios.
* La lista de etiquetas y la precisión estimada por etiqueta.

    :::image type="content" source="../media/label-tool/custom-3.jpg" alt-text="Herramienta de vista de entrenamiento.":::

#### <a name="analyze-a-custom-form"></a>Análisis de un formulario personalizado

1. Seleccione el icono de **análisis** (bombilla) de la izquierda para probar el modelo. 

1. Seleccione el **archivo local** de origen y vaya a un archivo para seleccionar del conjunto de datos de ejemplo que descomprimió en la carpeta de prueba. 

1. Elija el botón **Ejecutar análisis** para obtener las predicciones de pares clave-valor, texto y tablas del formulario. La herramienta aplicará etiquetas en los cuadros de límite e informará de la confianza de cada etiqueta.

   :::image type="content" source="../media/analyze.png" alt-text="Vista de entrenamiento.":::

Eso es todo. Ha aprendido a usar la herramienta de ejemplo Form Recognizer para modelos precompilados, de diseño y personalizados de Form Recognizer. También ha aprendido a analizar un formulario personalizado con datos etiquetados manualmente. Ahora puede probar un SDK o la API REST de la biblioteca cliente de Form Recognizer.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Explorar el inicio rápido de uso de la API REST o el SDK de la biblioteca cliente de Form Recognizer](../quickstarts/get-started-sdk-rest-api.md)