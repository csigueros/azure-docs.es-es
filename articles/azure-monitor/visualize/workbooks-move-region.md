---
title: 'Libros de Azure Monitor: traslado de regiones'
description: Traslado de un libro a una región diferente
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: 6cbdc2a43cc4fa3ce18a2ede52a115fd6de580c5
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471585"
---
# <a name="move-an-azure-workbook-to-another-region"></a>Traslado de un libro de Azure a otra región

En este artículo, se explica cómo pueden trasladarse los recursos de libros de Azure a otra región. Puede mover sus recursos a otra región por varios motivos. Por ejemplo, para aprovechar una nueva región de Azure, para implementar características o servicios que solo están disponibles en regiones concretas, para cumplir los requisitos de gobernanza y las directivas internas o en respuesta a los requisitos de planeamiento de capacidad.

## <a name="prerequisites"></a>Requisitos previos

* Asegúrese de que los libros se admiten en la región de destino.

* Estas instrucciones se aplican a los libros compartidos (`microsoft.insights/workbooks`) guardados en Azure Monitor y en la mayoría de los tipos de recursos.

  Sin embargo, en el caso de los libros vinculados específicamente al tipo de recurso Application Insights, esos libros se almacenan en la región de Azure donde se guarda el recurso de Application Insights.

  Esos libros no se pueden migrar individualmente a otra región.

## <a name="move"></a>Mover

La manera más sencilla de trasladar un libro de Azure es usar "Guardar como" en la herramienta de libros en la interfaz de usuario del portal:

1. Abra el libro de destino en el visor de libros.
2. Use el botón de la barra de herramientas "Editar" para entrar en el modo de edición.
3. Use el botón de la barra de herramientas "Guardar como".
4. En el formulario Guardar, elija un nombre y la región que desee para el libro. Asegúrese de que los demás campos de suscripción, grupo de recursos y uso compartido son adecuados.

   > [!NOTE]
   > Es posible que tenga que usar un nuevo nombre para el libro para evitar los nombres duplicados.

5. Haga clic en Save (Guardar). 

## <a name="verify"></a>Comprobar

Use la interfaz de usuario de exploración de libros de Azure para buscar el nuevo libro. Asegúrese de que la ubicación es la ubicación de destino.

## <a name="clean-up"></a>Limpieza

Una vez creado el libro en la nueva región, elimine el libro original de la región anterior.
1. Abra el libro original en el visor de libros.
2. Use el botón de la barra de herramientas "Editar" para entrar en el modo de edición.
3. En el menú desplegable de las herramientas de edición (icono de lápiz), elija "Eliminar libro".

Si cambió el nombre del libro para importarlo en una nueva región, puede cambiar el nombre del libro al nombre anterior una vez eliminado el libro original mediante el elemento "Cambiar nombre" de la lista desplegable de las herramientas de edición de la barra de herramientas del modo de edición.

## <a name="next-steps"></a>Pasos siguientes

¿Necesita trasladar una plantilla de libro en lugar de un libro? Consulte [cómo trasladar una plantilla de libro de Azure a otra región](./workbook-templates-move-region.md).

Vea cómo [implementar libros y plantillas de libro](../visualize/workbooks-automate.md) a través de plantillas de ARM.
