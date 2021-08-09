---
title: Búsqueda de contenido cifrado de Azure Blob Storage
titleSuffix: Azure Cognitive Search
description: Aprenda a indexar y extraer texto de documentos cifrados en Azure Blob Storage con Azure Cognitive Search.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.openlocfilehash: e70361b747cac10b602efcf590963b707c7d5da7
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2021
ms.locfileid: "111559005"
---
# <a name="how-to-index-encrypted-blobs-using-blob-indexers-and-skillsets-in-azure-cognitive-search"></a>Indexación de blobs cifrados mediante indexadores de blobs y conjuntos de aptitudes en Azure Cognitive Search

En este artículo se muestra cómo usar [Azure Cognitive Search](search-what-is-azure-search.md) para indexar documentos que se han cifrado previamente en [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) mediante [Azure Key Vault](../key-vault/general/overview.md). Normalmente, los indexadores no pueden extraer contenido de archivos cifrados porque no tienen acceso a la clave de cifrado. Sin embargo, al aprovechar la aptitud personalizada [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile), seguida de [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md), puede proporcionar acceso controlado a la clave para descifrar los archivos y, a continuación, extraer contenido de ellos. De este modo, se desbloquea la capacidad de indexar estos documentos sin poner en peligro el estado de cifrado de los documentos almacenados.

A partir de documentos completos (texto no estructurado) cifrados previamente, como PDF, HTML, DOCX y PPTX en Azure Blob Storage, en esta guía se usan Postman y las API de REST de Búsqueda para realizar las tareas siguientes:

> [!div class="checklist"]
> * Definir una canalización que descifre los documentos y extraiga texto de ellos.
> * Definir un índice para almacenar la salida.
> * Ejecute la canalización para crear y cargar el índice.
> * Explore los resultados mediante la búsqueda de texto completo y una sintaxis de consulta enriquecida.

Si no tiene una suscripción a Azure, abra una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

En este ejemplo se da por supuesto que ha cargado los archivos en Azure Blob Storage y que los ha cifrado en el proceso. Si necesita ayuda la primera vez que se carguen y cifren los archivos, consulte [este tutorial](../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md), donde se explica cómo hacerlo.

+ [Almacenamiento de Azure](https://azure.microsoft.com/services/storage/)
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) en la misma suscripción que Azure Cognitive Search. El almacén de claves debe tener la **eliminación temporal** y la **protección contra purgas** habilitada.
+ [Azure Cognitive Search](search-create-service-portal.md) en un [nivel de servicio facturable](search-sku-tier.md#tier-descriptions) (básico o superior, en cualquier región).
+ [Función de Azure](https://azure.microsoft.com/services/functions/)
+ [Aplicación de escritorio Postman](https://www.getpostman.com/)

## <a name="1---create-services-and-collect-credentials"></a>1 - Creación de servicios y recopilación de credenciales

### <a name="set-up-the-custom-skill"></a>Configuración de la aptitud personalizada

En este ejemplo se usa el proyecto [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) de ejemplo del repositorio de GitHub [Azure Search Power Skills](https://github.com/Azure-Samples/azure-search-power-skills). En esta sección, implementará la aptitud en una instancia de Azure Functions para que se pueda usar en un conjunto de aptitudes. Un script de implementación integrado crea un recurso de Azure Functions cuyo nombre comienza por **psdbf-function-app-** y carga la aptitud. Se le pedirá que especifique una suscripción y un grupo de recursos. Asegúrese de elegir la misma suscripción en la que reside la instancia de Azure Key Vault.

A nivel operativo, la aptitud DecryptBlobFile toma la dirección URL y el token de SAS de cada blob como entradas, y genera el archivo descifrado descargado mediante el contrato de referencia de archivo que Azure Cognitive Search espera. Recuerde que DecryptBlobFile necesita la clave de cifrado para realizar el descifrado. Como parte de la configuración, también creará una directiva de acceso que concede a la función DecryptBlobFile acceso a la clave de cifrado de Azure Key Vault.

1. Haga clic en el botón **Implementar en Azure** que se encuentra en la [página de aterrizaje de DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile#deployment), que abrirá la plantilla de Resource Manager proporcionada en Azure Portal.

1. Seleccione **la suscripción en la que existe la instancia de Azure Key Vault** (esta guía no funcionará si selecciona otra suscripción) y seleccione un grupo de recursos existente o cree uno (si lo crea, también tendrá que seleccionar una región para implementarlo).

1. Seleccione **Revisar y crear**, asegúrese de que está de acuerdo con los términos y, luego, seleccione **Crear** para implementar Azure Functions.

    ![Plantilla de Resource Manager en Azure Portal](media/indexing-encrypted-blob-files/arm-template.jpg "Plantilla de Resource Manager en Azure Portal")

1. Espere a que finalice la implementación.

1. Vaya a la instancia de Azure Key Vault en Azure Portal. [Cree una directiva de acceso](../key-vault/general/assign-access-policy-portal.md) en Azure Key Vault que conceda acceso mediante clave a la aptitud personalizada.
 
    1. En **Configuración**, seleccione **Directivas de acceso** y, después, **Agregar directiva de acceso**.
     
       ![Agregar directiva de acceso en Keyvault](media/indexing-encrypted-blob-files/keyvault-access-policies.jpg "Directivas de acceso de Keyvault")

    1. En **Configurar desde plantilla**, seleccione **Azure Data Lake Storage o Azure Storage**.

    1. En el caso de la entidad de seguridad, seleccione la instancia de Azure Functions que ha implementado. Para buscarla, use el prefijo de recurso que se usó para crearla en el paso 2, que tiene un valor de prefijo predeterminado de **psdbf-function-app**.

    1. No seleccione nada en la opción de la aplicación autorizada.
     
        ![Agregar plantilla de directiva de acceso en Keyvault](media/indexing-encrypted-blob-files/keyvault-add-access-policy.jpg "Plantilla de directiva de acceso en Keyvault")

    1. Asegúrese de hacer clic en **Guardar** en la página de directivas de acceso antes de desplazarse para agregar realmente la directiva de acceso.
     
         ![Guardar directiva de acceso en Keyvault](media/indexing-encrypted-blob-files/keyvault-save-access-policy.jpg "Guardar directiva de acceso de Keyvault")

1. Vaya a la función **psdbf-function-app** en el portal y tome nota de las siguientes propiedades, ya que las necesitará más adelante en la guía:

    1. La dirección URL de la función, que se puede encontrar en **Essentials** en la página principal de la función.
    
        ![Dirección URL de la función](media/indexing-encrypted-blob-files/function-uri.jpg "Dónde encontrar la dirección URL de Azure Functions")

    1. El código de la clave del host y, para encontrarlo, es preciso desplazarse a **Claves de la aplicación**, hacer clic para mostrar la clave **predeterminada** y copiar el valor.
     
        ![Código de clave de host de función](media/indexing-encrypted-blob-files/function-host-key.jpg "Dónde encontrar el código de la clave de host de Azure Functions")

### <a name="cognitive-services"></a>Cognitive Services

Tanto el enriquecimiento con inteligencia artificial como la ejecución de un conjunto de aptitudes tienen el respaldo de Cognitive Services, lo que incluye Text Analytics y Computer Vision para el procesamiento de imágenes y del lenguaje natural. Si su objetivo fuera trabajar con un prototipo o un proyecto reales, en este punto aprovisionaría Cognitive Services (en la misma región que Azure Cognitive Search) para poder conectarlo con las operaciones de indexación.

Sin embargo, en este ejercicio puede omitir el aprovisionamiento de recursos, porque Azure Cognitive Search puede conectarse a Cognitive Services en segundo plano y proporcionarle 20 transacciones gratuitas por cada ejecución del indexador. Después de procesar 20 documentos, el indexador generará un error, salvo que se asocie una clave de Cognitive Services al conjunto de aptitudes. En el caso de proyectos mayores, planee aprovisionamiento de Cognitive Services en el nivel S0 de pago por uso. Para más información, consulte [Asociación de Cognitive Services](cognitive-search-attach-cognitive-services.md). Tenga en cuenta que se requiere una clave de Cognitive Services para ejecutar un conjunto de aptitudes con más de 20 documentos incluso aunque ninguna de las aptitudes cognitivas seleccionadas se conecte a Cognitive Services (por ejemplo, con el conjunto de aptitudes proporcionado si no se le agregan aptitudes).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

El último componente es Azure Cognitive Search, que se puede [crear en el portal](search-create-service-portal.md). Puede usar el nivel Gratis para completar esta guía. 

Al igual que con Azure Functions, dedique un momento a recopilar la clave de administrador. Además, cuando empiece a estructurar las solicitudes, deberá proporcionar el punto de conexión y la clave de la API de administración que se usan para autenticar cada solicitud.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Obtención de una clave de API de administración y una dirección URL para Azure Cognitive Search

1. [Inicie sesión en Azure Portal](https://portal.azure.com/) y, en la página **Información general** del servicio de búsqueda, obtenga el nombre del servicio de búsqueda. Para confirmar el nombre del servicio, revise la dirección URL del punto de conexión. Si la dirección URL del punto de conexión fuera `https://mydemo.search.windows.net`, el nombre del servicio sería `mydemo`.

2. En **Configuración** > **Claves**, obtenga una clave de administrador para tener derechos completos en el servicio. Se proporcionan dos claves de administrador intercambiables para lograr la continuidad empresarial, por si necesitara sustituir una de ellas. Puede usar la clave principal o secundaria en las solicitudes para agregar, modificar y eliminar objetos.

   ![Obtención del nombre del servicio y las claves de consulta y administrador](media/search-get-started-javascript/service-name-and-keys.png)

Todas las solicitudes enviadas al servicio necesitan una clave de API en el encabezado. Una clave válida genera la confianza, solicitud a solicitud, entre la aplicación que la envía y el servicio que se encarga de ella.

## <a name="2---set-up-postman"></a>2: Configuración de Postman

Instale y configure Postman.

### <a name="download-and-install-postman"></a>Descarga e instalación de Postman

1. Descargue el [código fuente de la recopilación de Postman](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json).
1. Seleccione **Archivo** > **Importar** para importar el código fuente en Postman.
1. Seleccione la pestaña **Recopilaciones** el botón **...** (puntos suspensivos).
1. Seleccione **Editar**. 
   
   ![Aplicación Postman que muestra la navegación](media/indexing-encrypted-blob-files/postman-edit-menu.jpg "Ir al menú de edición de Postman")
1. En el cuadro de diálogo **Edit** (Editar), seleccione la pestaña **Variables** (Variables). 

En la pestaña **Variables** (Variables) puede agregar valores para los intercambios de Postman cada vez que encuentre una variable específica entre llaves dobles. Por ejemplo, Postman reemplaza el símbolo `{{admin-key}}` por el valor actual que estableciera para `admin-key`. Postman realiza esta sustitución en las direcciones URL, los encabezados, el cuerpo de la solicitud, etc. 

Para obtener el valor de `admin-key`, use la clave de API de administrador de Azure Cognitive Search que anotó anteriormente. Establezca `search-service-name` en el nombre del servicio Azure Cognitive Search que usa. Establezca `storage-connection-string` mediante el valor de la pestaña **Claves de acceso** de la cuenta de almacenamiento y establezca `storage-container-name` en el nombre del contenedor de blobs de la cuenta de almacenamiento en la que se almacenan los archivos cifrados. Establezca `function-uri` en la dirección URL de Azure Functions que anotó antes y establezca `function-code` en el código de la clave del host de Azure Functions que anotó antes. Puede dejar los valores predeterminados para los demás valores.

![Pestaña de variables de la aplicación Postman](media/indexing-encrypted-blob-files/postman-variables-window.jpg "Ventana de variables de Postman")

| Variable    | Dónde obtenerla |
|-------------|-----------------|
| `admin-key` | En la página **Claves** del servicio Azure Cognitive Search.  |
| `search-service-name` | Nombre del servicio Azure Cognitive Search. La dirección URL es `https://{{search-service-name}}.search.windows.net`. |
| `storage-connection-string` | En la cuenta de almacenamiento, en la pestaña **Claves de acceso**, seleccione **key1** > **Cadena de conexión**. |
| `storage-container-name` | El nombre del contenedor de blobs que tiene los archivos cifrados que se van a indexar. |
| `function-uri` |  En Azure Functions, en **Essentials**, en la página principal. |
| `function-code` | En Azure Functions es preciso desplazarse a **Claves de la aplicación**, hacer clic para mostrar la clave **predeterminada** y copiar el valor. |
| `api-version` | Déjela como **2020-06-30**. |
| `datasource-name` | Déjela como **encrypted-blobs-ds**. |
| `index-name` | Déjela como **encrypted-blobs-idx**. |
| `skillset-name` | Déjela como **encrypted-blobs-ss**. |
| `indexer-name` | Déjela como **encrypted-blobs-ixr**. |

### <a name="review-the-request-collection-in-postman"></a>Revisión de la colección de solicitudes en Postman

Cuando ejecute esta guía, debe generar cuatro solicitudes HTTP:

- **Una solicitud PUT para crear el índice**: este índice contiene los datos que usa y devuelve Azure Cognitive Search.
- **Una solicitud POST para crear el origen de datos**: Este origen de datos conecta su servicio Azure Cognitive Search a su cuenta de almacenamiento y, por lo tanto, a los archivos de blob cifrados. 
- **Una solicitud PUT para crear el conjunto de aptitudes**: El conjunto de aptitudes especifica la definición de la aptitud personalizada de Azure Functions que descifrará los datos del archivo de blob y [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) para extraer el texto de cada documento una vez que se ha descifrado.
- **Una solicitud PUT para crear el indexador**: Al ejecutar el indexador, se leen los datos, se aplica el conjunto de aptitudes y se almacenan los resultados. Debe ejecutar esta solicitud en último lugar.

El [código fuente](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json) contiene una recopilación de Postman que tiene las cuatro solicitudes, así como algunas solicitudes de seguimiento útiles. Para generar las solicitudes, en Postman, seleccione la pestaña de las solicitudes y, después, seleccione **Enviar** para cada una de ellas.

## <a name="3---monitor-indexing"></a>3: Supervisión de la indexación

La indexación y el enriquecimiento comienzan en cuanto se envía la solicitud de creación de indexador. En función del número de documentos que haya en la cuenta de almacenamiento, la indexación puede tardar un tiempo. Para averiguar si el indexador todavía está en ejecución, use la solicitud **Get Indexer Status** que se proporciona como parte de la colección de Postman y examine la respuesta para saber si el indexador está en ejecución o para ver la información de los errores y advertencias.  

Si usa el nivel Gratis, se espera el siguiente mensaje: `"Could not extract content or metadata from your document. Truncated extracted text to '32768' characters"`. Este mensaje aparece porque en el nivel Gratis la indexación de blobs tiene un límite de [32 000 en la extracción de caracteres](search-limits-quotas-capacity.md#indexer-limits). En los niveles superiores no verá este mensaje para este conjunto de datos. 

## <a name="4---search"></a>4: Búsqueda

Una vez que finaliza la ejecución del indexador, puede ejecutar algunas consultas para comprobar que los datos se han descifrado e indexado correctamente. Vaya al servicio Azure Cognitive Search en el portal y use el [explorador de búsqueda](search-explorer.md) para ejecutar consultas sobre los datos indexados.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha indexado correctamente los archivos cifrados, puede [realizar iteraciones en esta canalización agregando más aptitudes cognitivas](cognitive-search-defining-skillset.md). Esto le permitirá enriquecer y obtener información adicional sobre los datos.

Si trabaja con datos de cifrado doble, es posible que desee investigar las características de cifrado de índice disponibles en Azure Cognitive Search. Aunque el indexador necesita datos descifrados para la indexación, una vez que el índice existe, se puede cifrar mediante una clave administrada por el cliente. Esto garantizará que los datos siempre estén cifrados cuando están en reposo. Para más información, consulte [Configuración de claves administradas por el cliente para el cifrado de datos en Azure Cognitive Search](search-security-manage-encryption-keys.md).