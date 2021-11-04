---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: b71810497c555a33fcf8a7359c32c8397cb6cec8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091032"
---
Una vez configurados el recurso y el contenedor de almacenamiento, cree un proyecto de clasificación de texto. Un proyecto es un área de trabajo para crear modelos de inteligencia artificial personalizados basados en datos. A su proyecto solo puede acceder usted y otros usuarios que tengan acceso de colaborador al recurso de Azure que se usa.

1. Inicio de sesión en [Language Studio](https://aka.ms/languageStudio). Aparecerá una ventana que le permitirá seleccionar la suscripción y el recurso de idioma. Seleccione el recurso que creó en el paso anterior.

2. En la sección **Classify text** (Clasificar texto) de Language Studio, busque la **clasificación de texto personalizado** de los servicios disponibles y selecciónela.

3. Seleccione **Create new project** (Crear proyecto) en el menú superior de la página de proyectos. La creación de un proyecto le permitirá etiquetar datos y entrenar, evaluar, mejorar e implementar los modelos. 

    :::image type="content" source="../media/create-project.png" alt-text="Captura de pantalla de la página de creación del proyecto." lightbox="../media/create-project.png":::

4. Si ha seguido los pasos anteriores para crear un recurso, deberá agregar información sobre el proyecto, como un nombre, y seleccionar el contenedor de almacenamiento.

    1. Seleccione el tipo de proyecto. Para este inicio rápido, crearemos un proyecto de clasificación de varias etiquetas. A continuación, haga clic en **Siguiente**.

    2. Escriba la información del proyecto, como el nombre, una descripción y el idioma de los archivos del proyecto. El nombre del proyecto no se podrá cambiar posteriormente.

        >[!TIP]
        > El conjunto de datos no tiene que estar completamente en el mismo idioma. Puede tener varios archivos, y cada uno de ellos con distintos idiomas admitidos. Si el conjunto de datos contiene archivos de distintos idiomas o si espera idiomas diferentes durante el tiempo de ejecución, seleccione **Enable multi-lingual dataset** (Habilitar conjunto de datos multilingüe) al especificar la información básica del proyecto.

    3. Seleccione el contenedor en el que ha cargado los datos. En este inicio rápido, se usará el archivo de etiquetas existente disponible en el contenedor. A continuación, haga clic en **Siguiente**.
 
    4. Examine los datos especificados y seleccione **Create Project** (Crear proyecto).
