---
title: Requisitos y consideraciones para el uso de la replicación entre regiones de volúmenes de Azure NetApp Files | Microsoft Docs
description: Describe los requisitos y las consideraciones para usar la funcionalidad de replicación entre regiones de volúmenes de Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/04/2021
ms.author: b-juche
ms.openlocfilehash: b94374d8eaa0efac50989a388686f96d9bae0622
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2021
ms.locfileid: "111537140"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>Requisitos y consideraciones del uso de la replicación entre regiones 

Tenga en cuenta los siguientes requisitos y consideraciones [sobre el uso de la funcionalidad de la replicación entre regiones de volúmenes](cross-region-replication-create-peering.md) de Azure NetApp Files:  

## <a name="requirements-and-considerations"></a>Requisitos y consideraciones 

* La característica de replicación entre regiones se encuentra actualmente en versión preliminar. Debe enviar una solicitud de lista de espera para acceder a la característica a través de la [página de envío de lista de espera de replicación entre regiones de Azure NetApp Files](https://aka.ms/anfcrrpreviewsignup). Antes de utilizar la característica de replicación entre regiones, debe esperar un correo electrónico de confirmación oficial del equipo de Azure NetApp Files.
* La replicación de Azure NetApp Files solo está disponible en determinados pares de regiones fijas. Vea [Pares de regiones admitidas](cross-region-replication-introduction.md#supported-region-pairs). 
* Los volúmenes SMB se admiten junto con los volúmenes NFS. La replicación de volúmenes SMB requiere una conexión de Active Directory en las cuentas de NetApp de origen y de destino. La conexión de AD de destino debe tener acceso a los servidores DNS o controladores de dominio de ADDS que sean accesibles desde la subred delegada en la región de destino. Para obtener más información, consulte [Requisitos para las conexiones de Active Directory](create-active-directory-connections.md#requirements-for-active-directory-connections). 
* La cuenta de destino debe estar en una región distinta de la región del volumen de origen. También puede seleccionar una cuenta de NetApp existente en otra región.  
* El volumen de destino de replicación es de solo lectura hasta que [conmute por error a la región de destino](cross-region-replication-manage-disaster-recovery.md#fail-over-to-destination-volume) para permitir la lectura y escritura del volumen de destino. 
* La replicación de Azure NetApp Files no admite actualmente varias suscripciones; todas las replicaciones deben realizarse en una sola suscripción.
* Puede configurar un máximo de cinco volúmenes para la replicación dentro de una sola suscripción por región. Puede abrir una incidencia de soporte técnico para solicitar un aumento de la cuota predeterminada de cinco volúmenes de destino de replicación (por suscripción en una región). 
* Pueden pasar hasta cinco minutos hasta que la interfaz refleje una instantánea recién agregada en el volumen de origen.  
* No se admiten las topologías en cascada y de distribución ramificada de entrada y salida.
* En este momento no se admite la configuración de la replicación de volúmenes para los volúmenes de origen creados a partir de una instantánea.
* Después de configurar la replicación entre regiones, el proceso de replicación crea *instantáneas de SnapMirror* para proporcionar referencias entre el volumen de origen y el volumen de destino. Las instantáneas de SnapMirror se recorren de forma cíclica automáticamente cuando se crea una nueva para cada transferencia incremental. No se pueden eliminar las instantáneas de SnapMirror hasta que se elimine la relación de replicación y el volumen. 
* Puede eliminar instantáneas manuales en el volumen de origen de una relación de replicación cuando la relación de replicación esté activa o interrumpida, y también después de que se elimine la relación de replicación. No se pueden eliminar instantáneas manuales del volumen de destino hasta que se interrumpa la relación de replicación.
* No se puede revertir un volumen de origen o destino de la replicación entre regiones a una instantánea. La funcionalidad de reversión de instantáneas está atenuada para los volúmenes en una relación de replicación. 

## <a name="next-steps"></a>Pasos siguientes
* [Creación de replicación de volumen](cross-region-replication-create-peering.md)
* [Visualización del estado de mantenimiento de la relación de la replicación](cross-region-replication-display-health-status.md)
* [Administración de la recuperación ante desastres](cross-region-replication-manage-disaster-recovery.md)
* [Métricas de replicación de volúmenes](azure-netapp-files-metrics.md#replication)
* [Eliminación de volúmenes o replicaciones de volúmenes](cross-region-replication-delete.md)
* [Solución de problemas de la replicación entre regiones](troubleshoot-cross-region-replication.md)


