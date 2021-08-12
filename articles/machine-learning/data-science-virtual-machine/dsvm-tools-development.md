---
title: Herramientas de desarrollo
titleSuffix: Azure Data Science Virtual Machine
description: Obtenga información sobre las herramientas y los entornos de desarrollo integrado disponibles en Data Science Virtual Machine.
keywords: herramientas de ciencia de datos, máquina virtual de ciencia de datos, herramientas para la ciencia de datos, ciencia de datos de linux
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 05/12/2021
ms.openlocfilehash: efe57637347b5886827f5da443acd9240974d687
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110070973"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Herramientas de desarrollo en Azure Data Science Virtual Machine

Data Science Virtual Machine (DSVM) agrupa varias herramientas populares en un entorno de desarrollo integrado (IDE) altamente productivo. A continuación presentamos algunas herramientas que se proporcionan en DSVM.

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

| Category | Value |
|--|--|
| ¿Qué es? | IDE de uso general |
| Versiones de DSVM compatibles | Windows Server 2019: Visual Studio 2019 |
| Usos típicos | Desarrollo de software |
| ¿Cómo se configura e instala en DSVM? | Carga de trabajo de ciencia de datos (herramientas de Python y R), carga de trabajo de Azure (Hadoop, Data Lake), Node.js, herramientas de SQL Server, [Azure Machine Learning para Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai) |
| Cómo usarla y ejecutarla | Acceso directo de escritorio (`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`). De forma gráfica, abra Visual Studio mediante el icono de escritorio o el menú **Inicio**. Busque programas (logotipo de Windows + S), seguido de **Visual Studio**. Desde allí, puede crear proyectos en lenguajes como C#, Python, R o Node.js. |

> [!NOTE]
> Quizás vea un mensaje que indica que el período de evaluación ha expirado. Escriba las credenciales de su cuenta Microsoft. O cree una nueva cuenta gratuita para acceder a Visual Studio Community.

## <a name="visual-studio-code"></a>Visual Studio Code 

| Category | Value |
|--|--|
| ¿Qué es? | IDE de uso general |
| Versiones de DSVM compatibles | Windows, Linux |
| Usos típicos | Editor de código e integración de Git |
| Cómo usarla y ejecutarla | Acceso directo de escritorio (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) en Windows o acceso directo de escritorio o terminal (`code`) en Linux |

## <a name="rstudio-desktop"></a>RStudio Desktop

| Category | Value |
|--|--|
| ¿Qué es? | IDE de cliente para el lenguaje R |
| Versiones de DSVM compatibles | Windows, Linux |
| Usos típicos | Desarrollo de R |
| Cómo usarla y ejecutarla | Acceso directo de escritorio (`C:\Program Files\RStudio\bin\rstudio.exe`) en Windows y acceso directo de escritorio (`/usr/bin/rstudio`) en Linux |

## <a name="pycharm"></a>PyCharm

| Category | Value |
|--|--|
| ¿Qué es? | IDE de cliente para el lenguaje Python |
| Versiones de DSVM compatibles | Windows 2019, Ubuntu 18.04 |
| Usos típicos | Desarrollo de Python |
| Cómo usarla y ejecutarla | Acceso directo de escritorio (`C:\Program Files\tk`) en Windows. Acceso directo de escritorio (`/usr/bin/pycharm`) en Linux |
