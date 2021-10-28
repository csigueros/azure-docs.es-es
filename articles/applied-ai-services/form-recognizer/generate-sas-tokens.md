---
title: Generación de tokens de SAS para contenedores y blobs con Azure Portal
description: Aprenda a generar tokens de firma de acceso compartido (SAS) para contenedores y blobs en Azure Portal.
ms.topic: how-to
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.date: 09/23/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 70ab13f1ca1eee2d5c01d3aa1af6255bc06aeb3d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233665"
---
# <a name="generate-sas-tokens-for-storage-containers"></a>Generación de tokens de SAS para contenedores de almacenamiento

En este artículo, aprenderá a generar tokens de firma de acceso compartido (SAS) de delegación de usuarios para contenedores de Azure Blob Storage. Un token de SAS de delegación de usuarios se firma con credenciales de Azure Active Directory (Azure AD) en lugar de claves de Azure Storage. Proporciona acceso delegado y seguro a los recursos de la cuenta de almacenamiento de Azure.

En líneas generales, así es cómo funciona: la aplicación proporciona el token de SAS a Azure Storage como parte de una solicitud. Si el servicio de almacenamiento confirma que la firma de acceso compartido es válida, la solicitud se autoriza. Si la firma de acceso compartido se considera no válida, la solicitud se rechaza con el código de error 403 (prohibido).

Azure Blob Storage ofrece tres tipos de recursos:

* Las cuentas de **almacenamiento** proporcionan un espacio de nombres único en Azure para los datos.
* Los **contenedores** se encuentran en las cuentas de almacenamiento y organizan los conjuntos de blobs.
* Los **blobs** se encuentran en los contenedores y almacenan los datos binarios y de texto.

> [!NOTE]
>
> * Si la cuenta de almacenamiento de Azure está protegida por una red virtual o un firewall, no puede conceder acceso mediante un token de SAS. Tiene que usar una [identidad administrada](managed-identity-byos.md) para conceder acceso al recurso de almacenamiento.
> * La [identidad administrada](managed-identity-byos.md) admite cuentas de Azure Blob Storage de acceso privado y público.
>

## <a name="when-to-use-a-shared-access-signature"></a>¿Cuándo debe usar una firma de acceso compartido?

* Si usa contenedores de almacenamiento con acceso público, puede optar por usar un token de SAS para conceder acceso limitado a los recursos de almacenamiento.
* Al entrenar un modelo personalizado, el conjunto reunido de documentos de entrenamiento *debe* cargarse en un contenedor de Azure Blob Storage. Puede conceder permiso a los recursos de entrenamiento con un token de SAS de delegación de usuarios.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesitará lo siguiente:

* Una [cuenta de Azure](https://azure.microsoft.com/free/cognitive-services/) activa. En caso de no tener ninguna, puede [crear una gratis](https://azure.microsoft.com/free/).
* Un recurso de [Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) o de [varios servicios de Cognitive Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).
* Una [cuenta de Azure Blob Storage](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) de **rendimiento estándar**. Creará contenedores para almacenar y organizar los datos de los blobs en la cuenta de almacenamiento. Si no sabe cómo crear una cuenta de almacenamiento de Azure con un contenedor, siga estos inicios rápidos:

  * [Crear una cuenta de almacenamiento](../../storage/common/storage-account-create.md). Al crear la cuenta de almacenamiento, seleccione el rendimiento **Estándar** en el campo **Detalles de instancia** > **Rendimiento**.
  * [Cree un contenedor](../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container). Al crear un contenedor, establezca **Nivel de acceso público** en **Contenedor** (acceso de lectura anónimo para contenedores y blobs) en la ventana **Nuevo contenedor**.

## <a name="upload-your-documents"></a>Carga de los documentos

1. Vaya a [Azure Portal](https://ms.portal.azure.com/#home). Seleccione **Cuenta de almacenamiento** > **Almacenamiento de datos** > **Contenedores**.

   :::image type="content" source="media/sas-tokens/data-storage-menu.png" alt-text="Captura de pantalla que muestra el menú Almacenamiento de datos en Azure Portal.":::

1. Seleccione un contenedor de la lista.
1. En el menú de la parte superior de la página, seleccione **Cargar**.

    :::image type="content" source="media/sas-tokens/container-upload-button.png" alt-text="Captura de pantalla que muestra el botón de carga de contenedores en Azure Portal.":::

   Aparece la ventana **Cargar blob**.
1. Seleccione los archivos que quiere cargar.

    :::image type="content" source="media/sas-tokens/upload-blob-window.png" alt-text="Captura de pantalla que muestra el botón Cargar blob en Azure Portal.":::

> [!NOTE]
> De manera predeterminada, la API REST usa documentos de formulario que se encuentran en la raíz del contenedor. También puede usar datos organizados en subcarpetas si lo especifica en la llamada API. Para obtener más información, vea [Organización de los datos en subcarpetas](./build-training-data-set.md#organize-your-data-in-subfolders-optional).

## <a name="create-a-shared-access-signature-with-the-azure-portal"></a>Creación de una firma de acceso compartido con Azure Portal

> [!IMPORTANT]
>
> Genere y recupere la firma de acceso compartido del contenedor, no de la propia cuenta de almacenamiento.

1. En [Azure Portal](https://ms.portal.azure.com/#home), seleccione **Cuenta de almacenamiento** > **Contenedores**.
1. Seleccione un contenedor de la lista.
1. Vaya a la derecha de la ventana principal y seleccione los puntos suspensivos asociados al contenedor elegido.
1. Seleccione **Generar SAS** en el menú desplegable para abrir la ventana **Generar SAS**.

    :::image type="content" source="media/sas-tokens/generate-sas.png" alt-text="Captura de pantalla que muestra el menú desplegable Generar token de SAS en Azure Portal.":::

1. Seleccione **Método de firma** > **Clave de delegación de usuario**.

1. Para definir los **Permisos**, active o desactive la casilla correspondiente. Asegúrese de que están seleccionados los permisos de **Lectura**, **Escritura**, **Eliminación** y **Enumeración**.

    :::image type="content" source="media/sas-tokens/sas-permissions.png" alt-text="Captura de pantalla que muestra los campos de permisos de SAS en Azure Portal.":::

    >[!IMPORTANT]
    >
    > * Si recibe un mensaje similar al siguiente, debe asignar acceso a los datos de blob de la cuenta de almacenamiento:
    >
    >     :::image type="content" source="media/sas-tokens/need-permissions.png" alt-text="Captura de pantalla que muestra la advertencia de falta de permisos.":::
    >
     > * El [control de acceso basado en rol de Azure](../../role-based-access-control/overview.md) (RBAC de Azure) es el sistema de autorización empleado para administrar el acceso a los recursos de Azure. Azure RBAC le ayuda a administrar el acceso y los permisos de los recursos de Azure.
    > * [Asignación de un rol de Azure para acceder a datos de blobs](../../role-based-access-control/role-assignments-portal.md?tabs=current) muestra cómo asignar un rol con permisos de lectura, escritura y eliminación para el contenedor de Azure Storage. Por ejemplo, vea [Colaborador de datos de blob de Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor).

1. Especifique la fecha y hora de **inicio** y **expiración** de la clave firmada. El valor del tiempo de expiración es de siete días como máximo a partir del inicio de la firma de acceso compartido.

1. El campo **Direcciones IP permitidas** es opcional y especifica una dirección IP o un intervalo de direcciones IP desde el que se aceptan solicitudes. Si la dirección IP de la solicitud no coincide con la dirección IP o el intervalo de direcciones especificado en el token de SAS, no se autorizará.

1. El campo **Protocolos permitidos** es opcional y especifica el protocolo permitido para una solicitud realizada con la firma de acceso compartido. El valor predeterminado es HTTPS.

1. Seleccione **Generar URL y token de SAS**.

1. La cadena de consulta **Token de SAS de blob** y **URL de SAS de blob** aparecen en el área inferior de la ventana. Para usar el token de SAS de blob, anéxelo a un URI del servicio de almacenamiento.

1. Copie y pegue los valores **Token de SAS de blob** y **URL de SAS de blob** en una ubicación segura. Solo se muestran una vez y no se pueden recuperar una vez cerrada la ventana.

## <a name="create-a-shared-access-signature-with-the-azure-cli"></a>Creación de una firma de acceso compartido con la CLI de Azure

1. Para crear una SAS de delegación de usuarios para un contenedor mediante la CLI de Azure, asegúrese de que ha instalado la versión 2.0.78 o posterior. Para comprobar la versión instalada, use el comando `az --version`.

1. Llame al comando [az storage container generate-sas](/cli/azure/storage/container?view=azure-cli-latest&preserve-view=true#az_storage_container_generate_sas).

1. Se necesitan los siguientes parámetros:

    * `auth-mode login`. Este parámetro garantiza que las solicitudes realizadas a Azure Storage se autoricen con las credenciales de Azure AD.
    * `as-user`. Este parámetro indica que la firma SAS generada es una firma SAS de delegación de usuarios.

1. Los permisos admitidos para una firma SAS de delegación de usuarios en un contenedor incluyen Agregar (a), Crear (c), Eliminar (d), Enumerar (e), Leer (r) y Escribir (w). Asegúrese de que **r**, **w**, **d** y **l** se incluyen como parte de los parámetros de permisos.

1. Cuando se crea una firma SAS de delegación de usuarios con la CLI de Azure, el intervalo máximo durante el cual la clave de delegación de usuarios es válida es de siete días a partir de la fecha de inicio. Especifique un tiempo de expiración para la firma de acceso compartido que no supere los siete días a partir del momento de inicio. Para obtener más información, vea [Creación de una SAS de delegación de usuarios para un contenedor o blob con la CLI de Azure](../../storage/blobs/storage-blob-user-delegation-sas-create-cli.md#use-azure-ad-credentials-to-secure-a-sas).

### <a name="example"></a>Ejemplo

Genere una clave de delegación de usuarios. Reemplace los valores del marcador de posición entre corchetes por los suyos propios:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions rwdl \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

## <a name="use-your-blob-sas-url"></a>Uso de la dirección URL de SAS de blob

Hay dos opciones de disponibles:

* Para usar la dirección URL de SAS de blob con la [API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync), agregue la dirección URL de SAS al cuerpo de la solicitud:

  ```json
  {
      "source":"<BLOB SAS URL>"
  }
  ```

* Para usar la dirección URL de SAS de blob con la [herramienta de etiquetado de Form Recognizer](https://fott-2-1.azurewebsites.net/connections/create), agregue la dirección URL de SAS al campo **Configuración de la conexión** > **Contenedor de blobs de Azure** > **URI de SAS**:

  :::image type="content" source="media/sas-tokens/fott-add-sas-uri.png" alt-text="Captura de pantalla que muestra el campo URI de SAS.":::

Eso es todo. Ha aprendido a generar tokens de SAS para autorizar cómo los clientes acceden a los datos.

## <a name="next-step"></a>Paso siguiente

> [!div class="nextstepaction"]
> [Creación de un conjunto de datos de aprendizaje](build-training-data-set.md)