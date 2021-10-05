---
title: Creación de un punto de conexión de Azure CDN | Microsoft Docs
description: En este artículo se muestra cómo crear un nuevo punto de conexión de Azure Content Delivery Network (CDN), incluida la configuración avanzada.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/09/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 18f4276c3013f50ceacf74f4c8cec068f0de88cb
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124775538"
---
# <a name="create-an-azure-cdn-endpoint"></a>Creación de un punto de conexión de Azure CDN
Este artículo describe toda la configuración para crear un punto de conexión de [Azure Content Delivery Network (CDN)](cdn-overview.md) en un perfil de CDN existente. Una vez haya creado un perfil y un punto de conexión, podrá iniciar la entrega de contenido a sus clientes. Para una guía de inicio rápido sobre cómo crear un perfil y un punto de conexión, consulte [Inicio rápido: Creación de un perfil y un punto de conexión de Azure CDN](cdn-create-new-endpoint.md).

## <a name="prerequisites"></a>Requisitos previos
Para poder crear un punto de conexión de CDN, debe haber creado al menos un perfil de CDN, que puede contener uno o varios puntos de conexión de CDN. Puede usar varios perfiles para organizar sus puntos de conexión de la red CDN por dominio de Internet, aplicación web o cualquier otro criterio. Dado que los precios de CDN se aplican en el nivel de perfil de CDN, debe crear varios perfiles de CDN si desea utilizar una combinación de los planes de tarifa de Azure CDN. Para crear un perfil de CDN, consulte [Creación de un nuevo perfil de CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal
Inicie sesión en [Azure Portal](https://portal.azure.com) con su cuenta de Azure.

## <a name="create-a-new-cdn-endpoint"></a>Crear un nuevo extremo de CDN

1. En [Azure Portal](https://portal.azure.com), vaya a su perfil de red CDN. Puede haberlo anclado al panel en el paso anterior. Si no es así, puede encontrarlo seleccionando **Todos los servicios** y, después, seleccionando **Perfiles de CDN**. En el panel **Perfiles de CDN**, seleccione el perfil al que planea agregar el punto de conexión. 
   
    Aparece el panel del perfil de CDN.

2. Seleccione el **punto de conexión**.
   
    ![Seleccionar punto de conexión de CDN](./media/cdn-create-endpoint-how-to/cdn-select-endpoint.png)
   
    Aparecerá la página **Agregar un punto de conexión**.
   
    ![Página Agregar punto de conexión](./media/cdn-create-endpoint-how-to/cdn-add-endpoint-page.png)

3. En **Nombre**, escriba un nombre único para el nuevo punto de conexión de CDN. Este nombre se usa para obtener acceso a sus recursos almacenados en caché en el dominio _\<endpointname>_ .azureedge.net.

4. En **Tipo de origen**, elija uno de los siguientes tipos de origen: 
   - **Almacenamiento** para Azure Storage
   - **Servicio en la nube** para Azure Cloud Services
   - **Aplicación web** para Azure Web Apps
   - **Origen personalizado** para cualquier otro servidor web de origen accesible públicamente (hospedado en Azure o en otro lugar)

5. En **Nombre de host de origen**, seleccione o escriba el dominio del servidor de origen. En la lista desplegable se muestran todos los servidores de origen disponibles del tipo especificado en el paso 4. Si seleccionó **Origen personalizado** como su tipo de origen, escriba el dominio de su origen personalizado.
    
6. En **Ruta de acceso de origen**, escriba la ruta de acceso a los recursos que desea almacenar en memoria caché. Para permitir el almacenamiento en caché de cualquier recurso en el dominio especificado en el paso 5, deje este valor en blanco.
    
7. En el **Encabezado del host de origen**, escriba el encabezado de host que quiera que Azure CDN envíe con cada solicitud o deje el valor predeterminado.
   
   > [!NOTE]
   > Algunos tipos de orígenes, como Azure Storage y Aplicaciones web, requieren que el encabezado del host coincida con el dominio del origen. A menos que tenga un origen que requiera un encabezado de host diferente de su dominio, debe dejar el valor predeterminado.
   > 
    
8. En **Protocolo** y **Puerto de origen**, especifique los protocolos y los puertos que se usan para acceder a sus recursos en el servidor de origen. Se debe seleccionar al menos un protocolo (HTTP o HTTPS). Use el dominio proporcionado por CDN ( _\<endpointname>_ .azureedge.net) para acceder al contenido HTTPS. 
   
   > [!NOTE]
   > El valor de **Puerto de origen** solo determina el puerto que utiliza el punto de conexión para recuperar información del servidor de origen. El propio punto de conexión solo está disponible para los clientes finales en los puertos HTTP y HTTPS predeterminados (80 y 443), con independencia de cuál sea el **puerto de origen**.  
   > 
   > Los puntos de origen de los perfiles de **Azure CDN de Akamai** no permiten el intervalo completo de puertos TCP para los puertos de origen. Para obtener una lista de los puertos de origen que no se permiten, consulte [Azure CDN from Akamai Allowed Origin Ports](/previous-versions/azure/mt757337(v=azure.100))(Puertos de origen permitidos de la red CDN de Azure de Akamai).  
   > 
   > La compatibilidad de HTTPS con los dominios personalizados de Azure CDN no se admite en los productos de **Azure CDN de Akamai**. Para más información, consulte [Configuración de HTTPS en un dominio personalizado de Azure CDN](cdn-custom-ssl.md).
    
9. En **Optimizado para**, seleccione el tipo de optimización que mejor coincida con el escenario y el tipo de contenido que desea que el punto de conexión entregue. Para más información, consulte [Optimización de la red CDN de Azure para el tipo de entrega de contenido](cdn-optimization-overview.md).

    Se admiten las siguientes opciones de tipo de optimización, según el tipo de perfil:
    - Perfiles **Azure CDN Estándar de Microsoft**:
       - [**Entrega web general**](cdn-optimization-overview.md#general-web-delivery)

    - Perfiles de **Azure CDN Estándar de Verizon** y **Azure CDN Premium de Verizon**:
       - [**Entrega web general**](cdn-optimization-overview.md#general-web-delivery)
       - [**Aceleración de sitios dinámicos**](cdn-optimization-overview.md#dynamic-site-acceleration)

    - Perfiles de **Azure CDN Estándar de Akamai**:
       - [**Entrega web general**](cdn-optimization-overview.md#general-web-delivery)
       - [**Streaming multimedia general**](cdn-optimization-overview.md#general-media-streaming)
       - [**Streaming multimedia de video bajo demanda**](cdn-optimization-overview.md#video-on-demand-media-streaming)
       - [**Descarga de archivos de gran tamaño**](cdn-optimization-overview.md#large-file-download)
       - [**Aceleración de sitios dinámicos**](cdn-optimization-overview.md#dynamic-site-acceleration)

10. Seleccione **Agregar** para crear el nuevo punto de conexión.
   
    Una vez creado el punto de conexión, aparece en la lista de puntos de conexión del perfil.
    
    ![Punto de conexión de CDN](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
    Dado que se tarda un tiempo en que el registro se propague, el punto de conexión no estará disponible para su uso de forma inmediata: 
    - En los perfiles de **Azure CDN Estándar de Microsoft**, la propagación se completa normalmente en 10 minutos. 
    - En los perfiles de **Azure CDN estándar**, la propagación normalmente se completa en un minuto. 
    - En los perfiles **Azure CDN estándar de Verizon** y **Azure CDN prémium de Verizon**, la propagación se completa normalmente en 30 minutos. 
   
    Si intenta usar el nombre de dominio de la red CDN antes de que la configuración del punto de conexión se haya propagado a los servidores de punto de presencia (POP), puede recibir un estado de respuesta HTTP 404. Si han pasado varias horas desde que creó el punto de conexión y aún recibe un estado de respuesta 404, consulte [Solución de problemas de puntos de conexión de Azure CDN que devuelven un código de estado 404](cdn-troubleshoot-endpoint.md).

> [!NOTE]
> En el caso de los *puntos de conexión de CDN de Verizon*, cuando un punto de conexión **está deshabilitado** o **detenido** por cualquier motivo, se limpiarán todos los recursos configurados a través del portal complementario de Verizon. Estas configuraciones no se pueden restaurar automáticamente reiniciando el punto de conexión. Tendrá que volver a realizar esos cambios de configuración.

## <a name="clean-up-resources"></a>Limpieza de recursos
Para eliminar un punto de conexión cuando ya no sea necesario, selecciónelo y, a continuación, seleccione **Eliminar**. 

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de los dominios personalizados, continúe con el tutorial para agregar un dominio personalizado al punto de conexión de CDN.

> [!div class="nextstepaction"]
> [Agregar un dominio personalizado](cdn-map-content-to-custom-domain.md)


