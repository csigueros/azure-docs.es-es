---
title: Introducción al libro de contabilidad de Azure SQL Database
description: Conozca los conceptos básicos sobre la característica libro de contabilidad de Azure SQL Database.
ms.custom: references_regions
ms.date: 07/23/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: 6b8ca057d896cc7fa353e8b09b1d1667b87cc273
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727057"
---
# <a name="azure-sql-database-ledger"></a>Libro de contabilidad de Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Actualmente, el libro de contabilidad de Azure SQL Database se encuentra en versión preliminar pública y está disponible en el Oeste de Europa, Sur de Brasil y Centro-oeste de EE. UU.

Establecer la confianza en torno a la integridad de los datos almacenados en los sistemas de base de datos ha sido un problema persistente para todas las organizaciones que administran datos financieros, médicos u otros datos confidenciales. La característica de libro de contabilidad de [Azure SQL Database](sql-database-paas-overview.md) proporciona funcionalidades de evidencia de alteración en la base de datos. Puede atestiguar criptográficamente a terceros, como auditores u otras partes interesadas de la empresa, que los documentos no se han modificado.

El libro de contabilidad ayuda a proteger los datos frente a cualquier atacante o usuario con privilegios elevados, incluidos los administradores de bases de datos, los administradores del sistema y los de la nube. Al igual que con un libro de contabilidad tradicional, la característica permite conservar los datos históricos. Si se actualiza una fila en la base de datos, su valor anterior se conserva y protege en una tabla de historial. El libro de contabilidad proporciona una crónica de todos los cambios realizados en la base de datos a lo largo del tiempo. 

El libro de contabilidad y los datos históricos se administran de forma transparente, lo que ofrece protección sin cambios en la aplicación. La característica conserva los datos históricos en un formato relacional para admitir consultas SQL con fines de auditoría, análisis forense y otros fines. Proporciona garantías de integridad de los datos criptográficos al tiempo que mantiene la eficacia, flexibilidad y rendimiento de Azure SQL Database.

:::image type="content" source="media/ledger/ledger-table-architecture.png" alt-text="Diagrama de la arquitectura de la tabla del libro de contabilidad.":::

## <a name="use-cases-for-azure-sql-database-ledger"></a>Casos de uso del libro de contabilidad de Azure SQL Database 

### <a name="streamlining-audits"></a>Simplificación de auditorías

El valor de cualquier sistema de producción se basa en la capacidad de confiar en los datos que el sistema consume y produce. Si un usuario malintencionado ha alterado los datos de la base de datos, esto puede tener resultados desastrosos en los procesos empresariales que dependen de esos datos. 

Mantener la confianza en los datos requiere una combinación de habilitar los controles de seguridad adecuados para reducir los posibles ataques, las prácticas de copia de seguridad y restauración y los procedimientos exhaustivos de recuperación ante desastres. Las auditorías realizadas por terceros garantizan la aplicación de estas prácticas. 

Los procesos de auditoría son actividades que consumen mucho tiempo. La auditoría requiere la inspección in situ de los procedimientos implementados, como revisar los registros de auditoría e inspeccionar los controles de autenticación y acceso. Aunque estos procesos manuales pueden sacar a la luz posibles brechas en la seguridad, lo que no pueden proporcionar es una prueba verificable de que los datos no se han modificado de forma malintencionada. 

El libro de contabilidad proporciona a los auditores la prueba criptográfica de la integridad de los datos. Esta prueba puede ayudar a simplificar el proceso de auditoría. También proporciona una prueba de no rechazo con respecto a la integridad de los datos del sistema.

### <a name="multiple-party-business-processes"></a>Procesos empresariales de varias partes

En algunos sistemas, como los sistemas de administración de una cadena de suministro, varias organizaciones deben compartir el estado de un proceso empresarial entre sí. Estos sistemas tienen dificultades con el desafío que supone compartir los datos y confiar en ellos. Muchas organizaciones están recurriendo a cadenas de bloques tradicionales, como Ethereum o Hyperledger Fabric, para transformar digitalmente sus procesos empresariales de varias partes. 

La cadena de bloques es una excelente solución para redes de varias partes en las que la confianza es baja entre las partes que participan en la red. Muchas de estas redes son soluciones fundamentalmente centralizadas donde la confianza es importante, pero una infraestructura totalmente descentralizada es una solución de gran peso. 

El libro de contabilidad proporciona una solución para estas redes. Los participantes pueden comprobar la integridad de los datos de forma centralizada, sin la complejidad y las implicaciones en materia de rendimiento que el consenso de la red introduce en una red de cadena de bloques.

### <a name="trusted-off-chain-storage-for-blockchain"></a>Almacenamiento fuera de la cadena de bloques de confianza

Cuando una red de cadena de bloques es necesaria para un proceso empresarial en el que participan varias partes, tener la capacidad de consultar los datos en la cadena de bloques sin sacrificar el rendimiento es un desafío. 

Los patrones típicos para resolver este problema implican la replicación de datos de la cadena de bloques a un almacén fuera de la cadena, como una base de datos. Sin embargo, una vez que los datos se replican en la base de datos desde la cadena de bloques, la integridad de los datos garantiza la pérdida de una oferta de cadena de bloques. El libro de contabilidad proporciona la integridad de los datos para el almacenamiento fuera de la cadena de las redes de cadena de bloques, lo que garantiza la plena confianza en los datos en todo el sistema.

## <a name="how-it-works"></a>Cómo funciona

Cada transacción que recibe la base de datos está codificada con hash criptográfico (SHA-256). La función hash usa el valor de la transacción, junto con el hash de la transacción anterior como entrada para la función hash. (El valor incluye los hashes de las filas contenidas en la transacción). La función vincula criptográficamente todas las transacciones como una cadena de bloques. 

Codificado con hash criptográfico([resúmenes de la base de datos](#database-digests)) representa el estado de la base de datos. Se generan periódicamente y se almacenan fuera de Azure SQL Database en una ubicación de almacenamiento a prueba de alteraciones. Un ejemplo de una ubicación de almacenamiento es la [característica de almacenamiento inmutable de Azure Blob Storage](../../storage/blobs/immutable-storage-overview.md) o [Azure Confidential Ledger](../../confidential-ledger/index.yml). Los resúmenes de la base de datos se usan posteriormente para comprobar la integridad de la base de datos comparando el valor del hash del código con los hash calculados de la base de datos. 

La funcionalidad del libro de contabilidad se introduce en las tablas en Azure SQL Database de dos formas:

- [Tablas de libro de contabilidad actualizables](#updatable-ledger-tables), que permiten actualizar y eliminar filas de las tablas.
- [Tablas de libro de contabilidad de solo anexión](#append-only-ledger-tables), que solo permiten inserciones en las tablas.

Tanto las tablas de libro de contabilidad actualizables como las tablas de libro de contabilidad de solo anexión proporcionan funcionalidades de evidencia de alteración y análisis forense digital. Comprender qué transacciones enviadas por los usuarios provocaron cambios en la base de datos es importante si quiere corregir posibles eventos de alteración o demostrar a terceros que las transacciones enviadas al sistema proceden de usuarios autorizados. 

La característica de libro de contabilidad permite a los usuarios, sus asociados o auditores analizar todas las operaciones históricas y detectar posibles alteraciones. Cada operación de fila va acompañada del identificador de la transacción que la realizó. El identificador permite a los usuarios obtener más información sobre el momento en que se produjo la transacción y la identidad del usuario que la ejecutó. A continuación, los usuarios pueden poner en correlación el identificador con otras operaciones que ha realizado la transacción.

Para más información sobre las limitaciones de las tablas del libro de contabilidad, consulte [Limitaciones del libro de contabilidad de Azure SQL Database](ledger-limits.md).

### <a name="ledger-database"></a>Base de datos de libro de contabilidad

En una base de datos de libro de contabilidad es evidente la alteración de los datos del usuario y dónde se almacenan en tablas de libro de contabilidad. Una base de datos de libro de contabilidad solo puede contener tablas de libro de contabilidad. Cada tabla se crea, de manera predeterminada, como una tabla actualizable de libro de contabilidad. Las bases de datos de libro de contabilidad proporcionan una solución fácil de usar para las aplicaciones que requieren que se proteja la integridad de todos los datos. 

### <a name="updatable-ledger-tables"></a>Tablas de libro de contabilidad actualizables

Las [tablas de libro de contabilidad actualizables](ledger-updatable-ledger-tables.md) son idóneas para los patrones de aplicación que esperan emitir actualizaciones y eliminaciones en las tablas de la base de datos, como las aplicaciones del sistema de registro (SOR). Los patrones de datos existentes de la aplicación no necesitan cambiar para habilitar la funcionalidad de libro de contabilidad.  

Las tablas de libro de contabilidad actualizables realizan un seguimiento del historial de cambios de todas las filas de la base de datos cuando se producen transacciones que realizan actualizaciones o eliminaciones. Una tabla de libro de contabilidad actualizable es una tabla con versiones del sistema que contiene una referencia a otra tabla con un esquema reflejado. 

La otra tabla se denomina *tabla de historial*. El sistema usa esta tabla para almacenar automáticamente la versión anterior de una fila de la tabla de libro de contabilidad cada vez que esta fila se actualiza o elimina. La tabla de historial se genera automáticamente cuando se crea una tabla de libro de contabilidad actualizable. 

Los valores de la tabla de libro de contabilidad actualizable y su tabla de historial correspondiente proporcionan una crónica de los valores de la base de datos a lo largo del tiempo. Para consultar fácilmente esta crónica de la base de datos, se crea una vista de libro de contabilidad generada por el sistema, que une la tabla de libro de contabilidad actualizable y la tabla de historial.

Para más información sobre las tablas de libro de contabilidad actualizables, consulte [Creación y uso de tablas actualizables del libro de contabilidad](ledger-how-to-updatable-ledger-tables.md).

### <a name="append-only-ledger-tables"></a>Tablas de libro de contabilidad de solo anexión

Las [tablas de libro de contabilidad de solo anexión](ledger-append-only-ledger-tables.md) son idóneas para los patrones de aplicación que son de solo inserción, como las aplicaciones de administración de eventos e información de seguridad (SIEM). Las tablas de libro de contabilidad de solo anexión bloquean las actualizaciones y eliminaciones en el nivel de API. Este bloqueo proporciona más protección contra alteraciones de usuarios con privilegios, como administradores del sistema y de la base de datos. 

Puesto que solo se permiten inserciones en el sistema, las tablas de libro de contabilidad de solo anexión no tienen una tabla de historial correspondiente, ya que no hay ningún historial que capturar. Al igual que con las tablas de libro de contabilidad actualizables, una vista de libro de contabilidad proporciona información sobre la transacción que insertó filas en la tabla de solo anexión y el usuario que realizó la inserción.

Para más información sobre las tablas de libro de contabilidad de solo anexión, consulte [Creación y uso de tablas de libro de contabilidad de solo anexión](ledger-how-to-append-only-ledger-tables.md).

### <a name="database-ledger"></a>Libro de contabilidad de base de datos

El [libro de contabilidad de base de datos](ledger-database-ledger.md) consta de tablas del sistema que almacenan los hashes criptográficos de las transacciones procesadas en el sistema. Puesto que las transacciones son la unidad de [atomicidad](/windows/win32/cossdk/acid-properties) del motor de base de datos, esta es la unidad de trabajo que se captura en el libro de contabilidad de base de datos. 

En concreto, cuando se confirma una transacción, el hash SHA-256 de las filas modificadas por la transacción en la tabla del libro de contabilidad se anexa como una *entrada de transacción* en el libro de contabilidad de la base de datos. La entrada de transacción también incluye algunos metadatos para la transacción, como la identidad del usuario que la ejecutó y su marca de tiempo de confirmación. 

Cada 30 segundos, se aplica un algoritmo hash SHA-256 a las transacciones procesadas por la base de datos mediante una estructura de datos en árbol de Merkle. El resultado es un hash raíz que forma un bloque. A continuación, se aplica un hash SHA-256 mediante el hash raíz del bloque junto con el hash raíz del bloque anterior como entrada a la función hash. Eso forma una cadena de bloques.

### <a name="database-digests"></a>Resúmenes de base de datos

El hash del bloque más reciente del libro de contabilidad de base de datos se denomina [resumen de la base de datos](ledger-digest-management-and-database-verification.md). Representa el estado de todas las tablas del libro de contabilidad de la base de datos en el momento en que se generó el bloque. 

Cuando se forma un bloque, su resumen de base de datos asociado se publica y almacena fuera de Azure SQL Database en un almacenamiento a prueba de alteraciones. Puesto que los resúmenes de base de datos representan el estado de la base de datos en el momento en que se generaron, es fundamental proteger los resúmenes de la alteración. Un atacante que tenga acceso para modificar los resúmenes podría:

1. Alterar los datos de la base de datos.
2. Generar los hashes que representan la base de datos con esos cambios.
3. Modifique los resúmenes para representar el hash actualizado de las transacciones del bloque . 

El libro de contabilidad proporciona la capacidad de generar y almacenar automáticamente los resúmenes de base de datos en un [almacenamiento inmutable](../../storage/blobs/immutable-storage-overview.md) o en [Azure Confidential Ledger](../../confidential-ledger/index.yml) para evitar la alteración. Como alternativa, los usuarios pueden generar manualmente resúmenes de base de datos y almacenarlos en la ubicación que prefieran. Los resúmenes de base de datos se usan para comprobar posteriormente que los datos almacenados en tablas de libro de contabilidad no se han alterado.

### <a name="ledger-verification"></a>Verificación del libro de contabilidad

La característica de libro de contabilidad no permite a los usuarios modificar su contenido. Sin embargo, un atacante o administrador del sistema que tenga el control de la máquina puede omitir todas las comprobaciones del sistema y alterar directamente los datos. Por ejemplo, un atacante o administrador del sistema puede editar los archivos de base de datos en el almacenamiento. Ledger no puede evitar estos ataques, pero garantiza que se detectará cualquier alteración cuando se comprueban los datos del libro de contabilidad. 

El proceso de [comprobación del libro de contabilidad](ledger-digest-management-and-database-verification.md) toma como entrada uno o varios resúmenes de base de datos generados previamente y vuelve a compilar los hash almacenados en el libro de contabilidad de base de datos, en función del estado actual de las tablas del libro de contabilidad. Si los hash calculados no coinciden con los resúmenes de entrada, se produce un error en la comprobación, lo que indica que los datos se han alterado. A continuación, el libro de contabilidad notifica todas las incoherencias que ha detectado.

Puesto que la comprobación del libro de contabilidad vuelve a calcular todos los hashes de las transacciones de la base de datos, puede ser un proceso que consume muchos recursos para las bases de datos con gran cantidad de datos. Los usuarios solo deben ejecutar la comprobación del libro de contabilidad cuando necesiten comprobar la integridad de su base de datos, en lugar de ejecutarla continuamente. 

Lo ideal es que los usuarios ejecuten la comprobación del libro de contabilidad solo cuando la organización que hospeda los datos pase por una auditoría y necesite proporcionar evidencia criptográfica con respecto a la integridad de sus datos a un tercero. Para reducir el costo de la verificación, esta característica expone las opciones para verificar las tablas del libro de contabilidad individuales o solo un subconjunto de las tablas del libro de contabilidad.

## <a name="next-steps"></a>Pasos siguientes
 
- [Inicio rápido: Creación de una instancia de SQL Database con el libro de contabilidad habilitado](ledger-create-a-single-database-with-ledger-enabled.md)
- [Acceso a los resúmenes almacenados en Azure Confidential Ledger](ledger-how-to-access-acl-digest.md)
- [Comprobación de una tabla de libro de contabilidad para detectar alteraciones](ledger-verify-database.md)