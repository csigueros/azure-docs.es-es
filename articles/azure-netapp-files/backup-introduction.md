---
title: Descripción de la copia de seguridad de Azure NetApp Files | Microsoft Docs
description: Describe el funcionamiento de la copia de seguridad de Azure NetApp Files, las regiones admitidas y el modelo de costes.
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
ms.date: 09/28/2021
ms.author: b-juche
ms.custom: references_regions
ms.openlocfilehash: 8880b128add7e13a34d3b4e3b6b6ad23a8c5df08
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218364"
---
# <a name="understand-azure-netapp-files-backup"></a>Descripción de la copia de seguridad de archivos de Azure NetApp Files

La copia de seguridad de Azure NetApp Files amplía las funcionalidades de protección de datos de Azure NetApp Files, al proporcionar una solución de copia de seguridad totalmente administrada para la recuperación, el archivo y el cumplimiento a largo plazo. Las copias de seguridad que crea el servicio se almacenan en Azure Storage, con independencia de las instantáneas de volúmenes que estén disponibles para la recuperación o clonación a corto plazo. Las copias de seguridad que realiza el servicio se pueden restaurar en nuevos volúmenes de Azure NetApp Files dentro de la región. La copia de seguridad de Azure NetApp Files admite tanto copias de seguridad basadas en directivas (programadas) como copias de seguridad manuales (a petición). Para obtener más información, consulte [Funcionamiento de las instantáneas de Azure NetApp Files](snapshots-introduction.md).

> [!IMPORTANT]
> La característica de copia de seguridad de Azure NetApp Files se encuentra actualmente en versión preliminar. Para acceder a la característica, debe enviar una solicitud de lista de espera a través de la página **[Versión preliminar pública de la copia de seguridad de Azure NetApp Files](https://aka.ms/anfbackuppreviewsignup)** . Espere a recibir el correo electrónico de confirmación oficial del equipo de Azure NetApp Files antes de utilizar la característica de copia de seguridad de dicho servicio.

## <a name="supported-regions"></a>Regiones admitidas 

La copia de seguridad de Azure NetApp Files se admite en las siguientes regiones:   

* Este de EE. UU.
* Este de EE. UU. 2
* Oeste de EE. UU. 
* Centro-sur de EE. UU.
* Oeste de EE. UU. 2
* Norte de Europa 
* Oeste de Europa
* Este de Australia
* Japón Oriental

## <a name="cost-model-for-azure-netapp-files-backup"></a>Modelo de costes de la copia de seguridad de Azure NetApp Files

Los precios de la copia de seguridad de Azure NetApp Files se basan en la cantidad total de almacenamiento que consume la copia. No se aplican cargos de configuración ni una cuota mínima de uso. El precio de la restauración de copias de seguridad se basa en la cantidad total de capacidad de copia de seguridad restaurada durante el ciclo de facturación.

Como ejemplo de precios, suponga las siguientes situaciones:

* El volumen de origen corresponde al nivel de servicio Premium de Azure NetApp Files. Tiene un tamaño de cuota de volumen de 1000 GiB y un tamaño de volumen consumido de 500 GiB al principio del primer día de un mes. El volumen se encuentra en la región Centro y Sur de EE. UU.
* Ha configurado una directiva de *instantánea* diaria para mantener cinco instantáneas locales y una directiva de *copia de seguridad* diaria para mantener 30 copias.
* Para simplificar, supongamos que su volumen de origen tiene un cambio de datos constante de un 1 % diario, pero el tamaño total consumido del volumen no crece (permanece en 500 GiB).

Cuando la directiva de copia de seguridad se asigna al volumen, se inicia la copia de seguridad de línea de base en el almacenamiento de Azure administrado por el servicio. Una vez completada la copia de seguridad, se agregará la copia de seguridad de línea base de 500 GiB a la lista de copia de seguridad del volumen. Después de la transferencia de línea base, solo se realizan copias de seguridad diarias de los bloques modificados. Suponiendo que se hayan agregado copias de seguridad incrementales diarias de 5 GiB, el almacenamiento total de copia de seguridad consumido sería `500GiB + 30*5GiB = 650GiB`.

La copia de seguridad se le facturará al final del mes a una tarifa de 0,05 USD mensuales por la cantidad total de almacenamiento consumida por la copia.  Es decir, 650 GiB se traducirán en un cargo de copia de seguridad mensual total de `650*$0.05=$32.5`. La capacidad de almacenamiento normal de Azure NetApp Files se aplica a las instantáneas locales. Para más información, consulte la página [Precios de Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/).

Si decide restaurar una copia de seguridad de, por ejemplo, 600 GiB en un nuevo volumen, se le cobrará a una tarifa de 0,02 USD por GiB de restauraciones de capacidad de copia de seguridad. En este caso, el coste de la operación de restauración será `600*$0.02 = $12`. 

## <a name="next-steps"></a>Pasos siguientes

* [Requisitos y consideraciones para Azure NetApp Files](backup-requirements-considerations.md)
* [Límites de recursos para Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Configuración de copias de seguridad basadas en directivas](backup-configure-policy-based.md)
* [Configuración de copias de seguridad manuales](backup-configure-manual.md)
* [Administrar directivas de copia de seguridad](backup-manage-policies.md)
* [Búsqueda de copias de seguridad](backup-search.md)
* [Restauración de una copia de seguridad en un volumen nuevo](backup-restore-new-volume.md)
* [Deshabilitación de la funcionalidad de copia de seguridad de un volumen](backup-disable.md)
* [Eliminación de copias de seguridad de un volumen](backup-delete.md)
* [Métricas de copia de seguridad de los volúmenes](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Preguntas frecuentes sobre la copia de seguridad de archivos de Azure NetApp Files](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)
* [Funcionamiento de las instantáneas de Azure NetApp Files](snapshots-introduction.md)
