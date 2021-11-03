---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: aeb4c7ef44791b174940790fa3e03f0f40ed1ba4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093560"
---
Una vez configurados el recurso y el contenedor de almacenamiento, cree un nuevo proyecto de NER personalizado. Un proyecto es un área de trabajo para crear modelos de inteligencia artificial personalizados basados en sus datos. Al proyecto solo puede acceder usted y otros usuarios que tengan acceso de colaborador al recurso de Azure que se usa.

1. Inicie sesión en el [portal de Language Studio](https://aka.ms/languageStudio). Aparecerá una ventana que le permitirá seleccionar la suscripción y el recurso de idioma. Seleccione el recurso que creó en el paso anterior. 

2. Busque la sección **Entity extraction** (Extracción de entidades) y seleccione **Custom named entity recognition** (Reconocimiento de entidades con nombre personalizado) en los servicios disponibles.

3. Seleccione **Create new project** (Crear proyecto) en el menú superior de la página de proyectos. La creación de un proyecto le permitirá etiquetar datos y entrenar, evaluar, mejorar e implementar los modelos. 

    
    :::image type="content" source="../media/create-project.png" alt-text="Captura de pantalla de la página de creación del proyecto." lightbox="../media/create-project.png":::


4.  Después de hacer clic en **Create new project** (Crear proyecto), aparecerá una pantalla que le permitirá conectar la cuenta de almacenamiento. Si no encuentra la cuenta de almacenamiento, asegúrese de que ha creado un recurso mediante los pasos anteriores. 

    >[!NOTE]
    > * Solo debe realizar este paso una vez con cada nuevo recurso que use. 
    > * Este proceso es irreversible: si conecta una cuenta de almacenamiento al recurso, no podrá desconectarla más tarde.
    > * Solo puede conectar el recurso a una cuenta de almacenamiento.
    > * Si ya ha conectado una cuenta de almacenamiento, verá la pantalla **Seleccionar tipo de proyecto** en su lugar. Vea el paso siguiente.
    
    :::image type="content" source="../media/connect-storage.png" alt-text="Captura de pantalla que muestra la pantalla de conexión de la cuenta de almacenamiento." lightbox="../media/connect-storage.png":::

<!--If you're using a preexisting resource, see [creating Azure resources](../concepts/use-azure-resources.md). When you are done, select **Next**.--> 

5. Escriba la información del proyecto, como el nombre, una descripción y el idioma de los archivos del proyecto. No podrá cambiar el nombre del proyecto más adelante. 

6. Examine los datos especificados y seleccione **Create Project** (Crear proyecto).
