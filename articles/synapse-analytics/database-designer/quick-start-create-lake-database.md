---
title: Inicio rápido sobre las plantillas de base de datos y de base de datos de lago en Azure Synapse
description: Inicio rápido sobre cómo usar las plantillas de base de datos y de base de datos de lago
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 615d31355c56d3a6616aac18d92cba27e4648057
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093444"
---
# <a name="quickstart-create-a-new-lake-database-leveraging-database-templates"></a>Inicio rápido: creación de una nueva base de datos de lago mediante plantillas de base de datos

Esta guía rápida proporciona un repaso de un escenario de un extremo a otro sobre cómo aplicar las plantillas de base de datos para crear una base de datos de lago, alinear los datos con el nuevo modelo y usar la experiencia integrada para analizar los datos. 

## <a name="prerequisites"></a>Prerrequisitos
- Se requieren al menos permisos de rol de usuario de Synapse para explorar una plantilla de base de datos de lago desde la Galería.
- Se requieren permisos de administrador o colaborador de Synapse en el área de trabajo de Synapse para crear una base de datos de lago.
- Los permisos de colaborador de datos de blobs de almacenamiento son necesarios en el lago de datos.

## <a name="create-a-lake-database-from-database-templates"></a>Creación de una base de datos de lago a partir de plantillas de base de datos de lago

Use la nueva funcionalidad de plantillas de base de datos (versión preliminar) para crear una base de datos de lago que podrá usar para configurar su modelo de datos para la base de datos en cuestión. 

En nuestro escenario, usaremos la plantilla de base de datos de venta al por menor y seleccionaremos las entidades siguientes: 
 - **RetailProduct**: un producto es cualquier artículo que se puede ofrecer a un mercado que pueda satisfacer una necesidad concreta por parte de clientes potenciales. Ese producto es la suma de todos los atributos físicos, psicológicos, simbólicos y de servicio asociados a él.
 - **Transacción**: el nivel más bajo de trabajo ejecutable o actividad de cliente.
Una transacción consta de uno o varios eventos discretos.
 - **TransactionLineItem**: componentes de una transacción desglosados por producto y cantidad, uno por cada elemento de línea.
 - **Entidad**: es una persona física, una organización, una entidad legal, una organización social o una unidad empresarial que resultan de interés para el negocio.
 - **Cliente**: es una persona física o una entidad legal que tiene o ha comprado un producto o servicio.
 - **Canal**: es una forma de vender y distribuir un producto o servicio.
 - 
La manera más fácil de encontrarlos es usando el cuadro de búsqueda situado encima de los distintos ámbitos de negocio que contienen las tablas. 
 
![Ejemplo de plantilla de base de datos](./media/quick-start-create-lake-database/model-example.png)

 
## <a name="configure-lake-database"></a>Configuración de una base de datos de lago
 
Una vez que haya creado la base de datos, asegúrese de que la cuenta de almacenamiento y la ruta de acceso al archivo estén establecidas en una ubicación en la que quiera almacenar los datos. De forma predeterminada, la ruta de acceso estará establecida en la cuenta de almacenamiento principal en Synapse Analytics, pero se puede cambiar según sus necesidades. 
  
 ![Ejemplo de base de datos de lago](./media/quick-start-create-lake-database/lake-database-example.png)
 
Para guardar su diseño y hacer que esté disponible en Synapse, publique todos los cambios. Con este paso se completa la configuración de la base de datos de lago y se habilita para que esté disponible en todos los componentes dentro de Synapse Analytics y fuera. 

## <a name="ingest-data-to-lake-database"></a>Ingestión de datos en una base de datos de lago

Para ingerir datos en la base de datos de lago, puede ejecutar [canalizaciones](../data-integration/data-integration-data-lake.md) con asignaciones de flujos de datos sin código, que tienen un conector de **Base de datos del área de trabajo** para cargar los datos directamente en la tabla de la base de datos. También puede usar los cuadernos interactivos de Spark para ingerir datos en las tablas de la base de datos de lago:

```Spark
%%sql
INSERT INTO `retail_mil`.`customer` VALUES (1,'2021-02-18',1022,557,101,'Tailspin Toys (Head Office)','Waldemar Fisar',90410,466);
```

## <a name="query-the-data"></a>Consultar los datos

Una vez creada la base de datos de lago, hay distintas formas de consultar los datos. En estos momentos, admitimos consultas de SQL OnDemand en Synapse, que entiende automáticamente el formato de la base de datos de lago recién creada y expone los datos mediante este. 

```sql
SELECT TOP (100) [ProductId]
,[ProductName]
,[ProductDescription]
,[ProductInternalName]
,[ItemSku]
,[PrimaryBrandId]
FROM [Retail_mil].[dbo].[RetailProduct]
```

La otra forma de acceder a los datos de Synapse es abrir un nuevo cuaderno de Spark y usar la experiencia integrada ahí:

```spark
df = spark.sql("SELECT * FROM `Retail_mil`.`RetailProduct`")
df.show(10)
```

## <a name="train-machine-learning-models"></a>Entrenamiento de modelos de Machine Learning

Puede usar la base de datos de lago para entrenar sus modelos de Machine Learning y puntuar los datos. Para obtener más información, consulte [Entrenamiento de modelos de Machine Learning](../machine-learning/tutorial-automl.md). 

## <a name="next-steps"></a>Pasos siguientes

Siga explorando las funcionalidades del diseñador de bases de datos mediante los vínculos siguientes.
 - [Concepto de plantillas de base de datos](concepts-database-templates.md)
 - [Concepto de base de datos de lago](concepts-lake-database.md)
 - [Cómo crear una base de datos de lago](create-empty-lake-database.md)
