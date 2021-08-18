---
title: Reglas de evaluación para la migración de SQL Server a Azure SQL Managed Instance
description: Reglas de evaluación para identificar los problemas con la instancia de origen de SQL Server que deben solucionarse antes de migrar a Azure SQL Managed Instance.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: rajeshsetlem
ms.author: rsetlem
ms.reviewer: mathoma, cawrites
ms.date: 12/15/2020
ms.openlocfilehash: abe88555bc7d545e62faa4c22a5e3f02e5eef630
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743652"
---
# <a name="assessment-rules-for-sql-server-to--azure-sql-managed-instance-migration"></a>Reglas de evaluación para la migración de SQL Server a Azure SQL Managed Instance
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Las herramientas de migración validan la instancia de origen de SQL Server mediante la ejecución de una serie de reglas de evaluación para identificar los problemas que deben solucionarse antes de migrar la base de datos de SQL Server a Azure SQL Managed Instance. 

En este artículo se proporciona una lista de las reglas que se usan para evaluar la viabilidad de la migración de la base de datos de SQL Server a Azure SQL Managed Instance. 

## <a name="analysiscommand-job"></a>Trabajo AnalysisCommand<a id="AnalysisCommandJob"></a>

**Título: No se admite el paso de trabajo AnalysisCommand en Azure SQL Managed Instance.**    
**Categoría**: Advertencia   


**Descripción**   
Se trata de un paso de trabajo que ejecuta un comando de Analysis Services. No se admite el paso de trabajo AnalysisCommand en Azure SQL Managed Instance.

**Recomendación**     
Revise la sección de objetos afectados en Azure Migrate para ver todos los trabajos que usan el paso de trabajo de comando de Analysis Services y evaluar si el paso de trabajo o el objeto afectado se pueden quitar. También puede migrar a SQL Server en la máquina virtual de Azure.

Más información: [Diferencias del Agente SQL Server en Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="analysisquery-job"></a>Trabajo AnalysisQuery<a id="AnalysisQueryJob"></a>

**Título: No se admite el paso de trabajo AnalysisQuery en Azure SQL Managed Instance.**    
**Categoría**: Advertencia   

**Descripción**   
Se trata de un paso de trabajo que ejecuta una consulta de Analysis Services. No se admite el paso de trabajo AnalysisQuery en Azure SQL Managed Instance.


**Recomendación**   
Revise la sección de objetos afectados en Azure Migrate para ver todos los trabajos que usan el paso de trabajo de consulta de Analysis Services y evaluar si el paso de trabajo o el objeto afectado se pueden quitar. También puede migrar a SQL Server en la máquina virtual de Azure.

Más información: [Diferencias del Agente SQL Server en Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="assembly-from-file"></a>Ensamblado del archivo<a id="AssemblyFromFile"></a>

**Título: No se admiten "CREATE ASSEMBLY" y "ALTER ASSEMBLY" con un parámetro de archivo en Azure SQL Managed Instance.**    
**Categoría**: Problema   

**Descripción**   
Azure SQL Managed Instance no admite "CREATE ASSEMBLY" o "ALTER ASSEMBLY" con un parámetro de archivo. Se admite un parámetro binario. Consulte la sección Objetos afectados para el objeto específico donde se usa el parámetro de archivo.

**Recomendación**   
Revise los objetos mediante "CREATE ASSEMBLY" o "ALTER ASSEMBLY" con un parámetro de archivo. Si alguno de estos objetos es necesario, convierta el parámetro de archivo en un parámetro binario. También puede migrar a SQL Server en la máquina virtual de Azure. 

Más información: [Diferencias de CLR en Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#clr)

## <a name="bulk-insert"></a>Inserción masiva<a id="BulkInsert"></a>

**Título: No se admite BULK INSERT con orígenes de datos de blobs que no son de Azure en Azure SQL Managed Instance.**    
**Categoría**: Problema   

**Descripción**   
Azure SQL Managed Instance no puede acceder a recursos compartidos de archivos ni a carpetas de Windows. Vea en la sección "Objetos afectados" los usos específicos de las instrucciones BULK INSERT que no hacen referencia a un BLOB de Azure. Los objetos con "BULK INSERT" en los que el origen no sea Azure Blob Storage no funcionarán después de migrar a Azure SQL Managed Instance.


**Recomendación**   
Necesitará convertir las instrucciones BULK INSERT que usen archivos locales o recursos compartidos de archivo para que usen archivos de Azure Blob Storage al realizar la migración a Azure SQL Managed Instance.

Más información: [Diferencias de inserción masiva y OPENROWSET en Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="clr-security"></a>Seguridad CLR<a id="ClrStrictSecurity"></a>

**Título: Los ensamblados CLR marcados como SAFE o EXTERNAL_ACCESS se consideran UNSAFE**   
**Categoría**: Advertencia   

**Descripción**   
Se aplica el modo de seguridad estricta de CLR en Azure SQL Managed Instance. Este modo está habilitado de manera predeterminada y presenta cambios importantes para las bases de datos que contienen ensamblados CLR definidos por el usuario y marcados como SAFE o EXTERNAL_ACCESS.


**Recomendación**   
CLR usa la seguridad de acceso del código (CAS) de .NET Framework, que ya no se admite como un límite de seguridad. A partir del motor de base de datos de SQL Server 2017 (14.x), se introduce una opción `sp_configure` denominada clr strict security para mejorar la seguridad de los ensamblados CLR. La opción clr strict security está habilitada de manera predeterminada y trata los ensamblados CLR SAFE y EXTERNAL_ACCESS como si estuvieran marcados como UNSAFE. Cuando la opción clr strict security está deshabilitada, un ensamblado CLR creado con PERMISSION_SET = SAFE puede acceder a los recursos externos del sistema, llamar a código no administrado y adquirir privilegios sysadmin. Después de habilitar la seguridad estricta, los ensamblados que no estén firmados no podrán cargarse. Además, si una base de datos tiene ensamblados SAFE o EXTERNAL_ACCESS, se pueden completar las instrucciones RESTORE o ATTACH DATABASE, pero es posible que no se puedan cargar los ensamblados. Para cargarlos, debe modificar o eliminar y volver a crear cada ensamblado para que se firme con un certificado o clave asimétrica que tenga un inicio de sesión correspondiente con el permiso UNSAFE ASSEMBLY en el servidor.

Más información: [CLR strict security](/sql/database-engine/configure-windows/clr-strict-security)

## <a name="compute-clause"></a>Cláusula Compute<a id="ComputeClause"></a>

**Título: Se ha descontinuado y quitado la cláusula COMPUTE.**    
**Categoría**: Advertencia   

**Descripción**   
La cláusula COMPUTE genera totales que aparecen como columnas de resumen adicionales al final del conjunto de resultados. Sin embargo, esta cláusula ya no se admite en Azure SQL Managed Instance.



**Recomendación**   
En su lugar, se debe volver a escribir el módulo T-SQL con el operador ROLLUP. En el código siguiente se muestra cómo se puede reemplazar COMPUTE por ROLLUP: 

```sql
USE AdventureWorks GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) 
BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP;
```

Más información: [Funcionalidad del motor de base de datos descontinuada en SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="cryptographic-provider"></a>Proveedor de servicios criptográficos<a id="CryptographicProvider"></a>

**Título: Se ha encontrado un uso de CREATE CRYPTOGRAPHIC PROVIDER o ALTER CRYPTOGRAPHIC PROVIDER, que no se admite en Azure SQL Managed Instance.**    
**Categoría**: Problema   

**Descripción**   
Azure SQL Managed Instance no es compatible con las instrucciones CRYPTOGRAPHIC PROVIDER porque no puede acceder a los archivos. Consulte en la sección de objetos afectados los usos específicos de las instrucciones CRYPTOGRAPHIC PROVIDER. Los objetos con "CREATE CRYPTOGRAPHIC PROVIDER" o "ALTER CRYPTOGRAPHIC PROVIDER" no funcionarán correctamente después de migrar a Azure SQL Managed Instance.


**Recomendación**   
Revise los objetos con "CREATE CRYPTOGRAPHIC PROVIDER" o "ALTER CRYPTOGRAPHIC PROVIDER". En los objetos necesarios, quite los usos de estas características. También puede migrar a SQL Server en la máquina virtual de Azure. 

Más información: [Diferencias de proveedor de servicios criptográficos en Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md#cryptographic-providers)

## <a name="database-compatibility"></a>Compatibilidad de bases de datos<a id="DbCompatLevelLowerThan100"></a>

**Título: No se admite el nivel de compatibilidad de la base de datos por debajo de 100**   
**Categoría**: Advertencia   

**Descripción**   
El nivel de compatibilidad de la base de datos es una preciada herramienta que sirve para modernizar las bases de datos, ya que permite actualizar el motor de base de datos de SQL Server mientras se conserva el estado funcional de las aplicaciones conectadas porque se mantiene el mismo nivel de compatibilidad de la base de datos previo a la actualización. Azure SQL Managed Instance no admite niveles de compatibilidad por debajo de 100. Cuando la base de datos con un nivel de compatibilidad inferior a 100 se restaura en Azure SQL Managed Instance, el nivel de compatibilidad se actualiza a 100. 


**Recomendación**... Evalúe si la funcionalidad de la aplicación está intacta cuando el nivel de compatibilidad de la base de datos se actualiza a 100 en Azure SQL Managed Instance. También puede migrar a SQL Server en la máquina virtual de Azure.

Más información: [Niveles de compatibilidad admitidos en Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#compatibility-levels)

## <a name="database-principal-alias"></a>Alias de entidad de seguridad de base de datos<a id="DatabasePrincipalAlias"></a>

**Título: SYS.DATABASE_PRINCIPAL_ALIASES se ha descontinuado y quitado.**    
**Categoría**: Problema   

**Descripción**   
SYS.DATABASE_PRINCIPAL_ALIASES se ha descontinuado y quitado de Azure SQL Managed Instance.


**Recomendación**   
Utilice roles en lugar de alias.

Más información: [Funcionalidad del motor de base de datos descontinuada en SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="disable_def_cnst_chk-option"></a>Opción DISABLE_DEF_CNST_CHK<a id="DisableDefCNSTCHK"></a>

**Título: Se descontinúa y quita la opción SET DISABLE_DEF_CNST_CHK.**    
**Categoría**: Problema   

**Descripción**   
Se descontinúa y quita la opción SET DISABLE_DEF_CNST_CHK de Azure SQL Managed Instance.


Más información: [Funcionalidad del motor de base de datos descontinuada en SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>Sugerencia FASTFIRSTROW<a id="FastFirstRowHint"></a>

**Título: La sugerencia de consulta FASTFIRSTROW se ha descontinuado y quitado.**    
**Categoría**: Advertencia   

**Descripción**   
La sugerencia de consulta FASTFIRSTROW se ha descontinuado y quitado de SQL Managed Instance.


**Recomendación**   
En lugar de la sugerencia de consulta FASTFIRSTROW, use OPTION (FAST n).

Más información: [Funcionalidad del motor de base de datos descontinuada en SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>FileStream<a id="FileStream"></a>

**Título: FileStream y Filetable no se admiten en Azure SQL Managed Instance.**    
**Categoría**: Problema   

**Descripción**   
La característica FileStream, que permite almacenar datos no estructurados, como documentos de texto, imágenes y vídeos, en el sistema de archivos NTFS, no se admite en Azure SQL Managed Instance. **Esta base de datos no se puede migrar porque la copia de seguridad que contiene los grupos de archivos FileStream no se puede restaurar en Azure SQL Managed Instance.**


**Recomendación**   
Cargue los archivos no estructurados en Azure Blob Storage y almacene los metadatos relacionados con estos archivos (nombre, tipo, ubicación de dirección URL, clave de almacenamiento, etc.) en Azure SQL Managed Instance. Es posible que tenga que volver a diseñar la aplicación para habilitar la transmisión de blobs desde la instancia de Azure SQL Managed Instance y hacia ella. También puede migrar a SQL Server en la máquina virtual de Azure.

Más información: [Blog de la transmisión de blobs hacia la instancia de Azure SQL y desde ella](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/)

## <a name="heterogeneous-ms-dtc"></a>MS DTC heterogéneo<a id="MIHeterogeneousMSDTCTransactSQL"></a>

**Título: No se admite BEGIN DISTRIBUTED TRANSACTION con un servidor remoto que no sea SQL Server en Azure SQL Managed Instance.**    
**Categoría**: Problema   

**Descripción**   
La transacción distribuida iniciada por Transact SQL BEGIN DISTRIBUTED TRANSACTION y administrada por el Coordinador de transacciones distribuidas de Microsoft (MS DTC) no se admite en Azure SQL Managed Instance. 


**Recomendación**   
Revise la sección de objetos afectados en Azure Migrate para ver todos los objetos que usan BEGIN DISTRUBUTED TRANSACTION. Considere la posibilidad de migrar las bases de datos de participantes a Azure SQL Managed Instance donde se admiten las transacciones distribuidas entre varias instancias (actualmente en versión preliminar). También puede migrar a SQL Server en la máquina virtual de Azure.

Más información: [Transacciones entre varios servidores de Azure SQL Managed Instance ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)

## <a name="homogenous-ms-dtc"></a>MS DTC homogéneo<a id="MIHomogeneousMSDTCTransactSQL"></a>

**Título: Se admite BEGIN DISTRIBUTED TRANSACTION en varios servidores para Azure SQL Managed Instance.**    
**Categoría**: Problema   

**Descripción**   
La transacción distribuida iniciada por Transact SQL BEGIN DISTRIBUTED TRANSACTION y administrada por el Coordinador de transacciones distribuidas de Microsoft (MS DTC) se admite en varios servidores de Azure SQL Managed Instance.


**Recomendación**   
Revise la sección de objetos afectados en Azure Migrate para ver todos los objetos que usan BEGIN DISTRUBUTED TRANSACTION. Considere la posibilidad de migrar las bases de datos de participantes a Azure SQL Managed Instance donde se admiten las transacciones distribuidas entre varias instancias (actualmente en versión preliminar). También puede migrar a SQL Server en la máquina virtual de Azure. 

Más información: [Transacciones entre varios servidores de Azure SQL Managed Instance](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="linked-server-non-sql-provider"></a>Servidor vinculado (proveedor que no es de SQL)<a id="LinkedServerWithNonSQLProvider"></a>

**Título: El servidor vinculado con un proveedor que no es de SQL Server no se admite en Azure SQL Managed Instance.**    
**Categoría**: Problema   

**Descripción**   
Los servidores vinculados permiten al Motor de base de datos de SQL Server ejecutar comandos en orígenes de datos OLE DB fuera de la instancia de SQL Server. El servidor vinculado con un proveedor que no es de SQL Server no se admite en Azure SQL Managed Instance. 


**Recomendación**   
Azure SQL Managed Instance no admite la funcionalidad de servidor vinculado si el proveedor del servidor remoto no es SQL Server, como Oracle, Sybase, etc. 

Se recomiendan las siguientes acciones para eliminar la necesidad de servidores vinculados: 
- Identifique las bases de datos dependientes de instancias remotas que no son SQL Server y considere la posibilidad de moverlas a la base de datos que se está migrando. 
- Migre las bases de datos dependientes a destinos compatibles, como SQL Managed Instance, SQL Database, Azure Synapse SQL e instancias de SQL Server. 
- Considere la posibilidad de crear un servidor vinculado entre Azure SQL Managed Instance y SQL Server en una máquina virtual de Azure (máquina virtual de SQL).  A continuación, desde la máquina virtual de SQL, cree un servidor vinculado a Oracle, Sybase, etc. Este enfoque implica dos saltos, pero se puede usar como solución temporal.  
- También puede migrar a SQL Server en la máquina virtual de Azure.

Más información: [Diferencias de servidor vinculado en Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)

## <a name="merge-job"></a>Trabajo de mezcla<a id="MergeJob"></a>

**Título: No se admite el paso de trabajo de mezcla en Azure SQL Managed Instance.**    
**Categoría**: Advertencia   

**Descripción**   
Se trata de un paso de trabajo que activa el Agente de mezcla de replicación. El Agente de mezcla de replicación es una utilidad ejecutable que aplica la instantánea inicial contenida en las tablas de base de datos a los Suscriptores. También mezcla los cambios incrementales de los datos que tienen lugar en el publicador después de la creación de la instantánea inicial y reconcilia los conflictos según las reglas configuradas por el usuario o mediante un solucionador personalizado creado por el usuario. No se admite el paso de trabajo de mezcla en Azure SQL Managed Instance.


**Recomendación**   
Revise la sección de objetos afectados en Azure Migrate para ver todos los trabajos que usan el paso de trabajo de mezcla y evaluar si el paso de trabajo o el objeto afectado se pueden quitar. También puede migrar a SQL Server en la máquina virtual de Azure.

Más información: [Diferencias del Agente SQL Server en Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="mi-database-size"></a>Tamaño de la base de datos MI<a id="MIDatabaseSize<"></a>

**Título: Azure SQL Managed Instance no admite un tamaño de base de datos superior a 8 TB.**    
**Categoría**: Problema   

**Descripción**   
El tamaño de la base de datos es mayor que el almacenamiento reservado máximo de la instancia. **Esta base de datos no se puede seleccionar para la migración, ya que el tamaño ha superado el límite permitido.**


**Recomendación**   
Evalúe si los datos se pueden archivar comprimidos o particionados en varias bases de datos. También puede migrar a SQL Server en la máquina virtual de Azure.

Más información: [Características de generación de hardware de Azure SQL Managed Instance ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)



## <a name="mi-instance-size"></a>Tamaño de instancia MI<a id="MIInstanceSize<"></a>

**Título: El tamaño máximo de almacenamiento de la instancia en Azure SQL Managed Instance no puede ser superior a 8 TB.**    
**Categoría**: Advertencia   

**Descripción**   
El tamaño de todas las bases de datos es mayor que el almacenamiento reservado máximo de la instancia.  


**Recomendación**   
Considere la posibilidad de migrar las bases de datos a diferentes instancias de Azure SQL Managed Instance o a SQL Server en la máquina virtual de Azure si todas las bases de datos deben existir en la misma instancia. 

Más información: [Características de generación de hardware de Azure SQL Managed Instance ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)


## <a name="multiple-log-files"></a>Varios archivos de registro<a id="MultipleLogFiles<"></a>

**Título: Azure SQL Managed Instance no admite varios archivos de registro.**    
**Categoría**: Problema   

**Descripción**   
SQL Server permite que una base de datos registre en varios archivos. Esta base de datos tiene varios archivos de registro que no se admiten en Azure SQL Managed Instance. **Esta base de datos no se puede migrar porque no se puede restaurar la copia de seguridad en Azure SQL Managed Instance. 
**

**Recomendación**   
Azure SQL Managed Instance solo admite un único registro por base de datos. Debe eliminar todos los archivos de registro excepto uno antes de migrar esta base de datos a Azure: 

```sql
ALTER DATABASE [database_name] REMOVE FILE [log_file_name]
```

Más información: [Opciones de base de datos no admitidas en Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#database-options)



## <a name="next-column"></a>Columna Next<a id="NextColumn"></a>

**Título: Las tablas y columnas denominadas NEXT darán lugar a un error en Azure SQL Managed Instance.**    
**Categoría**: Problema   

**Descripción**   
Se detectaron tablas o columnas denominadas NEXT. Las secuencias, que se introdujeron en Microsoft SQL Server, usan la función NEXT VALUE FOR del estándar ANSI. Si una tabla o columna se denomina NEXT y la columna tiene el alias VALUE, y si se omite la palabra clave AS del estándar ANSI, la instrucción resultante puede producir un error.


**Recomendación**   
Reescriba las instrucciones para incluir la palabra clave AS del estándar ANSI cuando asigne un alias a una tabla o columna. Por ejemplo, cuando un columna se denomina NEXT y tiene el alias VALUE, la consulta SELECT NEXT VALUE FROM TABLE produce un error y debe reescribirse como SELECT NEXT AS VALUE FROM TABLE. Del mismo modo, cuando una tabla se denomina NEXT y esa tabla tiene el alias VALUE, la consulta SELECT Col1 FROM NEXT VALUE produce un error y se debe reescribirse como SELECT Col1 FROM NEXT AS VALUE.



## <a name="non-ansi-style-left-outer-join"></a>Combinación externa izquierda de estilo no ANSI<a id="NonANSILeftOuterJoinSyntax"></a>

**Título: La combinación externa izquierda de estilo no ANSI se ha descontinuado y quitado.**    
**Categoría**: Advertencia   

**Descripción**   
La combinación externa izquierda de estilo no ANSI se ha descontinuado y quitado de Azure SQL Managed Instance. 


**Recomendación**   
Utilice la sintaxis de unión de ANSI.

Más información: [Funcionalidad del motor de base de datos descontinuada en SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="non-ansi-style-right-outer-join"></a>Combinación externa derecha de estilo no ANSI<a id="NonANSIRightOuterJoinSyntax"></a>

**Título: La combinación externa derecha de estilo no ANSI se ha descontinuado y quitado.**    
**Categoría**: Advertencia   

**Descripción**   
La combinación externa derecha de estilo no ANSI se ha descontinuado y quitado de Azure SQL Managed Instance. 



Más información: [Funcionalidad del motor de base de datos descontinuada en SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

**Recomendación**   
Utilice la sintaxis de unión de ANSI.

## <a name="databases-exceed-100"></a>Las bases de datos superan las 100 <a id="NumDbExceeds100"></a>

**Título: Azure SQL Managed Instance admite un máximo de 100 bases de datos por instancia.**    
**Categoría**: Advertencia   

**Descripción**   
El número máximo de bases de datos admitidas en Azure SQL Managed Instance es 100, a menos que se alcance el límite de tamaño de almacenamiento de la instancia.



**Recomendación**   
Considere la posibilidad de migrar las bases de datos a diferentes instancias de Azure SQL Managed Instance o a SQL Server en la máquina virtual de Azure si todas las bases de datos deben existir en la misma instancia. 

Más información: [Límites de recursos de Azure SQL Managed Instance ](../../managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="openrowset-non-blob-data-source"></a>OPENROWSET (origen de datos que no son blobs)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Título: OpenRowSet no se admite en operaciones masivas con orígenes de datos que no son Azure Blob Storage en Azure SQL Managed Instance.**    
**Categoría**: Problema   

**Descripción**   
OPENROWSET admite operaciones masivas a través de un proveedor integrado BULK que permite que los datos se lean y se devuelvan en forma de conjunto de filas. No se admite OPENROWSET con orígenes de datos que no son Azure Blob Storage en Azure SQL Managed Instance. 

**Recomendación**   
Azure SQL Managed Instance no puede acceder a los recursos compartidos de archivos ni carpetas de Windows, por lo que los archivos se deben importar desde Azure Blob Storage. Por lo tanto, solo se admite el tipo de blob DATASOURCE en la función OPENROWSET. También puede migrar a SQL Server en la máquina virtual de Azure.

Más información: [Diferencias de inserción masiva y OPENROWSET en Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)

## <a name="openrowset-non-sql-provider"></a>OPENROWSET (proveedor que no es de SQL)<a id="OpenRowsetWithNonSQLProvider"></a>

**Título: No se admite OpenRowSet con proveedor que no es de SQL en Azure SQL Managed Instance.**    
**Categoría**: Problema   

**Descripción**   
Es un método alternativo para tener acceso a las tablas de un servidor vinculado y, al mismo tiempo, es un método ad hoc para conectarse y tener acceso a datos remotos utilizando OLE DB. No se admite OpenRowSet con proveedor que no es de SQL en Azure SQL Managed Instance. 



**Recomendación**   
La función OPENROWSET puede utilizarse para ejecutar consultas solo en instancias de SQL Server (ya sean administradas, locales o en máquinas virtuales). Solo se admiten los valores SQLNCLI, SQLNCLI11 y SQLOLEDB como proveedor. Por lo tanto, la acción recomendada es identificar las bases de datos dependientes de instancias remotas que no son SQL Server y considere la posibilidad de moverlas a la base de datos que se está migrando.

Más información: [Diferencias de inserción masiva y OPENROWSET en Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="powershell-job"></a>Trabajo de PowerShell<a id="PowerShellJob"></a>

**Título: No se admite el paso de trabajo de PowerShell en Azure SQL Managed Instance.**    
**Categoría**: Advertencia   

**Descripción**   
Se trata de un paso de trabajo que ejecuta un script de PowerShell. No se admite el paso de trabajo de PowerShell en Azure SQL Managed Instance. 



**Recomendación**   
Revise la sección de objetos afectados en Azure Migrate para ver todos los trabajos que usan el paso de trabajo de PowerShell y evaluar si el paso de trabajo o el objeto afectado se pueden quitar.  Evalúe si se puede usar Azure Automation. También puede migrar a SQL Server en la máquina virtual de Azure.

Más información: [Diferencias del Agente SQL Server en Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="queue-reader-job"></a>Trabajo de lector de cola<a id="QueueReaderJob"></a>

**Título: No se admite el paso de trabajo de lector de cola en Azure SQL Managed Instance.**    
**Categoría**: Advertencia   

**Descripción**   
Se trata de un paso de trabajo que activa el Agente de lectura de cola de replicación. El Agente de lectura de cola de replicación es un ejecutable que lee los mensajes almacenados en una cola de Microsoft SQL Server o una cola de mensajes de Microsoft y luego aplica esos mensajes al publicador. El Agente de lectura de cola se utiliza con la instantánea y las publicaciones transaccionales que permiten la actualización en cola. No se admite el paso de trabajo de lector de cola en Azure SQL Managed Instance.


**Recomendación**   
Revise la sección de objetos afectados en Azure Migrate para ver todos los trabajos que usan el paso de trabajo de lector de cola y evaluar si el paso de trabajo o el objeto afectado se pueden quitar. También puede migrar a SQL Server en la máquina virtual de Azure.

Más información: [Diferencias del Agente SQL Server en Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Título: Las llamadas a RAISERROR de estilo heredado se deben reemplazar por equivalentes modernos.**    
**Categoría**: Advertencia   

**Descripción**   
Las llamadas a RAISERROR, como el ejemplo siguiente, se denominan de estilo heredado porque no incluyen las comas y los paréntesis. RAISERROR 50001 "Esto es una prueba". Este método de llamada a RAISERROR se ha descontinuado y quitado de Azure SQL Managed Instance.



**Recomendación**   
Vuelva a escribir la instrucción con la sintaxis de RAISERROR actual o evalúe si es viable el enfoque moderno de `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH`.

Más información: [Funcionalidad del motor de base de datos descontinuada en SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="sql-mail"></a>SQL Mail<a id="SqlMail"></a>

**Título: SQL Mail se ha descontinuado.**    
**Categoría**: Advertencia   


**Descripción**   
SQL Mail se ha descontinuado y quitado de Azure SQL Managed Instance.



**Recomendación**   
Usar Database Mail.

Más información: [Funcionalidad del motor de base de datos descontinuada en SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>


**Título: Se han detectado instrucciones que hacen referencia a procedimientos almacenados del sistema que se han quitado y no están disponibles en Azure SQL Managed Instance.**    
**Categoría**: Advertencia   

**Descripción**   
No se pueden usar en Azure SQL Managed Instance los siguientes procedimientos almacenados extendidos y del sistema no admitidos: `sp_dboption`, `sp_addserver`, `sp_dropalias`, `sp_activedirectory_obj`, `sp_activedirectory_scp` y `sp_activedirectory_start`. 




**Recomendación**   
Quite las referencias a los procedimientos del sistema no admitidos que se han quitado de Azure SQL Managed Instance.

Más información: [Funcionalidad del motor de base de datos descontinuada en SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="transact-sql-job"></a>Trabajo Transact-SQL<a id="TransactSqlJob"></a>

**Título: El paso de trabajo TSQL incluye comandos no admitidos en Azure SQL Managed Instance**   
**Categoría**: Advertencia   


**Descripción**   
Se trata de un paso de trabajo que ejecuta scripts de TSQL en el momento programado. El paso de trabajo TSQL incluye comandos no admitidos que no se admiten en Azure SQL Managed Instance.



**Recomendación**   
Revise la sección de objetos afectados en Azure Migrate para ver todos los trabajos que incluyen comandos no admitidos en Azure SQL Managed Instance y evaluar si el paso de trabajo o el objeto afectado se pueden quitar. También puede migrar a SQL Server en la máquina virtual de Azure.

Más información: [Diferencias del Agente SQL Server en Azure SQL Managed Instance ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="trace-flags"></a>Marcas de seguimiento<a id="TraceFlags"></a>

**Título: Se encontraron marcas de seguimiento no admitidas en Azure SQL Managed Instance**   
**Categoría**: Advertencia   


**Descripción**   
Azure SQL Managed Instance solo admite un número limitado de marcas de seguimiento globales. No se admiten marcas de seguimiento de sesión.



**Recomendación**   
Revise la sección de objetos afectados en Azure Migrate para ver todas las marcas de seguimiento que no se admiten en Azure SQL Managed Instance y evaluar si se pueden quitar. También puede migrar a SQL Server en la máquina virtual de Azure.

Más información: [Marcas de seguimiento](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql#trace-flags)


## <a name="windows-authentication"></a>Autenticación de Windows<a id="WindowsAuthentication"></a>

**Título: Los usuarios de base de datos asignados con la autenticación de Windows (seguridad integrada) no se admiten en Azure SQL Managed Instance**   
**Categoría**: Advertencia   


**Descripción**   
Azure SQL Managed Instance admite dos tipos de autenticación: 
- Autenticación de SQL, que usa un nombre de usuario y una contraseña.
- Autenticación de Azure Active Directory, la cual usa las identidades administradas por Azure Active Directory y es compatible con los dominios administrados e integrados. 

Los usuarios de base de datos asignados con la autenticación de Windows (seguridad integrada) no se admiten en Azure SQL Managed Instance. 


**Recomendación**   
Federe la instancia local de Active Directory con Azure Active Directory. La identidad de Windows se puede reemplazar por las identidades de Azure Active Directory equivalentes. También puede migrar a SQL Server en la máquina virtual de Azure.

Más información: [Capacidades de seguridad de SQL Managed Instance](../../database/security-overview.md#authentication)


## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Título: no se admite xp_cmdshell en Azure SQL Managed Instance.**    
**Categoría**: Problema   


**Descripción**   
Xp_cmdshell, que genera un shell de comandos de Windows y pasa una cadena para su ejecución, no se admite en Azure SQL Managed Instance. 



**Recomendación**   
Revise la sección de objetos afectados en Azure Migrate para ver todos los objetos que usan xp_cmdshell y evaluar si se puede quitar la referencia a xp_cmdshell o al objeto afectado. Considere la posibilidad de explorar Azure Automation que ofrece un servicio de automatización y configuración basado en la nube. También puede migrar a SQL Server en la máquina virtual de Azure.

Más información: [Diferencias de procedimiento almacenado en Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)

## <a name="next-steps"></a>Pasos siguientes

Para empezar a migrar SQL Server a Azure SQL Managed Instance, consulte la [guía de migración de SQL Server a SQL Managed Instance](sql-server-to-managed-instance-guide.md).

- Para obtener una matriz de los servicios y las herramientas de Microsoft y de otros fabricantes que están disponibles para ayudarlo en diversos escenarios de migración de datos y bases de datos, además de las tareas especializadas, consulte [Servicios y herramientas de migración de datos](../../../dms/dms-tools-matrix.md).

- Para obtener más información acerca Azure SQL Managed Instance, consulte:
   - [Niveles de servicio en Azure SQL Managed Instance](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Diferencias entre SQL Server y Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Calculadora del costo total de propiedad de Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para más información sobre el marco y el ciclo de adopción de las migraciones en la nube, consulte:
   -  [Cloud Adoption Framework para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedimientos recomendados para la gestión de los costos y los ajustes de tamaño de las cargas de trabajo migradas a Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para evaluar el nivel de acceso de la aplicación, consulte [Data Access Migration Toolkit (versión preliminar)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para más información sobre cómo realizar pruebas A/B en la capa de acceso a datos, consulte [Información general del Asistente para experimentación con bases de datos](/sql/dea/database-experimentation-assistant-overview).
