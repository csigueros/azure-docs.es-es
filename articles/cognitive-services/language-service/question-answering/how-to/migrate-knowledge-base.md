---
title: 'Migración de proyectos y bases de conocimiento: respuesta personalizada a preguntas'
description: La migración de un proyecto de respuesta personalizada a preguntas requiere exportar un proyecto desde un recurso y, a continuación, importarlo en otro.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 46c092fa096c6f68299f6e0ac8254bfd25c7ce75
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093276"
---
# <a name="migrate-projects-and-question-answer-sources"></a>Migración de proyectos y orígenes de preguntas y respuestas

Existen distintos motivos por los que puede que quiera crear una copia de su proyecto:

* Para implementar un proceso de copia de seguridad y restauración
* Integración con una canalización de CI/CD
* Cuando se quieren mover los datos a otras regiones

## <a name="prerequisites"></a>Requisitos previos

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de empezar.
* Un [recurso de idioma](https://aka.ms/create-language-resource) con la característica de respuesta personalizada a preguntas habilitada en Azure Portal. Recuerde el identificador de Azure Active Directory, la suscripción y el nombre del recurso de idioma que seleccionó al crear el recurso.

## <a name="export-a-project"></a>Exportación de un proyecto

La exportación de un proyecto permite migrar todos los orígenes de preguntas y respuestas incluidos en un único proyecto o realizar una copia de seguridad de estos.

1. Inicie sesión en [Language Studio](https://language.azure.com/).
1. Seleccione el recurso de idioma desde el que quiere migrar un proyecto.
1. En la página **Proyectos**, tiene las opciones para exportar en dos formatos, Excel o TSV. Esto determinará el contenido del archivo. El archivo en sí se exportará como archivo .zip que contiene todas las bases de conocimiento.

## <a name="import-a-project"></a>Importación de un proyecto  

1. Seleccione el recurso de idioma, que será el destino del proyecto exportado anteriormente.
1. En la página **Proyectos**, seleccione **Importar** y elija el formato que se usó al seleccionar la exportación. A continuación, vaya al archivo .zip local que contiene el proyecto exportado. Escriba un nombre para el proyecto recién importado y seleccione **Listo**.

## <a name="export-question-and-answers"></a>Exportación de preguntas y respuestas

1. Seleccione el recurso de idioma desde el que quiere migrar un origen de preguntas y respuestas individual.
1. Seleccione el proyecto que contiene el origen de preguntas y respuestas que quiere exportar.
1. En la página de edición de la base de conocimiento, seleccione el icono de puntos suspensivos (`...`) situado a la derecha de **Enable rich text** (Habilitar texto enriquecido) en la barra de herramientas. Tiene la opción de exportar en Excel o TSV.

## <a name="import-question-and-answers"></a>Importación de preguntas y respuestas

1. Seleccione el recurso de idioma, que será el destino del origen de preguntas y respuestas exportado anteriormente.
1. Seleccione el proyecto en el que quiere importar un origen de preguntas y respuestas.
1. En la página de edición de la base de conocimiento, seleccione el icono de puntos suspensivos (`...`) situado a la derecha de **Enable rich text** (Habilitar texto enriquecido) en la barra de herramientas. Tiene la opción de importar un archivo Excel o TSV.
1. Vaya a la ubicación local del archivo con la opción **Elegir archivo** y seleccione **Listo**.

<!-- TODO: Replace Link-->
### <a name="test"></a>Prueba

**Pruebe** el origen de preguntas y respuestas; para ello, seleccione la opción **Test** (Probar) en la barra de herramientas de la página **Edit knowledge base** (Editar la base de conocimiento), lo que iniciará el panel de pruebas. Vea cómo [probar la base de conocimiento](../../../qnamaker/How-To/test-knowledge-base.md).

### <a name="deploy"></a>Implementar

<!-- TODO: Replace Link-->
**Implemente** la base de conocimiento y cree un bot de chat. Aprenda cómo [implementar la base de conocimiento](../../../qnamaker/Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="chat-logs"></a>Registros de chat

No hay ninguna forma de migrar registros de chat con proyectos o bases de conocimiento. Si los registros de diagnóstico están habilitados, los registros de chat se almacenan en el recurso de Azure Monitor asociado.

## <a name="next-steps"></a>Pasos siguientes

<!-- TODO: Replace Link-->
> [!div class="nextstepaction"]
> [Editar una base de conocimiento](../../../qnamaker/How-To/edit-knowledge-base.md)
