---
title: Generación de tokens de firma de acceso compartido (SAS) para contenedores y blobs con Azure Portal
description: Generación de tokens de firma de acceso compartido (SAS) para contenedores y blobs en Azure Portal
ms.topic: how-to
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.date: 09/23/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 6e1b145c5efa9135a198cd6623c450f5965a6c2c
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129387950"
---
# <a name="generate-sas-tokens-for-storage-containers"></a>Generación de tokens de SAS para contenedores de almacenamiento

 En este artículo, aprenderá a generar tokens de firma de acceso compartido (SAS) de delegación de usuarios para contenedores de Azure Blob Storage. Un token de SAS de delegación de usuarios se firma con credenciales de Azure Active Directory (Azure AD) en lugar de con claves de almacenamiento de Azure. Proporciona acceso delegado y seguro a los recursos de la cuenta de almacenamiento de Azure.
En líneas generales, así es cómo funciona: la aplicación proporciona el token de SAS al almacenamiento de Azure como parte de una solicitud. Si el servicio de almacenamiento confirma que la firma SAS es válida, la solicitud se autoriza. Si la firma no se considera válida, la solicitud se rechaza con el código de error 403 (prohibido).

Azure Blob Storage ofrece tres tipos de recursos:

* Las cuentas de **almacenamiento** proporcionan un espacio de nombres único en Azure para los datos.
* Los **contenedores** se encuentran en las cuentas de almacenamiento y organizan los conjuntos de blobs.
* Los **blobs** se encuentran en los contenedores y almacenan los datos binarios y de texto.

> [!NOTE]
>
> * Si la cuenta de almacenamiento de Azure está protegida por una red virtual o un firewall, no puede conceder acceso mediante un token de SAS. Tendrá que usar una [**identidad administrada**](managed-identity-byos.md) para conceder acceso al recurso de almacenamiento.
>
> * La [**identidad administrada**](managed-identity-byos.md) admite cuentas de Azure Blob Storage de acceso privado y público.
>

## <a name="when-to-use-a-shared-access-signature"></a>¿Cuándo debe usar una firma de acceso compartido?

* Si usa contenedores de almacenamiento con acceso público, puede optar por usar un token de SAS para conceder acceso limitado a los recursos de almacenamiento.

* Al entrenar un modelo personalizado, el conjunto ensamblado de documentos de entrenamiento **debe** cargarse en un contenedor de Azure Blob Storage. Puede conceder permiso a los recursos de entrenamiento con un token de SAS de delegación de usuarios.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesitará lo siguiente:

* Una [**cuenta de Azure**](https://azure.microsoft.com/free/cognitive-services/) activa.  En caso de no tener ninguna, puede crear una [**cuenta gratuita**](https://azure.microsoft.com/free/).
* Un recurso de [**Form Recognizer**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) o de [**servicios múltiples de Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).
* Una [**cuenta de Azure Blob Storage**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) con un **rendimiento estándar**. Creará contenedores para almacenar y organizar los datos de los blobs en la cuenta de almacenamiento. Si no sabe cómo crear una cuenta de almacenamiento de Azure con un contenedor, siga estos inicios rápidos:

  * [**Creación de una cuenta de almacenamiento**](/azure/storage/common/storage-account-create). Al crear la cuenta de almacenamiento, asegúrese de seleccionar rendimiento **Estándar** en el campo **Detalles de instancia → Rendimiento**.
  * [**Creación de un contenedor**](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container). Al crear un contenedor, establezca el campo **Nivel de acceso público** en **Contenedor** (acceso anónimo de lectura para contenedores y blobs) en la ventana **Nuevo contenedor**.

## <a name="upload-your-documents"></a>Carga de los documentos

1. Vaya a [Azure Portal](https://ms.portal.azure.com/#home) y desplácese de la manera siguiente:  **Su cuenta de almacenamiento** → **Almacenamiento de datos** → **Contenedores**

   :::image type="content" source="media/sas-tokens/data-storage-menu.png" alt-text="Captura de pantalla: menú Almacenamiento de datos en Azure Portal.":::

1. Seleccione un **contenedor** de la lista.
1. En el menú de la parte superior de la página, seleccione **Cargar**.

    :::image type="content" source="media/sas-tokens/container-upload-button.png" alt-text="Captura de pantalla: botón de carga de contenedores en Azure Portal.":::

1. Aparece la ventana **Cargar blob**.
1. Seleccione los archivos que quiere cargar.

    :::image type="content" source="media/sas-tokens/upload-blob-window.png" alt-text="Captura de pantalla: botón Cargar blob en Azure Portal.":::

> [!NOTE]
> De manera predeterminada, la API REST usará documentos de formulario que se encuentren en la raíz de su contenedor. Sin embargo, puede usar datos organizados en subcarpetas si se especifican en la llamada API. *Consulte* [**Organización de los datos en subcarpetas**](/azure/applied-ai-services/form-recognizer/build-training-data-set#organize-your-data-in-subfolders-optional)

## <a name="create-a-sas-with-the-azure-portal"></a>Creación de una firma SAS con Azure Portal

> [!IMPORTANT]
>
> Genere y recupere la firma SAS del contenedor, no de la propia cuenta de almacenamiento.

1. En [Azure Portal](https://ms.portal.azure.com/#home), desplácese de la manera siguiente:

     **Su cuenta de almacenamiento** → **Contenedores**
1. Seleccione un contenedor de la lista.
1. Desplácese a la parte derecha de la ventana principal y seleccione los tres puntos suspensivos asociados al contenedor elegido.
1. Seleccione **Generar SAS** en el menú desplegable para abrir la ventana **Generar SAS**.

    :::image type="content" source="media/sas-tokens/generate-sas.png" alt-text="Captura de pantalla (Azure Portal): menú desplegable del token Generar SAS.":::

1. Seleccione **Método de firma** → **Clave de delegación de usuario**.

1. Para definir los **permisos**, active o desactive la casilla correspondiente. Asegúrese de que están seleccionados los permisos de **Lectura**, **Escritura**, **Eliminación** y **Enumeración**.

    :::image type="content" source="media/sas-tokens/sas-permissions.png" alt-text="Captura de pantalla (Azure Portal): campos de permiso de SAS.":::

    >[!IMPORTANT]
    >
    > * Si recibe un mensaje similar al siguiente, deberá asignar acceso a los datos de blob en la cuenta de almacenamiento:
    >
    >     :::image type="content" source="media/sas-tokens/need-permissions.png" alt-text="Captura de pantalla: advertencia de falta de permisos.":::
    >
     > * El [**control de acceso basado en rol**](/azure/role-based-access-control/overview) (Azure RBAC) es el sistema de autorización que puede utilizar para administrar el acceso a los recursos de Azure. Azure RBAC le ayuda a administrar el acceso y los permisos de los recursos de Azure.
    > * Siga nuestra guía [**Asignación de un rol de Azure para el acceso a datos de blobs**](/azure/role-based-access-control/role-assignments-portal?tabs=current) para asignar un rol con permisos de lectura, escritura y eliminación para el contenedor de almacenamiento de Azure, por ejemplo, [**Colaborador de datos de Storage Blob**](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor).

1. Especifique la fecha y hora de **inicio** y **expiración** de la clave firmada. **El valor de la hora de expiración es de siete días como máximo a partir del inicio de la firma SAS**.

1. El campo **Direcciones IP permitidas** es opcional y especifica una dirección IP o un intervalo de direcciones IP desde el que se aceptan solicitudes. Si la dirección IP de la solicitud no coincide con la dirección IP o el intervalo de direcciones especificado en el token de SAS, no se autorizará.

1. El campo **Protocolos permitidos** es opcional y especifica el protocolo permitido para una solicitud realizada con la firma de acceso compartido. El valor predeterminado es HTTPS.

1. Revise y, a continuación, seleccione **Generar URL y token de SAS**.

1. La cadena de consulta del **token de SAS de blob** y de la **dirección URL de SAS de blob** aparecerán en el área inferior de la ventana. Para usar el **token de SAS de blob**, anéxele un URI del servicio de almacenamiento.

1. **Copie y pegue estos valores en una ubicación segura. Estos solo se mostrarán una vez y no se podrán recuperar una vez cerrada la ventana.**

## <a name="create-a-sas-with-azure-command-line-interface-cli"></a>Creación de una firma SAS con la Interfaz de la línea de comandos de Azure (CLI)

1. Para crear una firma SAS de delegación de usuarios mediante la CLI de Azure, asegúrese de que ha instalado la versión 2.0.78 o posterior. Para comprobar la versión instalada, use el comando `az --version`.

1. Llame al comando [az storage container generate-sas](/cli/azure/storage/container?view=azure-cli-latest&preserve-view=true#az_storage_container_generate_sas).

1. Se necesitan los siguientes parámetros:

    * `auth-mode login`. Este parámetro garantiza que las solicitudes realizadas a Azure Storage se autorizan con sus credenciales de Azure AD.
    * `as-user`.  Este parámetro indica que la firma SAS generada es una firma SAS de delegación de usuarios.

1. Los permisos admitidos para una firma SAS de delegación de usuarios en un contenedor incluyen Agregar (a), Crear (c), Eliminar (d), Enumerar (e), Leer (r) y Escribir (w).  Asegúrese de que **r**, **w**, **d** y **l** se incluyen como parte de los parámetros de permisos.

1. Cuando se crea una firma SAS de delegación de usuarios con la CLI de Azure, el intervalo máximo durante el cual la clave de delegación de usuarios es válida es de siete días a partir de la fecha de inicio. Por lo tanto, debe especificar una hora de expiración para la firma SAS que esté dentro de los siete días posteriores a la hora de inicio. *Consulte* [**Creación de una SAS de delegación de usuarios para un contenedor o blob con la CLI de Azure**](/azure/storage/blobs/storage-blob-user-delegation-sas-create-cli#use-azure-ad-credentials-to-secure-a-sas)

### <a name="example"></a>Ejemplo

Genere una clave de delegación de usuarios.  Reemplace los valores del marcador de posición entre corchetes por los suyos propios:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions rwdl \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

## <a name="how-to-use-your-blob-sas-url"></a>Uso de la dirección URL de SAS de blob

* Para usar la dirección URL de SAS de blob con la [**API REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync), agregue la dirección URL de SAS al cuerpo de la solicitud:

  ```json
  {
      "source":"<BLOB SAS URL>"
  }
  ```

* Para usar su **dirección URL de SAS de blob** con la [**herramienta de etiquetado Form Recognizer**](https://fott-2-1.azurewebsites.net/connections/create), agregue la dirección URL de SAS al campo **Configuración de la conexión** → **Contenedor de blobs de Azure** → **URI de SAS**:

  :::image type="content" source="media/sas-tokens/fott-add-sas-uri.png" alt-text="{alt-text}":::

Eso es todo. Ha aprendido a generar tokens de SAS para autorizar cómo los clientes acceden a los datos.

> [!div class="nextstepaction"]
> [Creación de un conjunto de datos de aprendizaje](build-training-data-set.md)
