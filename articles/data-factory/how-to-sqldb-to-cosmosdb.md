---
title: Migración de tablas de Azure SQL Database a Azure CosmosDB con Azure Data Factory
description: Tome un esquema de base de datos normalizado existente de Azure SQL Database y mígrelo a un contenedor desnormalizado de Azure CosmosDB con Azure Data Factory.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.date: 04/29/2020
ms.openlocfilehash: d37496d8f29585c8a9ad956e3f5790ac11fb748e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733019"
---
# <a name="migrate-normalized-database-schema-from-azure-sql-database-to-azure-cosmosdb-denormalized-container"></a>Migración del esquema de base de datos normalizado de Azure SQL Database a un contenedor desnormalizado de Azure CosmosDB

En esta guía se explica cómo tomar un esquema de base de datos normalizado existente en Azure SQL Database y convertirlo en un esquema desnormalizado de Azure CosmosDB para cargarlo en Azure CosmosDB.

Los esquemas de SQL normalmente se modelan utilizando la tercera forma normal, lo que da lugar a esquemas normalizados que proporcionan altos niveles de integridad de datos y menos valores de datos duplicados. Las consultas pueden combinar entidades entre tablas para su lectura. CosmosDB está optimizado para transacciones y consultas muy rápidas dentro de una colección o un contenedor a través de esquemas desnormalizados con datos independientes dentro de un documento.

Con Azure Data Factory, crearemos una canalización que use un flujo de datos de asignación único para la lectura de dos tablas normalizadas de Azure SQL Database que contienen claves principales y externas como relación de entidad. ADF unirá esas tablas en una sola secuencia mediante el motor de Spark de flujo de datos, recopilará las filas unidas en matrices y generará documentos limpios individuales para insertarlos en un nuevo contenedor de Azure CosmosDB.

En esta guía se compilará un nuevo contenedor sobre la marcha denominado "orders" que usará las tablas ```SalesOrderHeader``` y ```SalesOrderDetail``` de la base de datos de ejemplo estándar AdventureWorks de SQL Server. Estas tablas representan las transacciones de ventas unidas por ```SalesOrderID```. Cada registro de detalle único tiene su propia clave principal de ```SalesOrderDetailID```. La relación entre el encabezado y el detalle es ```1:M```. Realizaremos la combinación en ```SalesOrderID``` en ADF y luego extraeremos cada registro de detalle relacionado en una matriz denominada "detail".

La consulta SQL representativa para esta guía es:

```
  SELECT
  o.SalesOrderID,
  o.OrderDate,
  o.Status,
  o.ShipDate,
  o.SalesOrderNumber,
  o.ShipMethod,
  o.SubTotal,
  (select SalesOrderDetailID, UnitPrice, OrderQty from SalesLT.SalesOrderDetail od where od.SalesOrderID = o.SalesOrderID for json auto) as OrderDetails
FROM SalesLT.SalesOrderHeader o;
```

El contenedor de CosmosDB resultante incrustará la consulta interna en un único documento, que tendrá el siguiente aspecto:

![Colección](media/data-flow/cosmosb3.png)

## <a name="create-a-pipeline"></a>Crear una canalización

1. Seleccione **+Nueva canalización** para iniciar una nueva canalización.

2. Agregue una actividad de flujo de datos.

3. En la actividad de flujo de datos, seleccione **New Mapping Data Flow** (Nuevo flujo de datos de asignación).

4. Crearemos el gráfico de flujo de datos siguiente:

![Gráfico de flujo de datos](media/data-flow/cosmosb1.png)

5. Defina el origen de "SourceOrderDetails". Para el conjunto de datos, cree un nuevo conjunto de datos de Azure SQL Database que apunte a la tabla ```SalesOrderDetail```.

6. Defina el origen de "SourceOrderHeader". Para el conjunto de datos, cree un nuevo conjunto de datos de Azure SQL Database que apunte a la tabla ```SalesOrderHeader```.

7. En el origen superior, agregue una transformación de columna derivada después de "SourceOrderDetails". Llame a la nueva transformación "TypeCast". Necesitamos redondear la columna ```UnitPrice``` y convertirla a un tipo de datos doble para CosmosDB. Establezca la fórmula en: ```toDouble(round(UnitPrice,2))```.

8. Agregue otra columna derivada y denomínela "MakeStruct". Aquí es donde se creará una estructura jerárquica para contener los valores de la tabla de detalles. Recuerde que los detalles son una relación de ```M:1``` con el encabezado. Asigne a la nueva estructura el nombre ```orderdetailsstruct``` y cree la jerarquía de esta manera, de manera que cada subcolumna se establezca en el nombre de la columna entrante:

![Create Structure](media/data-flow/cosmosb9.png)

9. Ahora vamos a ir al origen del encabezado de ventas. Agregue una transformación Combinación. En el lado derecho, seleccione "MakeStruct". Déjelo establecido en combinación interna y elija ```SalesOrderID``` para ambos lados de la condición de combinación.

10. Haga clic en la pestaña Data Preview (Vista previa de datos) en la nueva combinación que ha agregado para poder ver los resultados hasta este punto. Debería ver todas las filas de encabezado combinadas con las filas de detalles. Este es el resultado de la combinación que se está formando desde el objeto ```SalesOrderID```. A continuación, combinaremos los detalles de las filas comunes en la estructura de detalles y agregaremos las filas comunes.

![Join](media/data-flow/cosmosb4.png)

11. Para poder crear las matrices para desnormalizar estas filas, primero es necesario quitar las columnas no deseadas y asegurarse de que los valores de datos coinciden con los tipos de datos de CosmosDB.

12. Agregue una transformación Selección a continuación y establezca la asignación de campos de modo que tenga este aspecto:

![Limpieza de columnas](media/data-flow/cosmosb5.png)

13. A continuación volveremos a convertir una columna de divisa, esta vez ```TotalDue```. Como antes en el paso 7, establezca la fórmula en: ```toDouble(round(TotalDue,2))```.

14. Aquí es donde se desnormalizarán las filas mediante la agrupación por la clave común ```SalesOrderID```. Agregue una transformación Agregado y establezca Agrupar por en ```SalesOrderID```.

15. En la fórmula de agregado, agregue una nueva columna denominada "details" y use esta fórmula para recopilar los valores de la estructura que hemos creado anteriormente denominada ```orderdetailsstruct```: ```collect(orderdetailsstruct)```.

16. La transformación Agregado solo dará como resultado columnas que formen parte de las fórmulas Agregados o Agrupar por. Por lo tanto, es necesario incluir también las columnas del encabezado de ventas. Para ello, agregue un patrón de columna en esa misma transformación Agregado. Este patrón incluirá todas las demás columnas en la salida:

```instr(name,'OrderQty')==0&&instr(name,'UnitPrice')==0&&instr(name,'SalesOrderID')==0```

17. Use la sintaxis "this" en las otras propiedades para que podamos mantener los mismos nombres de columna y usar la función ```first()``` como un agregado:

![Agregado](media/data-flow/cosmosb6.png)

18. Estamos preparados para finalizar el flujo de migración mediante la adición de una transformación de receptor. Haga clic en "nuevo" junto al conjunto de datos y agregue un conjunto de datos de CosmosDB que apunte a la base de datos de CosmosDB. Para la colección, lo llamaremos "orders" y no tendrá ningún esquema ni ningún documento, ya que se creará sobre la marcha.

19. En Sink Settings (Configuración del receptor), establezca Clave de partición en ```\SalesOrderID``` y la acción de recopilación en "Volver a crear". Asegúrese de que la pestaña de asignación tiene el siguiente aspecto:

![Captura de pantalla que muestra la pestaña Asignación.](media/data-flow/cosmosb7.png)

20. Haga clic en la vista previa de datos para asegurarse de que ve estas 32 filas establecidas para insertar como nuevos documentos en el nuevo contenedor:

![Captura de pantalla que muestra la pestaña Vista previa de los datos.](media/data-flow/cosmosb8.png)

Si todo parece correcto, ya está listo para crear una nueva canalización, agregar esta actividad de flujo de datos a esa canalización y ejecutarla. Puede ejecutarla desde la depuración o desde una ejecución desencadenada. Después de unos minutos, debería tener un nuevo contenedor desnormalizado de pedidos denominado "orders" en la base de datos de CosmosDB.

## <a name="next-steps"></a>Pasos siguientes

* Compile el resto de la lógica del flujo de datos mediante [transformaciones](concepts-data-flow-overview.md) de flujos de datos de asignación.
* [Descargue la plantilla de canalización completada](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/SQL%20Orders%20to%20CosmosDB.zip) para este tutorial e importe la plantilla en su fábrica.
