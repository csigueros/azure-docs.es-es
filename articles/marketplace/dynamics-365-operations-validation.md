---
title: Validación funcional de una oferta de Dynamics 365 Operations en Microsoft AppSource (Azure Marketplace)
description: Valide funcionalmente una oferta de Dynamics 365 Operations en Microsoft AppSource (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: vamahtan
ms.author: vamahtan
ms.date: 05/19/2021
ms.openlocfilehash: 45bdf3e0006aeaf24b8c3ede0f7090aabfe85808
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110483355"
---
# <a name="dynamics-365-for-operations-functional-validation"></a>Validación funcional de Dynamics 365 for Operations

Para publicar una oferta de Dynamics 365 for Operations en el [Centro de partners](https://partner.microsoft.com/dashboard/home), hacen falta dos validaciones funcionales:

- Carga de un vídeo de demostración del entorno de Dynamics 365 que muestre la funcionalidad básica.
- Presentación de capturas de pantalla que muestren el entorno de [Lifecycle Services](https://lcs.dynamics.com/) (LCS) de la solución.

> [!NOTE]
> Las publicaciones de recertificación posteriores no requieren demostración. Para más información, consulte el [documento de directivas de AppSource](/legal/marketplace/certification-policies#1440-dynamics-365-finance-ops).

## <a name="how-to-validate"></a>Validación

Hay dos opciones para la validación funcional:

- Mantener una conferencia telefónica de 30 minutos con nosotros en horario comercial de la hora estándar del Pacífico (PST) para demostrar y grabar el entorno [LCS](https://lcs.dynamics.com/) y la solución, o bien
- En el Centro de partners, ir a [Marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) > **Introducción** y cargar una dirección URL de un vídeo de demostración y capturas de pantalla de LCS en la pestaña Contenido adicional de la oferta.

El equipo de certificación de Microsoft revisa el vídeo y los archivos, y aprueba la solución o le envía un correo electrónico con los pasos a seguir.

### <a name="option-1-30-minute-conference-call"></a>Opción 1: conferencia telefónica de 30 minutos

Para programar una llamada de revisión final, póngase en contacto con [appsourceCRM@microsoft.com](mailto:appsourceCRM@microsoft.com) y proporcione el nombre de la oferta y algunas posibles franjas horarias entre las 8 de la mañana y las 5 de la tarde, hora del Pacífico.

### <a name="option-2-upload-a-demo-video-and-lcs-screenshots"></a>Opción 2: carga de un vídeo de demostración y capturas de pantalla de LCS

1. Grabe un vídeo y cargue la dirección en el sitio de hospedaje de su elección. Siga estas instrucciones:

    - Que sea visible para el equipo de certificación de Microsoft.
    - Que dure menos de 20 minutos.
    - Que incluya hasta tres aspectos destacados de la funcionalidad principal de la solución en el entorno de Dynamics 365.

    > [!NOTE]
    > Puede usar un vídeo de marketing existente si cumple con las directrices.

2. Realice las siguientes capturas de pantalla del entorno [LCS](https://lcs.dynamics.com/) que coincida con la oferta o solución que desee publicar. Tienen que ser suficientemente claras para que el equipo de certificación pueda leer el texto. Guárdelas como archivos JPG. En lugar de proporcionar capturas de pantalla, también puede proporcionar un permiso a [appSourceCRM@microsoft.com](mailto:appSourceCRM@microsoft.com) para acceder a su entorno de LCS y comprobar la configuración.

    1. Vaya a **LCS** > **Modelador de procesos empresariales** > **Project library** (Biblioteca de proyecto). Realice capturas de pantalla de todos los pasos del proceso. Incluya las columnas **Diagramas** y **Revisada**, como se muestra aquí:

       :::image type="content" source="media/dynamics-365-operations/project-library.png" alt-text="Muestra la ventana biblioteca de proyectos.":::

      2. Vaya a **LCS** > **Administración de soluciones** > **Test Solution Package** (Paquete de solución de prueba). Realice capturas de pantallas que incluyan la información general del paquete y el contenido que se muestra en estos ejemplos:

    | Campo | Imagen |
    | --- | --- |
    | Introducción al paquete | [![Captura de pantalla que muestra la ventana "Introducción al paquete".](media/dynamics-365-operations/package-overview-45.png)](media/dynamics-365-operations/package-overview.png#lightbox) |
    | <ul><li>Aprobadores de soluciones</li></ul> | [![Pantalla de información general del paquete](media/dynamics-365-operations/solution-approvers-45.png)](media/dynamics-365-operations/solution-approvers.png#lightbox) |
    | Contenido del paquete<ul><li>Modelo</li><li>Paquete de software implementable</li></ul> | [![Pantalla uno de contenido del paquete](media/dynamics-365-operations/package-contents-1-45.png)](media/dynamics-365-operations/package-contents-1.png#lightbox) |
    | <ul><li>Configuración de GER</li><li>Copia de seguridad completa de base de datos</li></ul><br>No se necesitan artefactos en la sección de **configuración de GER**. | [![Pantalla dos de contenido del paquete](media/dynamics-365-operations/package-contents-2-45.png)](media/dynamics-365-operations/package-contents-2.png#lightbox) |
    | <ul><li>Modelo de informe de Power BI</li><li>Artefacto de BPM</li></ul><br>Los artefactos no son necesarios en la sección **Power BI**. | [![Pantalla tres de contenido del paquete](media/dynamics-365-operations/package-contents-3-45.png)](media/dynamics-365-operations/package-contents-3.png#lightbox) |
    | <ul><li>Paquete de datos de proceso</li><li>Contrato de licencia de la solución y directiva de privacidad</li></ul><br>Las secciones **Configuración de GER** y **Modelo de informe de Power BI** se pueden incluir de manera opcional en las ofertas de operaciones. | [![Pantalla cuatro de contenido del paquete ](media/dynamics-365-operations/package-contents-4-45.png)](media/dynamics-365-operations/package-contents-4.png#lightbox) |

    Para más información acerca de cada sección del portal de LCS, consulte el [manual del usuario de LCS](/dynamics365/fin-ops-core/dev-itpro/lifecycle-services/lcs-user-guide).

3. Realice la carga en el Centro de partners.

    1. Cree un documento de texto que incluya la dirección del vídeo de demostración y las capturas de pantalla, o guarde las capturas de pantalla como archivos JPG independientes.
    2. Agregue el texto y las imágenes a un archivo .zip en el [Centro de partners](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) en la pestaña **Supplemental content** (Contenido adicional).

    [![Muestra la ventana de biblioteca del proyecto](media/dynamics-365-operations/supplemental-content.png)](media/dynamics-365-operations/supplemental-content.png#lightbox)

## <a name="next-steps"></a>Pasos siguientes

- Para empezar a crear una oferta, consulte [Planeamiento de una oferta de Microsoft Dynamics 365](marketplace-dynamics-365.md).
- Si ha terminado de crear la oferta, es el momento de hacer clic en [Review and publish](dynamics-365-review-publish.md) (Revisar y publicar).
