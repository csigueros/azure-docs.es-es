---
title: 'Ubicaciones de datos para Azure Virtual Desktop (clásico): Azure'
description: Una breve introducción a las ubicaciones en las que se almacenan los datos y metadatos de Azure Virtual Desktop (clásico).
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9ddfc6cd8a18e9bdbee071d0c5fd64a0c14b2e95
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111754482"
---
# <a name="data-locations-for-azure-virtual-desktop-classic"></a>Ubicaciones de datos para Azure Virtual Desktop (clásico)

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop (clásico), que no admite objetos de Azure Resource Manager. Si está intentando administrar objetos de Azure Virtual Desktop para Azure Resource Manager, consulte [este artículo](../data-locations.md).

Azure Virtual Desktop está disponible actualmente para todas las ubicaciones geográficas. Inicialmente, los metadatos del servicio solo se pueden almacenar en el área geográfica de Estados Unidos (EE. UU.). Los administradores pueden elegir la ubicación para almacenar los datos de usuario cuando creen las máquinas virtuales del grupo de hosts y los servicios asociados, como los servidores de archivos. Obtenga más información sobre las ubicaciones geográficas de Azure en el [mapa de centros de datos de Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft no controla ni limita las regiones en las que usted o sus usuarios pueden acceder a los datos específicos de la aplicación o del usuario.

>[!IMPORTANT]
>Azure Virtual Desktop almacena información de metadatos globales como los nombres de inquilinos, nombres de grupos de hosts, nombres de grupos de aplicaciones y nombres principales de usuario en un centro de datos ubicado en Estados Unidos. Los metadatos almacenados se cifran en reposo y los reflejos con redundancia geográfica se mantienen en Estados Unidos. Todos los datos del cliente, como la configuración de la aplicación y los datos de usuario, residen en la ubicación que el cliente elige y no los administra el servicio.

Los metadatos del servicio se replican en Estados Unidos con fines de recuperación ante desastres.