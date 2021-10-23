---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: kengaderdus
ms.openlocfilehash: 9567ec7c71445fb50c19a5a87846dab0a6eef0f0
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130038133"
---
#### <a name="app-registrations"></a>[Registros de aplicaciones](#tab/app-reg-ga/) 

1. Seleccione **App registrations** (Registros de aplicaciones).
1. Seleccione la aplicación *webapi1* para abrir su página **Información general**.
1. En **Administrar**, seleccione **Exponer una API**.
1. Junto a **URI de id. de aplicación**, seleccione el vínculo **Establecer**.
1. Reemplace el valor predeterminado (un GUID) por `api` y, a continuación, seleccione **Guardar**. Se muestra el URI completo, que debe tener el formato `https://your-tenant-name.onmicrosoft.com/api`. Cuando la aplicación web solicita un token de acceso para la API, debe agregar este URI como prefijo para cada ámbito que se defina para la API.
1. En **Ámbitos definidos con esta API**, seleccione **Agregar un ámbito**.
1. Escriba los valores siguientes para crear un ámbito que defina el acceso de lectura a la API y, a continuación, seleccione **Agregar ámbito**:
    1. **Nombre de ámbito**: `demo.read`
    1. **Nombre para mostrar del consentimiento del administrador**: `Read access to demo API`
    1. **Descripción del consentimiento del administrador**: `Allows read access to the demo API`
1. Seleccione **Agregar un ámbito**, escriba los valores siguientes para agregar un ámbito que defina el acceso de escritura a la API y, a continuación, seleccione **Agregar ámbito**:
    1. **Nombre de ámbito**: `demo.write`
    1. **Nombre para mostrar del consentimiento del administrador**: `Write access to demo API`
    1. **Descripción del consentimiento del administrador**: `Allows write access to the demo API`

#### <a name="applications-legacy"></a>[Aplicaciones (heredado)](#tab/applications-legacy/)

1. Seleccione **Aplicaciones (heredado)** .
1. Seleccione la aplicación *webapi1* para abrir su página **Propiedades**.
1. Seleccione **Ámbitos publicados**. Los ámbitos publicados se pueden utilizar para conceder a una aplicación cliente ciertos permisos para la API web.
1. En **ÁMBITO** escriba `demo.read` y en **DESCRIPCIÓN**, `Read access to the web API`.
1. En **ÁMBITO** escriba `demo.write` y en **DESCRIPCIÓN**, `Write access to the web API`.
1. Seleccione **Guardar**.