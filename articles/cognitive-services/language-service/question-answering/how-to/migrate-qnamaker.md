---
title: Migración de bases de conocimiento de QnA Maker a respuesta personalizada a preguntas
description: Migre las bases de conocimiento de QnA Maker heredadas a respuesta personalizada a preguntas para aprovechar las características más recientes.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.author: diagarw
author: DishaAgarwal
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 2ffd43a2747d462257b4223285b12b5f81e3e4e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093317"
---
# <a name="migrate-from-qna-maker-to-custom-question-answering"></a>Migración de QnA Maker a respuesta personalizada a preguntas

Respuesta personalizada a preguntas apareció en mayo de 2021 con varias características nuevas, como la relevancia mejorada mediante un clasificador de aprendizaje profundo, las respuestas precisas y la compatibilidad con regiones de un extremo a otro. Cada proyecto de respuesta personalizada a preguntas es equivalente a una base de conocimiento en QnA Maker. Puede migrar fácilmente las bases de conocimiento de un recurso de QnA Maker a proyectos de respuesta personalizada a preguntas dentro de un [recurso de idioma](https://aka.ms/create-language-resource). También puede optar por migrar bases de conocimiento de varios recursos de QnA Maker a un recurso de idioma específico.

Para migrar correctamente las bases de conocimiento, **la cuenta que realiza la migración necesita acceso de colaborador al recurso de QnA Maker y de idioma seleccionados**. Cuando se realiza la migración de una base de conocimiento, se copian los detalles siguientes en el nuevo proyecto de respuesta personalizada a preguntas:

- Pares de pregunta y respuesta, incluidas sugerencias de aprendizaje activo.
- Sinónimos y respuesta predeterminada del recurso de QnA Maker.
- El nombre de la base de conocimiento se copia en el campo de descripción del proyecto.

Los valores de nivel de recurso, como el control de acceso basado en rol (RBAC), no se migra al recurso nuevo. Estos valores de nivel de recurso tendrían que volver a configurarse para el recurso de idioma después de la migración. También tendrá que [volver a habilitar el análisis](analytics.md) para el recurso de idioma.

## <a name="steps-to-migrate"></a>Pasos para la migración

Puede seguir los pasos siguientes para migrar las bases de conocimiento:

1. Cree un [recurso de idioma](https://aka.ms/create-language-resource) con la característica de respuesta personalizada a preguntas habilitada. Al crear el recurso de idioma en Azure Portal, verá la opción para habilitar respuesta personalizada a preguntas. Al seleccionar esa opción y continuar, se le pedirán detalles de Azure Search para guardar las bases de conocimiento.

2. Si quiere agregar bases de conocimiento en varios idiomas al recurso de idioma, visite [Language Studio](https://lanuage.azure.com) para crear el primer proyecto de respuesta personalizada a preguntas y seleccione la primera opción, como se muestra a continuación. La configuración de idioma del recurso de idioma solo se puede especificar al crear un proyecto. Si quiere migrar las bases de conocimiento existentes en un único idioma al recurso de idioma, puede omitir este paso.

   > [!div class="mx-imgBorder"]
   > ![Captura de la pantalla de la interfaz de usuario de elección del idioma"](../media/migrate-qnamaker/choose-language.png)

3. Visite [https://www.qnamaker.ai](https://www.qnamaker.ai) y seleccione **Iniciar migración** en la nota de migración de la página de la base de conocimiento. Se abrirá un cuadro de diálogo para iniciar la migración.

   :::image type="content" source="../media/migrate-qnamaker/start-migration.png" alt-text="Botón Iniciar migración que aparece en un banner en qnamaker.ai" lightbox="../media/migrate-qnamaker/start-migration.png":::

4. Rellene los detalles necesarios para iniciar la migración. El inquilino se seleccionará de forma automática. Puede elegir cambiar el inquilino.

   > [!div class="mx-imgBorder"]
   > ![Migración de QnA Maker con el cuadro de selección rojo alrededor de la opción de selección de inquilino](../media/migrate-qnamaker/tenant-selection.png)

5. Seleccione el recurso de QnA Maker que contiene las bases de conocimiento que se van a migrar.

   > [!div class="mx-imgBorder"]
   > ![Migración de QnA Maker con el cuadro de selección rojo alrededor de la opción de selección del recurso de QnA Maker](../media/migrate-qnamaker/select-resource.png)

6. Seleccione el recurso de idioma al que quiera migrar las bases de conocimiento. Solo podrá ver los recursos de idioma que tienen habilitada la respuesta personalizada a preguntas. La configuración de idioma para el recurso de idioma se muestra en las opciones. No podrá migrar bases de conocimiento en varios idiomas desde recursos de QnA Maker a un recurso de idioma si no se especifica su configuración de idioma.

   > [!div class="mx-imgBorder"]
   > ![Migración de QnA Maker con el cuadro de selección rojo alrededor de la opción de recurso de idioma seleccionada que actualmente contiene la información de que el idioma no está especificado](../media/migrate-qnamaker/language-setting.png)

    Si quiere migrar bases de conocimiento en varios idiomas al recurso de idioma, debe habilitar la configuración de varios idiomas al crear el primer proyecto de respuesta personalizada a preguntas para el recurso de idioma. Para ello, siga las instrucciones del paso 2. **Si no se especifica la configuración de idioma para el recurso de idioma, se le asigna el idioma del recurso de QnA Maker seleccionado**.

7. Seleccione todas las bases de conocimiento que quiera migrar > y después **Siguiente**.

   > [!div class="mx-imgBorder"]
   > ![Migración de QnA Maker con el cuadro de selección rojo alrededor de la opción de selección de base de conocimiento con una lista desplegable en la que se muestran los nombres de tres bases de conocimiento](../media/migrate-qnamaker/select-knowledge-bases.png)

8. Puede revisar las bases de conocimiento que planea migrar. Puede haber algunos errores de validación en los nombres de proyecto, ya que para los proyectos de respuesta personalizada a preguntas se siguen reglas de validación más estrictas. 

    > [!CAUTION]
    > Si migra una base de conocimiento con el mismo nombre que un proyecto que ya existe en el recurso de idioma de destino, **el contenido del proyecto se reemplazará** por el de la base de conocimiento seleccionada.

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla de un mensaje de error en el que se indica que los nombres de proyecto no puede contener caracteres especiales](../media/migrate-qnamaker/special-characters.png)

9. Después de resolver los errores de validación, seleccione **Siguiente**.

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla con los caracteres especiales quitados](../media/migrate-qnamaker/validation-errors.png)

10. La migración tardará unos minutos. No cancele la migración mientras está en curso. Puede navegar a los proyectos migrados dentro de [Language Studio](https://lanuage.azure.com) después de la migración.

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla de las bases de conocimiento migradas correctamente con información que puede publicar mediante Language Studio](../media/migrate-qnamaker/migration-success.png)

    Si alguna base de conocimiento no se puede migrar a proyectos de respuesta personalizada a preguntas, se mostrará un error. Los errores de migración más comunes se producen cuando:
    
    - Los recursos de origen y destino no son válidos.
    - Se intenta migrar una base de conocimiento (KB) vacía.
    - Se ha alcanzado el límite de una instancia de Azure Search vinculada a los recursos de destino.

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla de una migración con errores con un error de ejemplo](../media/migrate-qnamaker/migration-errors.png)

    Una vez que resuelva estos errores, puede volver a ejecutar la migración.

11. La migración solo copiará las instancias de prueba de las bases de conocimiento. Una vez que se haya completado la migración, tendrá que implementar manualmente las bases de conocimiento para copiar el índice de prueba en el índice de producción.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo volver a habilitar el análisis con los [registros de diagnóstico de Azure Monitor](analytics.md).
