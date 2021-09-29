---
title: 'API REST: operaciones del sistema de archivos en Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Uso de la API de REST WebHDFS para realizar operaciones del sistema de archivos en Azure Data Lake Storage Gen1
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: normesta
ms.openlocfilehash: d5343e34b499ce885705f11fa1997d374bea9689
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128592306"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operaciones del sistema de archivos en Azure Data Lake Storage Gen1 mediante el uso de la API de REST
> [!div class="op_single_selector"]
> * [SDK de .NET](data-lake-store-data-operations-net-sdk.md)
> * [SDK de Java](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

En este artículo aprenderá a usar las API de REST WebHDFS y las API de REST de Data Lake Storage Gen1 para realizar operaciones del sistema de archivos en Azure Data Lake Storage Gen1. Para instrucciones sobre cómo realizar operaciones de administración de cuentas en Data Lake Storage Gen1 con la API de REST, consulte [Operaciones de administración de cuentas en Data Lake Storage Gen1 con la API de REST](data-lake-store-get-started-rest-api.md).

## <a name="prerequisites"></a>Requisitos previos
* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **Cuenta de Azure Data Lake Storage Gen1**. Siga las instrucciones de [Introducción a Azure Data Lake Storage Gen1 con Azure Portal](data-lake-store-get-started-portal.md).

* **[cURL](https://curl.haxx.se/)** . En este artículo se usa cURL para demostrar cómo realizar llamadas de la API de REST en una cuenta de Data Lake Storage Gen1.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>¿Cómo se puede autenticar mediante Azure Active Directory?
Puede usar dos enfoques para autenticar con Azure Active Directory.

* Para la autenticación del usuario final para la aplicación (interactiva), consulte el artículo sobre la [autenticación del usuario final con Data Lake Storage Gen1 mediante el SDK de .NET](data-lake-store-end-user-authenticate-rest-api.md).
* Para la autenticación entre servicios para la aplicación (no interactiva), consulte el artículo sobre la [autenticación entre servicios con Data Lake Storage Gen1 mediante el SDK de .NET](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-folders"></a>Crear carpetas
Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Use el siguiente comando cURL. Reemplace **\<yourstorename>** por el nombre de cuenta de Data Lake Storage Gen1.

```console
curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'
```

En el comando anterior, reemplace \<`REDACTED`\> por el token de autorización que recuperó anteriormente. Este comando crea un directorio denominado **mytempdir** bajo la carpeta raíz de su cuenta de Data Lake Storage Gen1.

Si la operación se completa correctamente, verá una respuesta similar al fragmento de código siguiente:

```output
{"boolean":true}
```

## <a name="list-folders"></a>Lista de carpetas
Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Use el siguiente comando cURL. Reemplace **\<yourstorename>** por el nombre de cuenta de Data Lake Storage Gen1.

```console
curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'
```

En el comando anterior, reemplace \<`REDACTED`\> por el token de autorización que recuperó anteriormente.

Si la operación se completa correctamente, verá una respuesta similar al fragmento de código siguiente:

```output
{
"FileStatuses": {
    "FileStatus": [{
        "length": 0,
        "pathSuffix": "mytempdir",
        "type": "DIRECTORY",
        "blockSize": 268435456,
        "accessTime": 1458324719512,
        "modificationTime": 1458324719512,
        "replication": 0,
        "permission": "777",
        "owner": "<GUID>",
        "group": "<GUID>"
    }]
}
}
```

## <a name="upload-data"></a>Carga de datos
Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Use el siguiente comando cURL. Reemplace **\<yourstorename>** por el nombre de cuenta de Data Lake Storage Gen1.

```console
curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'
```

En la sintaxis anterior, el parámetro **-T** es la ubicación del archivo que se va a cargar.

La salida será similar al fragmento de código siguiente:
   
```output
HTTP/1.1 307 Temporary Redirect
...
Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
...
Content-Length: 0

HTTP/1.1 100 Continue

HTTP/1.1 201 Created
...
```

## <a name="read-data"></a>Lectura de datos
Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

La lectura de datos desde una cuenta de Data Lake Storage Gen1 es un proceso de dos pasos.

* Primero, envíe una solicitud GET en el punto de conexión `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Esta llamada devolverá una ubicación a la que se debe enviar la siguiente solicitud GET.
* Después, envíe la solicitud GET en el punto de conexión `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Esta llamada muestra el contenido del archivo.

Sin embargo, como no existe ninguna diferencia en los parámetros de entrada entre el primer y el segundo paso, puede usar el parámetro `-L` para enviar la primera solicitud. La opción `-L` combina dos solicitudes en una y hace que cURL vuelva a realizar la solicitud en la nueva ubicación. Por último, se muestra la salida de todas las llamadas de solicitud, como se muestra en el fragmento de código siguiente. Reemplace **\<yourstorename>** por el nombre de cuenta de Data Lake Storage Gen1.

```console
curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'
```

Debería ver una salida similar al siguiente fragmento de código:

```output
HTTP/1.1 307 Temporary Redirect
...
Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
...

HTTP/1.1 200 OK
...

Hello, Data Lake Store user!
```

## <a name="rename-a-file"></a>Cambio del nombre de un archivo
Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Para cambiar el nombre de un archivo, use el siguiente comando cURL. Reemplace **\<yourstorename>** por el nombre de cuenta de Data Lake Storage Gen1.

```console
curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'
```

Debería ver una salida similar al siguiente fragmento de código:

```output
HTTP/1.1 200 OK
...

{"boolean":true}
```

## <a name="delete-a-file"></a>Eliminación de un archivo
Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Para eliminar un archivo, use el siguiente comando cURL. Reemplace **\<yourstorename>** por el nombre de cuenta de Data Lake Storage Gen1.

```console
curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'
```

Debe ver algo parecido a lo siguiente:

```output
HTTP/1.1 200 OK
...

{"boolean":true}
```

## <a name="next-steps"></a>Pasos siguientes
* [Account management operations on Data Lake Storage Gen1 using REST API](data-lake-store-get-started-rest-api.md) (Operaciones de administración de cuentas en Data Lake Storage Gen1 mediante la API de REST).

## <a name="see-also"></a>Consulte también
* [Azure Data Lake Storage Gen1 REST API Reference](/rest/api/datalakestore/) (Referencia sobre la API de REST de Azure Data Lake Storage Gen1)
* [Abrir aplicaciones de macrodatos de código abierto que funcionan con Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)