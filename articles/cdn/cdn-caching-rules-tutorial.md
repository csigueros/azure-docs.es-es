---
title: 'Tutorial: Establecimiento de las reglas de almacenamiento en caché de Azure CDN | Microsoft Docs'
description: En este tutorial, se establecen reglas de almacenamiento en caché globales y personalizadas de Azure CDN.
services: cdn
documentationcenter: ''
author: duongau
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: duau
ms.custom: mvc
ms.openlocfilehash: a95888225d1b8e695f8950081fbf4247e7959fc9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131430959"
---
# <a name="tutorial-set-azure-cdn-caching-rules"></a>Tutorial: Establecimiento de las reglas de almacenamiento en caché de Azure CDN

> [!NOTE] 
> Las reglas de almacenamiento en caché solo están disponibles para los perfiles **Azure CDN estándar de Verizon** y **Azure CDN estándar de Akamai**. Para los perfiles de **Azure CDN de Microsoft**, debe usar el [motor de reglas estándar](cdn-standard-rules-engine-reference.md). Para los perfiles de **Azure CDN Premium de Verizon**, debe usar el [motor de reglas de Verizon Premium](./cdn-verizon-premium-rules-engine.md) en el portal **Administrar** para obtener una funcionalidad similar.
 

En este tutorial se describe cómo puede usar reglas de almacenamiento en caché de Azure Content Delivery Network (CDN) para establecer o modificar el comportamiento de expiración de caché predeterminado, tanto globalmente como con condiciones personalizadas, como una ruta de dirección URL y extensión de archivo. Azure CDN proporciona dos tipos de reglas de almacenamiento en caché:
- Reglas de almacenamiento en caché globales: puede establecer una regla de almacenamiento en caché global para cada punto de conexión en su perfil, lo que afecta a todas las solicitudes al punto de conexión. La regla de almacenamiento en caché global invalida todos los encabezados de directiva de caché HTTP, si está establecida.

- Reglas de almacenamiento en caché personalizadas: puede establecer una o varias reglas de almacenamiento en caché para cada punto de conexión en el perfil. Las reglas de almacenamiento en caché personalizadas coinciden con rutas de acceso y extensiones de archivo específicas, se procesan en orden e invalidan la regla de almacenamiento en caché global, si está establecida. 

En este tutorial, aprenderá a:
> [!div class="checklist"]
> - Abrir la página de reglas de almacenamiento en caché
> - Crear una regla de almacenamiento en caché global
> - Crear una regla de almacenamiento en caché personalizada

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Requisitos previos

Para poder completar los pasos de este tutorial, primero es preciso crear un perfil de CDN y al menos un punto de conexión de CDN. Para más información, consulte [Inicio rápido: Creación de un perfil y un punto de conexión de Azure CDN](cdn-create-new-endpoint.md).

## <a name="open-the-azure-cdn-caching-rules-page"></a>Abrir la página de reglas de almacenamiento en caché de Azure CDN

1. En [Azure Portal](https://portal.azure.com), seleccione un perfil de CDN y luego seleccione un punto de conexión.

2. En el panel izquierdo, debajo de Configuración, haga clic en **Reglas de caché**.

   ![Botón Reglas de caché de CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   Aparece la página **Reglas de caché**.

   ![Página de reglas de almacenamiento en caché de CDN](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="set-global-caching-rules"></a>Establecer reglas de almacenamiento en caché globales

Cree una regla de almacenamiento en caché global de la manera siguiente:

1. En **Reglas de almacenamiento en caché globales**, establezca **Comportamiento del almacenamiento en caché de cadenas de consulta** en **Ignorar cadenas de consulta**.

2. Establezca **Comportamiento de almacenamiento en caché** en **Establecer si falta**.
       
3. Para **Duración de expiración de caché**, escriba 10 en el campo **Días**.

    La regla de almacenamiento en caché global afecta a todas las solicitudes al punto de conexión. Esta regla respeta los encabezados de directiva de caché de origen, si existen (`Cache-Control` o `Expires`); en caso contrario, si no se especifican, establece la memoria caché en 10 días. 

    ![Reglas de almacenamiento en caché globales](./media/cdn-caching-rules/cdn-global-caching-rules.png)

## <a name="set-custom-caching-rules"></a>Establecer reglas de almacenamiento en caché personalizadas

Cree una regla de almacenamiento en caché personalizada de la manera siguiente:

1. En **Reglas de almacenamiento en caché personalizadas**, establezca **Condición de coincidencia** en **Ruta de acceso** y **Valor de coincidencia** en `/images/*.jpg`.
    
2. Establezca **Comportamiento de almacenamiento en caché** en **Invalidar** y escriba 30 en el campo **Días**.
       
    Esta regla de almacenamiento en caché personalizada establece una duración de caché de 30 días en cualquier archivo de imagen `.jpg` en la carpeta `/images` de su punto de conexión. Invalida todos los encabezados HTTP `Cache-Control` o `Expires` que envía el servidor de origen.

    ![Reglas de almacenamiento en caché personalizadas](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

    
## <a name="clean-up-resources"></a>Limpieza de recursos

En los pasos anteriores, creó reglas de almacenamiento en caché. Si ya no quiere usar estas reglas de almacenamiento en caché, puede quitarlas siguiendo estos pasos:
 
1. Seleccione un perfil de CDN y luego el punto de conexión con las reglas de almacenamiento en caché que quiere quitar.

2. En el panel izquierdo, debajo de Configuración, haga clic en **Reglas de caché**.

3. En **Reglas de almacenamiento en caché globales**, establezca **Comportamiento de almacenamiento en caché** en **No establecido**.
 
4. En **Reglas de almacenamiento en caché personalizadas**, seleccione la casilla situada junto a la regla que quiere eliminar.

5. Seleccione **Eliminar**.

6. En la parte superior de la página, seleccione **Guardar**.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:

> [!div class="checklist"]
> - Abrir la página de reglas de almacenamiento en caché
> - Crear una regla de almacenamiento en caché global
> - Crear una regla de almacenamiento en caché personalizada

Avance hasta el siguiente artículo para obtener información sobre cómo configurar opciones de reglas de almacenamiento en caché adicionales.

> [!div class="nextstepaction"]
> [Control del comportamiento del almacenamiento en caché de Azure CDN con reglas de caché](cdn-caching-rules.md)