---
title: 'Administración de knowledge bases: respuesta a preguntas'
description: La respuesta a preguntas personalizada le permite administrar proyectos proporcionando acceso a la configuración y el contenido del proyecto.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 1a28a0a4bf66824cc1c25d73512415e2a5613827
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093278"
---
# <a name="create-and-manage-project-settings"></a>Creación y administración de la configuración del proyecto

La respuesta a preguntas le permite administrar los proyectos o las knowledge bases proporcionando acceso a los orígenes de datos y la configuración del proyecto. Si no ha creado un proyecto de respuesta de preguntas antes, se recomienda comenzar con el [artículo de introducción](create-test-deploy.md).

## <a name="prerequisites"></a>Prerrequisitos

> * Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de empezar.
> * Un [recurso de idioma](https://aka.ms/create-language-resource) con la característica de respuesta a preguntas personalizada habilitada en Azure Portal. Recuerde el identificador de Azure Active Directory, la suscripción y el nombre del recurso de idioma que seleccionó al crear el recurso.

## <a name="create-a-project"></a>Crear un proyecto

1. Inicie sesión en el portal [Language Studio](https://language.azure.com/) con las credenciales de Azure.

2. Abra la página de [respuesta a preguntas](https://language.azure.com/languageStudio/questionAnswering/projects).

3. Seleccione **Create new project** (Crear nuevo proyecto).

4. Si va a crear el primer proyecto asociado al recurso de idioma, tiene la opción de crear proyectos futuros con varios idiomas para el mismo recurso. Si decide establecer explícitamente el idioma en un solo idioma en el primer proyecto, no podrá modificar esta configuración más adelante y todos los proyectos posteriores de ese recurso usarán el idioma seleccionado durante la creación del primer proyecto.

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla de la interfaz de usuario de selección de idioma.](../media/manage-knowledge-base/choose-language-option.png)

5. Escriba la configuración básica del proyecto:

    |Configuración| Valor|
    |-------|------|
    |**Nombre** | Escriba el nombre del proyecto único aquí ("proyecto" y "knowledge base" hacen referencia a lo mismo en la respuesta a preguntas). |
    |**Descripción** | Escriba una descripción para el proyecto. |
    |**Idioma de origen** | Tanto si este valor está atenuado como si no, depende de la selección realizada cuando se creó el primer proyecto asociado al recurso de idioma.  |
    |**Respuesta predeterminada** | La respuesta predeterminada que enviará el sistema si no se encontró ninguna respuesta a la pregunta. Puede cambiar esto en cualquier momento en la configuración del proyecto.

## <a name="manage-projects"></a>Administrar proyectos

En la página de respuesta de preguntas principal de Language Studio puede:

- Crear proyectos
- Eliminar proyectos
- Exportar los proyectos existentes para copia de seguridad o para migrar a otros recursos de idioma
- Importe proyectos o knowledge bases (el formato de archivo esperado es un archivo `.zip` que contiene un proyecto o una knowledge base que se exportó en formato `excel` o `.tsv`).
- Los proyectos se pueden ordenar por fecha de **última modificación** o **última publicación**.

## <a name="manage-sources"></a>Administración de orígenes

1. Seleccione **Administración de orígenes** en la barra de navegación de la izquierda.

1.  Hay tres tipos de orígenes: **direcciones URL**, **archivos** y **charlas**

       |Objetivo|Acción|
       |--|--|
       |Agregar origen|Puede agregar nuevos orígenes y contenido de preguntas más frecuentes al proyecto seleccionando **Agregar origen** > y eligiendo **direcciones URL**, **archivos** o **charlas**|
       |Eliminación del origen|Puede eliminar los orígenes existentes seleccionando a la izquierda del origen, lo que hará que aparezca un círculo azul con una marca de verificación > seleccione el icono de papelera. |
       |Marcado del contenido como no estructurado|Si quiere marcar el contenido del archivo cargado como no estructurado, active **Unstructured content** (Contenido no estructurado) en la lista desplegable al agregar el origen.|
       |Detección automática| Permita preguntas y respuestas para intentar determinar si el contenido está estructurado o no lo está.|

## <a name="manage-large-projects"></a>Administración de proyectos grandes

En la **página Editar knowledge base** puede:

* **Buscar proyecto**: puede buscar el proyecto escribiendo en el cuadro de texto situado en la parte superior del panel de respuesta a preguntas. Presione Entrar para buscar en el contenido de la pregunta, la respuesta o los metadatos.

* **Paginación**: muévase rápidamente a través de los orígenes de datos para administrar proyectos de gran tamaño. Los números de página aparecen en la parte inferior de la interfaz de usuario y a veces están fuera de la pantalla.

## <a name="delete-project"></a>Eliminar proyecto

La eliminación de un proyecto es una operación permanente. No se puede deshacer. Antes de eliminar un proyecto, debe exportarlo desde la página de respuesta a preguntas principal dentro de Language Studio.

Si comparte el proyecto con colaboradores y, posteriormente, lo elimina, todo el mundo perderá el acceso al mismo.

## <a name="next-steps"></a>Pasos siguientes

* [Configure resources](./configure-resources.md)
