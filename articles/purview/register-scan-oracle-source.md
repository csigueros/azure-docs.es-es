---
title: Conexión y administración de Oracle
description: En esta guía se describe cómo conectarse a v en Azure Purview y utilizar las características de Purview para explorar y administrar el origen de Oracle.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 6b04ef7f0ea408f1529346ed7fbc2b4cc45b4b35
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010942"
---
# <a name="connect-to-and-manage-oracle-in-azure-purview"></a>Conexión y administración de Oracle en Azure Purview

En este artículo se describe cómo registrar Oracle y cómo autenticar e interactuar con Oracle en Azure Purview. Para obtener más información sobre Azure Purview, consulte el [artículo de introducción](overview.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

|**Extracción de metadatos**|  **Examen completo**  |**Examen incremental**|**Examen con ámbito**|**Clasificación**|**Directiva de acceso**|**Lineage**|
|---|---|---|---|---|---|---|
| [Sí](#register)| [Sí](#scan)| No | No | No | No| [Sí](how-to-lineage-oracle.md)|

> [!Important]
> Las versiones de bases de datos de Oracle admitidas abarcan desde la 6i a la 19c.

No se admite el servidor proxy al examinar el origen de Oracle.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de Purview](create-catalog-portal.md) activo.

* Tendrá que ser administrador de orígenes de datos y lector de datos para poder registrar un origen y administrarlo en Purview Studio. Para obtener más información, consulte la [página Permisos de Azure Purview](catalog-permissions.md).

* Configure la versión más reciente del [entorno de ejecución de integración autohospedado](https://www.microsoft.com/download/details.aspx?id=39717). Para obtener más información, consulte la [guía de creación y configuración de un entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md).

* Asegúrese de que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) esté instalado en la máquina virtual donde también lo esté el entorno de ejecución de integración autohospedado.

* Asegúrese de que Visual C++ Redistributable para Visual Studio 2012 Update 4 esté instalado en la máquina del entorno de ejecución de integración autohospedado. Si no tiene instalada esta actualización, [puede descargarla de aquí](https://www.microsoft.com/download/details.aspx?id=30679).

* Descargue manualmente un controlador JDBC de Oracle desde [aquí](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html) en la máquina virtual donde se ejecuta el entorno de ejecución de integración autohospedado.

    > [!Note]
    > Todas las cuentas de la máquina virtual deben poder acceder al controlador. No lo instale en una cuenta de usuario.

## <a name="register"></a>Register

En esta sección se describe cómo registrar Oracle en Azure Purview mediante [Purview Studio](https://web.purview.azure.com/).

### <a name="prerequisites-for-registration"></a>Requisitos previos para el registro

Se requiere un acceso de solo lectura a las tablas del sistema.

El usuario debe contar con permiso para crear una sesión y tener asignado el rol SELECT\_CATALOG\_ROLE. También puede tener concedido el permiso SELECT en cada tabla del sistema en donde este conector consulte metadatos:

```sql
grant create session to [user];
grant select on all_users to [user];
grant select on dba_objects to [user];
grant select on dba_tab_comments to [user];
grant select on dba_external_locations to [user];
grant select on dba_directories to [user];
grant select on dba_mviews to [user];
grant select on dba_clu_columns to [user];
grant select on dba_tab_columns to [user];
grant select on dba_col_comments to [user];
grant select on dba_constraints to [user];
grant select on dba_cons_columns to [user];
grant select on dba_indexes to [user];
grant select on dba_ind_columns to [user];
grant select on dba_procedures to [user];
grant select on dba_synonyms to [user];
grant select on dba_views to [user];
grant select on dba_source to [user];
grant select on dba_triggers to [user];
grant select on dba_arguments to [user];
grant select on dba_sequences to [user];
grant select on dba_dependencies to [user];
grant select on dba_type_attrs to [user];
grant select on V_$INSTANCE to [user];
grant select on v_$database to [user];
```

### <a name="authentication-for-registration"></a>Autenticación para registro

La única autenticación admitida en un origen de Oracle es la **autenticación básica**.

### <a name="steps-to-register"></a>Pasos para registrarse

Para registrar un nuevo origen de Oracle en el catálogo de datos, haga lo siguiente:

1. Vaya a la cuenta de Purview en [Purview Studio](https://web.purview.azure.com/resource/).
1. Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.
1. Seleccione **Registrar**.
1. En Register sources (Registrar orígenes), seleccione **Oracle**. Seleccione **Continuar**.

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="Registro de orígenes" border="true":::

En la pantalla **Register sources (Oracle)** (Registrar orígenes (Oracle)), haga lo siguiente:

1. Escriba un **Name** (Nombre) con el que se muestre el origen de datos en el catálogo.

1. Escriba el nombre de **Host** para conectarse a un origen de Oracle. Puede ser:
    * Un nombre de host usado por JDBC para conectarse al servidor de bases de datos. Por ejemplo, MyDatabaseServer.com.
    * Dirección IP. Por ejemplo: 192.169.1.2.
    * Su cadena de conexión de JDBC completa. Por ejemplo:

         ```
        jdbc:oracle:thin:@(DESCRIPTION=(LOAD_BALANCE=on)(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver1)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver2)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver3)(PORT=1521))(CONNECT_DATA=(SERVICE_NAME=orcl)))
        ```

1. Escriba el **Port number** (Número de puerto) que use JDBC para conectarse al servidor de bases de datos (1521 de manera predeterminada en el caso de Oracle).

1. Escriba el **Oracle service name** (Nombre del servicio de Oracle) que usa JDBC para conectarse al servidor de bases de datos.

1. Seleccione una colección o cree una nueva (opcional).

1. Seleccione Finish (Finalizar) para registrar el origen de datos.

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="Opciones de registro de orígenes" border="true":::

## <a name="scan"></a>Examinar

Siga los pasos que tiene a continuación para examinar Oracle e identificar automáticamente los recursos y clasificar los datos. Para obtener más información sobre el examen en general, consulte la [introducción a los exámenes y la ingesta](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1. En el centro de administración, seleccione Entornos de ejecución de integración. Asegúrese de que está configurado un entorno de ejecución de integración autohospedado. Si no lo está, use los pasos que se indican [aquí](./manage-integration-runtimes.md) para crear un entorno de ejecución de integración autohospedado.

1. Vaya a **Sources** (Orígenes).

1. Seleccione el origen de Oracle registrado.

1. Seleccione **+ New scan** (+ Nuevo examen).

1. Especifique los detalles siguientes:

    1. **Name** (Nombre): el nombre del examen.

    1. **Conectar mediante un entorno de ejecución de integración**: seleccione el entorno de ejecución de integración configurado.

    1. **Credential** (Credencial): seleccione la credencial para conectarse al origen de datos. Asegúrese de que:
        * Selecciona la autenticación básica al crear una credencial.
        * Proporciona el nombre de usuario que usa JDBC para conectarse al servidor de bases de datos en el campo de entrada de nombre de usuario.
        * Almacena la contraseña de usuario que usa JDBC para conectarse al servidor de bases de datos en la clave secreta.

    1. **Schema** (Esquema): indique el subconjunto de esquemas que se va a importar expresado como una lista separada por puntos y coma. Por ejemplo, schema1; schema2. Si esa lista está vacía, se importan todos los esquemas del usuario. De forma predeterminada, se ignoran todos los esquemas del sistema (por ejemplo, SysAdmin) y los objetos. Si la lista está vacía, se importan todos los esquemas disponibles.
        Los patrones de nombres de esquema aceptables que usan la sintaxis de expresiones SQL LIKE incluyen el uso de %.
        Por ejemplo, A%; %B; %C%; D
           - empieza por A o
           - termina en B o
           - contiene C o
           - igual a D

        No se acepta el empleo de NOT ni de caracteres especiales.

1. **Driver location** (Ubicación del controlador): especifique la ruta de acceso a la ubicación del controlador JDBC en la máquina virtual donde se ejecuta el entorno de ejecución de integración autohospedado. Debe ser la ruta de acceso a la ubicación válida de la carpeta JAR.

    > [!Note]
    > Todas las cuentas de la máquina virtual deben poder acceder al controlador. No lo instale en una cuenta de usuario.

1. **Maximum memory available** (Memoria máxima disponible): memoria máxima (en GB) disponible en la máquina virtual del cliente que van a usar los procesos de examen. Depende del tamaño del origen de SAP S/4HANA que se va a examinar.

    > [!Note]
    > Como regla general, especifique 1 GB de memoria por cada 1000 tablas.

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="Examen de Oracle" border="true":::

1. Seleccione **Continuar**.

1. Elija el **desencadenador del examen**. Puede configurar una programación o ejecutar el examen una vez.

1. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha registrado el origen, siga las guías a continuación para obtener más información sobre Purview y sus datos.

- [Información sobre datos en Azure Purview](concept-insights.md)
- [Linaje en Azure Purview](catalog-lineage-user-guide.md)
- [Búsqueda en Data Catalog](how-to-search-catalog.md)
