---
title: 'Tutorial: Uso de las API REST'
description: En este tutorial se describe cómo usar las API REST de Azure Purview para acceder al contenido del catálogo.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: de49c820100140f0822149bba84f7a38f034323b
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473287"
---
# <a name="tutorial-use-the-rest-apis"></a>Tutorial: Uso de las API REST

En este tutorial aprenderá a usar las API REST de Azure Purview. Cualquier persona puede usar las API REST para enviar datos a un catálogo de Azure Purview, incluir el catálogo como parte de un proceso automatizado o crear su propia experiencia de usuario en el catálogo.

En este tutorial, aprenderá a:

> [!div class="checklist"]
>
> * Crear una entidad de servicio (aplicación).
> * Configurar el catálogo para que confíe en la entidad de servicio (aplicación).
> * Consultar la documentación de las API REST.
> * Recopilar los valores necesarios para usar las API REST.
> * Usar el cliente de Postman para llamar a las API REST.
> * Generar un cliente de .NET para llamar a las API REST.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.

## <a name="prerequisites"></a>Prerrequisitos

* Para comenzar, debe tener ya una cuenta de Azure Purview. Si no tiene un catálogo, consulte el [inicio rápido para crear una cuenta de Azure Purview](create-catalog-portal.md).

* Si necesita agregar datos al catálogo, consulte el [tutorial para ejecutar el Starter Kit y examinar los datos](tutorial-scan-data.md).

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en [Azure Portal](https://portal.azure.com).

## <a name="create-a-service-principal-application"></a>Creación de una entidad de servicio (aplicación)

Para que un cliente de la API REST acceda al catálogo, debe tener una entidad de servicio (aplicación) y una identidad que el catálogo reconozca y cuya configuración sea de confianza. Al realizar llamadas de API REST al catálogo, se usa la identidad de la entidad de servicio.

Muchos de los clientes que han usado entidades de servicio existentes (identificadores de aplicación) se han encontrado errores. Por lo tanto, se recomienda crear una nueva entidad de servicio para llamar a las API.

Para crear una entidad de servicio:

1. En [Azure Portal](https://portal.azure.com), busque y seleccione **Azure Active Directory**.
1. En la página **Azure Active Directory**, seleccione **Registros de aplicaciones** en el panel izquierdo.
1. Seleccione **Nuevo registro**.
1. En la página **Registrar una aplicación**:
    1. Escriba un valor en **Nombre** para la aplicación (nombre de la entidad de servicio).
    1. Seleccione **Accounts in this organizational directory only (Solo las cuentas de este directorio organizativo) ( _&lt;nombreDelInquilino&gt;_ solo: inquilino único)** .
    1. En **URI de redirección (opcional)** , seleccione **Web** y escriba un valor (no tiene por qué ser un URI válido).
    1. Seleccione **Registrar**.
1. En la página de la nueva entidad de servicio, copie los valores de **Nombre para mostrar** y **Id. de la aplicación (cliente)** y guárdelos para más adelante.

   El identificador de la aplicación es el valor de `client_id` del código de ejemplo.

Para usar la entidad de servicio (aplicación), debe obtener la contraseña. A continuación, se indica cómo puede hacerlo.

1. En Azure Portal, busque y seleccione **Azure Active Directory** y luego seleccione **Registros de aplicaciones** en el panel izquierdo.
1. Seleccione la entidad de servicio (aplicación) en la lista.
1. Seleccione **Certificados y secretos** en el menú.
1. Seleccione **Nuevo secreto de cliente**.
1. En la página **Add a client secret** (Agregar un secreto de cliente), escriba lo que corresponda en **Descripción**, seleccione una hora de expiración en **Expiración** y, después, **Agregar**.

   En la página **Client secrets** (Secretos de cliente), la cadena de la columna **Valor** del secreto nuevo es la contraseña. Guarde esta cadena.

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="Captura de pantalla que muestra un secreto de cliente.":::

## <a name="configure-your-catalog-to-trust-the-service-principal-application"></a>Configuración del catálogo para que confíe en la entidad de servicio (aplicación)

Para configurar Azure Purview para que confíe en la nueva entidad de servicio:

1. Vaya a la cuenta de Purview.

1. En la página de la **cuenta de Purview**, abra **Purview Studio**.

1. Seleccione **Data Map** (Mapa de datos) en el menú izquierdo.

1. Seleccione **Collections** (Colecciones).

1. Seleccione la colección raíz en el menú de colecciones. Es la colección superior de la lista y tendrá el mismo nombre que la cuenta de Purview.

1. Seleccione la pestaña **Asignaciones de roles**.

1. Desplácese hacia abajo hasta **Data curators** (Seleccionadores de datos) y seleccione el botón **+** (Usuario).

    > [!Note]
    > Solo los administradores de colecciones pueden editar los permisos de una colección. Si no es un administrador de colecciones, póngase en contacto con uno de los administradores que aparecen en la colección raíz. Para más información, consulte la [página de permisos de Purview](catalog-permissions.md).

1. Busque el nombre de la entidad de servicio creada anteriormente que desee asignar y haga clic en su nombre en el panel de resultados.

1. Haga clic en **Guardar**.

Ya ha configurado la entidad de servicio como administrador de la aplicación, con lo que podrá enviar contenido al catálogo.

## <a name="view-the-rest-apis-documentation"></a>Visualización de la documentación de las API REST

Para ver la documentación de API Swagger,descargue [PurviewCatalogAPISwagger.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/PurviewCatalogAPISwagger.zip), extraiga los archivos y abra index.html.

Si quiere más información sobre la API de búsqueda o sugerencias avanzada que proporciona Azure Purview, consulte la documentación del filtro de búsqueda de API REST. El cliente generado por AutoRest no admite actualmente parámetros de búsqueda personalizados. Para solucionar el problema, siga el documento del filtro de búsqueda para definir las clases de filtro en el código como parámetros de llamada de API. El documento index.html contiene ejemplos de estas API.

## <a name="collect-the-necessary-values-to-use-the-rest-apis"></a>Recopilación de los valores necesarios para usar las API REST

Busque y guarde los valores siguientes:

* Identificador de inquilino:
  * En [Azure Portal](https://portal.azure.com), busque y seleccione **Azure Active Directory**.
  * En la sección **Administrar** en el panel izquierdo, seleccione **Propiedades**, busque **Id. del inquilino** y, a continuación, el icono **Copiar en el portapapeles** para guardar su valor.
* Punto de conexión de Atlas:
  * En la [página de cuentas de Azure Purview](https://aka.ms/purviewportal) de Azure Portal, busque y seleccione su cuenta de Azure Purview en la lista.
  * Seleccione **Propiedades**, busque **Atlas Endpoint** (Punto de conexión de Atlas) y seleccione el icono **Copiar en el portapapeles** para guardar su valor. Quite la parte *https://* de la cadena cuando la use más adelante.
* Nombre de cuenta:
  * Extraiga el nombre del catálogo de la cadena de punto de conexión de Atlas. Por ejemplo, si el punto de conexión de Atlas es `https://ThisIsMyCatalog.catalog.purview.azure.com`, el nombre de la cuenta es `ThisIsMyCatalog`.

## <a name="use-the-postman-client-to-call-the-rest-apis"></a>Uso del cliente de Postman para llamar a las API REST

1. Instale el [cliente de Postman](https://www.getpostman.com/).
1. En el cliente, seleccione **Import** (Importar) y use [Test.postman_collection.json](https://raw.githubusercontent.com/Azure/Purview-Samples/master/rest-api/Test.postman_collection.json).
1. Seleccione **Collections** (Colecciones) y, después, **Test** (Probar).
1. Seleccione **Get Token** (Obtener token):
    1. En la dirección URL situada junto a POST, reemplace *&lt;id-del-inquilino&gt;* por el identificador de inquilino que copió en la sección anterior.
    1. Seleccione la pestaña **Body** (Cuerpo) y reemplace los marcadores de posición de la ruta de acceso y el cuerpo del paso anterior.

       Después de seleccionar **Send** (Enviar), el cuerpo de la respuesta contiene una estructura JSON que incluye el nombre *token_de_acceso* y un valor de cadena entre comillas. 
    1. Copie el valor del token de portador (sin comillas) para usarlo en el paso siguiente.

1. Seleccione **/v2/types/typedefs**:
    1. Reemplace el marcador de posición de la ruta de acceso por el valor del punto de conexión de Atlas. Para obtener este valor, vaya a la instancia del catálogo en el portal de Ibiza y haga clic en la información general. 

       El token de portador se establece desde el primer paso (o puede copiarlo manualmente de la pestaña "Authorization" [Autorización]).

    1. Seleccione **Send** (Enviar) para obtener la respuesta.

## <a name="generate-a-net-client-to-call-the-rest-apis"></a>Generación de un cliente de .NET para llamar a las API REST

### <a name="install-autorest"></a>Instalación de AutoRest



1. Instale [Node.js](https://github.com/Azure/autorest/blob/v2/docs/installing-autorest.md).
1. Abra PowerShell y ejecute el comando siguiente:

   ```powershell
   npm install -g autorest@V2
   ```

### <a name="download-rest-api-specszip-and-create-the-client"></a>Descarga de rest-api-specs.zip y creación del cliente

1. Descargue [rest-api-specs.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/rest-api-specs.zip) y extraiga los archivos.
1. Ejecute el siguiente comando en PowerShell desde la carpeta rest-api-scpecs que ha extraído:

   ```powershell
   autorest --input-file=data-plane/preview/purviewcatalog.json --csharp --output-folder=PurviewCatalogClient/CSharp --namespace=PurviewCatalog --add-credentials
   ```

   La salida de este proceso crea una carpeta PurviewCatalogClient y una subcarpeta CSharp en la carpeta rest-api-specs.

### <a name="create-a-sample-net-console-application"></a>Creación de una aplicación de consola de .NET de ejemplo

1. Abra Visual Studio 2019. Estas instrucciones se han probado con la edición Community gratuita.
1. En la página **Crear un nuevo proyecto**, cree un proyecto de **aplicación de consola (.NET Core)** en C#.
1. Copie y pegue el [código de ejemplo](#sample-code-for-the-console-application) proporcionado.
1. Reemplace *accountName*, *servicePrincipalId*, *servicePrincipalKey* y *tenantId* por os valores que recopiló anteriormente.
1. Use el **Explorador de soluciones** para agregar una carpeta denominada **Generated** en el proyecto de Visual Studio.
1. Copie la carpeta rest-api-specs\PurviewCatalogClient\CSharp que creó anteriormente en la carpeta \Generated. Use el Explorador de archivos o el símbolo de la línea de comandos para la operación de copia, que desencadenará Visual Studio para agregar automáticamente los archivos al proyecto.
1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Manage NuGet Packages** (Administrar paquetes NuGet).
1. Seleccione la pestaña **Examinar**. Busque y seleccione **Microsoft.Rest.ClientRuntime**.
1. Asegúrese de que la versión sea al menos la 2.3.21 y seleccione **Instalar**.
1. Compile y ejecute la aplicación.

El código de ejemplo devuelve el número de typedefs que hay en el catálogo y muestra cómo administrar las asignaciones de roles. Para más información, consulte `DoRoleAssignmentOperations()` en el código de ejemplo. Para más información sobre el proyecto, consulte [Configuración del proyecto](https://github.com/Azure/autorest/blob/v2/docs/client/proj-setup.md).

### <a name="sample-code-for-the-console-application"></a>Código de ejemplo para la aplicación de consola

```csharp
using Microsoft.Rest;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PurviewCatalogSdkTest
{
    class Program
    {
        private static string accountName = "{account-name}";
        private static string servicePrincipalId = "{service-principal-id}";
        private static string servicePrincipalKey = "{service-principal-key}";
        private static string tenantId = "{tenant-id}";

        static void Main(string[] args)
        {
            Console.WriteLine("Azure Purview client");

            // You need to change the api path below (e.g. /api) based on what you're trying to call
            string baseUri = string.Format("https://{0}.catalog.purview.azure.com/api", accountName);

            // Get token and set auth
            var svcClientCreds = new TokenCredentials(getToken(), "Bearer");
            var client = new PurviewCatalog.PurviewCatalogServiceRESTAPIDocument(svcClientCreds);
            client.BaseUri = new System.Uri(baseUri);

            // /v2/types/typedefs
            var task = client.TypesREST.GetAllTypeDefsWithHttpMessagesAsync();
            Console.WriteLine("\nURI:\n" + task.Result.Request.RequestUri);
            Console.WriteLine("\nStatus Code:\n" + task.Result.Response.StatusCode);
            Console.WriteLine("\nResult:\n" + JsonConvert.SerializeObject(task.Result.Body, Formatting.Indented));
        }

        // Replace client_id and client_secret with application ID and password value from service principal
        private static string getToken()
        {
            var values = new Dictionary<string, string>

            // The "resource" could be "https://purview.azure.net" or "73c2949e-da2d-457a-9607-fcc665198967"
            {
                { "grant_type", "client_credentials" },
                { "client_id", servicePrincipalId },
                { "client_secret", servicePrincipalKey },
                { "resource", "73c2949e-da2d-457a-9607-fcc665198967" }
            };

            string authUrl = string.Format("https://login.windows.net/{0}/oauth2/token", tenantId);
            var content = new FormUrlEncodedContent(values);

            HttpClient authClient = new HttpClient();
            var bearerResult = authClient.PostAsync(authUrl, content);
            bearerResult.Wait();
            var resultContent = bearerResult.Result.Content.ReadAsStringAsync();
            resultContent.Wait();
            var bearerToken = JObject.Parse(resultContent.Result)["access_token"].ToString();
            var svcClientCreds = new TokenCredentials(bearerToken, "Bearer");

            return bearerToken;
        }
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Administración de orígenes de datos](manage-data-sources.md)
