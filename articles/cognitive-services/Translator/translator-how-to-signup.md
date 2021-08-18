---
title: Creación de un recurso de Traductor
titleSuffix: Azure Cognitive Services
description: En este artículo se muestra cómo crear un recurso Translator de Azure Cognitive Services y obtener una clave de suscripción y una dirección URL de punto de conexión.
services: cognitive-services
author: laujan
ms.author: lajanuar
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: c21d78e9731bc1711e62d26d6cca269868ac1714
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744717"
---
# <a name="create-a-translator-resource"></a>Creación de un recurso de Traductor

En este artículo, aprenderá a crear un recurso Translator en Azure Portal. [Azure Translator](translator-overview.md) es un servicio de traducción automática basado en la nube que forma parte de la familia de API REST de [Azure Cognitive Services](../what-are-cognitive-services.md). Los recursos de Azure son instancias de los servicios que crea. Todas las solicitudes de API a los servicios de Azure requieren una dirección URL de **punto de conexión** y una **clave de suscripción** de solo lectura para autenticar el acceso.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesitará una [**cuenta de Azure**](https://azure.microsoft.com/free/cognitive-services/) activa.  En caso de no tener ninguna, puede crear una [**suscripción gratuita de 12 meses**](https://azure.microsoft.com/free/).

## <a name="translator-resource-types"></a>Tipos de recurso de Translator

Se puede acceder al servicio Translator a través de dos tipos de recursos diferentes:

* Los tipos de recurso de **servicio único** permiten el acceso a una clave de API y un punto de conexión de un servicio único.  

* Los tipos de recurso de **varios servicios** permiten el acceso a varias instancias de Cognitive Services mediante una única clave de API y un punto de conexión. El recurso de Cognitive Services está disponible actualmente para los siguientes servicios:
  * Lenguaje ([Translator](../translator/translator-overview.md), [Language Understanding (LUIS)](../luis/what-is-luis.md), [Text Analytics](../text-analytics/overview.md))  
  * Visión ([Computer Vision](../computer-vision/overview.md)), ([Face](../face/overview.md))  
  * Decisiones ([Content Moderator](../content-moderator/overview.md))  

## <a name="create-your-resource"></a>Creación del recurso

* Vaya directamente a la página [**Create Translator**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) (Crear instancia de Translator) en Azure Portal para completar los detalles del proyecto.

* Vaya directamente a la página [**Create Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) (Crear instancia de Create Cognitive Services) en Azure Portal para completar los detalles del proyecto.

>[!TIP]
>Si lo prefiere, puede empezar en la página principal de Azure Portal para comenzar el proceso de **creación** como se indica a continuación:
>
> 1. Vaya a la página principal de [**Azure Portal**](https://ms.portal.azure.com/#home).
> 1. Seleccione ➕**Crear un recurso** en el menú de servicios de Azure.
>1. En el cuadro de búsqueda **Buscar en el Marketplace**, escriba y seleccione **Translator** (recurso de un único servicio) o **Cognitive Services** (recurso de varios servicios).  *Consulte* la sección anterior [Elección del tipo de recurso](#create-your-resource).
> 1. Seleccione **Crear** y se le dirigirá a la página de detalles del proyecto.
><br/><br/>

## <a name="complete-your-project-and-instance-details"></a>Completar los detalles del proyecto y de la instancia

1. **Suscripción**. Seleccione una de las suscripciones de Azure disponibles.

1. **Grupo de recursos**. El grupo de recursos de Azure que elija actúa como un contenedor virtual para el nuevo recurso. Puede crear un nuevo grupo de recursos o agregar el recurso a un grupo ya existente que comparta el mismo ciclo de vida, permisos y directivas.

1. **Región del recurso**. Elija **Global** a menos que su empresa o aplicación requiera una región específica. Translator es un servicio no regional ya que no hay ninguna dependencia de una región específica de Azure. *Consulte* [Regiones y Availability Zones en Azure](../../availability-zones/az-overview.md).

1. **Nombre**. Escriba el nombre que ha elegido para el recurso. El nombre que elija debe ser único en Azure.

> [!NOTE]
> Si usa una característica de Translator que requiere un punto de conexión de dominio personalizado, el valor que especifique en el campo Nombre será el parámetro del nombre de dominio personalizado del punto de conexión.

5. **Plan de tarifa**. Seleccione un [plan de tarifa](https://azure.microsoft.com/pricing/details/cognitive-services/translator) que satisfaga sus necesidades:

   * Cada suscripción tiene un plan gratuito.
   * El plan gratuito tiene las mismas características y funcionalidades que los planes de pago y no expira.
   * Solo se permite una suscripción gratuita por cuenta.</li></ul>

1. Si ha creado un recurso de varios servicios, tendrá que confirmar los detalles de uso adicionales mediante las casillas.

1. Seleccione **Revisar + crear**.

1. Revise los términos de servicio y seleccione **Crear** para implementar el recurso.

1. Una vez que el recurso se haya implementado correctamente, seleccione **Ir al recurso**.

### <a name="authentication-keys-and-endpoint-url"></a>Claves de autenticación y dirección URL del punto de conexión

Todas las solicitudes de API de Cognitive Services requieren una dirección URL de punto de conexión y una clave de solo lectura para la autenticación.

* **Claves de autenticación**. La clave es una cadena única que se pasa en cada solicitud al servicio Translator. Puede pasar la clave mediante un parámetro de cadena de consulta o mediante su especificación en el encabezado de solicitud HTTP.

* **Dirección URL del punto de conexión**. Use el punto de conexión Global en la solicitud de la API a menos que necesite una región de Azure específica. *Consulte* [Direcciones URL base](reference/v3-0-reference.md#base-urls). La dirección URL del punto de conexión Global es `api.cognitive.microsofttranslator.com`.

## <a name="get-your-authentication-keys-and-endpoint"></a>Obtención de las claves y el punto de conexión de autenticación

1. Una vez implementado el nuevo recurso, seleccione **Ir al recurso** o vaya directamente a la página de recursos.
1. En el raíl izquierdo, en *Administración de recursos*, seleccione **Claves y punto de conexión**.
1. Copie y pegue las claves de suscripción y la dirección URL del punto de conexión en una ubicación adecuada, como el *Bloc de notas de Microsoft*.

:::image type="content" source="../media/cognitive-services-apis-create-account/get-cog-serv-keys.png" alt-text="Obtención de una clave y un punto de conexión.":::

## <a name="how-to-delete-a--resource-or-resource-group"></a>Eliminación de un recurso o un grupo de recursos

> [!Warning]
> Al eliminar un grupo de recursos se eliminan también todos los recursos contenidos en el mismo.

Para eliminar un recurso de Cognitive Services o Translator, puede **eliminar el recurso** o **eliminar el grupo de recursos**.

Para eliminar el recurso:

1. Vaya a su grupo de recursos en Azure Portal.
1. Seleccione los recursos que se van a eliminar seleccionando la casilla adyacente.
1. Seleccione **Eliminar** en el menú superior cerca del borde derecho.
1. Escriba *sí* en el cuadro de diálogo **Recursos eliminados**.
1. Seleccione **Eliminar**.

Para eliminar el grupo de recursos:

1. Vaya a su grupo de recursos en Azure Portal.
1. Seleccione **Eliminar grupo de recursos** en la barra de menús superior situada junto al borde izquierdo.
1. Para confirmar la solicitud de eliminación, escriba el nombre del grupo de recursos y seleccione **Eliminar**.

## <a name="how-to-get-started-with-translator"></a>Introducción a Translator

En nuestro inicio rápido, aprenderá a usar el servicio Translator con las API REST.

> [!div class="nextstepaction"]
> [Introducción a Translator](quickstart-translator.md)

## <a name="more-resources"></a>Más recursos

* [Ejemplos de código de Microsoft Translator](https://github.com/MicrosoftTranslator).  Hay ejemplos de código de Translator disponibles en varios lenguajes de programación en GitHub.
* [Foro de soporte técnico de Microsoft Translator](https://www.aka.ms/TranslatorForum)
* [Introducción a Azure (vídeo de 3 minutos)](https://azure.microsoft.com/get-started/?b=16.24)