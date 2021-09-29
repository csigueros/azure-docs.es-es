---
title: Copia masiva desde una base de datos con una tabla de control
description: Obtenga información sobre cómo usar una plantilla de solución para la copia de datos masiva desde una base de datos con una tabla de control externa a fin de almacenar una lista de particiones de las tablas de origen con Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: 94f7210c05056db2ead8b1aa54d9a0d16e3d8849
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743586"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Copia masiva desde una base de datos con una tabla de control

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Para copiar datos desde un almacenamiento de datos como Oracle Server, Netezza, Teradata o SQL Server en Azure Synapse Analytics, tiene que cargar enormes cantidades de datos de varias tablas. Normalmente, los datos se tienen que dividir aún más en cada tabla para poder cargar filas con varios subprocesos en paralelo desde una única tabla. En este artículo se describe una plantilla para utilizarla en estos escenarios.

 >Nota: Si quiere copiar datos desde un número reducido de tablas con un volumen de datos relativamente pequeño en Azure Synapse Analytics, resulta más eficaz usar la [herramienta Copiar datos de Azure Data Factory](copy-data-tool.md). La plantilla que se describe en este artículo es más de lo que necesita para esa situación.

## <a name="about-this-solution-template"></a>Acerca de esta plantilla de solución

Esta plantilla recupera una lista de particiones de la base de datos de origen para copiar desde una tabla de control externa. A continuación, recorre en iteración cada partición en la base de datos de origen y copia los datos en el destino.

La plantilla contiene tres actividades:
- **Lookup** recupera una lista de particiones de la base de datos de origen para copiar desde una tabla de control externa.
- **ForEach** obtiene la lista de particiones de la actividad Lookup y recorre en iteración cada partición para la actividad Copy.
- **Copy** copia cada partición del almacén de la base de datos de origen en el almacén de destino.

La plantilla define los parámetros siguientes:
- *Control_Table_Name* es la tabla de control externo, que almacena la lista de partición para la base de datos de origen.
- *Control_Table_Schema_PartitionID* es el nombre de columna en la tabla de control externa que almacena cada identificador de partición. Asegúrese de que el identificador de partición es único para cada partición en la base de datos de origen.
- *Control_Table_Schema_SourceTableName* es la tabla de control externa que almacena cada nombre de tabla desde la base de datos de origen.
- *Control_Table_Schema_FilterQuery* es el nombre de la columna en la tabla de control externa que almacena la consulta de filtro para obtener los datos de cada partición de la base de datos de origen. Por ejemplo, si realizó una partición en los datos por año, la consulta almacenada en cada fila podría ser similar a select * from datasource where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999''.
- *Data_Destination_Folder_Path* es la ruta de acceso en que se copian los datos en el almacén de destino (aplicable cuando se selecciona "Sistema de archivos" o "Azure Data Lake Storage Gen1" como destino). 
- *Data_Destination_Container* es la ruta de acceso de la carpeta raíz del lugar donde los datos se copian en el almacén de destino. 
- *Data_Destination_Directory* es la ruta de acceso del directorio en la raíz donde los datos se copian en el almacén de destino. 

Los tres últimos parámetros, que definen la ruta de acceso en el almacén de destino, solo están visibles si el destino que elige es almacenamiento basado en archivos. Si elige "Azure Synapse Analytics" como almacén de destino, estos parámetros no son necesarios. Pero los nombres de tabla y el esquema en Azure Synapse Analytics deben ser iguales que los de la base de datos de origen.

## <a name="how-to-use-this-solution-template"></a>Uso de esta plantilla de solución

1. Cree una tabla de control en un servidor SQL Server o Azure SQL Database para almacenar la lista de particiones de la base de datos de origen para la copia masiva. En el ejemplo siguiente, hay cinco particiones en la base de datos de origen. Tres particiones son para *datasource_table* y dos para *project_table*. La columna *LastModifytime* se utiliza para dividir los datos de la tabla *datasource_table* desde la base de datos de origen. La consulta que se usa para leer la primera partición es select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' y LastModifytime <= ''2015-12-31 23:59:59.999''. Puede usar una consulta similar para leer datos de otras particiones.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. Vaya a la plantilla **Copia masiva desde base de datos**. Crear una conexión **nueva** a la tabla de control externo que ha creado en el paso 1.

    :::image type="content" source="media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png" alt-text="Creación de una nueva conexión a la tabla de control":::

3. Creación de una **nueva** conexión a la base de datos de origen desde la que está copiando datos.

    :::image type="content" source="media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png" alt-text="Creación de una nueva conexión a la base de datos de origen":::
    
4. Cree una conexión **nueva** al almacén de datos de destino en el que está copiando datos.

    :::image type="content" source="media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png" alt-text="Creación de una nueva conexión al almacén de destino":::

5. Seleccione **Usar esta plantilla**.

6. Ve la canalización, como se muestra en el ejemplo siguiente:

    :::image type="content" source="media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png" alt-text="Revisión de la canalización":::

7. Seleccione **Depurar**, escriba los **parámetros** y, a continuación, seleccione **Finalizar**.

    :::image type="content" source="media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png" alt-text="Clic en **Depurar**":::

8. Ve resultados similares al ejemplo siguiente:

    :::image type="content" source="media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png" alt-text="Revisión del resultado":::

9. (Opcional) Si elige "Azure Synapse Analytics" como destino de datos, debe especificar una conexión a una instancia de Azure Blob Storage como almacenamiento provisional, porque así lo requiere PolyBase de Azure Synapse Analytics. La plantilla generará automáticamente una ruta de acceso de contenedor para Blob Storage. Consulte si el contenedor se ha creado después de la ejecución de la canalización.
    
    :::image type="content" source="media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png" alt-text="Configuración de PolyBase":::
       
## <a name="next-steps"></a>Pasos siguientes

- [Introducción al servicio Azure Data Factory](introduction.md)
