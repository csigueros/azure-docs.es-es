---
title: Creación de un token de firma de acceso compartido (SAS) para contenedores y blobs con el Explorador de Microsoft Azure Storage
description: Cómo crear un token de firma de acceso compartido (SAS) para contenedores y blobs con el Explorador de Microsoft Azure Storage y Azure Portal.
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 09/16/2021
ms.openlocfilehash: ecc62d7b45bfb3b0b108057d5e3e25b189297165
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128653312"
---
# <a name="generate-sas-tokens-for-your-storage-containers"></a>Creación de tokens de SAS para los contenedores de almacenamiento

En este artículo, aprenderá a crear tokens de firma de acceso compartido (SAS) mediante el Explorador de Azure Storage o Azure Portal. Un token de SAS proporciona acceso delegado y seguro a los recursos de la cuenta de almacenamiento de Azure.

## <a name="create-your-sas-tokens-with-azure-storage-explorer"></a>Creación de tokens de SAS con Explorador de Azure Storage

### <a name="prerequisites"></a>Requisitos previos

* Necesitará una aplicación [**Explorador de Azure Storage**](../../../vs-azure-tools-storage-manage-with-storage-explorer.md) instalada en el entorno de desarrollo de Windows, macOS o Linux. Explorador de Azure Storage es una herramienta gratuita que le permite administrar con facilidad los recursos del almacenamiento en la nube de Azure.
* Una vez instalada la aplicación Explorador de Azure Storage, [conéctela a la cuenta de almacenamiento](../../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#connect-to-a-storage-account-or-service) que esté usando para la traducción de documentos.

### <a name="create-your-tokens"></a>Creación de los tokens

### <a name="sas-tokens-for-containers"></a>[Tokens de SAS para contenedores](#tab/Containers)

1. Abra la aplicación Explorador de Azure Storage en la máquina local y vaya a las **cuentas de almacenamiento** conectadas.
1. Expanda el nodo Cuentas de almacenamiento y seleccione **Contenedores de blob**.
1. Expanda el nodo Contenedores de blob y haga clic con el botón derecho en un nodo **contenedor** de almacenamiento o para mostrar el menú de opciones.
1. Seleccione **Obtener firma de acceso compartido...** en el menú de opciones.
1. En la ventana **Firma de acceso compartido**, realice las selecciones siguientes:
    * Seleccione la **directiva de acceso** (el valor predeterminado es ninguna).
    * Especifique la fecha y hora de **inicio** y de **expiración** de la clave firmada. Se recomienda una duración corta porque, una vez generada, no se puede revocar una SAS.
    * Seleccione la **zona horaria** de la fecha y hora de inicio y de expiración (el valor predeterminado es la zona horaria local).
    * Para definir los **permisos** del contenedor, active o desactive la casilla correspondiente.
    * Repase y seleccione **Crear**.

1. Aparecerá una nueva ventana con el nombre del **contenedor**, el **URI** y la **cadena de consulta** del contenedor.  
1. **Copie y pegue estos valores en una ubicación segura. Estos solo se mostrarán una vez y no se podrán recuperar una vez cerrada la ventana.**
1. Para construir una dirección URL de SAS, asocie el token de SAS (URI) a la dirección URL de un servicio de almacenamiento.

### <a name="sas-tokens-for-blobs"></a>[Tokens de SAS para blobs](#tab/blobs)

1. Abra la aplicación Explorador de Azure Storage en la máquina local y vaya a las **cuentas de almacenamiento** conectadas.
1. Expanda el nodo almacenamiento y seleccione **Contenedores de blob**.
1. Expanda el nodo Contenedores de blob y seleccione un nodo **contenedor** para mostrar el contenido de la ventana principal.
1. Seleccione el blob en el que desea delegar el acceso de SAS y haga clic con el botón derecho para que aparezca el menú de opciones.
1. Seleccione **Obtener firma de acceso compartido...** en el menú de opciones.
1. En la ventana **Firma de acceso compartido**, realice las selecciones siguientes:
    * Seleccione la **directiva de acceso** (el valor predeterminado es ninguna).
    * Especifique la fecha y hora de **inicio** y de **expiración** de la clave firmada. Se recomienda una duración corta porque, una vez generada, no se puede revocar una SAS.
    * Seleccione la **zona horaria** de la fecha y hora de inicio y de expiración (el valor predeterminado es la zona horaria local).
    * Para definir los **permisos** del contenedor, active o desactive la casilla correspondiente.
    * Repase y seleccione **Crear**.
1. Aparecerá una nueva ventana con el nombre del **blob**, el **URI** y la **cadena de consulta** del blob.  
1. **Copie y pegue estos valores en una ubicación segura. Estos solo se mostrarán una vez y no se podrán recuperar una vez cerrada la ventana.**
1. Para construir una dirección URL de SAS, asocie el token de SAS (URI) a la dirección URL de un servicio de almacenamiento.

---

## <a name="create-sas-tokens-for-blobs-in-the-azure-portal"></a>Creación de tokens de SAS para blobs en Azure Portal

<!-- markdownlint-disable MD024 -->
### <a name="prerequisites"></a>Requisitos previos

Para empezar, necesitará lo siguiente:

* Una [**cuenta de Azure**](https://azure.microsoft.com/free/cognitive-services/) activa.  En caso de no tener ninguna, puede crear una [**cuenta gratuita**](https://azure.microsoft.com/free/).
* Un recurso del servicio [**Translator**](https://ms.portal.azure.com/#create/Microsoft) (**no** un recurso multiservicio de Cognitive Services).  *Consulte* [Creación de un nuevo recurso de Azure](../../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource).  
* Una [**cuenta de Azure Blob Storage**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Creará contenedores para almacenar y organizar los datos de los blobs en la cuenta de almacenamiento.

### <a name="create-your-tokens"></a>Creación de los tokens

Vaya a [Azure Portal](https://ms.portal.azure.com/#home) y vaya a:  

 **Su cuenta de almacenamiento** → **contenedores** → **su contenedor** → **su blob**

1. Seleccione la pestaña **Generar SAS** en el menú junto a la parte superior de la página.

1. Seleccione **Método de firma** → **Clave de delegación de usuario**.

1. Para definir los **permisos**, active o desactive la casilla correspondiente.

1. Especifique la fecha y hora de **inicio** y **expiración** de la clave firmada.

1. El campo **Direcciones IP permitidas** es opcional y especifica una dirección IP o un intervalo de direcciones IP desde el que se aceptan solicitudes. Si la dirección IP de la solicitud no coincide con la dirección IP o el intervalo de direcciones especificado en el token de SAS, no se autorizará.

1. El campo **Protocolos permitidos** es opcional y especifica el protocolo permitido para una solicitud realizada con la firma de acceso compartido. El valor predeterminado es HTTPS.

1. Revise y, a continuación, seleccione **Generar URL y token de SAS**.

1. La cadena de consulta del **token de SAS de blob** y de la **dirección URL de SAS de blob** aparecerán en el área inferior de la ventana.  

1. **Copie y pegue estos valores en una ubicación segura. Estos solo se mostrarán una vez y no se podrán recuperar una vez cerrada la ventana.**

1. Para construir una dirección URL de SAS, asocie el token de SAS (URI) a la dirección URL de un servicio de almacenamiento.

## <a name="learn-more"></a>Más información

* [Creación de tokens de SAS para blobs o contenedores mediante programación](../../../storage/blobs/sas-service-create.md)
* [Permisos para un directorio, un contenedor o blob](/rest/api/storageservices/create-service-sas#permissions-for-a-directory-container-or-blob)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Introducción a la traducción de documentos](get-started-with-document-translation.md)
>
>
