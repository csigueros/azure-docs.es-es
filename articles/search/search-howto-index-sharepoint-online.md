---
title: Indexación de datos de SharePoint Online (versión preliminar)
titleSuffix: Azure Cognitive Search
description: Configure un indexador de SharePoint Online para automatizar la indexación del contenido de la biblioteca de documentos en Azure Cognitive Search.
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 61e9787c0a85ad412d3e70cfb2452d288a48d36a
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/27/2021
ms.locfileid: "112983057"
---
# <a name="index-data-from-sharepoint-online"></a>Indexación de datos de SharePoint Online

> [!IMPORTANT] 
> La compatibilidad con SharePoint Online se encuentra actualmente en versión preliminar pública según los [Términos de uso complementarios](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Solicite el acceso](https://aka.ms/azure-cognitive-search/indexer-preview) a esta característica y, después de habilitarlo, use una [API REST de versión preliminar (2020-06-30-preview o posterior)](search-api-preview.md) para indexar el contenido. Actualmente hay compatibilidad limitada con el portal y no la hay con .NET SDK.

En este artículo se explica cómo usar Azure Cognitive Search para indexar documentos (por ejemplo, archivos PDF, documentos de Microsoft Office y otros diversos formatos comunes) almacenados en bibliotecas de documentos de SharePoint Online en un índice de Azure Cognitive Search. En primer lugar, se explican los conceptos básicos de cómo instalar y configurar un indexador. A continuación, se ofrecen más detalles sobre los comportamientos y escenarios que es probable que encuentre.

> [!NOTE]
> SharePoint Online admite un modelo de autorización granular que determina el acceso por usuario en el nivel de documento. El indexador de SharePoint Online no extrae estos permisos en el índice de búsqueda y Cognitive Search no admite la autorización de nivel de documento. Cuando un documento se indexa desde SharePoint Online en un servicio de búsqueda, el contenido está disponible para cualquier persona que tenga acceso de lectura al índice. Si requiere permisos de nivel de documento, debe investigar los filtros de seguridad para recortar los resultados de contenido no autorizado. Para más información, consulte [Filtros de seguridad para limitar los resultados de Azure Cognitive Search mediante las identidades de Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="functionality"></a>Funcionalidad

Un indexador en Azure Cognitive Search es un rastreador que extrae metadatos y datos que permiten búsquedas de un origen de datos. El indexador de SharePoint Online se conecta al sitio de SharePoint Online e indexa los documentos de una o varias bibliotecas de documentos. El indexador proporciona la funcionalidad siguiente:
+ Indexación del contenido de una o varias bibliotecas de documentos de SharePoint Online.
+ Indexación del contenido de las bibliotecas de documentos de SharePoint Online que se encuentran en el mismo inquilino que el servicio Azure Cognitive Search. El indexador no funcionará con los sitios de SharePoint que se encuentren en un inquilino distinto al del servicio Azure Cognitive Search. 
+ El indexador admitirá la indexación incremental, lo que significa que identificará qué contenido de la biblioteca de documentos ha cambiado y solo indexará aquel que se haya actualizado en futuras ejecuciones de indexación. Por ejemplo, si el indexador indexa originalmente 5 archivos PDF, se actualiza 1; luego, si se vuelve a ejecutar el indexador, solo se indexará el PDF que se actualizó.
+ El texto y las imágenes normalizadas se extraen de forma predeterminada de los documentos que se indexaron. Opcionalmente, se puede agregar un conjunto de aptitudes a la canalización para enriquecer aún más el contenido. Puede encontrar más información sobre los conjuntos de aptitudes en el artículo [Conceptos sobre los conjuntos de aptitudes en Azure Cognitive Search](cognitive-search-working-with-skillsets.md).

## <a name="supported-document-formats"></a>Formatos de documento admitidos

El indexador de SharePoint Online de Azure Cognitive Search puede extraer texto de documentos con el siguiente formato:

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="incremental-indexing-and-deletion-detection"></a>Indexación incremental y detección de eliminación
De forma predeterminada, el indexador de SharePoint Online admite la indexación incremental, lo que significa que identificará qué contenido de la biblioteca de documentos ha cambiado y solo indexará aquel que se haya actualizado en futuras ejecuciones de indexación. Por ejemplo, si el indexador indexa originalmente 5 documentos de Word, se actualiza 1; luego, si se vuelve a ejecutar el indexador, solo se volverá a indexar el documento de Word que se actualizó.

La detección de eliminaciones también se admite de forma predeterminada. Esto significa que, si se elimina un documento de una biblioteca de documentos de SharePoint Online, el indexador detectará la eliminación durante una indexación futura y quitará el documento del índice.

## <a name="setting-up-sharepoint-online-indexing"></a>Configuración de la indexación de SharePoint Online
Para configurar el indexador de SharePoint Online, tendrá que realizar algunas acciones en Azure Portal y otras con la API REST en versión preliminar. Esta versión preliminar no es compatible con el SDK.

 En el vídeo siguiente se muestra cómo configurar el indexador de SharePoint Online.
 
> [!VIDEO https://www.youtube.com/embed/QmG65Vgl0JI]

### <a name="step-1-enable-system-assigned-managed-identity"></a>Paso 1: Habilitación de una identidad administrada asignada por el sistema
Cuando se habilita una identidad administrada asignada por el sistema, Azure crea una identidad para el servicio de búsqueda que el indexador puede usar.

![Habilitación de una identidad administrada asignada por el sistema](media/search-howto-index-sharepoint-online/enable-managed-identity.png "Habilitación de una identidad administrada asignada por el sistema")

Después de seleccionar **Guardar**, verá un identificador de objeto que se ha asignado al servicio de búsqueda.

![Identidad administrada asignada por el sistema](media/search-howto-index-sharepoint-online/system-assigned-managed-identity.png "Identidad administrada asignada por el sistema")

### <a name="step-2-create-an-aad-application"></a>Paso 2: Creación de una aplicación de AAD
El indexador de SharePoint Online usará esta aplicación de AAD para la autenticación. 

1.  Acceda a [Azure Portal](https://portal.azure.com/).

1.  Abra el menú del lado izquierdo de la página principal y seleccione **Azure Active Directory** y, luego, **Registros de aplicaciones**. Seleccione **+ Nuevo registro**.
    1.  Proporcione un nombre para la aplicación.
    2.  Seleccione **Inquilino único**.
    3.  No se necesita un URI de redirección.
    4.  Seleccione **Registrar**.

1.  Seleccione **Permisos de API** en el menú de la izquierda y, luego, **Agregar un permiso**, **Microsoft Graph** y **Permisos delegados**. Agregue los siguientes permisos de API: 
    1.  **Delegado: Files.Read.All** 
    2.  **Delegado: Sites.Read.All** 
    3.  **Delegado: User.Read**

    ![Permisos de API delegados](media/search-howto-index-sharepoint-online/delegated-api-permissions.png "Permisos de API delegados")

    El uso de permisos delegados significa que el indexador tendrá acceso al sitio de SharePoint en el contexto del usuario. Por lo tanto, al ejecutar el indexador, solo se tendrá acceso al contenido al que tenga acceso el usuario que ha iniciado sesión. El inicio de sesión de usuario se produce al crear el indexador o al actualizar el origen de datos. El paso de inicio de sesión se describe más adelante en este artículo.

1.  Seleccione la pestaña **Autenticación**. Establezca **Allow public client flows** (Permitir flujos de clientes públicos) en **Sí** y, luego, seleccione **Guardar**.

1.  Seleccione **+ Agregar una plataforma**, después **Aplicaciones móviles y de escritorio** y, luego, vaya a `https://login.microsoftonline.com/common/oauth2/nativeclient` y haga clic en **Configurar**.

    ![Configuración de la autenticación de aplicaciones de AAD](media/search-howto-index-sharepoint-online/aad-app-authentication-configuration.png "Configuración de la autenticación de aplicaciones de AAD")

1.  Proporcione el consentimiento del administrador (solo es necesario para determinados inquilinos).

    Algunos inquilinos están bloqueados y hace falta el consentimiento del administrador para usar estos permisos de API delegados. Si este es el caso, deberá tener dicho consentimiento para esta aplicación de AAD antes de crear el indexador.

    Dado que no todos los inquilinos tienen este requisito, se recomienda omitir primero este paso y continuar con las instrucciones. Sabrá si necesita el consentimiento del administrador si, al crear el indexador, se produce un error en la autenticación que indica que necesita que un administrador apruebe la autenticación. En ese caso, use el siguiente botón para que un administrador de inquilinos le conceda el consentimiento.

    ![Concesión del consentimiento del administrador para aplicaciones de AAD](media/search-howto-index-sharepoint-online/aad-app-grant-admin-consent.png "Concesión del consentimiento del administrador para aplicaciones de AAD")

<a name="create-data-source"></a>

### <a name="step-3-create-data-source"></a>Paso 3: Creación del origen de datos
> [!IMPORTANT] 
> A partir de esta sección, en el resto de los pasos se deberá usar la API REST en versión preliminar. Si no está familiarizado con la API REST de Azure Cognitive Search, se recomienda echar un vistazo a este [inicio rápido](search-get-started-rest.md).

Un origen de datos especifica los datos que se deben indexar, las credenciales necesarias para obtener acceso a estos y las directivas para identificar cambios en los datos de forma eficaz (filas nuevas, modificadas o eliminadas). Varios indexadores pueden usar el mismo origen de datos en el mismo servicio de búsqueda.

Para realizar la indexación de SharePoint, el origen de datos debe tener las siguientes propiedades obligatorias:
+ **name** es el nombre único del origen de datos dentro del servicio de búsqueda.
+ **type**: debe ser "sharepoint". Esta propiedad distingue mayúsculas de minúsculas.
+ **credentials**: proporciona el punto de conexión de SharePoint Online y el identificador de la aplicación de AAD (cliente). Un punto de conexión de SharePoint Online de ejemplo es `https://microsoft.sharepoint.com/teams/MySharePointSite`. Para obtenerlo, vaya a la página principal del sitio de SharePoint y copie la dirección URL desde el explorador.
+ **container**: especifica la biblioteca de documentos que se va a indexar. Puede encontrar más información sobre cómo crear el contenedor en la sección [Control de los documentos que se van a indexar](#controlling-which-documents-are-indexed) de este documento.

Pasos para crear un origen de datos:

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-datasource",
    "type" : "sharepoint",
    "credentials" : { "connectionString" : "SharePointOnlineEndpoint=[SharePoint Online site url];ApplicationId=[AAD App ID]" },
    "container" : { "name" : "defaultSiteLibrary", "query" : null }
}
```

### <a name="step-4-create-an-index"></a>Paso 4: Creación de un índice
El índice especifica los campos de un documento, los atributos y otras construcciones que conforman la experiencia de búsqueda.

Aquí se muestra cómo crear un índice con un campo de contenido que permite búsquedas para almacenar el texto extraído de los documentos de una biblioteca de documentos:

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-index",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "metadata_spo_item_name", "type": "Edm.String", "key": false, "searchable": true, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_path", "type": "Edm.String", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_content_type", "type": "Edm.String", "key": false, "searchable": false, "filterable": true, "sortable": false, "facetable": true },
        { "name": "metadata_spo_item_last_modified", "type": "Edm.DateTimeOffset", "key": false, "searchable": false, "filterable": false, "sortable": true, "facetable": false },
        { "name": "metadata_spo_item_size", "type": "Edm.Int64", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}

```

Para más información, consulte [Creación de un índice (API REST)](/rest/api/searchservice/create-index).

### <a name="step-5-create-an-indexer"></a>Paso 5: Creación de un indexador
Un indexador conecta un origen de datos con un índice de búsqueda de destino y proporciona una programación para automatizar la actualización de datos. Una vez creados el índice y el origen de datos, ya puede crear el indexador:

En esta sección se le pedirá que inicie sesión con las credenciales de su organización que permiten acceso al sitio de SharePoint. Si es posible, se recomienda crear otra cuenta de usuario de la organización y dar a ese nuevo usuario los permisos exactos que quiere que tenga el indexador.

Para crear el indexador, es necesario realizar algunos pasos:

1.  Envíe una solicitud para intentar crear el indexador.

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
        {
          "name" : "sharepoint-indexer",
          "dataSourceName" : "sharepoint-datasource",
          "targetIndexName" : "sharepoint-index",
          "fieldMappings" : [
            { 
              "sourceFieldName" : "metadata_spo_site_library_item_id", 
              "targetFieldName" : "id", 
              "mappingFunction" : { 
                "name" : "base64Encode" 
              } 
            }
          ]
        }
    
    ```

1.  La primera vez que cree el indexador, la operación producirá el siguiente error. Vaya al vínculo que aparece en el mensaje de error. Si no ha ido al vínculo al cabo de 10 minutos, el código expirará y tendrá que volver a crear el [origen de datos](#create-data-source).

    ```http
    {
        "error": {
            "code": "",
            "message": "Error with data source: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code <CODE> to authenticate.  Please adjust your data source definition in order to proceed."
        }
    }
    ```

1.  Proporcione el código que se incluyó en el mensaje de error.

    ![Entrada de código del dispositivo](media/search-howto-index-sharepoint-online/enter-device-code.png "Entrada de código del dispositivo")

1.  El indexador de SharePoint tendrá acceso al contenido de SharePoint con el usuario con sesión iniciada. El usuario que inicia sesión durante este paso será el usuario con sesión iniciada. Por lo tanto, si inicia sesión con una cuenta de usuario que no tiene acceso a un documento de la biblioteca de documentos que quiere indexar, el indexador no tendrá acceso a ese documento.

    Si es posible, se recomienda crear otra cuenta de usuario y dar a ese nuevo usuario los permisos exactos que quiere que tenga el indexador.

1.  Apruebe los permisos que se solicitan.

    ![Aprobación de permisos de API](media/search-howto-index-sharepoint-online/aad-app-approve-api-permissions.png "Aprobación de permisos de API")

1.  Vuelva a enviar la solicitud de creación del indexador. Esta vez la solicitud se debería realizar correctamente. 

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "sharepoint-indexer",
        "dataSourceName" : "sharepoint-datasource",
        "targetIndexName" : "sharepoint-index"
    }
    ```

### <a name="step-6-check-the-indexer-status"></a>Paso 6: Comprobación del estado del indexador
Después de que se ha creado el indexador, realice la solicitud siguiente para comprobar su estado.

```http
GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]
```

Puede encontrar más información sobre el estado del indexador aquí: [Obtención del estado del indexador](/rest/api/searchservice/get-indexer-status).

## <a name="updating-the-data-source"></a>Actualización del origen de datos
Si no hay ninguna actualización del objeto de origen de datos, el indexador se puede ejecutar según una programación sin interacción del usuario. Sin embargo, cada vez que se actualice el objeto de origen de datos de Azure Cognitive Search, deberá volver a iniciar sesión para que se ejecute el indexador. Por ejemplo, si cambia la consulta del origen de datos, tendrá que iniciar sesión de nuevo con `https://microsoft.com/devicelogin` y un nuevo código.

Una vez actualizado el origen de datos, siga estos pasos:

1.  Inicie manualmente una ejecución del indexador.

    ```http
    POST https://[service name].search.windows.net/indexers/sharepoint-indexer/run?api-version=2020-06-30-Preview  
    Content-Type: application/json
    api-key: [admin key]
    ```

    Puede encontrar más información sobre la solicitud de ejecución del indexador aquí: [Ejecución del indexador](/rest/api/searchservice/run-indexer).

1.  Compruebe el estado del indexador. Si la última ejecución del indexador muestra un error que le indica que vaya a `https://microsoft.com/devicelogin`, hágalo y proporcione el nuevo código. 

    ```http
    GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    ```

    Puede encontrar más información sobre el estado del indexador aquí: [Obtención del estado del indexador](/rest/api/searchservice/get-indexer-status).

1.  Inicio de sesión

1.  Vuelva a iniciar manualmente una ejecución del indexador y compruebe el estado de este. Esta vez, la ejecución del indexador debería iniciarse correctamente.

## <a name="indexing-document-metadata"></a>Indexación de los metadatos de los documentos
Si ha establecido el indexador para que indexe los metadatos de los documentos, los siguientes metadatos estarán disponibles para indexarse.

> [!NOTE]
> Los metadatos personalizados no se incluyen en la versión preliminar actual.

| Identificador | Tipo | Descripción | 
| ------------- | -------------- | ----------- |
| metadata_spo_site_library_item_id | Edm.String | La clave de combinación de los identificadores de sitio, biblioteca y elemento que identifica de forma única un elemento en una biblioteca de documentos de un sitio. |
| metadata_spo_site_id | Edm.String | Identificador del sitio de SharePoint Online. |
| metadata_spo_library_id | Edm.String | Identificador de la biblioteca de documentos. |
| metadata_spo_item_id | Edm.String | Identificador del elemento (documento) de la biblioteca. |
| metadata_spo_item_last_modified | Edm.DateTimeOffset | Fecha y hora de la última modificación (UTC) del elemento. |
| metadata_spo_item_name | Edm.String | Nombre del elemento. |
| metadata_spo_item_size | Edm.Int64 | Tamaño (en bytes) del elemento. | 
| metadata_spo_item_content_type | Edm.String | Tipo de contenido del elemento. | 
| metadata_spo_item_extension | Edm.String | Extensión del elemento. |
| metadata_spo_item_weburi | Edm.String | URI del elemento. |
| metadata_spo_item_path | Edm.String | Combinación de la ruta de acceso principal y el nombre del elemento. | 

El indexador de SharePoint Online también admite metadatos específicos de cada tipo de documento. Puede encontrar más información en [Propiedades de los metadatos de contenido usadas en Azure Cognitive Search](search-blob-metadata-properties.md).

<a name="controlling-which-documents-are-indexed"></a>

## <a name="controlling-which-documents-are-indexed"></a>Control de los documentos que se van a indexar
Un único indexador de SharePoint Online puede indexar el contenido de una o varias bibliotecas de documentos. Al crear el origen de datos para indicar las bibliotecas de documentos que quiere indexar, use el parámetro *container*. El parámetro *container* del origen de datos tiene dos propiedades: *name* y *query*. 

### <a name="name"></a>Nombre
La propiedad *name* es obligatoria y debe tener uno de estos tres valores:
+ *defaultSiteLibrary*
    + Indexa todo el contenido de la biblioteca de documentos predeterminada del sitio.
+   *allSiteLibraries*
    + Indexa todo el contenido de todas las bibliotecas de documentos de un sitio. No se indexan las bibliotecas de documentos de un subsitio. Si bien, estas se pueden especificar en la propiedad *query*.
+   *useQuery*
    + Solo se indexa el contenido definido en la propiedad *query*.

### <a name="query"></a>Consultar
La propiedad *query* se compone de pares de palabra clave y valor. A continuación, se muestran las palabras clave que se pueden usar. Los valores son direcciones URL de sitio o de biblioteca de documentos.

> [!NOTE]
> Para obtener el valor de una palabra clave determinada, se recomienda abrir SharePoint Online en un explorador e ir a la biblioteca de documentos que intenta incluir o excluir y copiar el URI desde el explorador. Esta es la forma más fácil de obtener el valor que se va a usar con una palabra clave en la consulta.

| Palabra clave | Descripción de la consulta | Ejemplo |
| ------------- | -------------- | ----------- |
| null | Si es NULL o está vacía, se indexa la biblioteca de documentos predeterminada o todas las bibliotecas de documentos en función del nombre del contenedor. | Se indexa todo el contenido de la biblioteca del sitio predeterminada: <br><br>  ``` "container" : { "name" : "defaultSiteLibrary", "query" : null } ``` |
| includeLibrariesInSite | Se indexa el contenido de todas las bibliotecas del sitio definido en la cadena de conexión. Estas se limitan a los subsitios del sitio. <br><br> El valor de *query* de esta palabra clave debe ser el URI del sitio o del subsitio. | Se indexa todo el contenido de todas las bibliotecas de documentos de mi sitio. <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/mysite" } ``` |
| includeLibrary | Se indexa el contenido de esta biblioteca. <br><br> El valor de *query* de esta palabra clave debe estar en uno de los siguientes formatos: <br><br> Ejemplo 1: <br><br> *includeLibrary=[sitio o subsitio]/[biblioteca de documentos]* <br><br> Ejemplo 2: <br><br> URI copiado desde el explorador. | Se indexa todo el contenido de MyDocumentLibrary: <br><br> Ejemplo 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary" } ``` <br><br> Ejemplo 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| excludeLibrary |  No se indexa el contenido de esta biblioteca. <br><br> El valor de *query* de esta palabra clave debe estar en uno de los siguientes formatos: <br><br> Ejemplo 1: <br><br> *excludeLibrary=[URI de sitio o subsitio]/[biblioteca de documentos]* <br><br> Ejemplo 2: <br><br> URI copiado desde el explorador. | Se indexa todo el contenido de todas las bibliotecas, excepto de MyDocumentLibrary: <br><br> Ejemplo 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mysite.sharepoint.com/subsite1; excludeLibrary=https://mysite.sharepoint.com/subsite1/MyDocumentLibrary" } ``` <br><br> Ejemplo 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/teams/mysite; excludeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |

## <a name="index-by-file-type"></a>Índice por tipo de archivo
Puede controlar qué documentos se indexan y cuáles se omiten.

### <a name="include-documents-having-specific-file-extensions"></a>Inclusión de los documentos que tienen extensiones de archivo concretas
Puede indexar solo los documentos que tengan las extensiones de nombre de archivo que especifique mediante el parámetro de configuración del indexador `indexedFileNameExtensions`. El valor es una cadena que contiene una lista separada por comas de extensiones de archivo (con un punto inicial). Por ejemplo, para indexar solamente los documentos .PDF y .DOCX, haga lo siguiente:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>Exclusión de los documentos que tienen extensiones de archivo concretas
Puede excluir de la indexación documentos que tengan extensiones de nombre de archivo específicas mediante el parámetro de configuración `excludedFileNameExtensions`. El valor es una cadena que contiene una lista separada por comas de extensiones de archivo (con un punto inicial). Por ejemplo, para indexar todo el contenido excepto el que tenga las extensiones .PNG y .JPEG, haga lo siguiente:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Si los dos parámetros `indexedFileNameExtensions` y `excludedFileNameExtensions` existen, Azure Cognitive Search mira primero en `indexedFileNameExtensions` y, luego, en `excludedFileNameExtensions`. Esto significa que si la misma extensión de archivo está presente en las dos listas, se excluirá de la indexación.

## <a name="handling-errors"></a>Control de errores
De forma predeterminada, el indexador de SharePoint Online se detiene cuando encuentra un documento con un tipo de contenido no admitido (por ejemplo, una imagen). Por supuesto, puede usar el parámetro `excludedFileNameExtensions` para omitir determinados tipos de contenido. Sin embargo, puede que tenga que indexar documentos sin conocer de antemano todos los tipos de contenido posibles. Para reanudar la indexación cuando se encuentra un tipo de contenido no admitido, establezca el parámetro de configuración `failOnUnsupportedContentType` en false:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
}
```

Con algunos documentos, Azure Cognitive Search no puede determinar el tipo de contenido o no puede procesar un documento de otro tipo de contenido admitido. Para ignorar este modo de error, establezca el parámetro de configuración `failOnUnprocessableDocument` en false:

```http
"parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Azure Cognitive Search limita el tamaño de los documentos que se indexan. Estos límites se documentan en [Límites de servicio en Azure Cognitive Search](./search-limits-quotas-capacity.md). Los documentos de gran tamaño se tratan como errores de forma predeterminada. Sin embargo, puede indexar los metadatos de almacenamiento de documentos demasiado grandes si establece el parámetro de configuración `indexStorageMetadataOnlyForOversizedDocuments` en true:

```http
"parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

También puede continuar con la indexación si se producen errores en cualquier punto del procesamiento, bien mientras se analizan documentos o se agregan estos a un índice. Para omitir un número específico de errores, establezca los parámetros de configuración `maxFailedItems` y `maxFailedItemsPerBatch` en los valores deseados. Por ejemplo:

```http
{
    ... other parts of indexer definition
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

## <a name="see-also"></a>Consulte también
+ [Indexadores de Azure Cognitive Search](search-indexer-overview.md)
+ [Propiedades de los metadatos de contenido usadas en Azure Cognitive Search](search-blob-metadata-properties.md)