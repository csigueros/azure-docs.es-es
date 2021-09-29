---
title: Edición de una API con Azure Portal | Microsoft Docs
description: Aprenda a usar API Management (APIM) para editar una API. Agregue, elimine o cambie el nombre de las operaciones en la instancia de APIM o edite el Swagger de la API.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/08/2017
ms.author: danlep
ms.openlocfilehash: db8ceb0921d7ed18cfaa7651d208d5f7e899a6e7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654449"
---
# <a name="edit-an-api"></a>Edición de una API

Los pasos descritos en este tutorial muestran cómo usar API Management (APIM) para editar una API. 

+ Para hacerlo, puede agregar, eliminar, cambiar el nombre de las operaciones en la instancia de APIM. 
+ Puede editar el swagger de la API.

## <a name="prerequisites"></a>Prerrequisitos

+ [Creación de una instancia de Azure API Management](get-started-create-service-instance.md)
+ [Importación y publicación de la primera API](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>Edición de una API en APIM

![Captura de pantalla que resalta el proceso de edición de una API en APIM.](./media/edit-api/edit-api001.png)

1. Haga clic en la pestaña **API**.
2. Seleccione una de las API que haya importado previamente.
3. Seleccione la pestaña **Diseño**.
4. Seleccione una operación que desee editar.
5. Para cambiar el nombre de la operación, seleccione un **lápiz** en la ventana **Frontend**.

## <a name="update-the-swagger"></a>Actualización del swagger

Puede actualizar su back-end API desde Azure Portal siguiendo estos pasos:

1. Seleccione **Todas las operaciones**.
2. Haga clic en lápiz en la ventana **front-end**.

    ![Captura de pantalla que resalta el icono de lápiz en la pantalla de front-end.](./media/edit-api/edit-api002.png)

    Aparece el swagger de la API.

    ![Edición de una API](./media/edit-api/edit-api003.png)

3. Actualización del swagger.
4. Presione **Save**(Guardar).

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Directivas de ejemplo de APIM](./policy-reference.md)
> [Transformación y protección de una API publicada](transform-api.md)