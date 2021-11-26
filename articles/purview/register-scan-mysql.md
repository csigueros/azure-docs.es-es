---
title: Conexión y administración de MySQL
description: En esta guía se describe cómo conectarse a MySQL en Azure Purview y utilizar las características de Purview para explorar y administrar el origen de MySQL.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to
ms.openlocfilehash: 8f7d2dbdab590d2236032d3dd96a2008e2aa79c0
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553845"
---
# <a name="connect-to-and-manage-mysql-in-azure-purview-preview"></a>Conexión y administración de MySQL en Azure Purview (versión preliminar)

En este artículo se describe cómo registrar MySQL y cómo autenticar e interactuar con MySQL en Azure Purview. Para obtener más información sobre Azure Purview, consulte el [artículo de introducción](overview.md).

> [!IMPORTANT]
> MySQL está actualmente en versión preliminar. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register)| [Sí](#scan)| No | No | No | No| Sí|

Las versiones de servidor de MySQL admitidas son de la 5.7 a la 8.x.

Al examinar el origen de MySQL, Purview admite lo siguiente:

- Extraer metadatos, incluidos el servidor, las bases de datos, los esquemas, las tablas, las vistas y las columnas de tabla o vista de MySQL.
- Capturar el linaje en relaciones de recursos entre tablas y vistas.

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener detalles, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

* Configure la versión más reciente del [entorno de ejecución de integración autohospedado](https://www.microsoft.com/download/details.aspx?id=39717). Para obtener más información, consulte la [guía de creación y configuración de un entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md).

* Asegúrese de que [JDK 11](https://www.oracle.com/java/technologies/javase/jdk11-archive-downloads.html) esté instalado en la máquina virtual donde también lo esté el entorno de ejecución de integración autohospedado.

* Asegúrese de que Visual C++ Redistributable para Visual Studio 2012 Update 4 esté instalado en la máquina del entorno de ejecución de integración autohospedado. Si no tiene instalada esta actualización, [puede descargarla de aquí](https://www.microsoft.com/download/details.aspx?id=30679).

* El usuario de MySQL debe tener los permisos SELECT, SHOW VIEW y EXECUTE para cada esquema de MySQL de destino que contenga metadatos.

## <a name="register"></a>Register

En esta sección se describe cómo registrar MySQL en Azure Purview mediante [Purview Studio](https://web.purview.azure.com/).

### <a name="steps-to-register"></a>Pasos para registrarse

Para registrar un nuevo origen de MySQL en el catálogo de datos, haga lo siguiente:

1. Vaya a la cuenta de Purview en [Purview Studio](https://web.purview.azure.com/resource/).
1. Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.
1. Seleccione **Registrar**.
1. En Register sources (Registrar orígenes), seleccione **MySQL**. Seleccione **Continuar**.

En la pantalla **Register sources (MySQL)** (Registrar orígenes [MySQL]), haga lo siguiente:

1. Escriba un **Name** (Nombre) con el que se muestre el origen de datos en el catálogo.

1. Escriba el nombre del **servidor** para conectarse a un origen de MySQL. Puede ser:
    * Un nombre de host usado para conectarse al servidor de bases de datos. Por ejemplo: `MyDatabaseServer.com`
    * Dirección IP. Por ejemplo: `192.169.1.2`
    * Su cadena de conexión de JDBC completa. Por ejemplo:

        ```
        jdbc:mysql://COMPUTER_NAME_OR_IP/DATABASE_NAME
        ```

1. Escriba el **puerto** usado para conectarse al servidor de bases de datos (3306 de forma predeterminada para MySQL).

1. Seleccione una colección o cree una nueva (opcional).

1. Seleccione Finish (Finalizar) para registrar el origen de datos.

    :::image type="content" source="media/register-scan-mysql/register-sources.png" alt-text="Opciones de registro de orígenes" border="true":::

## <a name="scan"></a>Examinar

Siga los pasos que tiene a continuación para examinar MySQL e identificar automáticamente los recursos y clasificar los datos. Para obtener más información sobre el examen en general, consulte la [introducción a los exámenes y la ingesta](concept-scans-and-ingestion.md).

### <a name="authentication-for-a-scan"></a>Autenticación para un examen

El tipo de autenticación admitido para un origen de MySQL es la **autenticación básica**.

### <a name="create-and-run-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1. En el centro de administración, seleccione Entornos de ejecución de integración. Asegúrese de que está configurado un entorno de ejecución de integración autohospedado. Si no lo está, use los pasos que se indican [aquí](./manage-integration-runtimes.md) para crear un entorno de ejecución de integración autohospedado.

1. Vaya a **Sources** (Orígenes).

1. Seleccione el origen de MySQL registrado.

1. Seleccione **+ New scan** (+ Nuevo examen).

1. Especifique los detalles siguientes:

    1. **Name** (Nombre): el nombre del examen.

    1. **Connect via integration runtime** (Conectar mediante IR): seleccione el entorno de ejecución de integración configurado.

    1. **Credential** (Credencial): seleccione la credencial para conectarse al origen de datos. Asegúrese de que:
        * Seleccionar **Autenticación básica** al crear una credencial.
        * Proporcionar el nombre de usuario usado para conectarse al servidor de bases de datos en el campo de entrada Nombre de usuario.
        * Almacenar la contraseña de usuario usada para conectarse al servidor de bases de datos en la clave secreta.

    1. **Base de datos**: indique el subconjunto de esquemas que se va a importar expresado como una lista separada por puntos y coma. Por ejemplo: `schema1; schema2`. Si esa lista está vacía, se importan todos los esquemas del usuario. De forma predeterminada, se ignoran todos los esquemas del sistema (por ejemplo, SysAdmin) y los objetos. Si la lista está vacía, se importan todos los esquemas disponibles.

        Los patrones de nombres de esquema aceptables que usan la sintaxis de expresiones SQL LIKE incluyen el uso de %. Por ejemplo: `A%; %B; %C%; D`
        * empieza por A o
        * termina en B o
        * contiene C o
        * es igual a D

        No se acepta el empleo de NOT ni de caracteres especiales.

    1. **Maximum memory available** (Memoria máxima disponible): memoria máxima (en GB) disponible en la máquina virtual del cliente que van a usar los procesos de examen. Depende del tamaño del origen de MySQL que se va a examinar.

        > [!Note]
        > Como regla general, especifique 1 GB de memoria por cada 1000 tablas.

        :::image type="content" source="media/register-scan-mysql/scan.png" alt-text="Examinar MySQL" border="true":::

1. Seleccione **Continuar**.

1. Elija el **desencadenador del examen**. Puede configurar una programación o ejecutar el examen una vez.

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías mostradas a continuación para obtener más información sobre Purview y los datos.

- [Conclusiones sobre los datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
