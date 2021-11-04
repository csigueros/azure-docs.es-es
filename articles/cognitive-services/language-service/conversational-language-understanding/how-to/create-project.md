---
title: Cómo crear proyectos en Conversational Language Understanding
titleSuffix: Azure Cognitive Services
description: Use este artículo para aprender a crear proyectos en Conversational Language Understanding.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: c837912ea60b0caf91e2d04382af343bd4b6a96a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093179"
---
# <a name="how-to-create-projects-in-conversational-language-understanding"></a>Cómo crear proyectos en Conversational Language Understanding

El Conversational Language Understanding le permite crear dos tipos de proyectos: proyectos de **conversación** y de **flujo de trabajo de orquestación**.

## <a name="sign-in-to-language-studio"></a>Inicio de sesión en Language Studio
Para empezar, primero debe iniciar sesión en [Language Studio](https://aka.ms/languageStudio) y crear un recurso de idioma. Seleccione **Listo** una vez completada la selección.

## <a name="navigate-to-conversational-language-understanding"></a>Vaya a Conversational Language Understanding

En Language Studio, busque la sección **Descripción del lenguaje de conversación** y seleccione **Conversational language understanding**.

Verá la página Proyectos de Conversational Language Understanding.

:::image type="content" source="../media/projects-page.png" alt-text="Una captura de pantalla que muestra la página de proyectos Conversational Language Understanding." lightbox="../media/projects-page.png":::

## <a name="create-a-conversation-project"></a>Creación de un proyecto de conversación
Después de seleccionar la conversación, debe proporcionar los detalles siguientes:
- Nombre: nombre del proyecto
- Descripción: Descripción opcional del proyecto
- Idioma principal de texto: el idioma principal del proyecto. Los datos de entrenamiento deben estar principalmente en este idioma.
- Habilitar varios idiomas: si desea permitir que el proyecto admita varios idiomas a la vez.

:::image type="content" source="../media/clu-project-modal.png" alt-text="Una captura de pantalla que muestra la ventana de creación del proyecto conversational de Conversational Language Understanding." lightbox="../media/clu-project-modal.png":::

Cuando haya terminado, haga clic en siguiente, revise los detalles y, a continuación, haga clic en Crear proyecto para completar el proceso. 

## <a name="create-an-orchestration-workflow-project"></a>Creación de un proyecto de flujo de trabajo de orquestación

Después de seleccionar la orquestación, debe proporcionar los detalles siguientes:
- Nombre: nombre del proyecto
- Descripción: Descripción opcional del proyecto
- Idioma principal de texto: el idioma principal del proyecto. Los datos de entrenamiento deben estar principalmente en este idioma.
- Habilitar varios idiomas: si desea permitir que el proyecto admita varios idiomas a la vez.

Una vez que haya terminado, ahora tiene la opción de conectarse a los demás proyectos y servicios a los que desea organizar. Cada conexión se representa mediante su tipo y los datos pertinentes. La intención debe tener un **nombre**, un **tipo de proyecto** (LUIS, respuesta a preguntas personalizadas (QnA) o Conversational Language Understanding) y, a continuación, seleccionar el proyecto al que desea conectarse por nombre. 

> [!NOTE]
> La lista de proyectos a los que puede conectarse son solo los proyectos que pertenecen al mismo recurso de idioma que usa para crear el proyecto de orquestación.

Este paso es opcional y seguirá teniendo la opción de agregar conexiones de intención después de crear el proyecto.

:::image type="content" source="../media/orchestration-project-detail.png" alt-text="Una captura de pantalla que muestra el proyecto de flujo de trabajo Conversational Language Understanding." lightbox="../media/orchestration-project-detail.png":::

## <a name="import-a-project"></a>Importación de un proyecto

Puede exportar un proyecto de Conversational Language Understanding como un archivo JSON en cualquier momento; para ello, vaya a la página de proyectos de conversación, seleccione un proyecto y presione **Exportar**.
Ese proyecto se puede volver a importar como un proyecto nuevo. Si importa un proyecto con el mismo nombre exacto, reemplaza los datos del proyecto por los datos del proyecto recién importado.

:::image type="content" source="../media/export.png" alt-text="Una captura de pantalla en la que se muestra el botón Exportar de Language Understanding." lightbox="../media/export.png":::

Si tiene una aplicación de LUIS existente, puede _importar_ directamente el JSON de la aplicación de LUIS a Conversational Language Understanding y creará un proyecto conversación con todas las partes que están disponibles actualmente: intenciones, entidades de ML y expresiones. Consulte [Compatibilidad con versiones anteriores con LUIS](../concepts/backwards-compatibility.md) para obtener más información.

Haga clic en el botón de flecha situado junto a **Crear un nuevo proyecto** y seleccione **Importar** y, a continuación, seleccione el archivo JSON de LUIS o Conversational Language Understanding.

:::image type="content" source="../media/import.png" alt-text="Una captura de pantalla que muestra el botón importar de Conversational Language Understanding." lightbox="../media/import.png":::

## <a name="next-steps"></a>Pasos siguientes

[Compilación de esquema](./build-schema.md)
