---
author: v-dalc
ms.service: databox
ms.topic: include
ms.date: 08/17/2021
ms.author: alkohli
ms.openlocfilehash: 708ac746156582291cf071e730701013f1e005b0
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397966"
---
Para crear un recurso de administración para un dispositivo solicitado a través de Azure Edge Hardware Center, siga estos pasos:

1. Use sus credenciales de Microsoft Azure para iniciar sesión en Azure Portal, en esta dirección URL: [https://portal.azure.com](https://portal.azure.com).

1. Hay dos maneras de empezar a crear un nuevo recurso de administración:

    - A través de Azure Edge Hardware Center: busque y seleccione **Azure Edge Hardware Center**. En Hardware Center, muestre **All order items** (Todos los artículos del pedido). Haga clic en el elemento **Nombre**. En el elemento **Información general**, seleccione **Configurar hardware**.
    
       La opción **Configurar hardware** aparece después de enviar un dispositivo. 

       ![Ilustración en la que se muestran cuatro pasos para iniciar la creación de recursos de administración desde un elemento del pedido en Azure Edge Hardware Center.](media/azure-edge-hardware-center-create-management-resource/create-management-resource-01-a.png#lightbox) 
    
    - En Azure Stack Edge: busque y seleccione **Azure Stack Edge**. Seleccione **+ Create** (+ Crear). A continuación, seleccione **Crear un recurso de administración**.
    
       ![Ilustración en la que se muestran tres pasos para iniciar la creación de recursos de administración en Azure Stack Edge.](media/azure-edge-hardware-center-create-management-resource/create-management-resource-01-b.png#lightbox) 

    Se abre el asistente **Crear un recurso de administración**.

1. En la pestaña **Aspectos básicos**, especifique las opciones siguientes:

    |Configuración                                  |Value                                                                                       |
    |-----------------------------------------|--------------------------------------------------------------------------------------------|
    |**Seleccionar una suscripción**<sup>1</sup>    |Seleccione la suscripción que se usará para el recurso de administración.                                 |
    |**Grupo de recursos**<sup>1</sup>           |Seleccione el grupo de recursos que se usará para el recurso de administración. |
    |**Nombre**                                 |Proporcione un nombre para el recurso de administración.                                                 |
    |**Implementar recurso de Azure en**             |Seleccione el país o la región donde residirán los metadatos del recurso de administración. Los metadatos se pueden almacenar en una ubicación diferente a la del dispositivo físico. |
    |**Tipo de dispositivo**<sup>2</sup>              |Seleccione el tipo de dispositivo. Esta opción corresponde a la configuración seleccionada para el producto de hardware en un pedido de Azure Edge Hardware Center.<br>Por ejemplo, para un dispositivo Azure Stack Edge Pro - GPU, el tipo de dispositivo es **Azure Stack Edge Pro - 1 GPU** o **Azure Stack Edge Pro - 1 GPU**.|       

    <sup>1</sup> Una organización puede usar suscripciones y grupos de recursos diferentes para solicitar los dispositivos que usan para administrarlos.

    <sup>2</sup> Si utiliza **Configurar hardware** desde el artículo del pedido, el tipo de dispositivo se toma del artículo del pedido y los **DETALLES DEL DISPOSITIVO** no se muestran. 

    ![Captura de pantalla de la pestaña Aspectos básicos de Crear un recurso de administración. La pestaña Aspectos básicos, las opciones y el botón Revisar y crear están resaltados.](media/azure-edge-hardware-center-create-management-resource/create-management-resource-02.png)

    Seleccione **Revisar y crear** para continuar.

5. En la pestaña **Revisar y crear**, revise la configuración básica del recurso de administración y los términos de uso. Seleccione **Crear**.

    - Si inició este procedimiento haciendo clic en **Configurar hardware** para un artículo entregado de un pedido de Azure Edge Hardware Center, el dispositivo, el nombre del recurso de pedido y el estado del pedido se muestran en la parte superior de la pantalla. 

      ![Captura de pantalla de la pestaña Revisar y crear cuando se crea un recurso de administración de Azure Stack Edge para un artículo del pedido en Azure Edge Hardware Center. La información del pedido del dispositivo está resaltada.](media/azure-edge-hardware-center-create-management-resource/create-management-resource-03.png)

    - Si empezó en Azure Stack Edge, en lugar de la información del pedido del dispositivo, verá el tipo de dispositivo enumerado en **Aspectos básicos**. 

      ![Captura de pantalla de la pestaña Revisar y crear cuando se inicia un recurso de administración de Azure Stack Edge en Azure Stack Edge. El tipo de dispositivo aparece resaltado en Aspectos básicos.](media/azure-edge-hardware-center-create-management-resource/create-management-resource-04.png)  

    El botón **Crear** no está disponible hasta que se han superado todas las comprobaciones de validación.

6. Cuando se completa el proceso, se abre el panel Información general del nuevo recurso.

    ![Captura de pantalla que muestra un recurso de administración completado en Azure Stack Edge.](media/azure-edge-hardware-center-create-management-resource/create-management-resource-05\.png) 
