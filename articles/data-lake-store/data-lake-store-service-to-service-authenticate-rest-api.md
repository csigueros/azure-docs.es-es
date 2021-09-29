---
title: 'Autenticación entre servicios con REST: Data Lake Storage Gen1: Azure'
description: Aprenda a realizar la autenticación entre servicios con Azure Data Lake Storage Gen1 y Azure Active Directory con la API REST.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: normesta
ms.openlocfilehash: d125e1c92ae64749c28703430fa62f63efd05a93
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649426"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Autenticación de servicio a servicio con Azure Data Lake Storage Gen1 mediante la API REST
> [!div class="op_single_selector"]
> * [Uso de Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Uso de SDK de .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Uso de Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Uso de la API de REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

En este artículo, aprenderá a usar la API REST para realizar la autenticación entre servicios con Azure Data Lake Storage Gen1. Para realizar la autenticación del usuario final con Data Lake Storage Gen1 mediante API REST, vea [End-user authentication with Data Lake Storage Gen1 using REST API](data-lake-store-end-user-authenticate-rest-api.md) (Autenticación del usuario final con Data Lake Storage Gen1 mediante API REST).

## <a name="prerequisites"></a>Prerrequisitos

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **Cree una aplicación "web" de Azure Active Directory**. Debe haber completado los pasos descritos en [Service-to-service authentication with Data Lake Storage Gen1 using Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md) (Autenticación entre servicios con Data Lake Storage Gen1 mediante Azure Active Directory).

## <a name="service-to-service-authentication"></a>Autenticación entre servicios

En este escenario, la aplicación proporciona sus propias credenciales para realizar las operaciones. Para ello, debe emitir una solicitud POST como la que se muestra en el siguiente fragmento de código:

```console
curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
  -F grant_type=client_credentials \
  -F resource=https://management.core.windows.net/ \
  -F client_id=<CLIENT-ID> \
  -F client_secret=<AUTH-KEY>
```

La salida de esta solicitud incluye un token de autorización (que se indica mediante `access-token` en la siguiente salida) que se pasa posteriormente con las llamadas de la API de REST. Guarde el token de autenticación en un archivo de texto; lo necesitará al realizar llamadas de REST a Data Lake Storage Gen1.

```output
{"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}
```

En este artículo se usa el enfoque **no interactivo** . Para más información sobre el enfoque no interactivo (llamadas de servicio a servicio), consulte el tema sobre [llamadas de servicio a servicio utilizando las credenciales del cliente](/previous-versions/azure/dn645543(v=azure.100)).

## <a name="next-steps"></a>Pasos siguientes

En este artículo, aprendió a usar la autenticación de servicio a servicio con Data Lake Storage Gen1 mediante la API REST. Ahora puede consultar los siguientes artículos, que tratan acerca de cómo usar la API REST con Data Lake Storage Gen1.

* [Account management operations on Data Lake Storage Gen1 using REST API](data-lake-store-get-started-rest-api.md) (Operaciones de administración de cuentas en Data Lake Storage Gen1 mediante API REST)
* [Data operations on Data Lake Storage Gen1 using REST API](data-lake-store-data-operations-rest-api.md) (Operaciones de datos en Data Lake Storage Gen1 mediante API REST)