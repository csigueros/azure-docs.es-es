---
title: Introducción a las plantillas de base de datos de Azure Synapse
description: Más información sobre las plantillas de base de datos
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: overview
ms.date: 11/02/2021
ms.custom: template-overview, ignite-fall-2021
ms.openlocfilehash: c3022ff0c5334b96fde38bf386c3340fb4b5c2e7
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997597"
---
# <a name="what-is-azure-synapse-database-templates"></a>¿Qué son las plantillas de base de datos de Azure Synapse?

Los datos adoptan muchas formas a medida que se mueven de los sistemas de origen a los almacenes de datos y data marts con la intención de resolver problemas empresariales. Las plantillas de base de datos pueden ayudar con la transformación de datos en conclusiones. Las plantillas de base de datos son un conjunto de definiciones de datos técnicos y empresariales que están diseñadas previamente para satisfacer las necesidades de un sector determinado. Actúan como planos técnicos que proporcionan elementos comunes derivados de procedimientos recomendados, regulaciones gubernamentales y las complejas necesidades de datos y análisis de una organización específica del sector. 

Las organizaciones pueden usar estos planos técnico de información para planear y diseñar soluciones de datos para la gobernanza de datos, la generación de informes, la inteligencia empresarial y los análisis avanzados. Los modelos de datos proporcionan arquitecturas de información integradas para toda la empresa que pueden ayudarle a implementar, de forma rápida y predecible, una arquitectura de datos probada del sector. 

Por ejemplo, si va a crear una solución de recomendación de productos para los clientes minoristas, necesitará un plano técnico básico para comprender lo que el cliente compró y la transacción que condujo a la compra. También puede que necesite información sobre la tienda donde se realizó la compra. Además, debe saber si el cliente forma parte de un programa de fidelización. Solo para llevar a cabo este caso de uso necesitamos los esquemas siguientes: 

 - Producto 
 - Transacción 
 - TransactionLineItem 
 - Customer 
 - CustomerLoyalty 
 - Tienda 

Puede configurar este caso de uso seleccionando las seis tablas de la plantilla de base de datos de venta al por menor. 

![imagen](https://user-images.githubusercontent.com/84302758/140736847-9d93436d-47b4-4175-8b09-a33de0bcde05.png)

Una plantilla de base de datos típica aborda los requisitos básicos de un sector específico y consta de: 

 - Un conjunto compatible de [plantillas de área de negocio](concepts-database-templates.md#business-area-templates).
 - Una o varias [plantillas de empresa](concepts-database-templates.md#enterprise-templates).  

## <a name="available-database-templates"></a>Plantillas de base de datos disponibles 

Actualmente hay seis plantillas de base de datos disponibles en Azure Synapse Studio que los clientes pueden usar para empezar a crear su base de datos de lago. 

 - **Banca**:para empresas que analizan datos bancarios.
 - **Bienes de consumo**: para fabricantes o productores de bienes comprados y usados por consumidores.
 - **Administración de fondos**: para empresas que administran fondos de inversión para los inversores.
 - **Anualidades y seguros de vida**: para empresas que proporcionan seguros de vida, venden anualidades o ambas cosas.
 - **Seguro de propiedad y accidentes**: para empresas que proporcionan seguros contra riesgos para la propiedad y diversas formas de cobertura de responsabilidad.
 - **Comercio minorista**: para vendedores de bienes de consumo o servicios a clientes a través de varios canales.

Como la gestión de las emisiones y el carbono es un tema importante en todos los sectores, hemos incluido estos componentes en todas las plantillas de bases de datos disponibles. Estos componentes facilitan la tarea a las empresas que necesitan seguir y notificar sus emisiones directas e indirectas de gases de efecto invernadero.

## <a name="next-steps"></a>Pasos siguientes
Siga explorando las funcionalidades del diseñador de bases de datos mediante los vínculos siguientes.
- [Concepto de plantillas de base de datos](concepts-database-templates.md)
- [Inicio rápido](quick-start-create-lake-database.md)
