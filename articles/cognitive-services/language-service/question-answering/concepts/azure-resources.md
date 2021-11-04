---
title: 'Recursos de Azure: respuestas a preguntas'
description: Las respuestas a preguntas usan varios orígenes de Azure, cada uno con un propósito diferente. Entender cómo se usan individualmente le permite planear y seleccionar el plan de tarifa correcto o saber cuándo debe cambiar el plan de tarifa. Entender cómo se usan en combinación le permite encontrar y corregir los problemas cuando se producen.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 10/10/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 0ed037e44ff202ec1fd0cacfbbbcfb16ee9be273
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093341"
---
# <a name="azure-resources-for-question-answering"></a>Recursos de Azure para respuestas a preguntas

Las respuestas a preguntas usan varios orígenes de Azure, cada uno con un propósito diferente. Entender cómo se usan individualmente le permite planear y seleccionar el plan de tarifa correcto o saber cuándo debe cambiar el plan de tarifa. Entender cómo se usan los recursos _en combinación_ le permite encontrar y corregir los problemas cuando se producen.

## <a name="resource-planning"></a>Planeamiento de recursos

Cuando se desarrolla por primera vez una base de conocimiento, en la fase de prototipo, es habitual tener un único recurso para pruebas y producción.

> [!TIP]
> "Knowledge Base" y "proyecto" son términos equivalentes en la respuesta a preguntas y se pueden usar indistintamente.

Al pasar a la fase de desarrollo del proyecto, debe tener en cuenta lo siguiente:

* ¿Cuántos idiomas tendrá su sistema de knowledge base?
* ¿En cuántas regiones necesita que la knowledge base esté disponible?
* ¿Cuántos documentos de cada dominio contendrá el sistema?

## <a name="pricing-tier-considerations"></a>Consideraciones sobre planes de tarifa

Normalmente hay tres parámetros que necesita tener en cuenta:

* **El rendimiento que necesita**:
    * La respuesta a preguntas es una característica gratuita, y presenta un rendimiento limitado actualmente a 10 transacciones por segundo para las API de administración y de predicción.
    * Esto debería influir también en la selección de la SKU de Azure **Cognitive Search**; consulte más detalles [aquí](../../../../search/search-sku-tier.md). Además, es posible que necesite ajustar la [capacidad](../../../../search/search-capacity-planning.md) de Cognitive Search con réplicas.

* **Tamaño y número de bases de conocimiento**: Elija la [SKU de Azure Search](https://azure.microsoft.com/pricing/details/search/) adecuada para su escenario. Normalmente, puede decidir el número de bases de conocimiento que necesita en función del número de dominios de sujeto distintos. Si el dominio de sujeto (para un único idioma) debe estar en una base de conocimiento.

    En la respuesta a preguntas personalizada existe la posibilidad de establecer el recurso de idioma en un solo idioma o en varios. Puede realizar esta selección al crear el primer proyecto en [Language Studio](https://language.azure.com/).

    > [!IMPORTANT]
    > Puede publicar N-1 knowledge bases de un solo idioma en un nivel particular o N/2 knowledge bases en distintos idiomas, donde N se corresponde con los índices máximos permitidos en el nivel. Compruebe también el tamaño y el número máximos de documentos permitidos por cada nivel.

    Por ejemplo, si el nivel tiene 15 índices permitidos, puede publicar 14 knowledge bases del mismo idioma (un índice por knowledge base publicada). El decimoquinto índice se utiliza para crear y probar todas las bases de conocimiento. Si opta por tener knowledge bases en distintos idiomas, solo podrá publicar siete knowledge bases.

* **Número de documentos como orígenes**: la respuesta a preguntas es una característica gratuita, sin límites en cuanto al número de documentos que se pueden agregar como orígenes. 

En la tabla siguiente se proporcionan algunas directrices de alto nivel.

|                            |Azure Cognitive Search | Limitaciones                      |
| -------------------------- |------------ | -------------------------------- |
| **Experimentación**        |Nivel Gratis    | Publicar hasta 2 KB; tamaño de 50 MB  |
| **Entorno de desarrollo/pruebas**   |Básico        | Publicaciones de hasta 14 KB; tamaño de 2 GB    |
| **Entorno de producción** |Estándar     | Publicar hasta 49 KB; tamaño de 25 GB |

## <a name="recommended-settings"></a>Configuración recomendada

La respuesta a preguntas personalizada es una característica gratuita, y presenta un rendimiento limitado actualmente a 10 transacciones por segundo para las API de administración y de predicción. Con el fin de obtener un rendimiento de diez transacciones por segundo con su servicio, se recomienda la SKU S1 (1 instancia) de Azure Cognitive Search.

## <a name="keys-in-question-answering"></a>Claves en la respuesta a preguntas

La respuesta a preguntas personalizada se ocupa de dos tipos de claves: **claves de creación** y **claves de Azure Cognitive Search** usadas para acceder al servicio en la suscripción del cliente.

Use estas claves al realizar solicitudes al servicio mediante las API.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla de la versión preliminar administrada de administración de claves](../../../qnamaker/media/qnamaker-how-to-key-management/custom-question-answering-key-management.png)

|Nombre|Location|Propósito|
|--|--|--|
|Clave de autorización o suscripción|[Azure Portal](https://azure.microsoft.com/free/cognitive-services/)|Estas claves se usan para acceder a las API de servicio de lenguaje. Estas API permiten editar las preguntas y respuestas de una base de conocimiento y publicar una base de conocimiento. Estas claves se crean al crear un recurso.<br><br>Busque estas claves en el recurso **Cognitive Services** de la página **Claves y punto de conexión**.|
|Clave de administración de Azure Cognitive Search|[Azure Portal](../../../../search/search-security-api-keys.md)|Estas claves se utilizan para comunicarse con el servicio Azure Cognitive Search implementado en la suscripción de Azure del usuario. Cuando se asocia una instancia de Azure Cognitive Search con la característica de respuesta a preguntas personalizada, la clave de administración se pasa automáticamente a la respuesta a preguntas. <br><br>Puede encontrar estas claves en el recurso **Azure Cognitive Search** de la página **Claves**.|

### <a name="find-authoring-keys-in-the-azure-portal"></a>Búsqueda de claves de creación en Azure Portal

Puede ver y restablecer las claves de creación desde Azure Portal, donde agregó la característica de respuesta a preguntas personalizada (versión preliminar) en el recurso de Text Analytics.

1. Vaya al recurso de Text Analytics en Azure Portal y seleccione el que tiene el tipo *Cognitive Services*:

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla de la lista de recursos de respuesta a preguntas](../../../qnamaker/media/qnamaker-how-to-setup-service/resources-created-question-answering.png)

2. Vaya a **Keys and Endpoint** (Claves y punto de conexión):

    > [!div class="mx-imgBorder"]
    > ![Captura de pantalla de la clave de suscripción](../../../qnamaker/media/qnamaker-how-to-key-management/custom-qna-keys-and-endpoint.png)

### <a name="management-service-region"></a>Región del servicio de administración

En la característica de respuesta a preguntas personalizada, los servicios de administración y predicción están ubicados en la misma región.

## <a name="resource-purposes"></a>Propósitos de los recursos

Cada recurso de Azure creado con la característica de respuesta a preguntas personalizada tiene una finalidad concreta:

* Recurso de idioma (también denominado recurso de Text Analytics, en función del contexto en el que se evalúe el recurso).
* Recurso de Cognitive Search

### <a name="language-resource"></a>Recurso de idioma

El recurso de idioma con la característica de respuesta a preguntas personalizada proporciona acceso a las API de creación y publicación, hospeda el entorno de ejecución de clasificación y ofrece información de telemetría.

### <a name="azure-cognitive-search-resource"></a>Recurso de Azure Cognitive Search

El recurso de [Cognitive Search](../../../../search/index.yml) se usa para:

* Almacenar pares de preguntas y respuestas
* Proporcionar la clasificación inicial (n.º 1 del clasificador) de los pares de preguntas y respuestas en el runtime

#### <a name="index-usage"></a>Uso de índices

Puede publicar N-1 knowledge bases de un solo idioma o N/2 knowledge bases de distintos idiomas en un nivel particular, donde N se corresponde con el número máximo de índices permitidos en el nivel de Azure Cognitive Search. Compruebe también el tamaño y el número máximos de documentos permitidos por cada nivel.

Por ejemplo, si el nivel tiene 15 índices permitidos, puede publicar 14 knowledge bases del mismo idioma (un índice por knowledge base publicada). El decimoquinto índice se utiliza para crear y probar todas las bases de conocimiento. Si opta por tener knowledge bases en distintos idiomas, solo podrá publicar siete knowledge bases.

#### <a name="language-usage"></a>Uso de idiomas

Con la respuesta a preguntas personalizada, tiene la opción de configurar el servicio para las bases de conocimiento en uno o varios idiomas. Puede elegir esta opción durante la creación de la primera knowledge base en el recurso de idioma.

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre [knowledge bases](../How-To/manage-knowledge-base.md) de respuesta a preguntas
