---
title: Creación de un entorno centrado en aplicaciones
description: En este artículo se muestra cómo crear un entorno centrado en aplicaciones con CloudShell Colony y Microsoft Azure.
ms.topic: how-to
ms.date: 11/26/2020
ms.openlocfilehash: 8b56a39996aa93e10ede28de91ae20857a8eefc0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627584"
---
# <a name="create-an-application-centric-environment"></a>Creación de un entorno centrado en aplicaciones

[CloudShell Colony de Quali](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) es una plataforma SaaS para ofrecer automatización de la infraestructura a escala para entornos complejos centrados en aplicaciones en tecnologías en la nube, como Azure y Kubernetes. CloudShell Colony complementa a Azure DevTest Labs para ayudar a los equipos de desarrolladores a implementar aplicaciones complejas en todo el flujo de valor, hasta la producción.

En este artículo se muestra cómo crear un entorno centrado en aplicaciones con CloudShell Colony y Microsoft Azure.

## <a name="set-up-the-environment-with-cloudshell-colony-and-microsoft-azure"></a>Configuración del entorno con CloudShell Colony y Microsoft Azure

1. Regístrese para obtener una evaluación gratuita de [Colony](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview).

    :::image type="content" source="./media/create-application-centric-environment/free-trial.png" alt-text="Suscríbase ahora para disfrutar de una prueba gratis":::.    
1. Vincule su cuenta de Azure ([vea los pasos aquí](https://colonysupport.quali.com/hc/articles/360008222234)).

    :::image type="content" source="./media/create-application-centric-environment/welcome.png" alt-text="Bienvenida de Colony":::     
1. Invite a los usuarios a su espacio.
1. Cree su primer plano técnico mediante un archivo YAML ([vea los pasos aquí](https://colonysupport.quali.com/hc/articles/360001680807-Steps-to-Developing-a-Blueprint)).
    1. Vincule el repositorio del plano técnico en GitHub o BitBucket con Colony.
    1. Use un plano técnico de ejemplo de Colony como base y modifíquelo según corresponda.

        :::image type="content" source="./media/create-application-centric-environment/performance-stress-tests.png" alt-text="Pruebas de esfuerzo":::    
    1. Publique su plano técnico para que lo usen otros.
1. Inicie el entorno de la aplicación en un espacio aislado mediante Colony.

    :::image type="content" source="./media/create-application-centric-environment/blueprints.png" alt-text="Inicio del entorno de la aplicación en un espacio aislado mediante Colony":::    

> [!NOTE]
> También puede integrar el plano técnico como parte de un flujo de trabajo de CI/CD en Azure DevOps ([vea los pasos que se indican aquí](https://colonysupport.quali.com/hc/articles/360008464234)).

:::image type="content" source="./media/create-application-centric-environment/devops-pipeline.png" alt-text="Conexión a la canalización de Azure DevOps":::    

## <a name="next-steps"></a>Pasos siguientes

[Solicitud de una demostración de Colony](https://info.quali.com/cloudshell-colony-demo-request)
