---
title: 'Personalización de un modelo de persona en Azure Video Analyzer for Media (anteriormente Video Indexer): Azure'
description: En este artículo se proporciona información general sobre qué es un modelo de persona en Azure Video Analyzer for Media (anteriormente Video Indexer) y cómo personalizarlo.
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 1d76c1124be542a22707b358ce1d050dfac380f8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614871"
---
# <a name="customize-a-person-model-in-video-analyzer-for-media"></a>Personalización de un modelo de marca en Video Analyzer for Media

Azure Video Analyzer for Media (anteriormente Video Indexer) admite el reconocimiento de celebridades en los vídeos. La característica de reconocimiento de celebridades abarca aproximadamente un millón de caras basadas en orígenes de datos comúnmente solicitados, como IMDB, Wikipedia y personas con más influencia de LinkedIn. Las caras que no reconoce Video Analyzer for Media también se detectan, pero no se les pone nombre. Los clientes pueden compilar modelos de persona personalizados y habilitar Video Analyzer for Media para que reconozca caras que no se reconocen de forma predeterminada. Los clientes pueden compilar estos modelos de persona mediante el emparejamiento de un nombre de persona con archivos de imagen de su cara.  

Si su cuenta sirve a diferentes casos de uso, podría serle beneficioso poder crear varios modelos de persona por cuenta. Por ejemplo, si el contenido de su cuenta está pensado para su ordenación en distintos canales, puede crear un modelo de persona independiente para cada canal. 

> [!NOTE]
> Cada modelo admite hasta 1 millón de personas y cada cuenta tiene un límite de 50 modelos. 

Una vez que se crea un modelo, para usarlo, proporcione el identificador de modelo de un modelo de persona específico al cargar/indexar o volver a indexar un vídeo. Al entrenar una nueva cara para un vídeo, se actualiza el modelo personalizado específico con el que está asociado ese vídeo. 

Si no necesita compatibilidad con varios modelos de persona, no asigne un identificador de modelo de persona a su vídeo al cargar/indexar o volver a indexar. En este caso, Video Analyzer for Media usará el modelo de persona predeterminado en su cuenta. 

Puede usar el sitio web de Video Analyzer for Media para editar las caras detectadas en un vídeo y administrar varios modelos de persona personalizados en su cuenta, como se describe en el tema [Customize a Person model using a website](customize-person-model-with-website.md) (Personalización de un modelo de persona mediante un sitio web). También puede usar la API, como se describe en el artículo de  [Personalización de un modelo de persona mediante las API](customize-person-model-with-api.md).
