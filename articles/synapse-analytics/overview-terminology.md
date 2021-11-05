---
title: 'Terminología: Azure Synapse Analytics'
description: Guía de referencia para dirigir a los usuarios por Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/02/2021
ms.author: saveenr
ms.reviewer: jrasnick
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5d525d3657a48e01240721e4c868fa481db58dc0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074712"
---
# <a name="azure-synapse-analytics-terminology"></a>Terminología de Azure Synapse Analytics

Este documento es una guía de los conceptos básicos de Azure Synapse Analytics.

## <a name="basics"></a>Aspectos básicos

Un **área de trabajo de Synapse** es un límite de colaboración protegible para realizar análisis empresariales basados en la nube en Azure. El área de trabajo se implementa en una región específica y tiene una cuenta de Azure Data Lake Storage Gen2 y un sistema de archivos asociados (para almacenar los datos temporales). Un área de trabajo está en un grupo de recursos.

Un área de trabajo le permite realizar análisis con SQL y Apache Spark. Los recursos disponibles para los análisis de SQL y Spark se organizan en **grupos** de SQL y Spark.

## <a name="linked-services"></a>Servicios vinculados

Las áreas de trabajo pueden contener cualquier número de cadenas de conexión esenciales de un **servicio vinculado** que definen la información de conexión necesaria para que el área de trabajo se conecte a recursos externos.

## <a name="synapse-sql"></a>SQL de Synapse

**Synapse SQL** es la capacidad de realizar análisis basados en T-SQL en el área de trabajo de Synapse. Synapse SQL tiene dos modelos de consumo: dedicado y sin servidor.  Para el modelo dedicado, use **grupos de SQL dedicados**. Un área de trabajo puede tener un número ilimitado de estos grupos. Para usar el modelo sin servidor, use los **grupos de SQL sin servidor**. Cada área de trabajo tiene uno de estos grupos.

Puede trabajar con grupos de SQL en Synapse Studio mediante la ejecución de **scripts SQL**.

## <a name="apache-spark-for-synapse"></a>Apache Spark para Synapse

Para usar el análisis de Spark, cree y use **grupos de Apache Spark sin servidor** en el área de trabajo de Synapse. Al empezar a usar un grupo de Spark, las áreas de trabajo crean una **sesión de Spark** para administrar los recursos asociados con esa sesión.

En Synapse hay dos maneras de usar Spark:

* **Cuadernos de Spark** para realizar ciencia e ingeniería de datos con Scala, PySpark, C# y SparkSQL.
* **Definiciones de trabajo de Spark** para ejecutar trabajos de Spark por lotes con archivos jar.

## <a name="pipelines"></a>Canalizaciones

Las canalizaciones son la forma en que Azure Synapse proporciona integración de datos (permiten mover datos entre servicios y organizar las actividades).

* Una **canalización** es una agrupación lógica de actividades que realizan una tarea de manera conjunta.
* Las **actividades** definen las acciones de una canalización que se deben realizar en los datos, como la copia de datos, la ejecución de un cuaderno o un script de SQL.
* Los **flujos de datos** son un tipo específico de actividad que proporciona una experiencia sin código para realizar una transformación de datos que usa Synapse Spark en segundo plano.
* **Desencadenador**: ejecuta una canalización. Se puede ejecutar de forma manual o automática (programación, ventana de saltos de tamaño constante o basada en eventos).
* **Conjunto de datos de integración**: vista con nombre de los datos que simplemente apunta o hace referencia a los datos que se van a usar en una actividad como entrada y salida. Pertenece a un servicio vinculado.

## <a name="data-explorer-preview"></a>Data Explorer (versión preliminar)

Azure Synapse Data Explorer proporciona a los clientes una experiencia de consulta interactiva para desbloquear información de datos de registro y telemetría.

* Los **grupos de Data Explorer** son clústeres dedicados que incluyen dos o más nodos de proceso con almacenamiento SSD local (caché en caliente) para optimizar el rendimiento de las consultas y almacenamiento de blobs múltiple (caché en frío) para la persistencia.
* Las **bases de datos de Data Explorer** se hospedan en grupos de Data Explorer y son entidades lógicas que se realizan en colecciones de tablas y otros objetos de base de datos. Puede tener más de una base de datos por grupo.
* Las **tablas** son objetos de base de datos que contienen datos organizados mediante un modelo de datos relacional tradicional. Los datos se almacenan en registros que se adhieren al esquema de tabla bien definido de Data Explorer que define una lista ordenada de columnas, cada columna con un nombre y un tipo de datos escalares. Los tipos de datos escalares pueden ser estructurados (*int*, *real*, *datetime* o *timespan*), semiestructurados (*dynamic*) o texto libre (*string*). El tipo dinámico es similar a JSON, ya que puede contener un único valor escalar, una matriz o un diccionario de estos valores.
* Las **tablas externas** son tablas que hacen referencia a un almacenamiento u origen de datos SQL fuera de la base de datos de Data Explorer. De forma similar a las tablas, una tabla externa tiene un esquema bien definido (una lista ordenada de pares de nombre de columna y tipo de datos). A diferencia de las tablas de Data Explorer en las que los datos se ingieren en grupos de Data Explorer, las tablas externas trabajan con datos almacenados y administrados fuera de los grupos. Las tablas externas no conservan ningún dato y se usan para consultar o exportar datos a un almacén de datos externo.

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a Azure Synapse Analytics](get-started.md)
* [Creación de un área de trabajo](quickstart-create-workspace.md)
* [Uso de grupos de SQL sin servidor](quickstart-sql-on-demand.md)
* [Creación de un grupo de Data Explorer con Synapse Studio](data-explorer/data-explorer-create-pool-studio.md)
