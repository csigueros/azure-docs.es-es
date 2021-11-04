---
title: Conceptos de plantillas de base de datos de Azure Synapse
description: Obtenga más información sobre las plantillas de base de datos de Azure Synapse
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 628c815905f5c8afb29df028d7ba74020b941043
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441312"
---
# <a name="lake-database-templates"></a>Plantillas de bases de datos de lago

Azure Synapse Analytics proporciona plantillas de base de datos estandarizadas para que diversos sectores puedan usar y crear fácilmente un modelo de base de datos que se adapte a las necesidades de la organización. Estas plantillas contienen metadatos enriquecidos que permiten comprender mejor un modelo de datos. Aproveche estas plantillas para crear la base de datos de lago y use el tiempo de ejecución analítico de Azure Synapse para proporcionar información a los usuarios empresariales.

Obtenga información sobre los conceptos relacionados con las plantillas de base de datos de lago de Azure Synapse. Use estas plantillas para crear una base de datos con metadatos enriquecidos a fin de mejorar la comprensión y la productividad.  

## <a name="business-area-templates"></a>Plantillas de área de negocio  

Las plantillas de área de negocio proporcionan la vista más completa y pormenorizada de los datos de un área de negocio o de asunto. Los modelos de área de negocio también se conocen como plantillas de área de asunto o de dominio. Las plantillas de área de negocio contienen tablas y columnas relevantes para una empresa determinada de un sector. Los administradores de datos, el equipo de gobernanza de datos y los equipos empresariales de una organización pueden usar las plantillas de área de negocio para crear un esquema de datos centrado en la empresa que facilite la comunicación detallada de los requisitos y el ámbito empresariales. Cada plantilla de área de negocio se construye partiendo de la base de un conjunto común de entidades a partir de la plantilla de base de datos empresarial del sector correspondiente, con el fin de garantizar que las plantillas de área de negocio tengan claves, atributos y definiciones comunes coherentes con otros modelos del sector. Por ejemplo, las plantillas de contabilidad e informes financieros, de marketing o de presupuestos y previsiones son plantillas de área de negocio de muchos sectores, como el minorista o el bancario. 

![Ejemplo de plantillas de área de negocio](./media/concepts-database-templates/business-area-template-example.png)

## <a name="enterprise-templates"></a>Plantillas empresariales 

Las plantillas de base de datos empresarial contienen un subconjunto de tablas que probablemente será de interés para una organización de un sector específico. Proporciona información general de alto nivel y describe la conectividad entre las plantillas de área de negocio relacionadas. Estas plantillas sirven de acelerador para muchos tipos de proyectos grandes. Por ejemplo, la plantilla de banca tiene una plantilla empresarial denominada "Banca". 

![Ejemplo de plantilla empresarial](./media/concepts-database-templates/enterprise-template-example.png)

## <a name="table"></a>Tabla

Una tabla es un objeto con una existencia independiente que se puede diferenciar de otros objetos. Por ejemplo, Customer, Store, Channel, etc.

## <a name="column"></a>Columna

Cada tabla se describe mediante un conjunto de columnas. Cada columna tiene un nombre, una descripción y un tipo de datos y está asociada a una tabla. Hay alrededor de 30 000 columnas en las plantillas de base de datos. Por ejemplo, CustomerId es una columna de la tabla Customer.

## <a name="primary-key"></a>Clave principal

La clave principal ayuda a identificar de forma única toda la tabla. Debe identificar de forma única las tuplas de una tabla. Por ejemplo, una clave de la columna CustomerId exige la unicidad para identificar a cada cliente de la tabla Customer.

## <a name="foreign-key"></a>Clave externa

Una clave externa es una columna o una combinación de columnas cuyos valores coinciden con una clave principal de una tabla diferente. Ayuda a establecer un vínculo entre dos tablas. Por ejemplo, el valor CustomerId de la tabla Transaction representa a un cliente que ha completado una transacción. Una clave externa siempre tiene una relación con una clave principal, por ejemplo, con la clave principal CustomerId de la tabla Customer.

## <a name="composite-key"></a>Clave compuesta

Una clave compuesta es aquella que se compone de dos o más columnas que se requieren juntas para identificar de forma única una tabla. Por ejemplo, en una tabla Order, es posible que se requieran OrderNumber y ProductId para identificar de forma única un registro.

## <a name="relationships"></a>Relaciones

Las relaciones son asociaciones o interacciones entre dos tablas cualesquiera. Por ejemplo, las tablas Customer y CustomerEmail están relacionadas entre sí. Hay dos tablas implicadas en una relación. Hay una tabla primaria y una tabla secundaria, a menudo conectadas mediante una clave externa. Se podría decir que la relación es de tabla a tabla.

## <a name="table-partitions"></a>Particiones de tabla

La base de datos de lago permite crear particiones de los datos subyacentes de una tabla con el objeto de mejorar el rendimiento. Puede establecer la configuración de partición en la configuración de almacenamiento de una tabla en el editor de bases de datos.

## <a name="next-steps"></a>Pasos siguientes

Siga explorando las funcionalidades del diseñador de bases de datos mediante los vínculos siguientes.
- [Inicio rápido](quick-start-create-lake-database.md)
- [Concepto de base de datos de lago](concepts-lake-database.md)
