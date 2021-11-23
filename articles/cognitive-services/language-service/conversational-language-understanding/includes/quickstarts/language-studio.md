---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8fb9e276c7f8d0e195c7d7618392e7c5e61c7c9e
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132590531"
---
## <a name="prerequisites"></a>Requisitos previos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services).

## <a name="sign-in-to-language-studio"></a>Inicio de sesión en Language Studio

1. Vaya a [Language Studio](https://aka.ms/languageStudio) e inicie sesión con su cuenta de Azure. 

2. En la ventana **Choose a language resource** (Elección de recurso de idioma) que aparece, busque la suscripción de Azure y elija el recurso de idioma. Si no tiene un recursos, puede crearlo.

    > [!NOTE]
    > Actualmente solo se pueden usar recursos con el plan de tarifa estándar (**S**) con el servicio de reconocimiento del lenguaje conversacional.
    
    :::image type="content" source="../../media/quickstart-language-resource.png" alt-text="Captura de pantalla que muestra la pantalla de selección de recursos en Language Studio." lightbox="../../media/quickstart-language-resource.png":::

## <a name="create-a-conversation-project"></a>Creación de un proyecto de conversación

Una vez que tenga un recurso de idioma asociado a su cuenta, cree un proyecto de reconocimiento del lenguaje conversacional. En este inicio rápido creará un proyecto que puede identificar comandos de correo electrónico, como leer correos electrónicos de determinadas personas, eliminar correos electrónicos y adjuntar un documento a un correo electrónico.

1. En [Language Studio](https://aka.ms/languageStudio), busque la sección con la etiqueta **Understand conversational language** (Reconocer el lenguaje conversacional) y haga clic en **Conversational language understanding** (Reconocimiento del lenguaje conversacional).  
    :::image type="content" source="../../media/select-custom-clu.png" alt-text="Captura de pantalla que muestra la ubicación de Conversational Language Understanding en la página de aterrizaje de Language Studio." lightbox="../../media/select-custom-clu.png"::: 
    

2. Así llegará a la página del **proyecto de conversaciones**. Haga clic en **Create new project** (Crear nuevo proyecto). Seleccione **Conversation project** (Proyecto de conversación) y, a continuación, haga clic en **Siguiente**.

    :::image type="content" source="../../media/projects-page.png" alt-text="Captura de pantalla que muestra la página del proyecto de conversación en Language Studio." lightbox="../../media/projects-page.png":::


A continuación, debe proporcionar los detalles siguientes:

|Valor  | Descripción  |
|---------|---------|
|Nombre     | Nombre del proyecto.        |
|Descripción    | Descripción opcional del proyecto.        |
|Idioma principal del texto     | Idioma principal del proyecto. Los datos de entrenamiento deben estar principalmente en este idioma. En esta guía de inicio rápido, elija **Inglés**.        |
|Habilitación de varios idiomas     |  Si desea permitir que el proyecto admita varios idiomas a la vez. Para este inicio rápido, habilite esta opción.       |

Cuando haya terminado, haga clic en **Siguiente**, revise los detalles y haga clic en **Crear proyecto** para completar el proceso. Ahora debería ver la pantalla **Compilación de esquema** en el proyecto.

## <a name="build-schema"></a>Compilación de esquema

1. Seleccione la pestaña Intenciones o Entidades en la página **Compilación de esquema** y haga clic en **Agregar**. Se le pedirá un nombre para completar la creación de la intención o entidad.

2. Cree tres intenciones con los nombres siguientes:
    - **Lectura**
    - **Eliminar**
    - **Adjuntar**

3. Cree tres entidades con los nombres siguientes:
    - **Sender**
    - **FileName**
    - **FileType**


Al hacer clic en la intención, se le llevará a la página de [etiquetado de expresiones](../../how-to/tag-utterances.md), donde puede agregar ejemplos de intenciones y etiquetarlas con entidades.


:::image type="content" source="../../media/quickstart-intents.png" alt-text="Captura de pantalla que muestra la página de esquema en Language Studio." lightbox="../../media/quickstart-intents.png":::

## <a name="tag-utterances"></a>Etiquetado de expresiones

En la página de etiquetado de expresiones, agregue algunos ejemplos a las intenciones. Seleccione la intención de **lectura** en el cuadro desplegable que indica **Select Intent** (Seleccionar intención).

En el cuadro de texto que pone **Write your example utterance and press enter** (Escriba la expresión de ejemplo y presione Entrar), escriba la frase "*Leer el correo electrónico de Carol*" y presione Entrar para agregarla como una expresión de ejemplo.

Arrastre el cursor sobre la palabra "*Carol*" y seleccione la entidad **Sender** para etiquetar "*Carol*" como entidad.

Agregue el resto de estas expresiones con las siguientes intenciones y entidades.

|Expresión|Intención|Entities|
|--|--|--|
|*Leer el correo electrónico de John para mí*|**Lectura**|"John": **Sender**|
|*¿Qué decía el correo electrónico de Matt?*|**Lectura**|"Matt": **Sender**|
|*Eliminar el último correo electrónico de Martha*|**Eliminar**|"Martha": **Sender**|
|*Eliminar esto*|**Eliminar**|_Ninguna entidad_|
|*Quitar esto*|**Eliminar**|_Ninguna entidad_|
|*Mover esto a la carpeta de eliminados*|**Eliminar**|_Ninguna entidad_|
|*Adjuntar el archivo de Excel denominado Reports q1*|**Adjuntar**|"excel": **FileType**, <br> "reports q1": **FileName**|
|*Adjuntar el archivo de PowerPoint*|**Adjuntar**|"PowerPoint": **FileType**|
|*Agregar el archivo PDF con el contrato firmado con nombre* |**Adjuntar**|"PDF": **FileType**, <br> "contrato firmado": **FileName**|


Cuando haya terminado, haga clic en **Guardar cambios** para guardar las expresiones y etiquetas en el proyecto. El icono situado junto al botón se volverá verde cuando se guarden los cambios. A continuación, vaya a la página de **entrenamiento del modelo**.

:::image type="content" source="../../media/quickstart-utterances.png" alt-text="Captura de pantalla que muestra la pantalla de etiquetado de intenciones en Language Studio." lightbox="../../media/quickstart-utterances.png":::

## <a name="train-your-model-and-view-its-details"></a>Entrenamiento del modelo y visualización de sus detalles

Haga clic en **Train model** (Entrenar modelo) a la izquierda de la pantalla. Para entrenar el modelo, debe proporcionarle un nombre. Escriba un nombre como "*v0.1*" y presione la tecla Entrar. 

Desactive **Run evaluation with training**(Ejecutar evaluación con entrenamiento) antes de hacer clic en **Train** (Entrenar). 

Debería ver la página **Ver detalles de los modelos**. Espere hasta que se complete el entrenamiento, que puede tardar unos 5 minutos. Cuando el entrenamiento se realice correctamente, seleccione **Deploy Model** (Implementar modelo) a la izquierda de la pantalla.

## <a name="deploy-your-model"></a>Implementación del modelo

En la página de **implementación del modelo** de la izquierda de la pantalla, seleccione el modelo entrenado y haga clic en el botón **Deploy model** (Implementar modelo). En la pantalla que aparece, haga clic en **Deploy** (Implementar).

## <a name="test-your-model"></a>Comprobación del modelo

Haga clic en **Test model** (Probar modelo) a la izquierda de la pantalla y seleccione el vínculo del modelo. Escriba la expresión "*enviar esto a la papelera de reciclaje*" y haga clic en **Run the test** (Ejecutar la prueba). 

Ahora verá la intención principal como **Eliminar** sin entidades.

Puede probar otras expresiones como las siguientes:
* "*adjuntar el archivo DOCX*", 
* "*leer el correo electrónico de Jason*", 
* "*adjuntar el archivo PPT denominado CLU demo*".

También puede probar expresiones en otros idiomas, como las frases siguientes:

* "*Joindre le fichier PDF*" (en francés: "*Adjuntar el archivo PDF*"), 
* "*lesen sie die e-mail von Macy*" (en alemán: "*Leer el correo electrónico de Macy*")
