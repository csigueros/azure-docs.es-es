---
title: 'Administración de una cuenta de Azure Data Lake Storage Gen1: CLI de Azure'
description: Use la CLI de Azure para crear una cuenta de Data Lake Storage Gen1 y realizar operaciones básicas.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: normesta
ms.openlocfilehash: 8f94506804f5edc4a728f06234f996ad30509eb6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621761"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-cli"></a>Introducción a Azure Data Lake Storage Gen1 con la CLI de Azure

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [CLI de Azure](data-lake-store-get-started-cli-2.0.md)
>
> 

Aprenda a usar la CLI de Azure para crear una cuenta de Azure Data Lake Storage Gen1 y realizar operaciones básicas como crear carpetas, cargar y descargar archivos de datos, eliminar la cuenta, etc. Para más información sobre Data Lake Storage Gen1, consulte [Información general de Data Lake Storage Gen1](data-lake-store-overview.md).

La CLI de Azure es la forma de usar la línea de comandos de Azure para administrar los recursos de Azure. Se puede usar en macOS, Linux y Windows. Para más información, consulte la [introducción a la CLI de Azure](/cli/azure). También puede examinar la [referencia de la CLI de Azure Data Lake Storage Gen1](/cli/azure/dls) para una lista completa de comandos y sintaxis.


## <a name="prerequisites"></a>Prerrequisitos
Antes de empezar este artículo, debe tener lo siguiente:

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **CLI de Azure**: consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli) para instrucciones.

## <a name="authentication"></a>Authentication

En este artículo se utiliza un enfoque de autenticación más sencillo con Data Lake Storage Gen1, donde se inicia sesión como usuario final. El nivel de acceso a la cuenta de Data Lake Storage Gen1 y al sistema de archivos está determinado por el nivel de acceso del usuario que ha iniciado sesión. No obstante, existen otros enfoques para realizar la autenticación con Data Lake Storage Gen1, como la **autenticación de usuario final** o la **autenticación de servicio a servicio**. Para obtener instrucciones y más información acerca de cómo realizar la autenticación, consulte [Autenticación de usuario final con Data Lake Store mediante Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md) o [Autenticación entre servicios con Data Lake Store mediante Azure Active Directory](./data-lake-store-service-to-service-authenticate-using-active-directory.md).


## <a name="log-in-to-your-azure-subscription"></a>Inicio de sesión en la suscripción de Azure

1. Inicie sesión en su suscripción de Azure.

    ```azurecli
    az login
    ```

    Obtendrá un código que usará en el paso siguiente. Use un explorador web para abrir la página https://aka.ms/devicelogin y escriba el código para autenticarse. Se le pedirá que inicie sesión con sus credenciales.

2. Una vez que inicie sesión, la ventana muestra todas las suscripciones de Azure que están asociadas a su cuenta. Use el comando siguiente para utilizar una suscripción específica.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Creación de una cuenta de Azure Data Lake Storage Gen1

1. Cree un nuevo grupo de recursos. En el siguiente comando, proporcione los valores de los parámetros que desee usar. Si el nombre de la ubicación contiene espacios, escríbalo entre comillas. Por ejemplo "Este de EE. UU. 2". 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Cree la cuenta de Data Lake Storage Gen1.
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>Creación de carpetas de la cuenta de Data Lake Storage Gen1

Puede crear carpetas en la cuenta de Azure Data Lake Storage Gen1 para administrar y almacenar datos. Use el siguiente comando para crear una carpeta denominada **mynewfolder** en la raíz de la cuenta de Data Lake Storage Gen1.

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> El parámetro `--folder` garantiza que el comando crea una carpeta. Si este parámetro no está presente, el comando crea un archivo vacío llamado mynewfolder en la raíz de la cuenta de Data Lake Storage Gen1.
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>Carga de datos en una cuenta de Data Lake Storage Gen1

Puede cargar datos en Data Lake Storage Gen1 directamente en el nivel raíz o en la carpeta que haya creado en la cuenta. Los fragmentos de código siguientes muestran cómo cargar algunos datos de ejemplo en la carpeta (**miNuevaCarpeta**) que creó en la sección anterior.

Si busca datos de ejemplo para cargar, puede obtener la carpeta **Ambulance Data** en el [repositorio Git de Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Descargue el archivo y almacénelo en un directorio local del equipo, como C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Como destino, debe especificar la ruta de acceso completa, incluido el nombre de archivo.
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>Enumeración de las carpetas en la cuenta de Data Lake Storage Gen1

Use el siguiente comando para enumerar los archivos de una cuenta de Data Lake Storage Gen1.

```azurecli
az dls fs list --account mydatalakestoragegen1 --path /mynewfolder
```

La salida de este comando debe ser similar a la siguiente:

```output
[
    {
        "accessTime": 1491323529542,
        "aclBit": false,
        "blockSize": 268435456,
        "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "length": 1589881,
        "modificationTime": 1491323531638,
        "msExpirationTime": 0,
        "name": "mynewfolder/vehicle1_09142014.csv",
        "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "pathSuffix": "vehicle1_09142014.csv",
        "permission": "770",
        "replication": 1,
        "type": "FILE"
    }
]
```

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>Cambio del nombre, descarga y eliminación de datos en la cuenta de Data Lake Storage Gen1 

* **Para cambiar el nombre de un archivo**, use el comando siguiente:
  
    ```azurecli
    az dls fs move --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **Para descargar un archivo**, use el comando siguiente. Asegúrese de que la ruta de destino especificada ya existe.
  
    ```azurecli     
    az dls fs download --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > El comando crea la carpeta de destino si no existe.
    > 
    >

* **Para eliminar un archivo**, use el comando siguiente:
  
    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Si quiere eliminar la carpeta **minuevacarpeta** y el archivo **vehicle1_09142014_copy.csv** juntos en un solo comando, use el parámetro -- recurse.

    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>Uso de permisos y listas de control de acceso para una cuenta de Data Lake Storage Gen1

En esta sección aprenderá a administrar listas de control de acceso y permisos mediante la CLI de Azure. Para una explicación detallada sobre cómo se implementan las listas de control de acceso de Azure Data Lake Storage Gen1, consulte [Control de acceso en Azure Data Lake Storage Gen1](data-lake-store-access-control.md).

* **Para actualizar el propietario de un archivo o carpeta**, use el siguiente comando:

    ```azurecli
    az dls fs access set-owner --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **Para actualizar los permisos de un archivo o carpeta**, use el siguiente comando:

    ```azurecli
    az dls fs access set-permission --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **Para obtener las listas ACL para una ruta de acceso dada**, use el siguiente comando:

    ```azurecli
    az dls fs access show --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv
    ```

    La salida debe ser similar a la siguiente:

    ```output
    {
        "entries": [
        "user::rwx",
        "group::rwx",
        "other::---"
        ],
        "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "permission": "770",
        "stickyBit": false
    }
    ```

* **Para establecer una entrada para una lista ACL**, use el siguiente comando:

    ```azurecli
    az dls fs access set-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **Para quitar una entrada de una lista ACL**, use el siguiente comando:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **Para quitar una lista ACL entera predeterminada**, use el siguiente comando:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder --default-acl
    ```

* **Para quitar una lista ACL entera no predeterminada**, use el siguiente comando:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>Eliminación de una cuenta de Data Lake Storage Gen1
Para eliminar una cuenta de Data Lake Storage Gen1 use el siguiente comando.

```azurecli
az dls account delete --account mydatalakestoragegen1
```

Cuando se le solicite, escriba **Y** para eliminar la cuenta.

## <a name="next-steps"></a>Pasos siguientes
* [Uso de Azure Data Lake Storage Gen1 para requisitos de macrodatos](data-lake-store-data-scenarios.md) 
* [Protección de datos en Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Use Azure Data Lake Analytics with Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md) (Uso de Azure Data Lake Analytics con Data Lake Storage Gen1)
* [Use Azure HDInsight with Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md) (Uso de Azure HDInsight con Data Lake Storage Gen1)