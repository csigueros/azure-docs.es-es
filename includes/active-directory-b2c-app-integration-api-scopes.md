---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: mimart
ms.openlocfilehash: 6b4ee09212bc62f43c583c73299ac33a842dc942
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779873"
---
1. Seleccione la aplicación *my-api1* que creó (id. de aplicación: 2) para abrir la página **Información general**.

1. En **Administrar**, seleccione **Exponer una API**.
1. Junto a **URI de id. de aplicación**, seleccione el vínculo **Establecer**. Reemplace el valor predeterminado (GUID) por un nombre único (por ejemplo, *tasks-api*) y, luego, seleccione **Guardar**. 
 
   Cuando la aplicación web solicite un token de acceso para la API web, deberá agregar este URI como prefijo para cada ámbito que se defina para la API.
1. En **Ámbitos definidos con esta API**, seleccione **Agregar un ámbito**.
1. Si desea crear un ámbito que defina el acceso de lectura a la API, escriba estos valores:

    a. **Nombre del ámbito**: *tasks.read*  
    b. **Nombre para mostrar del consentimiento del administrador**: *Acceso de lectura a la API de tareas*  
    c. **Descripción del consentimiento del administrador**: *Permite el acceso de lectura a la API de tareas*

1. Seleccione la opción **Agregar un ámbito**.

1. Seleccione **Agregar un ámbito** y, luego, agregue un ámbito que defina el acceso de lectura a la API. Para ello, escriba estos valores: 

    a. **Nombre del ámbito**: *tasks.write*  
    b. **Nombre para mostrar del consentimiento del administrador**: *Acceso de escritura a la API de tareas*  
    c. **Descripción del consentimiento del administrador**: *Permite el acceso de escritura a la API de tareas*
1. Seleccione la opción **Agregar un ámbito**.