---
title: Configuración del servicio de respuesta a preguntas
description: En este documento se describen las configuraciones avanzadas para recursos personalizados que tienen la respuesta a preguntas habilitada.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 2eae7e38422aa597c9b093dbefaef553cb084d09
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093145"
---
# <a name="configure-custom-question-answering-enabled-resources"></a>Configuración de recursos personalizados que tienen la respuesta a preguntas habilitada

Puede configurar la respuesta a preguntas para usar un recurso de Cognitive Search diferente.

## <a name="change-cognitive-search-resource"></a>Cambio del recurso de Cognitive Search

> [!WARNING]
> Si cambia el servicio Azure Search asociado al recurso de idioma, perderá el acceso a todas las knowledge bases o los proyectos que existen en él. Asegúrese de exportar los proyectos existentes antes de cambiar el servicio Azure Search.

Si crea un recurso de idioma y sus dependencias (como Search) en Azure Portal, se creará el servicio Search automáticamente y se vinculará al recurso de idioma. Después de crear estos recursos, puede actualizar el recurso Search en la pestaña **Características**.

1.  Vaya al recurso de idioma en Azure Portal.

2.  Seleccione **Características** y, después, la instancia del servicio Azure Cognitive Search que desea vincular con el recurso de idioma.

> [!div class="mx-imgBorder"]
> ![Adición de preguntas y respuestas a TA](../media/configure-resources/update-custom-feature.png)

3.  Seleccione **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

* [Cifrado de datos en reposo](./encrypt-data-at-rest.md)
