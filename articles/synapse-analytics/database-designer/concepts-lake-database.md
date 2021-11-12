---
title: Conceptos de la base de datos de lago de Azure Synapse
description: Obtenga información sobre el concepto de base de datos de lago y cómo ayuda a estructurar los datos.
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 5b030bc1551bc8d21101b127c123507f08abf62c
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510420"
---
# <a name="lake-database"></a>Base de datos de lago

La base de datos lago de Azure Synapse Analytics permite a los clientes reunir el diseño de la base de datos, la metainformación sobre los datos almacenados y la posibilidad de describir cómo y dónde se deben almacenar los datos. La base de datos de lago aborda el desafío de los lagos de datos actuales, donde es difícil entender cómo se estructuran los datos.  

![Información general de base de datos de lago](./media/concepts-lake-database/lake-database-overview.png)


## <a name="database-designer"></a>Diseñador de bases de datos

El nuevo diseñador de bases de datos ofrece la posibilidad de crear un modelo de datos para la base de datos de lago y agregarle información adicional. Todas las entidades y atributos se pueden describir para proporcionar más información a lo largo del modelo, que no solo contiene entidades, sino también relaciones. La falta de modelo de relaciones ha sido un desafío para la interacción en el lago de datos. Este desafío se aborda ahora con un diseñador integrado que proporciona posibilidades que han estado disponibles en las bases de datos, pero no en el lago. Además, la capacidad de agregar descripciones y posibles valores de demostración al modelo permite que las personas que interactúen con él en el futuro tengan información donde la necesiten para comprender mejor los datos. 

## <a name="data-storage"></a>Almacenamiento de datos 

Las bases de datos de lago usan un lago de datos en la cuenta de Azure Storage para almacenar los datos de la base de datos. Los datos se pueden almacenar en formato de archivo .csv o Parquet y se pueden usar diferentes configuraciones para optimizar el almacenamiento. Cada base de datos de lago usa un servicio vinculado para definir la ubicación de la carpeta de datos raíz. Para cada entidad, se crean carpetas independientes de forma predeterminada dentro de esta carpeta de base de datos en el lago de datos. De forma predeterminada, todas las tablas de una base de datos de lago usan el mismo formato, pero los formatos y la ubicación de los datos se pueden cambiar por entidad si se solicita. 


## <a name="database-compute"></a>Proceso de base de datos

La base de datos lago se expone en el grupo de SQL sin servidor de Synapse SQL y Apache Spark que proporciona a los usuarios la capacidad de desacoplar el almacenamiento del proceso. Los metadatos asociados a la base de datos de lago facilitan que los distintos motores de proceso no solo proporcionen una experiencia integrada, sino que también usen información adicional (por ejemplo, relaciones) que no se admite originalmente en el lago de datos. 

## <a name="next-steps"></a>Pasos siguientes

Siga explorando las funcionalidades del diseñador de bases de datos mediante los vínculos siguientes.
- [Inicio rápido de creación de una base de datos de lago](quick-start-create-lake-database.md)
- [Concepto de plantillas de base de datos](concepts-database-templates.md)
