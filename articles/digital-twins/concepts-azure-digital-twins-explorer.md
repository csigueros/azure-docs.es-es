---
title: Azure Digital Twins Explorer
titleSuffix: Azure Digital Twins
description: Obtenga información sobre las capacidades y el propósito de Azure Digital Twins Explorer, y cuándo puede ser una herramienta útil para visualizar modelos digitales, gemelos y grafos.
author: baanders
ms.author: baanders
ms.date: 10/29/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: bbcc693123f84d5cf789f53c8961f648ce2e90bf
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504253"
---
# <a name="azure-digital-twins-explorer-preview"></a>Azure Digital Twins Explorer (versión preliminar)

Este artículo contiene información sobre **Azure Digital Twins Explorer**, incluidos sus casos de uso y una introducción a sus características. Para obtener pasos detallados sobre el uso de cada característica, consulte [Uso de Azure Digital Twins Explorer](how-to-use-azure-digital-twins-explorer.md).

Azure Digital Twins Explorer es una herramienta para desarrolladores que se emplea para visualizar e interactuar con los datos de la instancia de Azure Digital Twins, incluidos los [modelos](concepts-models.md) y el [grafo del gemelo](concepts-twins-graph.md). 

>[!NOTE]
>Esta herramienta se encuentra actualmente en **versión preliminar pública**.

Esta es una vista de la ventana del explorador, en la que se muestran los modelos y gemelos que se han rellenado para un grafo de ejemplo:

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png" alt-text="Captura de pantalla de Azure Digital Twins Explorer que muestra modelos y gemelos de ejemplo." lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-demo.png":::

La interfaz visual es una gran herramienta para explorar y comprender la forma de su grafo y conjunto de modelos. También permite realizar cambios puntuales en gemelos y relaciones individuales.

## <a name="when-to-use"></a>Cuándo se usa

Azure Digital Twins Explorer es una herramienta visual diseñada para los usuarios que desean explorar su grafo del gemelo y modificar gemelos y relaciones en el contexto de este grafo.

Los desarrolladores pueden encontrar esta herramienta especialmente útil en los escenarios siguientes:
* **Exploración**: use el explorador para obtener información sobre Azure Digital Twins y la forma en que representa su entorno real. Importe modelos y grafos de ejemplo que pueda ver y editar para familiarizarse con el servicio. Si desea ver pasos guiados para empezar a usar Azure Digital Twins Explorer, consulte [Introducción a Azure Digital Twins Explorer](quickstart-azure-digital-twins-explorer.md).
* **Desarrollo**: use el explorador para ver y validar el grafo gemelo. También puede usarlo para investigar propiedades específicas de modelos, gemelos y relaciones. Realice modificaciones de acceso puntual en el grafo y sus datos. Para obtener instrucciones detalladas sobre cómo usar cada característica, consulte [Uso de Azure Digital Twins Explorer](how-to-use-azure-digital-twins-explorer.md). 

El propósito principal del explorador es ayudarle a visualizar y comprender el grafo, y actualizarlo según sea necesario. Para soluciones a gran escala y para trabajos que se deben repetir o automatizar, considere la posibilidad de usar [API y SDK](./concepts-apis-sdks.md) para interactuar con la instancia mediante código.

[!INCLUDE [digital-twins-access-explorer.md](../../includes/digital-twins-access-explorer.md)]

## <a name="features-and-capabilities"></a>Características y funcionalidades

Azure Digital Twins Explorer se organiza en paneles, cada uno con un conjunto diferente de funcionalidades para explorar y administrar los modelos, gemelos y relaciones.

Las secciones del explorador son las siguientes:
* **Explorador de consultas**: ejecute consultas en el grafo de gemelos y vea los resultados visuales en el panel **Grafo de gemelos**.
* **Modelos**: vea una lista de los modelos y realice acciones como agregar, quitar y ver detalles de un modelo.
* **Gemelos**: vea una lista plana de los gemelos y sus relaciones asociadas.
* **Grafo de gemelos**: visualice los gemelos y las relaciones como un grafo de gemelos personalizable. Cree y elimine gemelos y relaciones, y vea o edite sus propiedades.
* **Grafo de modelos**: visualice los modelos y las formas en que están interconectados en forma de un grafo de modelos personalizable.

:::image type="content" source="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-panels.png" alt-text="Captura de pantalla de Azure Digital Twins Explorer, con un resaltado alrededor de cada uno de los paneles descritos anteriormente." lightbox="media/concepts-azure-digital-twins-explorer/azure-digital-twins-explorer-panels.png":::

Para obtener instrucciones detalladas sobre cómo usar cada característica, consulte [Uso de Azure Digital Twins Explorer](how-to-use-azure-digital-twins-explorer.md). 

## <a name="how-to-contribute"></a>Cómo contribuir

Azure Digital Twins Explorer es una herramienta de **código abierto** que agradece las contribuciones al código y la documentación. La aplicación hospedada se implementa regularmente desde un repositorio de código fuente de GitHub.

Para ver el código fuente de la herramienta y leer instrucciones detalladas sobre cómo contribuir al código, visite su repositorio de GitHub: [digital-twins-explorer](https://github.com/Azure-Samples/digital-twins-explorer).

Para ver las instrucciones para contribuir a esta documentación, visite la [Guía para colaboradores de Docs](/contribute/).

## <a name="other-considerations"></a>Otras consideraciones

### <a name="region-support"></a>Regiones admitidas

Azure Digital Twins Explorer está disponible para su uso con todas las instancias de Azure Digital Twins en todas las [regiones admitidas](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).

Sin embargo, durante la versión preliminar pública, los datos se pueden enviar para su procesamiento a través de regiones diferentes de la región donde se hospeda la instancia. Para evitar que los datos de enruten de esta manera en situaciones en las que la soberanía de datos es un problema, puede descargar el [código fuente abierto](#how-to-contribute) para crear una versión hospedada localmente del explorador en su propia máquina.

### <a name="billing"></a>Facturación

Azure Digital Twins Explorer es una herramienta gratuita para interactuar con el servicio Azure Digital Twins. Aunque la propia herramienta es gratuita, se puede incurrir en costos durante el uso cuando se envían solicitudes al servicio Azure Digital Twins, el cual sigue estas directrices de precios: [Precios de Azure Digital Twins](https://azure.microsoft.com/pricing/details/digital-twins/).

## <a name="next-steps"></a>Pasos siguientes 

Para obtener información sobre cómo usar las características de Azure Digital Twins Explorer, consulte [Uso de Azure Digital Twins Explorer](how-to-use-azure-digital-twins-explorer.md).