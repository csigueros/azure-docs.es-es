---
title: Adopción de ontologías estándar del sector
titleSuffix: Azure Digital Twins
description: Obtenga información sobre las ontologías del sector existentes que se pueden adoptar para Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 51a35f13b8f9328abaad44b112acf4e2176796d6
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771189"
---
# <a name="adopting-an-industry-ontology"></a>Adopción de una ontología del sector

Como es posible que sea más fácil comenzar con una ontología de Lenguaje de definición de Digital Twins (DTDL) de código abierto que desde cero, Microsoft se está asociando con expertos de dominio para publicar ontologías. que representan convenciones del sector ampliamente aceptadas y admiten diversos casos de uso de clientes. 

El resultado es un conjunto de ontologías de código abierto basadas en DTDL que aprenden de los estándares del sector, se basan en ellos o los utilizan directamente. Las ontologías están diseñadas para satisfacer las necesidades de los desarrolladores que se encuentran en un nivel inferior, con la posibilidad de que el sector las adopte o extienda posteriormente ampliamente.

En este momento, Microsoft ha trabajado con asociados para desarrollar ontologías para [edificios inteligentes](#realestatecore-smart-building-ontology), [ciudades inteligentes](#smart-cities-ontology) y [redes eléctricas](#energy-grid-ontology). Estas ontologías proporcionan un denominador común para el modelado basado en estándares en estos sectores para evitar la necesidad de reinvención. 

Cada ontología se centra en un conjunto inicial de modelos. Los autores de la ontología le agradecen que contribuyan a ampliar el conjunto inicial de casos de uso y a mejorar los modelos existentes. 

## <a name="realestatecore-smart-building-ontology"></a>Ontología RealEstateCore para edificios inteligentes

*Obtenga la ontología en el repositorio siguiente:* [Ontología RealEstateCore basada en el lenguaje de definición de Digital Twins para edificios inteligentes](https://github.com/Azure/opendigitaltwins-building).

Microsoft se ha asociado con [RealEstateCore](https://www.realestatecore.io/) para entregar esta ontología de DTDL de código abierto para el sector inmobiliario. [RealEstateCore](https://www.realestatecore.io/) es un consorcio sueco de propietarios inmobiliarios, proveedores de software e instituciones de investigación.

Esta ontología para edificios inteligentes proporciona una base común para el modelado de edificios inteligentes mediante estándares del sector (como el  [esquema de BRICK](https://brickschema.org/ontology/) o la  [ontología de la topología para edificios W3C](https://w3c-lbd-cg.github.io/bot/index.html)) para evitar la reinvención. La ontología también incluye procedimientos recomendados para usarla y extenderla correctamente. 

Para más información sobre las convenciones de modelado y la estructura de la ontología, cómo usarla, extenderla y colaborar, visite el repositorio de la ontología en GitHub: [Azure/opendigitaltwins-building](https://github.com/Azure/opendigitaltwins-building). 

También puede leer más sobre la asociación con RealEstateCore y los objetivos de esta iniciativa en la siguiente entrada de blog y en el vídeo insertado: [Ya está disponible RealEstateCore, una ontología para edificios inteligentes para gemelos digitales](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="smart-cities-ontology"></a>Ontology para las ciudades inteligentes

*Obtenga la ontología en el repositorio siguiente:* [Ontología del lenguaje de definición de Digital Twins (DTDL) para ciudades inteligentes](https://github.com/Azure/opendigitaltwins-smartcities).

Microsoft ha colaborado con [Open Agile Smart Cities (OASC)](https://oascities.org/) y [Sirus](https://sirus.be/) para proporcionar una ontología basada en el lenguaje de definición de Digital Twins para ciudades inteligentes que empieza por [ETSI CIM NGSI-LD](https://www.etsi.org/committee/cim). Además de ETSI NGSI-LD, también hemos evaluado Saref4City, CityGML, ISO y otras.

Para más información sobre la ontología, cómo usarla y colaborar, visite el repositorio de la ontología en GitHub: [Azure/opendigitaltwins-smartcities](https://github.com/Azure/opendigitaltwins-smartcities). 

Para más información sobre las asociaciones y el enfoque para ciudades inteligentes, consulte esta entrada de blog y el vídeo insertado: [Ontología de ciudades inteligentes para Digital Twins](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585).

## <a name="energy-grid-ontology"></a>Ontología de la cuadrícula de energía

*Obtenga la ontología en el repositorio siguiente:* [Ontología del lenguaje de definición de Digital Twins (DTDL) para la cuadrícula de energía](https://github.com/Azure/opendigitaltwins-energygrid/).

Esta ontología se creó para ayudar a los proveedores de soluciones a acelerar el desarrollo de soluciones de gemelo digital para casos de uso de energía, por ejemplo, supervisión de los recursos de la red, análisis de interrupciones e impacto, simulación y mantenimiento predictivo. Además, la ontología se puede usar para habilitar tanto la transformación digital como la modernización de la red eléctrica. Es una adaptación del [Modelo de información común (CIM)](https://cimug.ucaiug.org/), un estándar global para la administración de recursos de la cuadrícula de energía, el modelado de las operaciones del sistema de alimentación y el mercado de productos energéticos físicos.

Para más información sobre la ontología, sobre cómo usarla y colaborar, visite el repositorio de la ontología en GitHub: [Azure/opendigitaltwins-energygrid](https://github.com/Azure/opendigitaltwins-energygrid/). 

También puede obtener más información sobre las asociaciones y el enfoque de las cuadrículas de energía en el blog sobre [ontología de la cuadrícula de energía para Digital Twins](https://techcommunity.microsoft.com/t5/internet-of-things/energy-grid-ontology-for-digital-twins-is-now-available/ba-p/2325134).

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre cómo extender las ontologías estándar del sector para que satisfagan las especificaciones en [Extensión de las ontologías del sector](concepts-ontologies-extend.md).

* O bien, continúe el proceso de aprendizaje para desarrollar modelos basados en ontologías: [Uso de estrategias de ontología en un procedimiento de desarrollo de modelos](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).