---
title: Descripción de los informes de Insights en Azure Purview
description: En este artículo se explica qué es la característica Insights en Azure Purview.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: bd2a8aa4a0296cf67cf754f0d921e2791abdbb4f
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "110788799"
---
# <a name="understand-insights-in-azure-purview"></a>Descripción de Insights en Azure Purview

En este artículo se proporciona información general sobre la característica Insights en Azure Purview.

Insights es uno de los pilares principales de Purview. Esta característica proporciona a los clientes una visualización sencilla del catálogo en un único panel y, además, proporciona información específica a los administradores de los orígenes de datos, los usuarios empresariales, los administradores de datos, los responsables de datos y los administradores de seguridad. Actualmente, Purview cuenta con los siguientes informes de Insights que estarán disponibles para los clientes en la versión preliminar pública.

## <a name="asset-insights"></a>Información de recursos

Este informe proporciona una vista general de los datos y su distribución por tipo de origen, por clasificación y por tamaño de archivo, así como algunas dimensiones. Este informe está dirigido a diferentes tipos de usuarios que administren la cuenta de Purview y ejecuten exámenes, o a aquellos usuarios empresariales que puedan estar interesados en saber cuántos recursos existen con una determinada clasificación dentro de los datos de la organización. 

El informe proporciona amplios detalles a través de gráficos y KPI, para profundizar más delante en anomalías específicas, como archivos en ubicaciones incorrectas. El informe también admite una experiencia de cliente de un extremo a otro, gracias a la cual el cliente puede ver el recuento de recursos con una clasificación específica, puede desglosar la información por tipos de fuente y carpetas principales, y también puede ver la lista de recursos para realizar una investigación más detallada.

## <a name="scan-insights"></a>Información de exámenes

Este informe permite a los administradores comprender el estado general de los exámenes: cuántos se han realizado correctamente, cuántos no se han podido realizar y cuántos se han cancelado. Asimismo, el informe proporciona una actualización de estado de los exámenes que se han ejecutado en la cuenta de Purview durante los últimos siete días o los últimos 30 días.

El informe también permite a los administradores profundizar y explorar qué exámenes han devuelto errores y en qué tipos de origen específicos. Para que los usuarios puedan investigar aún más, el informe les permite navegar a la página del historial de exámenes de la experiencia "Orígenes".

## <a name="glossary-insights"></a>Información de glosarios

En este informe se proporciona a los usuarios empresariales y a los administradores de datos un informe de estado sobre el glosario. Los usuarios pueden ver este informe para comprender la distribución de los términos del glosario por estado, conocer cuántos términos del glosario están asociados a los recursos y cuántos aún no se han adjuntado a ningún recurso. Igualmente, los usuarios empresariales también pueden obtener información sobre la integridad de los términos del glosario. 

Este informe resume los elementos principales en los que un usuario empresarial o un administrador de datos necesita centrarse para crear un glosario completo y utilizable para su organización. Los usuarios también pueden ir a la experiencia "Glosario" de la sección "Información de glosarios", si quieren realizar cambios en un término específico del glosario.

## <a name="classification-insights"></a>Información de la clasificación

En este informe se proporcionan detalles acerca de dónde se encuentran los datos clasificados, las clasificaciones detectadas durante un examen, y obtiene los detalles de los propios archivos clasificados. Permite a los administradores de seguridad comprender los tipos de información que se encuentran en los datos de su organización. 

En Azure Purview, las clasificaciones son similares a las etiquetas de asunto y se usan para marcar e identificar el contenido de un tipo específico de los datos.

Use el informe de Información de la clasificación para identificar el contenido con clasificaciones específicas y comprender acciones necesarias como agregar seguridad adicional a los repositorios o mover el contenido a una ubicación más segura.

Para obtener más información, consulte [Información de la clasificación sobre los datos de Azure Purview](classification-insights.md).

## <a name="sensitivity-labeling-insights"></a>Información de la etiqueta de confidencialidad

En este informe se proporcionan detalles acerca de las etiquetas de confidencialidad que se encuentran durante un examen, así como los detalles de los propios archivos etiquetados. Permite a los administradores de seguridad garantizar la seguridad de la información que se encuentra en los datos de la organización. 

En Azure Purview, las etiquetas de confidencialidad se usan para identificar categorías de tipos de clasificación, así como el grupo de las directivas de seguridad que quiere aplicar en cada categoría.

Use el informe de Información de la etiqueta para identificar las etiquetas de confidencialidad que se encuentran en el contenido y comprender acciones necesarias como administrar el acceso a repositorios o a archivos específicos.

Para obtener más información, consulte [Información de la etiqueta de confidencialidad sobre los datos de Azure Purview](sensitivity-insights.md).

## <a name="file-extension-insights"></a>Información de la extensión de archivo

En este informe se proporcionan detalles acerca de las extensiones de archivos o tipos de archivos que se encuentran durante un examen, así como los detalles de los propios archivos. 

Use el informe de Información de la extensión de archivo para comprender cuántos archivos tiene cada vez, dónde se encuentran y si se pueden analizar para comprobar si tienen datos confidenciales.

Para obtener más información, consulte [Información de la extensión de archivo sobre los datos de Azure Purview](file-extension-insights.md).

## <a name="next-steps"></a>Pasos siguientes

* [Información de glosarios](glossary-insights.md)
* [Conclusiones de exámenes](scan-insights.md)
* [Información de la clasificación](./classification-insights.md)
