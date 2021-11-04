---
title: Conexión y administración de bases de datos de metastore de Hive
description: En esta guía se describe cómo conectarse a bases de datos de metastore de Hive en Azure Purview y usar las características de Purview para examinar y administrar el origen de la base de datos de metastore de Hive.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 074a2cbef5a19185f2e817f9cedd4ea509a3d966
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457285"
---
# <a name="connect-to-and-manage-hive-metastore-databases-in-azure-purview"></a>Conexión y administración de bases de datos de metastore de Hive en Azure Purview

En este artículo se describe cómo registrar bases de datos de Hive Metastore y cómo autenticarse e interactuar con las bases de datos de metastore de Hive en Azure Purview. Para obtener más información sobre Azure Purview, lea el [artículo de introducción](overview.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register)| [Sí](#scan)| No | No | No | No| Sí |

> [!Important]
> Las plataformas admitidas son Apache Hadoop, Cloudera, Hortonworks y Databricks.
> Las versiones admitidas de Hive son de la 2.x a la 3.x. Las versiones admitidas de Databricks son 8.0 y posteriores.

## <a name="prerequisites"></a>Prerrequisitos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

* Configure la versión más reciente del [entorno de ejecución de integración autohospedado](https://www.microsoft.com/download/details.aspx?id=39717). Para obtener más información, consulte la [guía de creación y configuración de un entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md).

* Asegúrese de que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) esté instalado en la máquina virtual donde también lo esté el entorno de ejecución de integración autohospedado.

* Asegúrese de que Visual C++ Redistributable para Visual Studio 2012 Update 4 esté instalado en la máquina del entorno de ejecución de integración autohospedado. Si no tiene instalada esta actualización, [puede descargarla aquí](https://www.microsoft.com/download/details.aspx?id=30679).

* Descargue e instale el controlador JDBC de la base de datos de metastore de Hive en la máquina en la que se ejecuta el entorno de ejecución de integración autohospedado. Por ejemplo, si la base de datos usada es mssql, asegúrese de descargar el [controlador JDBCde Microsoft para SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

    > [!Note]
    > Todas las cuentas de la máquina virtual deben poder acceder al controlador. No lo instale en una cuenta de usuario.

## <a name="register"></a>Register

En esta sección se describe cómo registrar bases de datos de metastore de Hive en Azure Purview mediante [Purview Studio](https://web.purview.azure.com/).

La única autenticación admitida en una base de datos de metastore de Hive es la **autenticación básica**.

### <a name="steps-to-register"></a>Pasos para registrarse

1. Vaya a la cuenta de Purview.

1. Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.

1. Seleccione **Registrar**.

1. En Registrar orígenes, seleccione **Metastore de Hive**. Seleccione **Continue** (Continuar).

    :::image type="content" source="media/register-scan-hive-metastore-source/register-sources.png" alt-text="registro del origen de Hive" border="true":::

En la pantalla Registrar orígenes (metastore de Hive), haga lo siguiente:

1. Escriba un **Name** (Nombre) con el que se muestre el origen de datos en el catálogo.

1. Escriba la dirección **URL del clúster de Hive**. La dirección URL del clúster se puede obtener de la dirección URL de Ambari o de la dirección URL del área de trabajo de Databricks. Por ejemplo, hive.azurehdinsight.net o adb-19255636414785.5.azuredatabricks.net

1. Especifique la **dirección URL del servidor del metastore de Hive**. Por ejemplo, sqlserver://hive.database.windows.net o jdbc:spark://adb-19255636414785.5.azuredatabricks.net:443

1. Seleccione una colección o cree una nueva (opcional).

1. Seleccione Finish (Finalizar) para registrar el origen de datos.

    :::image type="content" source="media/register-scan-hive-metastore-source/configure-sources.png" alt-text="configuración del origen de Hive" border="true":::

## <a name="scan"></a>Examinar

Siga los pasos a continuación para examinar bases de datos de metastore de Hive a fin de identificar automáticamente los recursos y clasificar los datos. Para obtener más información sobre el examen en general, consulte la [introducción a los exámenes y la ingesta](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Creación y ejecución de un examen

1. En el centro de administración, seleccione Entornos de ejecución de integración. Asegúrese de que está configurado un entorno de ejecución de integración autohospedado. Si no está configurado, siga los pasos mencionados [aquí](./manage-integration-runtimes.md] para configurar un entorno de ejecución de integración autohospedado.

1. Vaya a **Sources** (Orígenes).

1. Seleccione la base de datos de **metastore de Hive** registrada.

1. Seleccione **+ New scan** (+ Nuevo examen).

1. Especifique los detalles siguientes:

    1. **Name** (Nombre): el nombre del examen.

    1. **Connect via integration runtime** (Conectar mediante el entorno de ejecución de integración): seleccione el entorno de ejecución de integración autohospedado configurado.

    1. **Credential** (Credencial): seleccione la credencial para conectarse al origen de datos. Asegúrese de que:

       * Selecciona la autenticación básica al crear una credencial.
       * Proporcione el nombre de usuario de Metastore en el campo de entrada Nombre de usuario.
       * Almacene la contraseña de Metastore en la clave secreta.

       Para obtener más información sobre credenciales, vea el vínculo que se indica [aquí](manage-credentials.md).

       **Uso de Databricks**: vaya al clúster de Databricks -> Aplicaciones -> Iniciar terminal web. Ejecute el cmdlet **cat /databricks/hive/conf/hive-site.xml**

       Se puede acceder al nombre de usuario y la contraseña desde las dos propiedades, como se muestra a continuación:

       :::image type="content" source="media/register-scan-hive-metastore-source/databricks-credentials.png" alt-text="databricks-username-password-details" border="true":::

    1. **Ubicación del controlador JDBC de Metastore**: especifique la ruta de acceso a la ubicación del controlador JDBC en la VM donde se ejecuta el entorno de ejecución de integración autohospedado. Debe ser la ruta de acceso a la ubicación válida de la carpeta JAR.

       Si va a examinar Databricks, vea la sección siguiente sobre Databricks.

       > [!Note]
       > Todas las cuentas de la máquina virtual deben poder acceder al controlador. No lo instale en una cuenta de usuario.

    1. **Clase de controlador JDBC de Metastore**: proporcione el nombre de la clase del controlador de conexión. Por ejemplo, \com.microsoft.sqlserver.jdbc.SQLServerDriver.

       **Uso de Databricks**: vaya al clúster de Databricks -> Aplicaciones -> Iniciar terminal web. Ejecute el cmdlet **cat /databricks/hive/conf/hive-site.xml**

       Se puede acceder a la clase de controlador desde la propiedad, como se muestra a continuación.

        :::image type="content" source="media/register-scan-hive-metastore-source/databricks-driver-class-name.png" alt-text="databricks-driver-class-details" border="true":::

    1. **Dirección URL de JDBC de Metastore**: proporcione el valor de la dirección URL de conexión y defina la conexión a la dirección URL del servidor de base de datos de Metastore. Por ejemplo: `jdbc:sqlserver://hive.database.windows.net;database=hive;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300`.

       **Uso de Databricks**: vaya al clúster de Databricks -> Aplicaciones -> Iniciar terminal web. Ejecute el cmdlet **cat /databricks/hive/conf/hive-site.xml**

       Se puede acceder a la dirección URL de JDBC desde la propiedad Dirección URL de conexión, como se muestra a continuación.

       :::image type="content" source="media/register-scan-hive-metastore-source/databricks-jdbc-connection.png" alt-text="databricks-jdbc-url-details" border="true":::

       > [!NOTE]
       > Al copiar la dirección URL de *hive-site.xml*, asegúrese de quitar `amp;` de la cadena para que no se produzca un error en el examen.

       A esta dirección URL anéxele la ruta de acceso a la ubicación donde está colocado el certificado SSL en la máquina virtual. El certificado SSL se puede descargar desde [aquí](../mysql/howto-configure-ssl.md).

       La dirección URL de JDBC del metastore es:

       `jdbc:mariadb://consolidated-westus2-prod-metastore-addl-1.mysql.database.azure.com:3306/organization1829255636414785?trustServerCertificate=true&amp;useSSL=true&sslCA=D:\Drivers\SSLCert\BaltimoreCyberTrustRoot.crt.pem`

    1. **Nombre de la base de datos del metastore**: proporcione el nombre de la base de datos del metastore de Hive.

       Si va a examinar Databricks, vea la sección siguiente sobre Databricks.

       **Uso de Databricks**: vaya al clúster de Databricks -> Aplicaciones -> Iniciar terminal web. Ejecute el cmdlet **cat /databricks/hive/conf/hive-site.xml**

       Se puede acceder al nombre de la base de datos desde la propiedad Dirección URL de JDBC, como se muestra a continuación. Por ejemplo: organization1829255636414785

       :::image type="content" source="media/register-scan-hive-metastore-source/databricks-data-base-name.png" alt-text="databricks-database-name-details" border="true":::

    1. **Esquema**: especifique la lista de esquemas de Hive que se importarán. Por ejemplo, schema1; schema2.

        Si esa lista está vacía, se importan todos los esquemas del usuario. De forma predeterminada, se ignoran todos los esquemas del sistema (por ejemplo, SysAdmin) y los objetos.

        Si la lista está vacía, se importan todos los esquemas disponibles. Los patrones de nombres de esquema aceptables que usan la sintaxis de expresiones SQL LIKE incluyen el uso de %. Por ejemplo, A%; %B; %C%; D

        * empieza por A o
        * termina en B o
        * contiene C o
        * igual a D

        No se acepta el empleo de NOT ni de caracteres especiales.

    1. **Maximum memory available** (Memoria máxima disponible): memoria máxima (en GB) disponible en la máquina virtual del cliente que van a usar los procesos de examen. Esto depende del tamaño del origen de la base de datos de Metastore de Hive que se va a examinar.

        :::image type="content" source="media/register-scan-hive-metastore-source/scan.png" alt-text="examinar el origen de Hive" border="true":::

1. Seleccione **Continuar**.

1. Elija el **desencadenador del examen**. Puede configurar una programación o ejecutar el examen una vez.

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías a continuación para obtener más información sobre Purview y sus datos.

- [Información sobre datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
