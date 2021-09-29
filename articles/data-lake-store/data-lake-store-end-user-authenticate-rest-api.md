---
title: Autenticación de usuario final en REST con Data Lake Storage Gen1: Azure
description: Aprenda a lograr la autenticación del usuario final con Azure Data Lake Storage Gen1 mediante Azure Active Directory con API REST
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: normesta
ms.openlocfilehash: 4b2a9259ca6177861ebf6dd3c84694941a0bb9aa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128648075"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Autenticación del usuario final con Azure Data Lake Storage Gen1 mediante API REST
> [!div class="op_single_selector"]
> * [Uso de Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Uso de SDK de .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Uso de Python](data-lake-store-end-user-authenticate-python.md)
> * [Uso de la API de REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

En este artículo, obtendrá información sobre cómo usar la API REST para realizar la autenticación del usuario final con Azure Data Lake Storage Gen1. Para la autenticación entre servicios con Data Lake Storage Gen1. mediante API REST, consulte [Service-to-service authentication with Data Lake Storage Gen1 using REST API](data-lake-store-service-to-service-authenticate-rest-api.md) (Autenticación entre servicios con Data Lake Storage Gen1 mediante API REST).

## <a name="prerequisites"></a>Requisitos previos

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **Cree una aplicación "nativa" de Azure Active Directory**. Debe completar los pasos descritos en [Autenticación de usuario final con Data Lake Storage Gen1 mediante Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[cURL](https://curl.haxx.se/)** . En este artículo se usa cURL para demostrar cómo realizar llamadas de la API de REST en una cuenta de Data Lake Storage Gen1.

## <a name="end-user-authentication"></a>Autenticación de usuario final
La autenticación de usuario final es el enfoque recomendado si quiere que un usuario inicie sesión en la aplicación a través de Azure AD. La aplicación puede acceder a los recursos de Azure con el mismo nivel de acceso que el usuario que ha iniciado sesión. El usuario tiene que proporcionar sus credenciales periódicamente para que la aplicación conserve el acceso.

El resultado del inicio de sesión por parte del usuario final es que la aplicación recibe un token de acceso y un token de actualización. El token de acceso se adjunta a cada solicitud hecha a Data Lake Storage Gen1 o Data Lake Analytics y es válido, de manera predeterminada, durante 1 hora. El token de actualización se puede usar para obtener un nuevo token de acceso y es válido, de manera predeterminada, hasta por dos semanas, si se usa de manera habitual. Puede usar dos enfoques distintos para el inicio de sesión del usuario final.

En este escenario, la aplicación pide al usuario que inicie sesión y todas las operaciones se realizan en el contexto del usuario. Lleve a cabo los siguiente pasos:

1. A través de la aplicación, redirija al usuario a la siguiente dirección URL:

    `https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>`

   > [!NOTE]
   > \<REDIRECT-URI> debe codificarse para utilizarse en una dirección URL. Por tanto, para https://localhost, utilice `https%3A%2F%2Flocalhost`.

    Para este tutorial, puede sustituir los valores de marcador de posición de la dirección URL anterior y pegarlos en la barra de direcciones del explorador web. Se le redirigirá a una página autenticarse con sus datos de inicio de sesión de Azure. Una vez que haya iniciado sesión correctamente, la respuesta se muestra en la barra de direcciones del explorador. La respuesta estará en el formato siguiente:

    `http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>`

2. Obtenga el código de autorización de la respuesta. Para este tutorial, puede copiar el código de autorización de la barra de direcciones del explorador web y pasarlo en la solicitud POST al punto de conexión de token, tal y como se muestra en el siguiente fragmento de código:

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
    -F redirect_uri=<REDIRECT-URI> \
    -F grant_type=authorization_code \
    -F resource=https://management.core.windows.net/ \
    -F client_id=<APPLICATION-ID> \
    -F code=<AUTHORIZATION-CODE>
    ```

   > [!NOTE]
   > En este caso, no es necesario codificar el \<REDIRECT-URI>.
   > 
   > 

3. La respuesta es un objeto JSON que contiene un token de acceso (por ejemplo, `"access_token": "<ACCESS_TOKEN>"`) y uno de actualización (por ejemplo, `"refresh_token": "<REFRESH_TOKEN>"`). La aplicación usa el token de acceso al acceder a Azure Data Lake Storage Gen1 y el token de actualización para obtener otro token de acceso cuando expira un token de acceso previo.

    ```json
    {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
    ```

4. Cuando expira el token de acceso, puede solicitar uno nuevo con el token de actualización, tal y como se muestra en el siguiente fragmento de código:

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
         -F grant_type=refresh_token \
         -F resource=https://management.core.windows.net/ \
         -F client_id=<APPLICATION-ID> \
         -F refresh_token=<REFRESH-TOKEN>
    ```

Para más información sobre la autenticación interactiva de usuarios, consulte el [flujo de concesión de un código de autorización](/previous-versions/azure/dn645542(v=azure.100)).

## <a name="next-steps"></a>Pasos siguientes
En este artículo aprendió a usar la autenticación entre servicios con Azure Data Lake Storage Gen1 mediante la API REST. Ahora puede consultar los siguientes artículos, que tratan acerca de cómo usar la API REST con Azure Data Lake Storage Gen1.

* [Account management operations on Data Lake Storage Gen1 using REST API](data-lake-store-get-started-rest-api.md) (Operaciones de administración de cuentas en Data Lake Storage Gen1 mediante API REST)
* [Data operations on Data Lake Storage Gen1 using REST API](data-lake-store-data-operations-rest-api.md) (Operaciones de datos en Data Lake Storage Gen1 mediante API REST)