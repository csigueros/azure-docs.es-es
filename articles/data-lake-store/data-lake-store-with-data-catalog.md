---
title: Integración de Data Lake Storage Gen1 con Azure Data Catalog
description: Obtenga información acerca de cómo registrar datos de Azure Data Lake Storage Gen1 en Azure Data Catalog para que los datos se puedan reconocer en la organización.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: normesta
ms.openlocfilehash: 364f7c11e9f15eada8ceb7ac0b73436f71bf38ee
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676891"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Registro de datos de Azure Data Lake Storage Gen1 en Azure Data Catalog
En este artículo, obtendrá información sobre cómo integrar Azure Data Lake Storage Gen1 con Azure Data Catalog para hacer que los datos sean reconocibles dentro de una organización mediante la integración con Data Catalog. Para más información sobre la catalogación de datos, consulte [¿Qué es Azure Data Catalog?](../data-catalog/overview.md). Para saber en qué escenarios se puede utilizar Data Catalog, consulte [Escenarios comunes de Azure Data Catalog](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Prerrequisitos
Antes de empezar este tutorial, debe contar con lo siguiente:

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
* **Habilite su suscripción a Azure** para Data Lake Storage Gen1. Consulte las [instrucciones](data-lake-store-get-started-portal.md).
* **Cuenta de Data Lake Storage Gen1**. Siga las instrucciones de [Introducción a Azure Data Lake Storage Gen1 con Azure Portal](data-lake-store-get-started-portal.md). Para este tutorial, cree una cuenta de Data Lake Storage Gen1 denominada **datacatalogstore**.

    Una vez creada la cuenta, cargue un conjunto de datos de ejemplo en ella. En este tutorial, vamos a cargar todos los archivos .csv en la carpeta **AmbulanceData** del [repositorio Git de Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Puede utilizar varios clientes, como el [explorador de Azure Storage](https://storageexplorer.com/), para cargar datos en un contenedor de blobs.
* **Azure Data Catalog**. Su organización ya debe tener un Azure Data Catalog creado. Se permite solo un catálogo por cada organización.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Registro de Data Lake Storage Gen1 como origen para Data Catalog

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Vaya a `https://azure.microsoft.com/services/data-catalog`y haga clic en **Iniciado**.
1. Inicie sesión en el portal de Azure Data Catalog y haga clic en **Publicar datos**.

    ![Registro de un origen de datos](./media/data-lake-store-with-data-catalog/register-data-source.png "Registro de un origen de datos")
1. En la siguiente página, haga clic en **Iniciar aplicación**. Esto descargará el archivo de manifiesto de la aplicación en el equipo. Haga doble clic en este archivo para iniciar la aplicación.
1. En la página principal, haga clic en **Iniciar sesión** y escriba sus credenciales.

    ![Pantalla principal](./media/data-lake-store-with-data-catalog/welcome.screen.png "Pantalla principal")
1. En la página Seleccionar un origen de datos, seleccione **Azure Data Lake Store** y, a continuación, haga clic en **Siguiente**.

    ![Selección de origen de datos](./media/data-lake-store-with-data-catalog/select-source.png "Selección de origen de datos")
1. En la siguiente página, proporcione el nombre de la cuenta de Data Lake Storage Gen1 que quiere registrar en Data Catalog. Deje las demás opciones con el valor predeterminado y, a continuación, haga clic en **Conectar**.

    ![Conexión a orígenes de datos](./media/data-lake-store-with-data-catalog/connect-to-source.png "Conexión a orígenes de datos")
1. La página resultante se puede dividir en los siguientes segmentos.

    a. El cuadro **Jerarquía de servidor** representa la estructura de carpetas de la cuenta de Data Lake Storage Gen1. **$Root** representa la raíz de la cuenta de Data Lake Storage Gen1 y **AmbulanceData** representa la carpeta creada en la raíz de dicha cuenta.

    b. El cuadro **Objetos disponibles** muestra los archivos y carpetas de la carpeta **AmbulanceData**.

    c. En el cuadro **Objetos que se registrarán** se enumeran los archivos y las carpetas que quiere registrar en Azure Data Catalog.

    ![Captura de pantalla de Microsoft Azure Data Catalog: cuadro de diálogo de la cuenta del almacén.](./media/data-lake-store-with-data-catalog/view-data-structure.png "Visualización de la estructura de datos")
1. Para este tutorial, deberá registrar todos los archivos del directorio. Para ello, haga clic en el botón (![movimiento de objetos](./media/data-lake-store-with-data-catalog/move-objects.png "Movimiento de objetos")) para mover todos los archivos al cuadro **Objetos que se registrarán** .

    Dado que los datos se registrarán en un catálogo de datos de toda la organización, es recomendable agregar algunos metadatos que podrá usar posteriormente para localizar rápidamente los datos. Por ejemplo, puede agregar una dirección de correo electrónico del propietario de los datos (por ejemplo, la persona que está cargando los datos) o agregar una etiqueta para identificar los datos. En la captura de pantalla siguiente se muestra una etiqueta que ha agregado a los datos.

    ![Captura de pantalla de Microsoft Azure Data Catalog: cuadro de diálogo de la cuenta del almacén con la etiqueta que se ha agregado a los datos resaltada.](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Visualización de la estructura de datos")

    Haga clic en **Registrar**.
1. La captura de pantalla siguiente indica que los datos se han registrado correctamente en el Catálogo de datos.

    ![Registro completado](./media/data-lake-store-with-data-catalog/registration-complete.png "Visualización de la estructura de datos")
1. Haga clic en **Ver portal** para volver atrás al portal del Catálogo de datos y comprobar que ya puede acceder a los datos registrados desde el portal. Para realizar búsquedas en los datos, puede utilizar la etiqueta que usó al registrar los datos.

     ![Búsqueda de datos en el catálogo](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Búsqueda de datos en el catálogo")
1. Ahora ya puede realizar operaciones como agregar anotaciones y documentación a los datos. Para más información, consulte los vínculos siguientes:

    * [Anotación de orígenes de datos](../data-catalog/data-catalog-how-to-annotate.md)
    * [Orígenes de datos de documentos](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Consulte también
* [Anotación de orígenes de datos](../data-catalog/data-catalog-how-to-annotate.md)
* [Orígenes de datos de documentos](../data-catalog/data-catalog-how-to-documentation.md)
* [Integración de Data Lake Storage Gen1 con otros servicios de Azure](data-lake-store-integrate-with-other-services.md)