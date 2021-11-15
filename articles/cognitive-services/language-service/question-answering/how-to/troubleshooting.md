---
title: 'Solución de problemas: respuesta a preguntas'
description: La lista de las preguntas frecuentes seleccionadas sobre la respuesta a preguntas le ayudará a adoptar este servicio de forma más rápida y a obtener mejores resultados.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.openlocfilehash: dfafd5185c62d3011dfef66eb9d244ebe8a9b549
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478093"
---
# <a name="troubleshooting-for-question-answering"></a>Solución de problemas para respuesta a preguntas

La lista de las preguntas frecuentes seleccionadas sobre la respuesta a preguntas le ayudará a adoptar este servicio de forma más rápida y a obtener mejores resultados.

## <a name="manage-predictions"></a>Administración de predicciones

<details>
<summary><b>¿Cómo puedo mejorar el rendimiento de las predicciones de consulta?</b></summary>

**Respuesta**: Los problemas de rendimiento indican que es necesario realizar un escalado vertical de Cognitive Search. Considere la posibilidad de agregar una réplica a su instancia de Cognitive Search para mejorar el rendimiento.

Obtenga más información sobre los [planes de tarifa](../Concepts/azure-resources.md).
</details>

## <a name="manage-your-project"></a>Administración del proyecto

<details>
<summary><b>¿Por qué mis direcciones URL o archivos no extraen pares de preguntas y respuestas?</b></summary>

**Respuesta**: Es posible que la respuesta a preguntas no pueda extraer automáticamente algún contenido de preguntas y respuestas (QnA) de las direcciones URL de P+F válidas. En tales casos, puede pegar el contenido de QnA en un archivo .txt y ver si la herramienta puede ingerirlo. Como alternativa, puede redactar contenido y agregarlo al proyecto o knowledge base a través del [portal de Language Studio](https://language.azure.com).

</details>

<details>
<summary><b>¿Cuál es el tamaño máximo para crear una base de conocimiento?</b></summary>

**Respuesta**: El tamaño de la knowledge base depende de la SKU de Azure Search que elija al crear el servicio QnA Maker. Obtenga más detalles [aquí](../concepts/azure-resources.md).

</details>

<details>
<summary><b>¿Cómo se puede compartir una base de conocimiento con otros usuarios?</b></summary>

**Respuesta**: El uso compartido funciona en el nivel del recurso de lenguaje, es decir, se pueden compartir todas las knowledge bases asociadas a un recurso de idioma.
</details>

<details>
<summary><b>¿Puede compartir una knowledge base con un colaborador que no esté en el mismo inquilino de Azure Active Directory para modificarla?</b></summary>

**Respuesta**: El uso compartido se basa en el control de acceso basado en rol (RBAC) de Azure. Si puede compartir _cualquier_ recurso en Azure con otro usuario, también puede compartir la respuesta a preguntas.

</details>

<details>
<summary><b>¿Puede asignar derechos de lectura o escritura a cinco usuarios distintos para que cada uno de ellos pueda acceder solo a una knowledge base de respuesta a preguntas?</b></summary>

**Respuesta**: Puede compartir un recurso de idioma completo, no knowledge bases individuales.

</details>

<details>
<summary><b>Las actualizaciones que realizo en mi knowledge base no se reflejan en producción. ¿Cuál es el motivo?</b></summary>

**Respuesta**: Cada edición, ya sea para actualizar la tabla, hacer pruebas o cambiar la configuración, debe guardarse para poder implementarla. Asegúrese de seleccionar **Guardar** después de realizar cambios y, a continuación, vuelva a implementar el proyecto para que esos cambios se reflejen en producción.

</details>

<details>
<summary><b>¿La base de conocimiento admite datos enriquecidos o multimedia?</b></summary>

**Respuesta**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Extracción automática multimedia para archivos y direcciones URL

* Direcciones URL: capacidad limitada de conversión de HTML a Markdown.
* Archivos: no compatibles.

#### <a name="answer-text-in-markdown"></a>Texto de respuesta en formato Markdown

Una vez que los pares de QnA estén en knowledge base, puede editar el texto de Markdown de la respuesta para incluir vínculos a los elementos multimedia disponibles desde direcciones URL públicas.

</details>

<details>
<summary><b>¿La respuesta a preguntas admite idiomas distintos del inglés?</b></summary>

**Respuesta**: Obtenga más detalles acerca de los [idiomas admitidos](../language-support.md).

Si tiene contenido en varios idiomas, asegúrese de crear un proyecto independiente para cada idioma.

</details>

## <a name="manage-service"></a>Administración de servicios

<details>
<summary><b>Eliminé mi servicio Search existente. ¿Cómo lo puedo corregir?</b></summary>

**Respuesta**: Si elimina un índice de Azure Cognitive Search, la operación es definitiva y no es posible recuperar el índice.

</details>

<details>
<summary><b>Eliminé mi índice `testkbv2` en el servicio Search. ¿Cómo lo puedo corregir?</b></summary>

**Respuesta:** En caso de que haya eliminado el índice `testkbv2` en el servicio Search, puede restaurar los datos desde la última KB publicada. Use la herramienta de recuperación [RestoreTestKBIndex](https://github.com/pchoudhari/QnAMakerBackupRestore/tree/master/RestoreTestKBFromProd) disponible en GitHub.

</details>

<details>
<summary><b>¿Se puede usar el mismo recurso de Azure Cognitive Search para bases de conocimiento que emplean varios idiomas?</b></summary>

**Respuesta**: Para usar varios idiomas y varias knowledge bases, el usuario tiene que crear un proyecto para cada idioma y en el primer proyecto creado para el recurso de idioma debe activarse la opción **I want to select the language when I create a project in this resource** (Deseo seleccionar el idioma al crear un proyecto en este recurso). De esta manera, se creará un servicio de Azure Search independiente por idioma.

</details>

## <a name="integrate-with-other-services-including-bots"></a>Integración con otros servicios, como bots

<details>
<summary><b>¿Es necesario utilizar Bot Framework para usar la respuesta a preguntas?</b></summary>

**Respuesta**: No, no es necesario usar [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) con la respuesta a preguntas. Sin embargo, la respuesta a preguntas se ofrece como una de las diversas plantillas de [Azure Bot Service](/azure/bot-service/). Bot Service permite el desarrollo rápido de bots inteligentes mediante Microsoft Bot Framework y se ejecuta en un entorno sin servidor.

</details>

<details>
<summary><b>¿Cómo puedo crear un nuevo bot con la respuesta a preguntas?</b></summary>

**Respuesta**: Siga las instrucciones de [esta](../tutorials/bot-service.md) documentación para crear su Bot con Azure Bot Service.

</details>

<details>
<summary><b>¿Cómo se usa una base de conocimiento diferente con un servicio de bot existente de Azure?</b></summary>

**Respuesta**: Debe tener la siguiente información sobre la base de conocimiento:

* Id. de base de conocimiento.
* Nombre de subdominio personalizado del punto de conexión publicado de la base de conocimiento, conocido como `host`, que se encuentra en la página **Configuración** después de la publicación.
* Clave del punto de conexión publicado de la base de conocimiento; se encuentra en **Settings** (Configuración) después de publicarlo.

Con esta información, vaya al servicio de aplicaciones del bot en Azure Portal. En **Configuración -> Configuración -> Configuración de la aplicación**, cambie esos valores.

La clave del punto de conexión de la base de conocimiento se llama `QnAAuthkey` en el servicio ABS.

</details>

<details>
<summary><b>¿Pueden dos o más aplicaciones cliente compartir una base de conocimiento?</b></summary>

**Respuesta**: Sí, la base de conocimiento se puede consultar desde cualquier número de clientes.

</details>

<details>
<summary><b>¿Cómo puedo insertar la respuesta a preguntas en mi sitio web?</b></summary>

**Respuesta**: Siga estos pasos para insertar el servicio de respuesta a preguntas como control de chat en web en su sitio web:

1. Cree su bot de P+F siguiendo las instrucciones que encontrará [aquí](../tutorials/bot-service.md).
2. Habilite el chat en web mediante los pasos que se indican [aquí](../tutorials/bot-service.md#integrate-the-bot-with-channels).

## <a name="data-storage"></a>Almacenamiento de datos

<details>
<summary><b>¿Qué datos se almacenan y dónde?</b></summary>

**Respuesta**:

Al crear el recurso de idioma para respuesta a preguntas, seleccionó una región de Azure. Sus bases de conocimiento y los archivos de registro se almacenan en esta región.

</details>
