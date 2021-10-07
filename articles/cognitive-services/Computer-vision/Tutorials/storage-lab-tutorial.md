---
title: 'Tutorial: Generación de metadatos para imágenes de Azure'
titleSuffix: Azure Cognitive Services
description: En este tutorial, aprenderá a integrar el servicio Azure Computer Vision en una aplicación web para generar metadatos para las imágenes.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 07/06/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: f97f01ecb1b11af6ca4292c8b2bfd3ebdb50e943
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360262"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Tutorial: Uso de Computer Vision para generar metadatos de imágenes en Azure Storage

En este tutorial, aprenderá a integrar el servicio Azure Computer Vision en una aplicación web para generar metadatos para las imágenes cargadas. Esto resulta útil para los escenarios de [administración de activos digitales (DAM)](../overview.md#computer-vision-for-digital-asset-management); por ejemplo, si una empresa quiere generar rápidamente leyendas descriptivas o palabras clave de búsqueda para todas sus imágenes.

Usará Visual Studio para escribir una aplicación web MVC que acepte imágenes cargadas por los usuarios y las almacene en Azure Blob Storage. Aprenderá a leer y escribir blobs en C# y a usar los metadatos del blob para adjuntar información adicional a los blobs que cree. A continuación, enviará cada imagen cargada por el usuario a Computer Vision API para generar un título y buscar metadatos para la imagen. Por último, puede implementar la aplicación en la nube mediante Visual Studio.

En este tutorial se muestra cómo realizar las siguientes acciones:

> [!div class="checklist"]
> * Crear una cuenta de almacenamiento y contenedores de almacenamiento mediante Azure Portal
> * Crear una aplicación web en Visual Studio e implementarla en Azure
> * Usar Computer Vision API para extraer información de las imágenes
> * Adjuntar metadatos a imágenes de Azure Storage
> * Comprobar los metadatos de las imágenes con el [Explorador de Azure Storage](http://storageexplorer.com/)

> [!TIP]
> La sección [Uso de Computer Vision para generar metadatos](#Exercise5) es más pertinente para el análisis de imágenes. Vaya a dicha sección si solo desea ver cómo se integra el análisis de imágenes en una aplicación establecida.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/cognitive-services) antes de empezar. 

## <a name="prerequisites"></a>Prerrequisitos

- [Visual Studio 2017 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs.aspx), o cualquier versión superior, con las cargas de trabajo "Desarrollo de ASP.NET y web" y "Desarrollo de Azure" instaladas.
- La herramienta [Explorador de Azure Storage](http://storageexplorer.com/) instalada.

<a name="Exercise1"></a>
## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

En esta sección, utilizará [Azure Portal](https://portal.azure.com?WT.mc_id=academiccontent-github-cxa) para crear una cuenta de almacenamiento. A continuación, creará un par de contenedores: uno para almacenar las imágenes cargadas por el usuario y otro para almacenar las miniaturas de imagen generadas a partir de las imágenes cargadas.

1. En el explorador, abra [Azure Portal](https://portal.azure.com?WT.mc_id=academiccontent-github-cxa). Si se le pide que inicie sesión, hágalo con su cuenta Microsoft.
1. Para crear una cuenta de almacenamiento, haga clic en **+ Crear un recurso** en la cinta de opciones de la izquierda. A continuación, haga clic en **Almacenamiento** y luego en **Cuenta de almacenamiento**.

    ![Creación de una cuenta de almacenamiento](Images/new-storage-account.png)
1. Escriba un nombre único para la cuenta de almacenamiento en el campo **Nombre** y asegúrese de que se muestre una marca de verificación verde junto a él. El nombre es importante, ya que forma una parte de la dirección URL mediante la cual se accede a los blobs creados en esta cuenta. Coloque la cuenta de almacenamiento en un nuevo grupo de recursos llamado "IntellipixResources" y seleccione la región más cercana. Para finalizar, haga clic en el botón **Revisar y crear** situado en la parte inferior de la pantalla para crear la nueva cuenta de almacenamiento.
    > [!NOTE]
    > Los nombres de las cuentas de almacenamiento pueden tener entre 3 y 24 caracteres y solo pueden incluir números y letras en minúscula. Además, el nombre que escriba debe ser único en Azure. Si alguien ha elegido el mismo nombre, se le notificará que el nombre no está disponible con un signo de exclamación rojo en el campo **Nombre**.
   
    ![Especificación de parámetros para una nueva cuenta de almacenamiento](Images/create-storage-account.png)
1. Haga clic en **Grupos de recursos** en la cinta de opciones de la izquierda. A continuación, haga clic en el grupo de recursos "IntellipixResources".

    ![Apertura del grupo de recursos](Images/open-resource-group.png)
1. En la pestaña que se abre para el grupo de recursos, haga clic en la cuenta de almacenamiento que ha creado. Si la cuenta de almacenamiento aún no aparece, puede hacer clic en **Actualizar** en la parte superior de la pestaña hasta que aparezca.

    ![Apertura de la nueva cuenta de almacenamiento](Images/open-storage-account.png)
1. En la pestaña de la cuenta de almacenamiento, haga clic en **Blobs** para ver una lista de contenedores asociados a esta cuenta.

    ![Botón para ver los blobs](Images/view-containers.png)

1. Actualmente, la cuenta de almacenamiento no tiene contenedores. Antes de poder crear un blob, debe crear un contenedor para almacenarlo. Haga clic en **+ Contenedor** para crear un nuevo contenedor. Escriba `photos` en el campo **Nombre** y seleccione **Blob** en el campo **Nivel de acceso público**. A continuación, haga clic en **Aceptar** para crear un contenedor llamado "photos".

    > De manera predeterminada, los contenedores y su contenido son privados. Al seleccionar **Blob** como nivel de acceso, los blobs del contenedor "photos" son accesibles públicamente, pero no hacen que el propio contenedor sea público. Esto es lo que quiere, porque las imágenes almacenadas en el contenedor "photos" se vincularán desde una aplicación web. 

    ![Creación del contenedor "photos"](Images/create-photos-container.png)

1. Repita el paso anterior para crear un contenedor llamado "thumbnails". Una vez más, asegúrese de que el campo **Nivel de acceso público** del contenedor esté establecido en **Blob**.
1. Confirme que ambos contenedores se muestren en la lista de contenedores de esta cuenta de almacenamiento y que los nombres estén escritos correctamente.

    ![Nuevos contenedores](Images/new-containers.png)

1. Cierre la pantalla "Blob service". Haga clic en **Claves de acceso** en el menú del lado izquierdo de la pantalla de la cuenta de almacenamiento y, a continuación, haga clic en el botón **Copiar** situado junto a **CLAVE** para **key1**. Pegue esta clave de acceso en su editor de texto favorito para su uso posterior.

    ![Copia de la clave de acceso](Images/copy-storage-account-access-key.png)

Ahora ha creado una cuenta de almacenamiento para almacenar las imágenes cargadas en la aplicación que va a crear y contenedores en los que almacenar las imágenes. 

<a name="Exercise2"></a>
## <a name="run-azure-storage-explorer"></a>Ejecución del Explorador de Azure Storage

El [Explorador de Azure Storage](http://storageexplorer.com/) es una herramienta gratuita que proporciona una interfaz gráfica para trabajar con Azure Storage en equipos que ejecutan Windows, macOS y Linux. Proporciona la mayor parte de la misma funcionalidad que Azure Portal y ofrece otras características, como la capacidad de ver los metadatos de los blobs. En esta sección, usará el Explorador de Microsoft Azure Storage para ver los contenedores que ha creado en la sección anterior.

1. Si no ha instalado el Explorador de Storage o desea asegurarse de que está ejecutando la versión más reciente, vaya a http://storageexplorer.com/, descárguelo e instálelo.
1. Inicie el Explorador de Storage. Si se le pide que inicie sesión, hágalo con su cuenta Microsoft, la misma que usó para iniciar sesión en Azure Portal. Si no ve la cuenta de almacenamiento en el panel izquierdo del Explorador de Storage, haga clic en el botón **Administrar cuentas** resaltado a continuación y asegúrese de que tanto la cuenta Microsoft como la suscripción usada para crear la cuenta de almacenamiento se hayan agregado al Explorador de Storage.

    ![Administración de cuentas en el Explorador de Storage](Images/add-account.png)

1. Haga clic en la flecha pequeña situada junto a la cuenta de almacenamiento para mostrar su contenido y, a continuación, haga clic en la flecha situada junto a **Contenedores de blobs**. Confirme que los contenedores que ha creado se muestren en la lista.

    ![Visualización de los contenedores de blobs](Images/storage-explorer.png)

Los contenedores están vacíos actualmente, pero esto cambiará una vez que se implemente la aplicación y empiece a cargar fotos. Tener el Explorador de Storage instalado le permitirá ver fácilmente lo que la aplicación escribe en Blob Storage.

<a name="Exercise3&quot;></a>
## <a name=&quot;create-a-new-web-app-in-visual-studio&quot;></a>Creación de una aplicación web en Visual Studio

En esta sección, creará una nueva aplicación web en Visual Studio y agregará código para implementar la funcionalidad básica necesaria para cargar imágenes, escribirlas en Blob Storage y mostrarlas en una página web.

1. Inicie Visual Studio y use el comando **Archivo -> Nuevo -> Proyecto** para crear un nuevo proyecto de **aplicación web ASP.NET** de Visual C# llamado &quot;Intellipix&quot; (abreviatura de &quot;Imágenes inteligentes").

    ![Creación de un nuevo proyecto de aplicación web](Images/new-web-app.png)

1. En el cuadro de diálogo "Nueva aplicación web ASP.NET", asegúrese de que esté seleccionado **MVC**. A continuación, haga clic en **Aceptar**.

    ![Creación de un nuevo proyecto MVC de ASP.NET](Images/new-mvc-project.png)

1. Tómese un momento para revisar la estructura del proyecto en el Explorador de soluciones. Entre otras cosas, hay una carpeta llamada **Controllers** (Controladores) que contiene los controladores MVC del proyecto y una carpeta llamada **Views** (Vistas) que contiene las vistas del proyecto. Trabajará con los recursos de estas carpetas y otras a medida que implemente la aplicación.

    ![El proyecto en el Explorador de soluciones](Images/project-structure.png)

1. Use el comando **Depurar -> Iniciar sin depurar** de Visual Studio (o pulse **Ctrl+F5**) para iniciar la aplicación en el explorador. Este es el aspecto de la aplicación en su estado actual:

    ![Aplicación inicial](Images/initial-application.png)

1. Cierre el explorador y vuelva a Visual Studio. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **Intellipix** y seleccione **Administrar paquetes NuGet...** . Haga clic en **Examinar**. A continuación, escriba `imageresizer` en el cuadro de búsqueda y seleccione el paquete NuGet llamado **ImageResizer**. Por último, haga clic en **Instalar** para instalar la versión estable más reciente del paquete. ImageResizer contiene las API que va a utilizar para crear miniaturas de imágenes a partir de las imágenes cargadas en la aplicación. Acepte los cambios y acepte las licencias que se le presenten.

    ![Instalación de ImageResizer](Images/install-image-resizer.png)

1. Repita este proceso para agregar el paquete NuGet llamado **WindowsAzure.Storage** al proyecto. Este paquete contiene las API para acceder a Azure Storage desde aplicaciones de .NET. Acepte los cambios y acepte las licencias que se le presenten.

    ![Instalación de WindowsAzure.Storage](Images/install-storage-package.png)

1. Abra el archivo _Web.config_ y agregue la siguiente instrucción a la sección ```<appSettings>```, reemplazando ACCOUNT_NAME por el nombre de la cuenta de almacenamiento que creó en la primera sección y ACCOUNT_KEY por la clave de acceso que guardó.

    ```xml
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=ACCOUNT_NAME;AccountKey=ACCOUNT_KEY" />
    ```

1. Abra el archivo llamado *_Layout.cshtml* en la carpeta **Views/Shared** del proyecto. En la línea 19, cambie "Application name" (Nombre de la aplicación) a "Intellipix". La línea debería ser similar a esta:

    ```C#
    @Html.ActionLink("Intellipix", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

    > [!NOTE]
    > En un proyecto MVC de ASP.NET, el archivo *_Layout.cshtml* es una vista especial que actúa como plantilla para otras vistas. Normalmente, en este archivo se define el contenido del encabezado y el pie de página que es común a todas las vistas.

1. Haga clic con el botón derecho en la carpeta **Models** del proyecto y use el comando **Agregar -> Clase...** para agregar un archivo de clase llamado *BlobInfo.cs* a la carpeta. A continuación, reemplace la clase vacía **BlobInfo** por la siguiente definición de clase:

    ```C#
    public class BlobInfo
    {
        public string ImageUri { get; set; }
        public string ThumbnailUri { get; set; }
        public string Caption { get; set; }
    }
    ```

1. Abra el archivo *HomeController.cs*, que encontrará en la carpeta **Controllers** (Controladores) del proyecto, y agregue las siguientes instrucciones `using` al principio del mismo:

    ```C#
    using ImageResizer;
    using Intellipix.Models;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Configuration;
    using System.Threading.Tasks;
    using System.IO;
    ```

1. Reemplace el método **Index** del archivo *HomeController.cs* por la siguiente implementación:

    ```C#
    public ActionResult Index()
    {
        // Pass a list of blob URIs in ViewBag
        CloudStorageAccount account = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);
        CloudBlobClient client = account.CreateCloudBlobClient();
        CloudBlobContainer container = client.GetContainerReference("photos");
        List<BlobInfo> blobs = new List<BlobInfo>();
    
        foreach (IListBlobItem item in container.ListBlobs())
        {
            var blob = item as CloudBlockBlob;
    
            if (blob != null)
            {
                blobs.Add(new BlobInfo()
                {
                    ImageUri = blob.Uri.ToString(),
                    ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/")
                });
            }
        }
    
        ViewBag.Blobs = blobs.ToArray();
        return View();
    }
    ```

    El nuevo método **Index** enumera los blobs del contenedor `"photos"` y pasa una matriz de objetos **BlobInfo** que representan esos blobs en la vista mediante la propiedad **ViewBag** de MVC de ASP.NET. Más adelante, modificará la vista para enumerar estos objetos y mostrar una colección de miniaturas de fotos. Las clases que usará para acceder a la cuenta de almacenamiento y enumerar los blobs ( **[CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount?view=azure-dotnet&preserve-view=true)** , **[CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient?view=azure-dotnet-legacy&preserve-view=true)** y **[CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer?view=azure-dotnet-legacy&preserve-view=true)** ) proceden del paquete **WindowsAzure.Storage** que instaló mediante NuGet.

1. Agregue el método siguiente a la clase **HomeController** en el archivo *HomeController.cs*:

    ```C#
    [HttpPost]
    public async Task<ActionResult> Upload(HttpPostedFileBase file)
    {
        if (file != null && file.ContentLength > 0)
        {
            // Make sure the user selected an image file
            if (!file.ContentType.StartsWith("image"))
            {
                TempData["Message"] = "Only image files may be uploaded";
            }
            else
            {
                try
                {
                    // Save the original image in the "photos" container
                    CloudStorageAccount account = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);
                    CloudBlobClient client = account.CreateCloudBlobClient();
                    CloudBlobContainer container = client.GetContainerReference("photos");
                    CloudBlockBlob photo = container.GetBlockBlobReference(Path.GetFileName(file.FileName));
                    await photo.UploadFromStreamAsync(file.InputStream);
    
                    // Generate a thumbnail and save it in the "thumbnails" container
                    using (var outputStream = new MemoryStream())
                    {
                        file.InputStream.Seek(0L, SeekOrigin.Begin);
                        var settings = new ResizeSettings { MaxWidth = 192 };
                        ImageBuilder.Current.Build(file.InputStream, outputStream, settings);
                        outputStream.Seek(0L, SeekOrigin.Begin);
                        container = client.GetContainerReference("thumbnails");
                        CloudBlockBlob thumbnail = container.GetBlockBlobReference(Path.GetFileName(file.FileName));
                        await thumbnail.UploadFromStreamAsync(outputStream);
                    }
                }
                catch (Exception ex)
                {
                    // In case something goes wrong
                    TempData["Message"] = ex.Message;
                }
            }
        }
    
        return RedirectToAction("Index");
    }
    ```

    Este es el método al que se llama al cargar una foto. Almacena cada imagen cargada como un blob en el contenedor `"photos"`, crea una imagen en miniatura a partir de la imagen original mediante el paquete `ImageResizer` y almacena la imagen en miniatura como un blob en el contenedor `"thumbnails"`.

1. Abra el archivo *Index.cshmtl* de la carpeta **Views/Home** del proyecto y reemplace su contenido por el código y marcado siguientes:

    ```HTML
    @{
        ViewBag.Title = "Intellipix Home Page";
    }
    
    @using Intellipix.Models
    
    <div class="container" style="padding-top: 24px">
        <div class="row">
            <div class="col-sm-8">
                @using (Html.BeginForm("Upload", "Home", FormMethod.Post, new { enctype = "multipart/form-data" }))
                {
                    <input type="file" name="file" id="upload" style="display: none" onchange="$('#submit').click();" />
                    <input type="button" value="Upload a Photo" class="btn btn-primary btn-lg" onclick="$('#upload').click();" />
                    <input type="submit" id="submit" style="display: none" />
                }
            </div>
            <div class="col-sm-4 pull-right">
            </div>
        </div>
    
        <hr />
    
        <div class="row">
            <div class="col-sm-12">
                @foreach (BlobInfo blob in ViewBag.Blobs)
                {
                    <img src="@blob.ThumbnailUri" width="192" title="@blob.Caption" style="padding-right: 16px; padding-bottom: 16px" />
                }
            </div>
        </div>
    </div>
    
    @section scripts
    {
        <script type="text/javascript" language="javascript">
            if ("@TempData["Message"]" !== "") {
                alert("@TempData["Message"]");
            }
        </script>
    }
    ```

    El lenguaje que se usa aquí es [Razor](http://www.asp.net/web-pages/overview/getting-started/introducing-razor-syntax-c), que permite insertar código ejecutable en el marcado HTML. La instrucción ```@foreach``` hacia la mitad del archivo enumera los objetos **BlobInfo** pasados desde el controlador en **ViewBag** y crea elementos ```<img>``` de HTML a partir de ellos. La propiedad ```src``` de cada elemento se inicializa con el identificador URI del blob que contiene la miniatura de la imagen.

1. Descargue y descomprima el archivo _photos.zip_ del [repositorio de datos de ejemplo de GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/ComputerVision/storage-lab-tutorial). Se trata de una variedad de fotos diferentes que puede usar para probar la aplicación.

1. Guarde los cambios y pulse **Ctrl+F5** para iniciar la aplicación en el explorador. A continuación, haga clic en **Upload a Photo** (Cargar una foto) y cargue una de las imágenes que ha descargado. Confirme que se muestre una versión en miniatura de la foto en la página.

    ![Intellipix con una foto cargada](Images/one-photo-uploaded.png)

1. Upload algunas imágenes más de la carpeta **photos**. Confirme que también aparecen en la página:

    ![Intellipix con tres fotos cargadas](Images/three-photos-uploaded.png)

1. Haga clic con el botón derecho en el explorador y seleccione **Ver código fuente de la página** para ver el código fuente de la página. Busque los elementos ```<img>``` que representan las miniaturas de las imágenes. Observe que las direcciones URL asignadas a las imágenes hacen referencia directamente a los blobs de Blob Storage. Esto se debe a que ha establecido el **Nivel de acceso público** de los contenedores en **Blob**, lo que hace que se pueda acceder públicamente a los blobs que se encuentran dentro.

1. Vuelva al Explorador de Azure Storage (o reinícielo si no lo dejó en ejecución) y seleccione el contenedor `"photos"` en la cuenta de almacenamiento. El número de blobs del contenedor debe ser igual al número de fotos que ha cargado. Haga doble clic en uno de los blobs para descargarlo y ver la imagen almacenada en el blob.

    ![Contenido del contenedor "photos"](Images/photos-container.png)

1. Abra el contenedor `"thumbnails"` en el Explorador de Storage. Abra uno de los blobs para ver las imágenes en miniatura generadas a partir de las cargas de imágenes.

La aplicación aún no ofrece una manera de ver las imágenes originales que ha cargado. Lo ideal es hacer clic en una miniatura de imagen para mostrar la imagen original. Agregará esa característica a continuación.

<a name="Exercise4"></a>
## <a name="add-a-lightbox-for-viewing-photos"></a>Adición de un elemento lightbox para ver fotos

En esta sección, usará una biblioteca gratuita de JavaScript de código abierto para agregar un visor lightbox que permita a los usuarios ver las imágenes originales que han cargado (en lugar de solo las miniaturas de imagen). Se le proporcionan los archivos. Todo lo que tiene que hacer es integrarlos en el proyecto y realizar una pequeña modificación en el archivo *Index.cshtml*.

1. Descargue los archivos _lightbox.css_ y _lightbox.js_ desde el [repositorio de código de GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/storage-lab-tutorial).
1. En el Explorador de soluciones, haga clic con el botón derecho en la carpeta **Scripts** del proyecto y use el comando **Agregar -> Nuevo elemento...** para crear el archivo *lightbox.js*. Pegue el contenido del archivo de ejemplo del [repositorio de código de GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/storage-lab-tutorial/scripts/lightbox.js).

1. Haga clic con el botón derecho en la carpeta "Content" (Contenido) del proyecto y use el comando **Agregar -> Nuevo elemento...** para crear el archivo *lightbox.css*. Pegue el contenido del archivo de ejemplo del [repositorio de código de GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/storage-lab-tutorial/css/lightbox.css).
1. Descargue y descomprima el archivo _buttons.zip_ del repositorio de archivos de datos de GitHub: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/ComputerVision/storage-lab-tutorial. Debe tener cuatro imágenes de botón.

1. Haga clic con el botón derecho en el proyecto Intellipix en el Explorador de soluciones y use el comando **Agregar -> Nueva carpeta** para agregar una carpeta llamada "Images" (Imágenes) al proyecto.

1. Haga clic con el botón derecho en la carpeta **Images** y use el comando **Agregar -> Elemento existente...** para importar las cuatro imágenes que descargó.

1. Abra el archivo *BundleConfig.cs* de la carpeta "App_Start" del proyecto. Agregue la siguiente instrucción al método ```RegisterBundles``` en el archivo **BundleConfig.cs**:

    ```C#
    bundles.Add(new ScriptBundle("~/bundles/lightbox").Include(
              "~/Scripts/lightbox.js"));
    ```

1. En el mismo método, busque la instrucción que crea un elemento ```StyleBundle``` a partir de "~/Content/css" y agregue *lightbox.css* a la lista de hojas de estilos de la agrupación. Esta es la instrucción modificada:

    ```C#
    bundles.Add(new StyleBundle("~/Content/css").Include(
              "~/Content/bootstrap.css",
              "~/Content/site.css",
              "~/Content/lightbox.css"));
    ```

1. Abra el archivo *_Layout.cshtml* de la carpeta **Views/Shared** del proyecto y agregue la siguiente instrucción justo antes de la instrucción ```@RenderSection``` cerca de la parte inferior:

    ```C#
    @Scripts.Render("~/bundles/lightbox")
    ```

1. La tarea final consiste en incorporar el visor lightbox en la página principal. Para ello, abra el archivo *Index.cshtml* (se encuentra en la carpeta **Views/Home** del proyecto) y reemplace el bucle ```@foreach``` por este:

    ```HTML
    @foreach (BlobInfo blob in ViewBag.Blobs)
    {
        <a href="@blob.ImageUri" rel="lightbox" title="@blob.Caption">
            <img src="@blob.ThumbnailUri" width="192" title="@blob.Caption" style="padding-right: 16px; padding-bottom: 16px" />
        </a>
    }
    ```

1. Guarde los cambios y pulse **Ctrl+F5** para iniciar la aplicación en el explorador. A continuación, haga clic en una de las imágenes que cargó anteriormente. Confirme que aparece un visor lightbox y que muestre una vista ampliada de la imagen.

    ![Imagen ampliada](Images/lightbox-image.png)

1. Haga clic en la **X** de la esquina inferior derecha del visor lightbox para descartarlo.

Ahora tiene una manera de ver las imágenes que ha cargado. El siguiente paso es hacer más cosas con esas imágenes.

<a name="Exercise5"></a>
## <a name="use-computer-vision-to-generate-metadata"></a>Uso de Computer Vision para generar metadatos

### <a name="create-a-computer-vision-resource"></a>Creación de un recurso de Computer Vision

Deberá crear un recurso de Computer Vision para su cuenta de Azure; este recurso administra el acceso al servicio Computer Vision de Azure. 

1. Siga las instrucciones que se indican en [Creación de un recurso de Azure Cognitive Services](../../cognitive-services-apis-create-account.md) para crear un recurso de Computer Vision.

1. Luego, vuelva al menú del grupo de recursos y haga clic en la suscripción de Computer Vision API que ha creado. Copie la dirección URL de **Punto de conexión** a cualquier lugar en que pueda recuperarla fácilmente en un momento. Luego, haga clic en **Show access keys** (Mostrar claves de acceso).

    ![Página de Azure Portal con el vínculo a las claves de acceso y a la dirección URL del punto de conexión resaltado](Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. En la ventana siguiente, copie el valor de **KEY 1** en el Portapapeles.

    ![Cuadro de diálogo Administrar claves con el botón Copiar resaltado](Images/copy-vision-key.png)

### <a name="add-computer-vision-credentials"></a>Adición de credenciales de Computer Vision

A continuación, agregará las credenciales necesarias a la aplicación para que pueda acceder a los recursos de Computer Vision.

Vaya al archivo *Web.config* en la raíz del proyecto. Agregue las siguientes instrucciones a la sección `<appSettings>` del archivo, pero reemplace `VISION_KEY` por la clave que copió en el paso anterior y `VISION_ENDPOINT` por la dirección URL que guardó en el paso anterior.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Luego, en el Explorador de soluciones, haga clic con el botón derecho en el proyecto y use el comando **Administrar paquetes NuGet** para instalar el paquete **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**. Dicho paquete contiene los tipos necesarios para llamar a Computer Vision API.

### <a name="add-metadata-generation-code"></a>Incorporación del código de generación de metadatos

A continuación, agregará el código que realmente usa el servicio Computer Vision para crear metadatos para las imágenes.

1. Abra el archivo *HomeController.cs*, que encontrará en la carpeta **Controllers** del proyecto, y agregue las siguientes `using` instrucciones al principio del mismo:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. A continuación, vaya al método **Upload**; este método convierte y carga imágenes en Blob Storage. Agregue el código siguiente inmediatamente después del bloque que comienza por `// Generate a thumbnail` (o al final del proceso de creación de blobs de imágenes). Este código toma el blob que contiene la imagen (`photo`) y utiliza Computer Vision para generar una descripción de ella. Computer Vision API también genera una lista de palabras clave que se aplican a la imagen. Tanto la descripción como las palabras clave generadas se almacenan en los metadatos del blob para que se pueden recuperar más adelante.

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    List<VisualFeatureTypes?> features = new List<VisualFeatureTypes?>() { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. A continuación, vaya al método **Index** en el mismo archivo. Este método enumera los blobs de imágenes almacenados en el contenedor de blobs de destino (como instancias de **IListBlobItem**) y los pasa a la vista de aplicación. Reemplace el bloque `foreach` de este método por el siguiente código. Este código llama a **CloudBlockBlob.FetchAttributes** para obtener los metadatos adjuntos de cada blob. Extrae la descripción generada por el equipo (`caption`) de los metadatos y la agrega al objeto **BlobInfo**, que se pasa a la vista.
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

### <a name="test-the-app"></a>Prueba de la aplicación

Guarde los cambios en Visual Studio y presione **Ctrl+F5** para iniciar la aplicación en el explorador. Use la aplicación para cargar algunas imágenes más, ya sea desde el conjunto de fotos que descargó o desde su propia carpeta. Cuando mueva el puntero sobre una de las nuevas imágenes de la vista, debería aparecer una ventana de información sobre herramientas y mostrar el título de la imagen generado automáticamente.

![La leyenda generada por el equipo](Images/thumbnail-with-tooltip.png)

Para ver todos los metadatos adjuntos, use el Explorador de Azure Storage para ver el contenedor de almacenamiento que usa para las imágenes. Haga clic con el botón derecho en cualquiera de los blobs del mismo y seleccione **Propiedades**. En el cuadro de diálogo, verá una lista de pares clave-valor. La descripción de la imagen generada automáticamente se almacena en el elemento `Caption` y las palabras clave de búsqueda se almacenan en `Tag0`, `Tag1`, etc. Cuando haya terminado, haga clic en **Cancelar** para cerrar el cuadro de diálogo.

![Ventana del cuadro de diálogo Propiedades de imagen en la que se muestran las etiquetas de metadatos](Images/blob-metadata.png)

<a name="Exercise6"></a>
## <a name="add-search-to-the-app"></a>Adición de un cuadro de búsqueda a la aplicación

En esta sección, agregará un cuadro de búsqueda a la página principal, lo que permite a los usuarios realizar búsquedas de palabras clave en las imágenes que han cargado. Las palabras clave son las que genera Computer Vision API y se almacenan en los metadatos de los blobs.

1. Abra el archivo *Index.cshtml* en la carpeta **Views/Home** del proyecto y agregue las siguientes instrucciones al elemento ```<div>``` vacío con el atributo ```class="col-sm-4 pull-right"```:

    ```HTML
    @using (Html.BeginForm("Search", "Home", FormMethod.Post, new { enctype = "multipart/form-data", @class = "navbar-form" }))
    {
        <div class="input-group">
            <input type="text" class="form-control" placeholder="Search photos" name="term" value="@ViewBag.Search" style="max-width: 800px">
            <span class="input-group-btn">
                <button class="btn btn-primary" type="submit">
                    <i class="glyphicon glyphicon-search"></i>
                </button>
            </span>
        </div>
    }
    ```

    Este código y marcado agregan un cuadro de búsqueda y un botón **Search** (Buscar) a la página principal.

1. Abra el archivo *HomeController.cs*, que encontrará en la carpeta **Controllers** (Controladores) del proyecto, y agregue el siguiente método a la clase **HomeController**:

    ```C#
    [HttpPost]
    public ActionResult Search(string term)
    {
        return RedirectToAction("Index", new { id = term });
    }
    ```

    Este es el método al que se llama cuando el usuario hace clic en el botón **Search** (Buscar) agregado en el paso anterior. Actualiza la página e incluye un parámetro de búsqueda en la dirección URL.

1. Reemplace el método **Index** por la siguiente implementación:

    ```C#
    public ActionResult Index(string id)
    {
        // Pass a list of blob URIs and captions in ViewBag
        CloudStorageAccount account = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);
        CloudBlobClient client = account.CreateCloudBlobClient();
        CloudBlobContainer container = client.GetContainerReference("photos");
        List<BlobInfo> blobs = new List<BlobInfo>();

        foreach (IListBlobItem item in container.ListBlobs())
        {
            var blob = item as CloudBlockBlob;

            if (blob != null)
            {
                blob.FetchAttributes(); // Get blob metadata

                if (String.IsNullOrEmpty(id) || HasMatchingMetadata(blob, id))
                {
                    var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;

                    blobs.Add(new BlobInfo()
                    {
                        ImageUri = blob.Uri.ToString(),
                        ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                        Caption = caption
                    });
                }
            }
        }

        ViewBag.Blobs = blobs.ToArray();
        ViewBag.Search = id; // Prevent search box from losing its content
        return View();
    }
    ```

    Observe que el método **Index** ahora acepta el parámetro _id_, que contiene el valor que el usuario ha especificado en el cuadro de búsqueda. Un parámetro _id_ vacío o que falta indica que se deben mostrar todas las fotos.

1. Agregue el método auxiliar siguiente a la clase **HomeController**:

    ```C#
    private bool HasMatchingMetadata(CloudBlockBlob blob, string term)
    {
        foreach (var item in blob.Metadata)
        {
            if (item.Key.StartsWith("Tag") && item.Value.Equals(term, StringComparison.InvariantCultureIgnoreCase))
                return true;
        }

        return false;
    }
    ```

    El método **Index** llama a este método para determinar si las palabras clave de metadatos asociadas a un blob de imagen determinado contienen el término de búsqueda especificado por el usuario.

1. Vuelva a iniciar la aplicación y cargue varias fotos. No dude en usar sus propias fotos, no solo las que se proporcionan con el tutorial.

1. Escriba una palabra clave como "river" (río) en el cuadro de búsqueda. A continuación, haga clic en el botón **Search** (Buscar).

    ![Realización de una búsqueda](Images/enter-search-term.png)

1. Los resultados de la búsqueda variarán en función de lo que haya especificado y de las imágenes que haya cargado. Sin embargo, el resultado debe ser una lista filtrada de imágenes cuyas palabras clave de metadatos incluyan todo o parte de la palabra clave que ha especificado.

    ![Resultados de la búsqueda](Images/search-results.png)

1. Haga clic en el botón Atrás del explorador para volver a mostrar todas las imágenes.

Casi hemos acabado. Es el momento de implementar la aplicación en la nube.

<a name="Exercise7"></a>
## <a name="deploy-the-app-to-azure"></a>Implementar la aplicación en Azure

En esta sección, implementará la aplicación en Azure desde Visual Studio. Permitirá que Visual Studio cree una aplicación web de Azure automáticamente, lo que le evitará tener que ir a Azure Portal y crearla por separado.

1. Haga clic con el botón derecho en el proyecto en el Explorador de soluciones y seleccione **Publicar...** en el menú contextual. Asegúrese de que **Microsoft Azure App Service** y **Crear nuevo** estén seleccionados y, a continuación, haga clic en el botón **Publicar**.

    ![Publicación de la aplicación](Images/publish-1.png)

1. En el siguiente cuadro de diálogo, seleccione el grupo de recursos "IntellipixResources" en **Grupo de recursos**. Haga clic en el botón **Nuevo...** situado junto a "Plan de App Service" y cree un nuevo plan de App Service en la misma ubicación que seleccionó para la cuenta de almacenamiento en [Crear una cuenta de almacenamiento](#Exercise1), aceptando los valores predeterminados en cualquier otro campo. Para finalizar, haga clic en el botón **Crear**.

    ![Creación de una aplicación web de Azure](Images/publish-2.png)

1. Transcurridos unos instantes, la aplicación aparecerá en una ventana del explorador. Observe la dirección URL en la barra de direcciones. La aplicación ya no se ejecuta localmente; está en la Web, donde es accesible públicamente.

    ![El producto final.](Images/vs-intellipix.png)

Si realiza cambios en la aplicación y desea insertar los cambios en la Web, vuelva a realizar el proceso de publicación. Todavía puede probar los cambios localmente antes de publicar en la Web.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si desea seguir trabajando en la aplicación web, consulte la sección [Pasos siguientes](#next-steps). Si no planea seguir usando esta aplicación, debe eliminar todos los recursos específicos de la misma. Para ello, puede eliminar el grupo de recursos que contiene la suscripción de Azure Storage y el recurso de Computer Vision. De esta forma se quita la cuenta de almacenamiento, los blobs que se han cargado en ella y el recurso de App Service necesario para conectarse con la aplicación web ASP.NET. 

Para eliminar el grupo de recursos, abra la pestaña **Grupos de recursos** del portal, vaya al grupo de recursos que usó para el proyecto y haga clic en **Eliminar grupo de recursos** en la parte superior de la vista. Se le pedirá que escriba el nombre del grupo de recursos para confirmar que desea eliminarlo. Una vez eliminado, no se puede recuperar un grupo de recursos.

## <a name="next-steps"></a>Pasos siguientes

Hay mucho más que puede hacer para usar Azure y desarrollar aún más la aplicación Intellipix. Por ejemplo, podría agregar compatibilidad para autenticar a los usuarios, eliminar fotos y, en lugar de obligar al usuario a esperar a que Cognitive Services procese una foto después de una carga, podría usar [Azure Functions](https://azure.microsoft.com/services/functions/?WT.mc_id=academiccontent-github-cxa) para llamar a Computer Vision API de forma asincrónica cada vez que se agrega una imagen al almacenamiento de blobs. También puede realizar cualquier otra operación de análisis de imágenes sobre la imagen, como se describe en la introducción.

> [!div class="nextstepaction"]
> [Introducción a Análisis de imágenes](../overview-image-analysis.md)
