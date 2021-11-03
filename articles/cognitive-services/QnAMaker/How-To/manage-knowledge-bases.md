---
title: Administración de bases de conocimiento (QnA Maker)
description: QnA Maker permite administrar las bases de conocimiento al proporcionar acceso a la configuración y el contenido de la base de conocimiento.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1b3ee4c693193457e3369a12936ee46d90bf225d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131038829"
---
# <a name="create-knowledge-base-and-manage-settings"></a>Creación de base de conocimiento y administración de la configuración

QnA Maker permite administrar las bases de conocimiento al proporcionar acceso a la configuración y los orígenes de datos de la base de conocimiento.

[!INCLUDE [Custom question answering](../includes/new-version.md)]

## <a name="prerequisites"></a>Prerrequisitos

> * Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de empezar.
> * Un [recurso de QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) creado en Azure Portal. Recuerde el identificador de Azure Active Directory, la suscripción y el nombre de recurso de QnA que seleccionó al crear el recurso.

## <a name="create-a-knowledge-base"></a>Creación de una base de conocimientos

1. Inicie sesión en el portal de [QnAMaker.ai](https://QnAMaker.ai) con las credenciales de Azure.

1. En el portal de QnA Maker, seleccione **Create a knowledge base** (Crear una base de conocimiento).

1. En la página **Crear**, omita el **paso 1** si ya tiene el recurso de QnA Maker.

    Si aún no ha creado el recurso, seleccione **Estable** y **Create a QnA service** (Crear un servicio de preguntas y respuestas). Se le dirigirá a [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) para configurar un servicio QnA Maker en la suscripción. Recuerde el identificador de Azure Active Directory, la suscripción y el nombre de recurso de QnA que seleccionó al crear el recurso.

    Cuando haya terminado de crear el recurso en Azure Portal, vuelva al portal de QnA Maker, actualice la página del explorador y continúe con el **paso 2**.

1. En el **paso 3**, seleccione Active Directory, la suscripción, el servicio (recurso) y el idioma de todas las bases de conocimiento creadas en el servicio.

   ![Captura de pantalla de la selección de una base de conocimiento del servicio QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. En el **paso 3**, asigne el nombre `My Sample QnA KB` a su base de conocimiento.

1. En el **paso 4**, establezca la configuración con los siguientes valores:
    
    |Configuración|Value|
    |--|--|
    |**Enable multi-turn extraction from URLs, .pdf or .docx files** (Habilitar extracción en varios turnos de direcciones URL, archivos .pdf o .docx).|Activado|
    |**Texto de respuesta predeterminado**| `Quickstart - default answer not found.`|
    |**+ Agregar dirección URL**|`https://azure.microsoft.com/en-us/support/faq/`|
    |**Charla**|Seleccione **Professional**|  


1. En el **paso 5**, seleccione **Create your KB** (Crear base de conocimiento).

    El proceso de extracción tarda unos minutos en leer el documento e identificar las preguntas y respuestas.

    Cuando QnA Maker crea correctamente la base de conocimiento, se abre la página **Knowledge base** (Base de conocimiento). En esta página puede modificar el contenido de la base de conocimiento.

## <a name="edit-knowledge-base"></a>Edición de la base de conocimiento

1.  Seleccione **Mis bases de conocimiento** en la barra de navegación superior.

       Puede observar que todos los servicios creados o compartidos con usted aparecen ordenados de forma descendente en función de la fecha de la **última modificación**.

       ![Mis bases de conocimiento](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Seleccione una base de conocimiento determinada para editarla.

1.  Seleccione **Configuración**. La lista siguiente contiene los campos que puede cambiar.

       |Objetivo|Acción|
       |--|--|
       |Agregar dirección URL|Puede agregar nuevas direcciones URL para agregar nuevo contenido de preguntas frecuentes a la base de conocimiento si hace clic en **Manage knowledge base -> + Add URL** (Administrar base de conocimiento > Agregar dirección URL).|
       |Eliminar dirección URL|Puede eliminar las direcciones URL existentes si selecciona el icono de eliminación (la papelera).|
       |Actualizar contenido|Si quiere que la base de conocimiento rastree el contenido más reciente de las direcciones URL existentes, active la casilla **Refresh** (Actualizar). Con esta acción se actualiza la base de conocimiento una vez con el contenido de la dirección URL más reciente. Esta acción no establece una programación periódica de actualizaciones.|
       |Agregar archivo|Puede agregar un documento de archivo admitido como parte de una base de conocimiento; para ello, seleccione **Manage knowledge base** (Administrar base de conocimiento) y, luego, seleccione **+ Add File** (+ Agregar archivo).|
    |Importar|También puede importar cualquier base de conocimiento existente si selecciona el botón **Import Knowledge base** (Importar base de conocimiento). |
    |Actualizar|La actualización de la base de conocimiento depende del **plan de tarifa de administración** que se va a usar al crear el servicio de QnA Maker asociado con su base de conocimiento. También puede actualizar el nivel de administración de Azure Portal, si es necesario.

    <br/>
  1. Una vez realizados los cambios en la base de conocimiento, haga clic en **Save and train** (Guardar y entrenar) en la esquina superior derecha de la página para almacenar los cambios.

       ![Guardar y entrenar](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >Si abandona la página antes de seleccionar **Save and train** (Guardar y entrenar), todos los cambios se perderán.

## <a name="manage-large-knowledge-bases"></a>Administración de bases de conocimiento grandes

* **Grupos de origen de datos**: Los pares de QnA se agrupan según el origen de datos del que se han extraído. Puede expandir o contraer el origen de datos.

    ![Uso de la barra de origen de datos de QnA Maker para contraer y expandir preguntas y respuestas acerca del origen de datos](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Buscar en Knowledge Base**: Puede buscar en la base de conocimiento si escribe en el cuadro de texto en la parte superior de la tabla de Knowledge Base. Haga clic en Entrar para buscar en el contenido de la pregunta, la respuesta o los metadatos. Haga clic en el icono X para quitar el filtro de búsqueda.

    ![Use el cuadro de búsqueda de QnA Maker situado encima de las preguntas y respuestas para reducir la vista a solo elementos que coincidan con el filtro](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Paginación**: Muévase rápidamente a través de los orígenes de datos para administrar bases de conocimiento de gran tamaño.

    ![Uso de las características de paginación de QnA Maker situadas encima de las preguntas y respuestas para desplazarse por las páginas de preguntas y respuestas](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Eliminación de bases de conocimiento

La eliminación de una base de conocimiento (KB) es una operación permanente. No se puede deshacer. Antes de eliminar una base de conocimiento, debe exportarla desde la página **Configuración** del portal de QnA Maker.

Si comparte su base de conocimiento con colaboradores,](collaborate-knowledge-base.md), elimínela y todos perderán el acceso a la KB.
