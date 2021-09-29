---
title: 'Inicio rápido: Creación de una instancia de Azure API Management'
description: Cree una nueva instancia de servicio de Azure API Management mediante Azure Portal.
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/08/2020
ms.author: danlep
ms.openlocfilehash: c2a822147b06cfdd950d12bd1fa2d5c1c583f22f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128660792"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-portal"></a>Inicio rápido: Creación de una nueva instancia de servicio de Azure API Management mediante Azure Portal

Azure API Management (APIM) ayuda a las organizaciones a publicar API para desarrolladores externos, asociados e internos a fin de desbloquear el potencial de sus datos y servicios. API Management proporciona las competencias esenciales para garantizar un programa de API de éxito mediante compromisos con desarrolladores, información detallada empresarial, análisis, seguridad y protección. APIM le permite crear y administrar modernas puertas de enlace de API para los servicios back-end existentes hospedados en cualquier lugar. Para más información, consulte la [introducción](api-management-key-concepts.md).

Esta guía de inicio rápido describe los pasos que deben seguirse para crear una nueva instancia de API Management mediante Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Instancia de API Management

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en [Azure Portal](https://portal.azure.com).

## <a name="create-a-new-service"></a>Creación de un nuevo servicio

1. En el menú de Azure Portal, seleccione **Crear un recurso**. También puede seleccionar **Crear un recurso** en la página **principal** de Azure. 
   
   Seleccione :::image type="content" source="media/get-started-create-service-instance/00-CreateResource-01.png" alt-text="Crear un recurso":::

   
1. En la página **Nuevo**, seleccione **Integración** > **API Management**.

   Nueva instancia de Azure API Management
   
1. En la página **servicio API Management**, escriba los valores.

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-create-new.png" alt-text="Nueva instancia":::
   
   | Configuración                 | Descripción   |                                                                     
   |-------------------------|-----------------------------------------------|
   | Nombre                | Un nombre único para el servicio API Management. Este nombre no se podrá modificar más adelante. El nombre del servicio se utiliza para hacer referencia al servicio y al recurso de Azure correspondiente. <br/> El nombre del servicio se usa para generar un nombre de dominio predeterminado: *\<name\>.azure-api.net.* Si desea utilizar un nombre de dominio personalizado, consulte [Configure a custom domain](configure-custom-domain.md) (Configuración de un dominio personalizado). |
   | **Suscripción**:          | La suscripción en que se creará esta nueva instancia de servicio.   |
   | **Grupos de recursos**      |  Seleccione un nuevo grupo de recursos o uno ya existente. Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. |
   | **Ubicación**          | Seleccione una región geográfica que esté cerca suyo de entre las ubicaciones del servicio API Management disponibles. | 
   | **Nombre de la organización**   | El nombre de su organización. Este nombre se usa en varios lugares, incluido el título del portal para desarrolladores y el remitente de correos electrónicos de notificación. |                                                         
   | **Correo electrónico del administrador** | La dirección de correo electrónico a la que se enviarán todas las notificaciones de **API Management**.   |  
   | **Plan de tarifa**        | Seleccione el nivel de **Desarrollador** para evaluar el servicio. Este nivel no puede utilizarse en producción. Para más información sobre el escalado de los niveles de API Management, consulte [Actualización y escalado](upgrade-and-scale.md). |

3. Seleccione **Crear**.

    > [!TIP]
    > Normalmente, se tarda entre 30 y 40 minutos en crear y activar una instancia del servicio API Management en este nivel. Si selecciona **Anclar al panel**, podrá encontrar el servicio que acaba de crear con más facilidad.

[!INCLUDE [api-management-navigate-to-instance](../../includes/api-management-navigate-to-instance.md)]

Revise las propiedades del servicio en la página de **información general**.

   Instancia de API Management

Cuando la instancia de servicio de API Management está en línea, está listo para usarla. Comience con el tutorial [Importación y publicación de la primera API](import-and-publish.md).

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, puede quitar el grupo de recursos y todos los recursos relacionados siguiendo los pasos a continuación:

1. En Azure Portal, busque y seleccione **Grupos de recursos**. También puede seleccionar **Grupos de recursos** en la página **principal**. 

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-01.png" alt-text="Navegación por grupos de recursos":::

1. En la página **Grupos de recursos**, seleccione el grupo de recursos.

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-02.png" alt-text="Seleccionar grupo de recursos":::

1. En la página del grupo de recursos, seleccione **Eliminar grupo de recursos**. 
   
1. Escriba el nombre del grupo de recursos y, después, seleccione **Eliminar**.

   Eliminación de un grupo de recursos

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Importación y publicación de la primera API](import-and-publish.md)
