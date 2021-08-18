---
title: Aplicación de ejemplo para acceder a datos de análisis del marketplace comercial
description: Use la aplicación de ejemplo para compilar su propia aplicación de análisis de marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: ed1fb34663a2e424b9d48e7adea422bb26726c77
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748239"
---
# <a name="sample-application-for-accessing-commercial-marketplace-analytics-data"></a>Aplicación de ejemplo para acceder a datos de análisis del marketplace comercial

Las aplicaciones de ejemplo se crean en lenguajes C# y Java y están disponibles en [GitHub](https://github.com/partneranalytics).

- [Aplicación de ejemplo de C#](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV)
- [Aplicación de ejemplo de Java](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV_Java)

Puede inspirarse en la aplicación de ejemplo y compilar su propia aplicación en cualquier lenguaje.

La aplicación de ejemplo logra los siguientes objetivos:

- Genera un token de Azure Active Directory (Azure AD).
- Obtiene los conjuntos de datos disponibles.
- Crea consultas definidas por el usuario.
- Obtiene consultas definidas por el usuario y el sistema.
- Programa un informe.

La aplicación de ejemplo no cubre el método de llamada a las API para otras funcionalidades. Sin embargo, el proceso de llamada a otras API sigue siendo el mismo que se ha descrito anteriormente.

## <a name="how-to-run-the-application"></a>Procedimiento para ejecutar la aplicación

1. Clone el repositorio en un sistema local mediante este comando:

    `git clone https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git`

    > [!NOTE]
    > Para obtener más instrucciones, consulte el archivo `ProgrammaticExportSampleAppISV/README.md` en el [repositorio](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git)de GitHub.

1. Para ejecutar rápidamente la aplicación, actualice el id. de cliente y el secreto de cliente en **appsettings.Development.json**.

    :::image type="content" source="./media/analytics-programmatic-access/appsettings-development-json.png" alt-text="Muestra un fragmento de código del archivo appsettings.Development.json.":::

Al ejecutar la aplicación, se iniciará un servidor web local y se abrirá una página (`https://localhost:44365/ProgrammaticExportSampleApp/sample`).

[![Muestra la página Programar informe.](./media/analytics-programmatic-access/schedule-report.png)](./media/analytics-programmatic-access/schedule-report.png#lightbox)

Esta página realizará llamadas API al servidor web que se ejecuta en el equipo local, que a su vez realizará las llamadas de API de acceso mediante programación reales.

## <a name="code-snippets"></a>Fragmentos de código

La estructura básica del código C# para realizar llamadas API de acceso mediante programación es la siguiente:

:::image type="content" source="./media/analytics-programmatic-access/code-snippets.png" alt-text="Captura de pantalla de las llamadas API.":::

## <a name="next-steps"></a>Pasos siguientes

- [API para acceder a datos de análisis del marketplace comercial](analytics-available-apis.md)
