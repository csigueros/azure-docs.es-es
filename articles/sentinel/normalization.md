---
title: Normalización y el modelo de información de Azure Sentinel (ASIM) | Microsoft Docs
description: En este artículo se explica cómo normaliza Azure Sentinel los datos de muchos orígenes diferentes mediante el modelo de información de Azure Sentinel (ASIM).
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2021
ms.author: bagol
ms.openlocfilehash: e03f343444aed0c3aafac28deccb0f38c35e2478
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515059"
---
# <a name="normalization-and-the-azure-sentinel-information-model-asim-public-preview"></a>Normalización y modelo de información de Azure Sentinel (ASIM) (versión preliminar pública)

Azure Sentinel ingiere datos de muchos orígenes. Para trabajar con varios tipos de datos y tablas juntos, es necesario que los comprenda todos, y que escriba y use conjuntos únicos para reglas de análisis, libros y consultas de búsqueda para cada tipo o esquema.


A veces, necesitará reglas, libros y consultas independientes, aunque los tipos de datos compartan elementos comunes, como dispositivos de firewall. También puede ser difícil establecer una correlación entre diferentes tipos de datos durante una investigación y una búsqueda.

En este artículo se proporciona información general sobre el modelo de información de Azure Sentinel (ASIM), que proporciona una solución para los desafíos que plantea el control de varios tipos de datos.

> [!TIP]
> Vea también el [seminario web de ASIM](https://www.youtube.com/watch?v=WoGD-JeC7ng) o revise las [diapositivas del seminario web](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG). Para más información, consulte la sección [Pasos siguientes](#next-steps).
>

> [!IMPORTANT]
> ASIM está actualmente en versión preliminar. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

## <a name="common-asim-usage"></a>Uso común de ASIM

El modelo de información de Azure Sentinel (ASIM) proporciona una experiencia perfecta a la hora de gestionar orígenes diversos en vistas uniformes y normalizadas, al proporcionar la funcionalidad siguiente:

- **Detección entre orígenes**. Las reglas de análisis normalizadas funcionan entre orígenes, locales y en la nube, y detectan ataques como los de fuerza bruta o viaje imposible a través de sistemas, incluidos Okta, AWS y Azure.

- **Contenido independiente de origen**. La cobertura del contenido integrado y personalizado mediante ASIM se expande automáticamente a cualquier origen que admita ASIM, incluso si el origen se agregó después de crear el contenido. Por ejemplo, el análisis de eventos de proceso admite cualquier origen que un cliente pueda usar para traer los datos, como Microsoft Defender para punto de conexión, eventos de Windows y Sysmon.

- **Compatibilidad con los orígenes personalizados**, en análisis integrados

- **Facilidad de uso**. Una vez que un analista aprende el modelo ASIM, escribir consultas es mucho más sencillo, ya que los nombres de campos siempre son los mismos.

### <a name="asim-and-the-open-source-security-events-metadata"></a>ASIM y metadatos de eventos de seguridad de código abierto

El modelo de información de Azure Sentinel se alinea con el modelo de información común [Open Source Security Events Metadata (OSSEM)](https://ossemproject.com/intro.html), lo que permite establecer una correlación previsible de entidades en tablas normalizadas.

OSSEM es un proyecto dirigido por la comunidad que se centra principalmente en la documentación y la normalización de registros de eventos de seguridad procedentes de diversos orígenes de datos y sistemas operativos. El proyecto también proporciona un Modelo de información común (CIM) que pueden usar los ingenieros de datos durante los procedimientos de normalización de datos para que los analistas de seguridad puedan consultar y analizar los datos de diversos orígenes.

Para obtener más información, consulte la [documentación de referencia de OSSEM](https://ossemproject.com/cdm/guidelines/entity_structure.html).

## <a name="asim-components"></a>Componentes de ASIM

En la siguiente imagen se muestra cómo traducir datos no normalizados al contenido normalizado y usarlos en Azure Sentinel. Por ejemplo, puede empezar por una tabla personalizada, específica del producto y no normalizada, y usar un analizador y un esquema de normalización para convertir dicha tabla en datos normalizados. Use los datos normalizados en Microsoft y en análisis, reglas, consultas o libros personalizados, entre otros.

 :::image type="content" source="media/normalization/sentinel-information-model-components.png" alt-text="Flujo de conversión de datos no normalizados en normalizados y uso de dichos datos en Azure Sentinel":::

El modelo de información de Azure Sentinel incluye los siguientes componentes:

|Componente  |Descripción  |
|---------|---------|
|**Esquemas normalizados**     |   Abarcan los conjuntos estándar de tipos de eventos previsibles que puede usar al crear funcionalidades unificadas. <br><br>Cada esquema define los campos que representan un evento, una convención normalizada de nomenclatura de columnas y un formato estándar para los valores de campo. <br><br> ASIM define actualmente los siguientes esquemas:<br> - [Sesión de red](normalization-schema.md)<br> - [Actividad de DNS](dns-normalization-schema.md)<br> - [Evento de proceso](process-events-normalization-schema.md)<br> - [Evento de autenticación](authentication-normalization-schema.md)<br> - [Evento del Registro](registry-event-normalization-schema.md)<br> - [Actividad de archivo](file-event-normalization-schema.md)  <br><br>Para más información, consulte [Esquemas del modelo de información de Azure Sentinel](normalization-about-schemas.md).  |
|**Analizadores**     |  Asigne los datos existentes a los esquemas normalizados usando [funciones KQL](/azure/data-explorer/kusto/query/functions/user-defined-functions). <br><br>Implemente los analizadores de normalización desarrollados por Microsoft desde la [carpeta "Parsers" del GitHub de Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers). Los analizadores normalizados se encuentran en subcarpetas que empiezan por **ASim**.  <br><br>Para más información, consulte [Analizadores del modelo de información de Azure Sentinel](normalization-about-parsers.md).     |
|**Contenido de cada esquema normalizado**     |    Incluye reglas de análisis, libros, consultas de búsqueda, etc. El contenido de cada esquema normalizado funciona en datos normalizados de cualquier tipo sin necesidad de crear contenido específico del origen. <br><br>Para más información, consulte [Contenido del modelo de información de Azure Sentinel](normalization-content.md).   |
| | |

### <a name="asim-terminology"></a>Terminología de ASIM

El modelo de información de Azure Sentinel usa los siguientes términos:

|Término  |Descripción  |
|---------|---------|
|**Dispositivo de informes**     |   Sistema que envía los registros a Azure Sentinel. Es posible que este sistema no sea el sistema sujeto del registro que se envía.      |
|**Registro**     |Unidad de datos enviada desde el dispositivo de informes. Un registro suele denominarse `log`, `event` o `alert`, pero también puede corresponder a otro tipo de datos.         |
|**Contenido** o **elemento de contenido**     |Diferentes artefactos, personalizables o creados por el usuario, que se pueden usar con Azure Sentinel. Estos artefactos incluyen, por ejemplo, reglas de análisis, consultas de búsqueda y libros. Un elemento de contenido es uno de estos artefactos.|
| | |

<br>

## <a name="getting-started-with-asim"></a>Introducción a ASIM

Para empezar a usar ASIM:

1. Implemente los analizadores de ASIM desde el [repositorio de GitHub de Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers), desde las carpetas que comienzan por `ASim*`.

1. Active las plantillas de reglas de análisis que usan ASIM. Para más información, consulte la [lista del contenido del modelo de información de Azure Sentinel (ASIM)](normalization-content.md#builtin).

1. Use ASIM en el área de trabajo usando los métodos siguientes:

    - Use las consultas de búsqueda de ASIM del repositorio de GitHub de Azure Sentinel al consultar registros en KQL en la página **Registros** de Azure Sentinel. Para más información, consulte la [lista del contenido del modelo de información de Azure Sentinel (ASIM)](normalization-content.md#builtin).

    - Escriba sus propias reglas de análisis mediante ASIM o [convierta las existentes](normalization-content.md#builtin).

    - Habilite los datos personalizados para usar análisis integrados mediante la [escritura de analizadores](normalization-about-parsers.md) para sus orígenes personalizados y su [incorporación](normalization-about-parsers.md#include) al analizador independiente de origen correspondiente.

## <a name="next-steps"></a>Pasos siguientes

En este artículo se describe la normalización en Azure Sentinel y el modelo de información de Azure Sentinel.

Para más información, consulte:

- El [seminario web de ASIM](https://www.youtube.com/watch?v=WoGD-JeC7ng) o las [diapositivas](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG)
- [Esquemas del modelo de información de Azure Sentinel](normalization-about-schemas.md)
- [Analizadores del modelo de información de Azure Sentinel](normalization-about-parsers.md)
- [Contenido del modelo de información de Azure Sentinel](normalization-content.md)
