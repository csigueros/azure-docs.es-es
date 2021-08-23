---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 929bcb52f724162454fc7c65939a4cec2ffd7ca6
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112255044"
---
## <a name="prerequisites"></a>Requisitos previos

### <a name="get-sample-code"></a>Obtención de código de ejemplo

En este tutorial se da por hecho que ya ha realizado los pasos de los [tutoriales anteriores](../articles/container-registry/container-registry-tutorial-quick-task.md) y que ha bifurcado y clonado el repositorio de ejemplo. Si aún no lo ha hecho, complete los pasos de la sección [Requisitos previos](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites) del tutorial anterior antes de continuar.

### <a name="container-registry"></a>Registro de contenedor

Debe tener un registro de contenedor de Azure en la suscripción de Azure para completar este tutorial. Si necesita un registro, consulte el [tutorial anterior](../articles/container-registry/container-registry-tutorial-quick-task.md) o esta [guía de inicio rápido: Creación de un registro de contenedor con la CLI de Azure](../articles/container-registry/container-registry-get-started-azure-cli.md).

### <a name="create-a-github-personal-access-token"></a>Creación de un token de acceso personal de GitHub

Para desencadenar una tarea en una confirmación en un repositorio GIT, ACR Tasks necesita un [token de acceso personal (PAT)](../articles/container-registry/container-registry-tasks-overview.md#personal-access-token) para acceder al repositorio. Si aún no dispone de un PAT, siga estos pasos para generar uno en GitHub:

1. Vaya a la página de creación del token de acceso personal en GitHub en https://github.com/settings/tokens/new
1. Escriba una breve **descripción** para el token, por ejemplo, "ACR Tasks Demo"
1. Seleccione los ámbitos de ACR para acceder al repositorio. Para acceder a un repositorio público como en este tutorial, en **repo**, habilite **repo:status** y **public_repo**

   ![Captura de pantalla de la página de generación de un token de acceso personal en GitHub][build-task-01-new-token]

   > [!NOTE]
   > Para generar un PAT y acceder a un repositorio *privado*, seleccione el ámbito para el control total del **repositorio**.

1. Seleccione el botón **Generar token** (puede que se le pida que confirme la contraseña)
1. Copie y guarde el token generado en una **ubicación segura** (usará este token cuando defina una tarea en la siguiente sección)

   ![Captura de pantalla del token de acceso personal que se generó en GitHub][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
