---
title: 'ML Studio (clásico): Complemento de Excel para servicios web: Azure'
description: Procedimiento para usar los servicios web de Machine Learning directamente en Excel sin escribir código.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: e915f6a713df68dba539d3e5aeb31501eea6ed8e
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688876"
---
# <a name="excel-add-in-for-machine-learning-studio-classic-web-services"></a>Complemento de Excel para servicios web de Machine Learning Studio (clásico)

**SE APLICA A:**  ![Se aplica a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clásico)   ![No se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

[!INCLUDE [ML Studio (classic) retirement](../../../includes/machine-learning-studio-classic-deprecation.md)]

Excel facilita la llamada a servicios web directamente sin necesidad de escribir ningún código.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Pasos para usar un servicio web existente en el libro

1. Abra el [archivo de Excel de ejemplo](https://aka.ms/amlexcel-sample-2)que contiene el complemento de Excel y los datos acerca de los pasajeros del Titanic. 
 
    > [!NOTE]
    > - Verá que la lista de los servicios web relacionados en el archivo y en la parte inferior una casilla para "Predicción automática". Si habilita la predicción automática, las predicciones en **todos** los servicios se actualizarán cada vez que haya un cambio en las entradas. Si la desactiva, tendrá que hacer clic en "Predecir todo" para actualizar. Para habilitar la predicción automática en un nivel de servicio, vaya al paso 6.
    > - El complemento de Excel para Machine Learning llamará a la tienda de complementos de Office para cargarse. Si su organización veta el acceso a la tienda de complementos de Office, verá un error al cargarlo. En este caso, implemente el complemento de Excel para Machine Learning desde el centro de administración de Microsoft 365. Después, invóquelo y agregue el servicio web manualmente pegando la dirección URL y la clave de API.

 

2. Elija el servicio web haciendo clic en él, "Predictor de supervivientes del Titanic (complemento de Excel de ejemplo) [puntuación]" en este ejemplo.
   
    ![Seleccionar un servicio web](./media/excel-add-in-for-web-services/image1.png)
3. Esto le lleva a la sección **Predicción**.  Este libro ya contiene datos de ejemplo, pero para un libro en blanco también puede seleccionar una celda en Excel y hacer clic en **Usar datos de ejemplo**.
4. Seleccione los datos con encabezados y haga clic en el icono del intervalo de datos de entrada.  Asegúrese de que está activada la casilla "Mis datos tienen encabezados".
5. En **Resultado**, escriba el número de la celda donde desea que se muestre el resultado, por ejemplo, "H1" aquí.
6. Haga clic en **Predicción**. Si selecciona la casilla "predicción automática", cualquier cambio en las áreas seleccionadas (las especificadas como entradas) desencadenará una solicitud y una actualización de las celdas de salida sin necesidad de presionar el botón de predicción.
   
    ![Sección Predicción](./media/excel-add-in-for-web-services/image1.png)

Implemente un servicio web o use uno existente. Para más información sobre la implementación de un servicio web, consulte el [Tutorial 3: Implementación del modelo de riesgo crediticio](tutorial-part3-credit-risk-deploy.md).

Obtenga la clave de API del servicio web. Realizará esta acción en un sitio u otro en función de si publicó un servicio web Machine Learning clásico o uno nuevo.

**Uso de un servicio web clásico** 

1. En Machine Learning Studio (clásico), haga clic en la sección **SERVICIOS WEB** en el panel izquierdo y, luego, seleccione el servicio web.
   
    ![Seleccionar un servicio web de Studio](./media/excel-add-in-for-web-services/image4.png)
2. Copie la clave de API del servicio web.
   
    ![Clave de API de Studio](./media/excel-add-in-for-web-services/image5.png)
3. En la pestaña **PANEL** del servicio web, haga clic en el vínculo **SOLICITUD-RESPUESTA**.
4. Busque la sección **URI de solicitud** .  Copie y guarde la URL.

> [!NOTE]
> Ahora se puede iniciar sesión en el portal [Servicios web Machine Learning](https://services.azureml.net) para obtener la clave de API de un servicio web Machine Learning clásico.
> 
> 

**Uso de un servicio web nuevo**

1. En el portal [Servicios web Machine Learning](https://services.azureml.net), haga clic en **Servicios web** y seleccione el servicio web. 
2. Haga clic en **Consume**(Consumo).
3. Busque la sección **Basic consumption info** (Información básica de consumo). Copie y guarde la **clave principal** y la URL de **solicitud-respuesta**.

## <a name="steps-to-add-a-new-web-service"></a>Pasos para agregar un nuevo servicio web

1. Implemente un servicio web o use uno existente. Para más información sobre la implementación de un servicio web, consulte el [Tutorial 3: Implementación del modelo de riesgo crediticio](tutorial-part3-credit-risk-deploy.md).
2. Haga clic en **Consume**(Consumo).
3. Busque la sección **Basic consumption info** (Información básica de consumo). Copie y guarde la **clave principal** y la URL de **solicitud-respuesta**.
4. En Excel, vaya a la sección **Servicios web** (si se encuentra en la sección **Predicción**, haga clic en la flecha Atrás para ir a la lista de servicios web).
   
    ![Ir a la selección de servicios web](./media/excel-add-in-for-web-services/image3.png)
5. Haga clic en **Agregar servicio web**.
6. Pegue la URL en el cuadro de texto de complemento de Excel denominado " **URL**".
7. Pegue la clave principal o de API en el cuadro de texto denominado **Clave de API**.
8. Haga clic en **Agregar**.
   
    ![Dirección URL y clave de API y de un servicio web clásico.](./media/excel-add-in-for-web-services/image6.png)
9. Para usar el servicio web, siga las instrucciones anteriores, "Pasos para usar un servicio web existente".

## <a name="sharing-your-workbook"></a>Compartir el libro
Si guarda el libro, también se guardarán la clave principal o de API de los servicios web que también ha guardado. Esto significa que solo debe compartir el libro con personas de confianza.

Plantee cualquier pregunta que le surja en la sección de comentarios a continuación o en nuestro [foro](/answers/topics/azure-machine-learning.html).