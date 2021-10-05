---
title: Descripción de las características de glosario empresarial de Azure Purview
description: En este artículo se explica qué es el glosario empresarial de Azure Purview.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: b332a00f511ea747c2978dafbf60ce20abca36a8
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214415"
---
# <a name="understand-business-glossary-features-in-azure-purview"></a>Descripción de las características de glosario empresarial de Azure Purview

En este artículo se proporciona información general sobre la característica de glosario empresarial de Azure Purview. 

## <a name="business-glossary"></a>Glosario empresarial

Un glosario proporciona vocabulario para los usuarios empresariales.  Consta de términos empresariales que se pueden relacionar entre sí y permite clasificarlos para que se puedan comprender en contextos diferentes. Estos términos se pueden asignar a recursos como una base de datos, tablas, columnas, etc. Esto ayuda a abstraer la jerga técnica asociada a los repositorios de datos y permite al usuario empresarial descubrir y trabajar con datos del vocabulario que más les resulte más familiar.

Un glosario empresarial es una recopilación de términos. Cada término representa un objeto de una organización y es muy probable que haya varios términos que representan el mismo objeto. Por ejemplo, un cliente podría denominarse también como comprador. Estos términos múltiples tienen una relación entre sí. La relación entre estos términos puede ser una de las siguientes:

- Sinónimos: términos diferentes con la misma definición
- Relacionado: nombre diferente con una definición similar

El mismo término también puede implicar varios objetos empresariales. Es importante que cada término esté bien definido y se entienda claramente dentro de la organización.

## <a name="custom-attributes"></a>Atributos personalizados

Azure Purview admite ocho atributos listos para usar para cualquier término del glosario empresarial:
- Nombre
- Definición
- Administradores de datos
- Expertos en datos
- Acrónimo
- Sinónimos
- Términos relacionados
- Recursos

Estos atributos no se pueden editar ni eliminar. Sin embargo, no son suficientes para definir completamente un término en una organización. Para solucionar este problema, Purview proporciona una característica en la que puede definir atributos personalizados para el glosario.

## <a name="term-templates"></a>Plantillas de términos

Las plantillas de términos proporcionan atributos personalizados del glosario para agruparlos lógicamente en el catálogo. La característica le permite agrupar todos los atributos personalizados relevantes en una plantilla y, a continuación, aplicar la plantilla al crear el término del glosario. Por ejemplo, todos los atributos personalizados relacionados con finanzas, como el centro de coste, el centro de beneficios o el código de contabilidad, se pueden agrupar en una plantilla de términos de finanzas, que se puede usar para crear términos de glosario financiero.

Todos los atributos estándar se agrupan en una plantilla predeterminada del sistema. Cualquier término que cree contendrá estos atributos junto con los atributos personalizados adicionales creados como parte del proceso de creación de plantillas.

## <a name="glossary-vs-classification-vs-sensitivity-labels"></a>Glosario frente a clasificación y etiquetas de confidencialidad

Aunque los términos, las clasificaciones y las etiquetas del glosario son anotaciones en un recurso de datos, cada uno de ellos tiene un significado diferente en el contexto del catálogo. 

### <a name="glossary"></a>Glosario

Como se indicó anteriormente, el término de glosario empresarial define el vocabulario empresarial de una organización y ayuda a acortar distancias entre los distintos departamentos de la empresa.

### <a name="classifications"></a>Clasificaciones

Las clasificaciones son anotaciones que se pueden asignar a entidades. La flexibilidad de las clasificaciones le permite utilizarlas en varios escenarios, como en los casos siguientes:

- Descripción de la naturaleza de los datos almacenados en los recursos de datos
- Definición de directivas de control de acceso

Actualmente, Purview tiene más de 200 clasificadores del sistema y el usuario puede definir sus propios clasificadores en el catálogo. Como parte del proceso de examen, estas clasificaciones se detectan automáticamente y se aplican a los recursos de datos y a los esquemas. Sin embargo, puede invalidarlas en cualquier momento. Las invalidaciones humanas nunca se sustituyen por exámenes automatizados.

### <a name="sensitivity-labels"></a>Etiquetas de confidencialidad

Las etiquetas de confidencialidad son un tipo de anotación que permite clasificar y proteger los datos de la organización sin obstaculizar la productividad y la colaboración. Las etiquetas de confidencialidad se usan para identificar categorías de tipos de clasificación en los datos de la organización, así como para agrupar las directivas que quiere aplicar a cada categoría. Purview usa los mismos tipos de información confidencial que Microsoft 365, lo que le permite ampliar las directivas de seguridad y la protección existentes en todo el contenido y los datos. Se pueden compartir las mismas etiquetas entre productos de Microsoft Office y recursos de datos en Purview.

## <a name="next-steps"></a>Pasos siguientes

- [Administración de plantillas de términos](how-to-manage-term-templates.md)
- [Examen del catálogo de datos de Azure Purview](how-to-browse-catalog.md)
