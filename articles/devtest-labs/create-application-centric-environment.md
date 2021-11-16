---
title: Creación de un entorno centrado en aplicaciones con Colony
description: En este artículo se muestra cómo crear un entorno centrado en aplicaciones con Colony y Azure.
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 9ce80a7467ac94a69227cc9616c72c622a6f575f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347250"
---
# <a name="create-an-application-centric-environment-with-colony"></a>Creación de un entorno centrado en aplicaciones con Colony

[CloudShell Colony de Quali](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) es una plataforma de software como servicio (SaaS) para ofrecer automatización de la infraestructura a escala. Colony se esfuerza para ayudar a los desarrolladores a implementar aplicaciones en entornos de nube complejos, como Azure y Kubernetes. Colony complementa a Azure DevTest Labs a lo largo del proceso de implementación de aplicaciones hasta la producción. En este artículo se muestra cómo crear un entorno centrado en aplicaciones con Colony y Azure.

## <a name="set-up-the-environment-with-colony-and-microsoft-azure"></a>Configuración del entorno con Colony y Microsoft Azure

1. Regístrese para obtener una evaluación gratuita de [Colony](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview).

    :::image type="content" source="./media/create-application-centric-environment/free-trial.png" alt-text="Captura de pantalla en la que se muestra la suscripción a una evaluación gratuita de Colony.":::
1. [Vincule la cuenta de Azure](https://colonysupport.quali.com/hc/articles/360008222234).

    :::image type="content" source="./media/create-application-centric-environment/welcome.png" alt-text="Captura de pantalla de la pantalla de bienvenida a Colony.":::
1. Invite a los usuarios a su espacio.
1. [Cree su primer plano técnico mediante un archivo YAML](https://colonysupport.quali.com/hc/articles/360001680807-Steps-to-Developing-a-Blueprint).
    1. Vincule el repositorio del plano técnico de GitHub o BitBucket con Colony.
    1. Use un plano técnico de ejemplo de Colony como base y modifíquelo según corresponda.

        :::image type="content" source="./media/create-application-centric-environment/performance-stress-tests.png" alt-text="Captura de pantalla en la que se muestran las pruebas de esfuerzo.":::
    1. Publique su plano técnico para que lo usen otros.
1. Inicie el entorno de la aplicación en un espacio aislado mediante Colony.

    :::image type="content" source="./media/create-application-centric-environment/blueprints.png" alt-text="Captura de pantalla del inicio del entorno de la aplicación en un espacio aislado mediante Colony.":::

También puede integrar el plano técnico como parte de un flujo de trabajo de integración continua y entrega continua (CI/CD) en Azure Pipelines. Para ver los pasos, consulte [Inicio de un espacio aislado desde Azure DevOps (VSTS)](https://colonysupport.quali.com/hc/articles/360008464234).

:::image type="content" source="./media/create-application-centric-environment/devops-pipeline.png" alt-text="Captura de pantalla en la que se muestra la conexión a una canalización de Azure Pipelines.":::

## <a name="next-steps"></a>Pasos siguientes

[Solicitud de una demostración de Colony](https://info.quali.com/cloudshell-colony-demo-request)
