---
title: 'De Oracle a Azure SQL Database: guía de migración'
description: Con esta guía aprenderá a migrar su esquema de Oracle a Azure SQL Database mediante SQL Server Migration Assistant para Oracle.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: cawrites
ms.author: chadam
ms.date: 08/25/2020
ms.openlocfilehash: 0a6f7bca2e6a3a1f0ac10f868ddeffe72d082c28
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746695"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>Guía de migración: de Oracle a Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Con esta guía aprenderá a [migrar](https://azure.microsoft.com/migration/migration-journey) su esquema de Oracle a Azure SQL Database mediante [SQL Server Migration Assistant](https://azure.microsoft.com/migration/sql-server/) para Oracle (SSMA para Oracle).

Para ver otras guías de migración, consulte la [Guía de Azure Database Migration](/data-migration).

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar a migrar el esquema de Oracle a SQL Database:

- Compruebe que el entorno de origen sea compatible.
- Descargue [SSMA para Oracle](https://www.microsoft.com/download/details.aspx?id=54258).
- Debe tener una instancia de [SQL Database](../../database/single-database-create-quickstart.md) de destino.
- Obtenga los [permisos necesarios de SSMA para Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) y el [proveedor](/sql/ssma/oracle/connect-to-oracle-oracletosql).

## <a name="pre-migration"></a>Antes de la migración

Una vez cumplidos los requisitos previos, estará listo para detectar la topología del entorno y evaluar la viabilidad de la [migración a la nube de Azure](https://azure.microsoft.com/migration). Esta parte del proceso implica la elaboración de un inventario de las bases de datos que se deben migrar, la evaluación de las bases de datos en busca de posibles problemas de migración o bloqueadores y la resolución de cualquier aspecto que pueda haber descubierto.

### <a name="assess"></a>Evaluar

Al usar SSMA para Oracle, puede revisar los objetos y datos de base de datos, evaluar las bases de datos para la migración, migrar los objetos de base de datos a SQL Database y, por último, migrar los datos a la base de datos.

Para crear una evaluación:

1. Abra [SSMA para Oracle](https://www.microsoft.com/download/details.aspx?id=54258).
1. Seleccione **Archivo** y, a continuación, seleccione **Nuevo proyecto**.
1. Escriba el nombre del proyecto y elija una ubicación para guardar el proyecto. Después, seleccione **Azure SQL Database** como destino de la migración en la lista desplegable y seleccione **Aceptar**.

   ![Captura de pantalla que muestra el cuadro de diálogo Conectarse a Oracle.](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

1. Seleccione **Connect to Oracle** (Conectarse a Oracle). Escriba los valores de los detalles de la conexión de Oracle en el cuadro de diálogo **Conectarse a Oracle**.

1. Seleccione los esquemas de Oracle que quiera migrar.

   ![Captura de pantalla que muestra la selección de esquema de Oracle.](./media/oracle-to-sql-database-guide/select-schema.png)

1. En **Explorador de metadatos de Oracle**, haga clic con el botón derecho en el esquema de Oracle que quiere migrar y seleccione **Crear informe** para generar un informe HTML. En lugar de ello, puede seleccionar una base de datos y luego la pestaña **Crear informe**.

   ![Captura de pantalla que muestra la opción Crear informe.](./media/oracle-to-sql-database-guide/create-report.png)

1. Revise el informe en HTML para comprender las estadísticas de conversión, y los errores o advertencias. También puede abrir el informe en Excel para obtener un inventario de objetos de Oracle y conocer el esfuerzo necesario para realizar las conversiones de esquema. La ubicación predeterminada del informe es la carpeta de informes dentro de SSMAProjects.

   Por ejemplo, vea `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`.

   ![Captura de pantalla que muestra un informe de valoración.](./media/oracle-to-sql-database-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>Validación de los tipos de datos

Valide las asignaciones predeterminadas de los tipos de datos y cámbielas según los requisitos, si es necesario. Para hacerlo, siga estos pasos:

1. En SSMA para Oracle, seleccione **Herramientas** y luego **Configuración del proyecto**.
1. Seleccione la pestaña **Asignación de tipo**.

   ![Captura de pantalla que muestra la pestaña Asignación de tipo.](./media/oracle-to-sql-database-guide/type-mappings.png)

1. Puede cambiar la asignación de tipo de cada tabla si selecciona la tabla en **Explorador de metadatos de Oracle**.

### <a name="convert-the-schema"></a>Conversión del esquema

Para convertir el esquema:

1. (Opcional) Agregue consultas dinámicas o ad hoc a las instrucciones. Haga clic con el botón derecho en el nodo y seleccione **Agregar instrucciones**.
1. Seleccione la pestaña **Conectar a Azure SQL Database**.
    1. En **SQL Database**, escriba los detalles de la conexión para conectar la base de datos.
    1. Seleccione la instancia de destino de SQL Database en la lista desplegable o escriba un nombre nuevo, en cuyo caso se creará una base de datos en el servidor de destino.
    1. Escriba los detalles de autenticación y seleccione **Conectar**.

    ![Captura de pantalla que muestra la pestaña Conectarse a Azure SQL Database.](./media/oracle-to-sql-database-guide/connect-to-sql-database.png)

1. En **Explorador de metadatos de Oracle**, haga clic con el botón derecho en el esquema de Oracle y seleccione **Convertir esquema**. O bien, puede seleccionar el esquema y luego la pestaña **Convertir esquema**.

   ![Captura de pantalla que muestra la pestaña Convertir esquema.](./media/oracle-to-sql-database-guide/convert-schema.png)

1. Una vez finalizada la conversión, compare y revise los objetos convertidos con los objetos originales para identificar posibles problemas y solucionarlos en función de las recomendaciones:

   ![Captura de pantalla que muestra el esquema de revisión de las recomendaciones.](./media/oracle-to-sql-database-guide/table-mapping.png)

1. Compare el texto de Transact-SQL convertido con el procedimiento almacenado original y revise las recomendaciones.

   ![Captura de pantalla que muestra la opción Revisar recomendaciones.](./media/oracle-to-sql-database-guide/procedure-comparison.png)

1. En el panel de resultados, seleccione **Revisar resultados** y revise los errores en el panel **Lista de errores**.
1. Guarde el proyecto localmente para un ejercicio de corrección de esquema sin conexión. En el menú **Archivo**, seleccione **Guardar proyecto**. Este paso le ofrece la oportunidad de evaluar los esquemas de origen y de destino sin conexión, y realizar correcciones antes de publicar el esquema en SQL Database.

## <a name="migrate"></a>Migrar

Después de evaluar las bases de datos y de solucionar las discrepancias, el siguiente paso consiste en ejecutar el proceso de migración. La migración conlleva dos pasos: publicar el esquema y migrar los datos.

Para publicar el esquema y migrar los datos, haga lo siguiente:

1. Para publicar el esquema, haga clic con el botón derecho en la base de datos desde el nodo **Bases de datos** del **Explorador de metadatos de Azure SQL Database** y seleccione **Sincronizar con la base de datos**.

   ![Captura de pantalla que muestra la pestaña Sincronizar con la base de datos.](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

1. Revise la asignación entre el proyecto de origen y el de destino.

   ![Captura de pantalla que muestra la revisión de Sincronizar con base de datos.](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)

1. Para migrar los datos, haga clic con el botón derecho en la base de datos o el objeto que quiera migrar en el **Explorador de metadatos de Oracle** y seleccione **Migrar datos**. O bien, puede seleccionar la pestaña **Migrar datos**. Para migrar datos de una base de datos completa, active la casilla situada junto al nombre de la base de datos. Para migrar datos de tablas concretas, expanda la base de datos, expanda **Tablas** y, a continuación, active las casillas que hay junto a las tablas. Para omitir datos de tablas concretas, desactive las casillas.

   ![Captura de pantalla que muestra la pestaña Migrar datos.](./media/oracle-to-sql-database-guide/migrate-data.png)

1. Especifique los detalles de la conexión para las instancias de Oracle y SQL Database.
1. Una vez completada la migración, vea el **Informe de migración de datos**.

   ![Captura de pantalla que muestra el Informe de migración de datos.](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. Conéctese a la instancia de SQL Database mediante [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) y revise los datos y el esquema para validar la migración.

   ![Captura de pantalla en la que se muestra SQL Server Management Studio.](./media/oracle-to-sql-database-guide/validate-data.png)

O bien, puede usar SQL Server Integration Services para realizar la migración. Para obtener más información, consulte:

- [Introducción a SQL Server Integration Services](/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services para Azure y movimiento de datos híbridos](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>Etapa posterior a la migración

Cuando haya completado correctamente la fase de *migración*, deberá realizar una serie de tareas posteriores para asegurarse de que todo funcione de la forma más fluida y eficaz posible.

### <a name="remediate-applications"></a>Corrección de las aplicaciones

Cuando se hayan migrado los datos al entorno de destino, todas las aplicaciones que antes utilizaban el origen deben empezar a utilizar el destino. En algunos casos, completar esta tarea requerirá la realización de cambios en las aplicaciones.

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) es una extensión para Visual Studio Code que permite analizar el código fuente de Java y detectar llamadas y consultas de la API de acceso a datos. El kit de herramientas proporciona una vista de un solo panel de lo que se debe solucionar para admitir el nuevo back-end de base de datos. Para obtener más información, consulte la entrada de blog [Migración de la aplicación Java desde Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727).

### <a name="perform-tests"></a>Realización de pruebas

El enfoque de prueba para la migración de bases de datos consta de las siguientes actividades:

1. **Desarrollar pruebas de validación**: para probar la migración de bases de datos, debe utilizar consultas SQL. Debe crear las consultas de validación para que se ejecuten en las bases de datos de origen y destino. Las consultas de validación deben abarcar el ámbito definido.
1. **Configurar un entorno de prueba**: el entorno de prueba debe contener una copia de la base de datos de origen y la base de datos de destino. Asegúrese de aislar el entorno de prueba.
1. **Ejecutar pruebas de validación**: ejecute las pruebas de validación en el origen y el destino y, luego, analice los resultados.
1. **Ejecutar pruebas de rendimiento**: ejecute la prueba de rendimiento en el origen y el destino y, luego, analice y compare los resultados.

### <a name="validate-migrated-objects"></a>Validación de objetos migrados

Microsoft SQL Server Migration Assistant para Oracle Tester (SSMA Tester) permite probar objetos de base de datos migrados. SSMA Tester se usa para comprobar que los objetos convertidos se comportan de la misma manera.

#### <a name="create-test-case"></a>Crear caso de prueba

1. Abra SSMA para Oracle, seleccione **Tester (Evaluador)** seguido de **New Test Case (Nuevo caso de prueba)** .
   ![Captura de pantalla que muestra cómo crear un nuevo caso de prueba.](./media/oracle-to-sql-database-guide/ssma-tester-new.png)

1. Proporcione la siguiente información para el nuevo caso de prueba:

   **Name** (Nombre): escriba el nombre para identificar el caso de prueba.

   **Creation date** (Fecha de creación): fecha actual de hoy, definida automáticamente.

   **Last Modified date** (Fecha de última modificación): se rellena automáticamente; no debe cambiarse.

   **Description** (Descripción): escriba cualquier información adicional para identificar la finalidad del caso de prueba.

   ![Captura de pantalla que muestra los pasos para inicializar un caso de prueba.](./media/oracle-to-sql-database-guide/tester-init-test-case.png)

1. Seleccione los objetos que forman parte del caso de prueba en el árbol de objetos de Oracle ubicado en el lado izquierdo.

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-select-configure-objects.png" alt-text="Captura de pantalla que muestra el paso para seleccionar y configurar el objeto.":::

   En este ejemplo, se seleccionan el procedimiento almacenado `ADD_REGION` y la tabla `REGION`.

     Para obtener más información, consulte [Selección y configuración de objetos de prueba (OracleToSQL)](/sql/ssma/oracle/selecting-and-configuring-objects-to-test-oracletosql).

1. A continuación, seleccione las tablas, las claves externas y otros objetos dependientes del árbol de objetos de Oracle en la ventana izquierda.

   :::image type="content" source="./media//oracle-to-sql-database-guide/tester-select-configure-affected.png" alt-text="Captura de pantalla que muestra el paso para seleccionar y configurar el objeto afectado.":::

   Para obtener más información, consulte [Selección y configuración de objetos afectados (OracleToSQL)](/sql/ssma/oracle/selecting-and-configuring-affected-objects-oracletosql).

1. Revise la secuencia de evaluación de objetos. Para cambiar el orden, haga clic en los botones de la cuadrícula.

   :::image type="content" source="./media/oracle-to-sql-database-guide/test-call-ordering.png" alt-text="Captura de pantalla que muestra el paso para secuenciar la ejecución de objetos de prueba.":::

1. Para finalizar el caso de prueba, revise la información proporcionada en los pasos anteriores. Configure las opciones de ejecución de pruebas en función del escenario de prueba.

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-finalize-case.png" alt-text="Captura de pantalla que muestra el paso para finalizar el objeto.":::

   Para más información sobre la configuración de casos de prueba, consulte [Finalización de la preparación del caso de prueba (OracleToSQL)](/sql/ssma/oracle/finishing-test-case-preparation-oracletosql).

1. Haga clic en Finalizar para crear el caso de prueba.

   :::image type="content" source="./media//oracle-to-sql-database-guide/tester-test-repo.png" alt-text="Captura de pantalla que muestra el paso para probar el repositorio.":::

#### <a name="run-test-case"></a>Ejecución del caso de prueba

Cuando el evaluador de SSMA ejecuta un caso de prueba, el motor de pruebas ejecuta los objetos seleccionados para las pruebas y genera un informe de comprobación.

1. Seleccione el caso de prueba en el repositorio de pruebas y haga clic en Ejecutar.

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-repo-run.png" alt-text="Captura de pantalla que se muestra para revisar el repositorio de pruebas.":::

1. Revise el caso de prueba de inicio y haga clic en Ejecutar.

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-run-test-case.png" alt-text="Captura de pantalla que muestra el paso para ejecutar el caso de prueba":::

1. A continuación, proporcione las credenciales de origen de Oracle. Haga clic en Conectar después de escribir las credenciales.

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-oracle-connect.png" alt-text="Captura de pantalla que muestra el paso para conectarse al origen de Oracle":::

1. Proporcione las credenciales de SQL Server de destino y haga clic en Conectar.

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-sql-connect.png" alt-text="Captura de pantalla que muestra el paso para conectarse al destino de SQL.":::

   Si se realiza correctamente, el caso de prueba pasa a la página de inicialización.

1. Una barra de progreso en tiempo real muestra el estado de ejecución de la ejecución de prueba.

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-run-status.png" alt-text="Captura de pantalla que muestra el progreso de la prueba del evaluador.":::

1. Revise el informe una vez completada la prueba. El informe proporciona las estadísticas, los errores durante la ejecución de pruebas y un informe detallado.

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-test-result.png" alt-text="Captura de pantalla que muestra un informe de prueba del evaluador de ejemplo":::

1. Haga clic en detalles para obtener más información.

   Ejemplo de validación de datos positivos.
   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-test-success.png" alt-text="Captura de pantalla que muestra un informe correcto del evaluador de ejemplo.":::

   Ejemplo de validación de datos con errores.

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-test-failed.png" alt-text="Captura de pantalla que muestra el informe de errores del evaluador.":::

### <a name="optimize"></a>Optimización

La fase posterior a la migración es fundamental para reconciliar cualquier problema de precisión de datos y comprobar su integridad, así como para solucionar problemas de rendimiento con la carga de trabajo.

> [!NOTE]
> Para obtener más información sobre estos problemas y los pasos para mitigarlos, consulte la [Guía de optimización y validación posterior a la migración](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Recursos de migración

Para obtener más ayuda para completar este escenario de migración, consulte los siguientes recursos. Se han desarrollado para ayudar en la interacción con un proyecto de migración real.

| **Título/vínculo**                                                                                                                                          | **Descripción**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Herramienta y modelo de evaluación de la carga de trabajo de datos](https://www.microsoft.com/download/details.aspx?id=103130) | Esta herramienta proporciona sugerencias de plataformas de destino "ideales", preparación para la nube y el nivel de corrección de la aplicación o base de datos para una carga de trabajo determinada. Ofrece un cálculo sencillo con un solo clic y una función de generación de informes que ayuda a acelerar las evaluaciones de grandes volúmenes, ya que proporciona un proceso de toma de decisiones de plataforma de destino uniforme y automatizado.                                                          |
| [Artefactos de script de inventario de Oracle](https://www.microsoft.com/download/details.aspx?id=103121)                 | Este recurso incluye una consulta PL/SQL que visita las tablas del sistema de Oracle y proporciona un recuento de objetos por tipo de esquema, tipo de objeto y estado. También proporciona una estimación aproximada de datos sin procesar en cada esquema y del tamaño de las tablas de cada esquema; almacena los resultados en formato CSV.                                                                                                               |
| [Automatización de la recopilación y consolidación de evaluaciones de SSMA para Oracle](https://www.microsoft.com/download/details.aspx?id=103120)                                             | Este conjunto de recursos usa un archivo .csv como entrada (sources.csv en las carpetas del proyecto) para generar los archivos XML necesarios para ejecutar una evaluación de SSMA en modo de consola. El cliente proporciona el archivo source.csv basado en un inventario de las instancias de Oracle existentes. Los archivos de salida son AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml y VariableValueFile.xml.|
| [Utilidad Comparación de bases de datos de Oracle a SQL DB](https://www.microsoft.com/download/details.aspx?id=103016)|SSMA para Oracle Tester es la herramienta recomendada para validar automáticamente la conversión de objetos de base de datos y la migración de datos. Se trata de un superconjunto de la funcionalidad Comparación de bases de datos.<br /><br />Si busca una opción alternativa de validación de datos, puede usar la utilidad Comparación de bases de datos para comparar los datos hasta el nivel de fila o de columna en todas las tablas, filas y columnas o únicamente en las seleccionadas.|

El equipo de ingeniería de datos SQL ha desarrollado estos recursos. El objetivo principal de este equipo es permitir y acelerar la modernización compleja de los proyectos de migración de la plataforma de datos a la de Azure, de Microsoft.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener una matriz de los servicios y las herramientas de Microsoft y de otros fabricantes que están disponibles para ayudarle en diversos escenarios de migración de datos y bases de datos, además de las tareas especializadas, consulte [Servicios y herramientas disponibles para escenarios de migración de datos](../../../dms/dms-tools-matrix.md).

- Para más información sobre SQL Database, consulte:
  - [¿Qué es Azure SQL Database?](../../database/sql-database-paas-overview.md)
  - [Calculadora del costo total de propiedad (TCO) de Azure](https://azure.microsoft.com/pricing/tco/calculator/)

- Para más información sobre el marco y el ciclo de adopción de las migraciones en la nube, consulte:
   -  [Cloud Adoption Framework para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedimientos recomendados para gestionar los costos y el tamaño de las cargas de trabajo migradas a Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
   -  [Recursos de migración a la nube](https://azure.microsoft.com/migration/resources)

- En cuanto al contenido de vídeo, consulte:
    - [Información general sobre el recorrido de la migración y las herramientas y servicios recomendados para llevar a cabo la evaluación y la migración](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).
