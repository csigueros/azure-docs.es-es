---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 023f3413fbd7b642300de19ad479029c94e7c4d5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699416"
---
## <a name="setting-up"></a>Instalación

### <a name="creating-the-visual-studio-project"></a>Creación del proyecto de Visual Studio

En Visual Studio 2019, cree un nuevo proyecto `Blank App (Universal Windows)`. Después de escribir el nombre del proyecto, no dude en elegir cualquier Windows SDK mayor que `10.0.17134`. 

### <a name="install-the-package-and-dependencies-with-nuget-package-manager"></a>Instalación de un paquete y sus dependencias con el Administrador de paquetes NuGet

Las API y bibliotecas de Calling SDK están disponibles públicamente a través de un paquete NuGet.
Los pasos siguientes ejemplifican cómo buscar, descargar e instalar el paquete NuGet de Calling SDK.

1. Abra el Administrador de paquetes NuGet (`Tools` -> `NuGet Package Manager` -> `Manage NuGet Packages for Solution`).
2. Haga clic en `Browse` y escriba `Azure.Communication.Calling` en el cuadro de búsqueda.
3. Asegúrese de que la casilla `Include prerelease` esté activada.
4. Haga clic en el paquete `Azure.Communication.Calling`.
5. Active la casilla correspondiente al proyecto de CS en la pestaña del lado derecho.
6. Haga clic en el botón `Install`.