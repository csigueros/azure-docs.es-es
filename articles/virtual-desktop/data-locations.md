---
title: 'Ubicaciones de datos para Azure Virtual Desktop: Azure'
description: Una breve introducción a las ubicaciones en las que se almacenan los datos y metadatos de Azure Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 06/30/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: bc356e828adbe9b2d93a202f6b58673dd4ec5efc
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113125913"
---
# <a name="data-locations-for-azure-virtual-desktop"></a>Ubicaciones de datos para Azure Virtual Desktop

Azure Virtual Desktop está disponible actualmente para todas las ubicaciones geográficas. Los administradores pueden elegir la ubicación para almacenar los datos de usuario cuando creen las máquinas virtuales del grupo de hosts y los servicios asociados, como los servidores de archivos. Obtenga más información sobre las ubicaciones geográficas de Azure en el [mapa de centros de datos de Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft no controla ni limita las regiones en las que usted o sus usuarios pueden acceder a los datos específicos de la aplicación o del usuario.

>[!IMPORTANT]
>Azure Virtual Desktop almacena varios tipos de información, como nombres de grupos de hosts, nombres de grupos de aplicaciones, nombres de áreas de trabajo y nombres principales de usuario de un centro de datos. Al crear cualquiera de los objetos de servicio, el cliente tiene que especificar la ubicación en la que se debe crear el objeto. La ubicación de este objeto determina el lugar en que se almacenará la información del objeto. El cliente elegirá una región de Azure y la información relacionada se almacenará en la geografía asociada. Visite [https://azure.microsoft.com/global-infrastructure/geographies/](https://azure.microsoft.com/global-infrastructure/geographies/) si desea una lista de todas las regiones de Azure y las geografías relacionadas.

En este artículo se describe qué información almacena el servicio Azure Virtual Desktop. Para más información sobre las definiciones de datos del cliente, consulte [Cómo categoriza Microsoft los datos para servicios en línea](https://www.microsoft.com/trust-center/privacy/customer-data-definitions).

## <a name="customer-input"></a>Entrada del cliente

Para configurar el servicio Azure Virtual Desktop, el cliente debe crear grupos de hosts y otros objetos de servicio. Durante la configuración, el cliente debe proporcionar información como el nombre del grupo de hosts, el nombre del grupo de aplicaciones, y mucho más. Esta información se considera como la entrada del cliente. La entrada del cliente se almacena en la geografía asociada a la región en la que se crea el objeto. Las rutas de acceso a los objetos de Azure Resource Manager se consideran información de la organización, por lo que la residencia de datos no se aplica a ellos. Los datos sobre las rutas de acceso de Azure Resource Manager se almacenarán fuera de la geografía elegida.

## <a name="customer-data"></a>Datos del cliente

El servicio no almacena directamente ninguna información relacionada con los usuarios o las aplicaciones, sino que almacena datos de clientes como nombres de aplicaciones y nombres principales de usuario, porque forman parte del proceso de configuración del objeto. Esta información se almacena en la geografía asociada a la región en la que el cliente creó el objeto.

## <a name="diagnostic-data"></a>Datos de diagnóstico

Azure Virtual Desktop recopila datos de diagnóstico generados por el servicio cada vez que el cliente o usuario interactúa con el servicio. Estos datos solo se usan para solucionar problemas, brindar soporte y comprobar el estado del servicio de forma agregada. Por ejemplo, desde el lado del host de sesión, cuando una máquina virtual se registra en el servicio, generamos información que incluye el nombre de la máquina virtual (VM), el grupo de hosts al que pertenece la máquina virtual, y así sucesivamente. Esta información se almacena en la geografía asociada a la región en la que se crea el grupo de hosts. Además, cuando un usuario se conecta al servicio e inicia un escritorio remoto, generamos información de diagnóstico que incluye el nombre principal de usuario, la ubicación del cliente, la dirección IP del cliente, el grupo de hosts al que se conecta el usuario, y mucho más. Esta información se envía a dos ubicaciones diferentes:

- La ubicación más cercana al usuario donde se encuentra la infraestructura de servicio (seguimientos de cliente, seguimientos de usuario, datos de diagnóstico).
- La ubicación donde se encuentra el grupo de hosts.

## <a name="service-generated-data"></a>Datos generados por el servicio

Para que Azure Virtual Desktop siga siendo confiable y escalable, agregamos patrones de tráfico y uso para comprobar el estado y el rendimiento del plano de control de infraestructura. Por ejemplo, para comprender cómo aumentar la capacidad de la infraestructura regional a medida que aumenta el uso del servicio, procesamos los datos del registro de uso del servicio. Luego, se revisan los registros de las horas punta y se decide qué centros de datos se van a agregar para satisfacer esta capacidad. Agregamos esta información desde todas las ubicaciones en las que se encuentra la infraestructura de servicio y, a continuación, la enviamos a la región de EE. UU. Los datos enviados a la región de EE. UU. incluyen datos limpios, pero no datos de clientes.

Por el momento, se admite el almacenamiento de los datos mencionados anteriormente en estas ubicaciones:

- Estados Unidos (EE. UU.) (disponible con carácter general)
- Europa (UE) (disponible con carácter general)
- Reino Unido (RU) (disponible con carácter general)
- Canadá (CA) (disponible con carácter general)

A medida que el servicio crezca, se agregarán más geografías. Los metadatos almacenados se cifran en reposo y los reflejos con redundancia geográfica se mantienen en la geografía. Los datos del cliente, como la configuración de la aplicación y los datos de usuario, residen en la ubicación que el cliente elige y no los administra el servicio.

Los datos descritos se replican dentro de la geografía de Azure con fines de recuperación ante desastres.
