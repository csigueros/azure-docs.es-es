---
title: Límites de recursos para Azure NetApp Files| Microsoft Docs
description: Describe los límites para los recursos de Azure NetApp Files y cómo solicitar un aumento del límite de recursos.
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
ms.date: 08/24/2021
ms.author: b-juche
ms.openlocfilehash: 0b05b934b4dc57b4db941755cfcfb2f8d21fc2c6
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824986"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Límites de recursos para Azure NetApp Files

Entender los límites de recursos de Azure NetApp Files ayuda a administrar los volúmenes.

## <a name="resource-limits"></a>Límites de recursos

En la tabla siguiente se describen los límites de recursos de Azure NetApp Files:

|  Resource  |  Límite predeterminado  |  Ajustable a través de la solicitud de soporte técnico  |
|----------------|---------------------|--------------------------------------|
|  [Cuota de capacidad regional por suscripción](#regional-capacity-quota)   |  25 TiB  |  Sí  |
|  Número de cuentas de NetApp por región de Azure por suscripción  |  10    |  Sí   |
|  Número de grupos de capacidad por cuenta de NetApp   |    25     |   Sí   |
|  Número de volúmenes por suscripción   |    500     |   Sí   |
|  Número de volúmenes por grupo de capacidad     |    500   |    Sí     |
|  Número máximo de instantáneas por volumen       |    255     |    No        |
|  Número de subredes que se delegan a Azure NetApp Files (Microsoft.NetApp/volumes) por cada red virtual de Azure    |   1   |    No    |
|  Número de direcciones IP en uso en una red virtual (incluidas las redes virtuales emparejadas inmediatamente) con Azure NetApp Files   |    1000   |    No   |
|  Tamaño mínimo de un único grupo de capacidades   |  4 TiB     |    No  |
|  Tamaño máximo de un único grupo de capacidades    |  500 TiB   |   No   |
|  Tamaño mínimo de un único volumen    |    100 GiB    |    No    |
|  Tamaño máximo de un único volumen     |    100 TiB    |    No    |
|  Tamaño máximo de un archivo individual     |    16 TiB    |    No    |    
|  Tamaño máximo de los metadatos de directorio en un solo directorio      |    320 MB    |    No    |    
|  Número máximo de archivos en un único directorio  | *Aproximadamente* 4 millones. <br> Consulte [Determinar si un directorio está llegando al tamaño límite](#directory-limit).  |    No    |   
|  Número máximo de archivos ([maxfiles](#maxfiles)) por volumen     |    100 millones    |    Sí    |    
|  Número máximo de reglas de directiva de exportación por volumen     |    5  |    No    | 
|  Rendimiento mínimo asignado para un volumen de QoS manual     |    1 MiB/s   |    No    |    
|  Rendimiento máximo asignado para un volumen de QoS manual     |    4500 MiB/s    |    No    |    
|  Número de volúmenes de protección de datos de replicación entre regiones (volúmenes de destino)     |    5    |    Sí    |     

Para más información, consulte [Preguntas más frecuentes sobre la administración de la capacidad](azure-netapp-files-faqs.md#capacity-management-faqs).

## <a name="determine-if-a-directory-is-approaching-the-limit-size"></a>Determinar si un directorio está llegando al tamaño límite <a name="directory-limit"></a>  

Puede usar el comando `stat` desde un cliente para ver si un directorio está llegando al límite de tamaño máximo de los metadatos del directorio (320 MB).   

En un directorio de 320 MB, el número de bloques es 655 360 y el tamaño de cada bloque es de 512 bytes.  (Es decir, 320 x 1024 x 1024/512). Este número se traduce en aproximadamente 4 millones archivos como máximo para un directorio de 320 MB. Sin embargo, el número máximo real de archivos puede ser menor, en función de factores como el número de archivos que contienen caracteres que no son ASCII en el directorio. Por lo tanto, debe usar el comando `stat` como se indica a continuación para determinar si el directorio está llegando a su límite.  

Ejemplos:

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```

## <a name="maxfiles-limits"></a>Límites de número máximo de archivos <a name="maxfiles"></a> 

Los volúmenes de Azure NetApp Files tienen un límite denominado *maxfiles*. El límite de maxfiles es el número de archivos que puede contener un volumen. Los sistemas de archivos de Linux hacen referencia al límite como *inodes*. El límite de maxfiles de un volumen de Azure NetApp Files se indexa en función del tamaño (cuota) del volumen. El límite de maxfiles de un volumen aumenta o disminuye a la velocidad de 20 millones de archivos por TiB del tamaño del volumen aprovisionado. 

El servicio ajusta dinámicamente el límite de maxfiles de un volumen en función de su tamaño aprovisionado. Por ejemplo, un volumen configurado inicialmente con un tamaño de 1 TiB tendría un límite de maxfiles de 20 millones. Los cambios posteriores en el tamaño del volumen provocarán un reajuste automático del límite de maxfiles en función de las siguientes reglas: 

|    Tamaño del volumen (cuota)     |  Reajuste automático del límite de maxfiles    |
|----------------------------|-------------------|
|    <= 1 TiB                |    20 millones     |
|    > 1 TiB, pero <= 2 TiB    |    40 millones     |
|    > 2 TiB, pero <= 3 TiB    |    60 millones     |
|    > 3 TiB, pero <= 4 TiB    |    80 millones     |
|    > 4 TiB                 |    100 millones    |

Si ya ha asignado al menos 4 TiB de cuota para un volumen, puede iniciar una [solicitud de soporte técnico](#request-limit-increase) para aumentar el límite de maxfiles (inodes) más allá de 100 millones. Por cada 100 millones de archivos que aumente (o una fracción de esta cantidad), debe aumentar la cuota de volumen correspondiente en 4 TiB.  Por ejemplo, si aumenta el límite de maxfiles de 100 millones de archivos a 200 millones de archivos (o cualquier número entre estos), debe aumentar la cuota de volumen de 4 TiB a 8 TiB.

Puede aumentar el límite de maxfiles a 500 millones si la cuota del volumen es de al menos 20 TiB. <!-- ANF-11854 --> 

## <a name="regional-capacity-quota"></a>Cuota de capacidad regional

Puede hacer clic en **Cuota** en Configuración de Azure NetApp Files para mostrar los tamaños de cuota actuales y predeterminados de la región. 

Por ejemplo: 

![Captura de pantalla que muestra cómo mostrar la información de cuota.](../media/azure-netapp-files/quota-display.png) 

Puede [enviar una solicitud de soporte técnico](#request-limit-increase) para aumentar una cuota de capacidad regional sin incurrir en costos adicionales. La solicitud de soporte técnico se enviará al equipo de administración de capacidad de Azure para su procesamiento. Normalmente, recibirá una respuesta en un plazo de dos días laborables. El equipo de administración de la capacidad de Azure podría ponerse en contacto con usted si la solicitud es de tamaño considerable.  

Un aumento de la cuota de capacidad regional no incurre en un aumento de facturación. La facturación se seguirá basando en los grupos de capacidad aprovisionados.
Por ejemplo, si actualmente tiene 25 TiB de capacidad aprovisionada, puede solicitar un aumento de la cuota a 35 TiB.  En un plazo de dos días laborables, el aumento de la cuota se aplicará a la región solicitada. Cuando se aplique el aumento de cuota, solo se pagará por la capacidad aprovisionada actual (25 TiB). Pero cuando aprovisione realmente los 10 TiB adicionales, se le facturarán 35 TiB.

Los [límites de los recursos](#resource-limits) actuales de Azure NetApp Files no cambian. Todavía podrá aprovisionar un grupo de capacidad de 500 TiB. Pero antes de hacerlo, la cuota de capacidad regional debe aumentarse a 500 TiB.

## <a name="request-limit-increase"></a>Solicitud de aumento del límite

Puede crear una solicitud de soporte técnico de Azure para aumentar los límites ajustables de la tabla [Límites de recursos](#resource-limits). 

1. Vaya a **Nueva solicitud de soporte técnico** en **Soporte técnico y solución de problemas**.
1. En la pestaña **Descripción del problema**, proporcione la información solicitada.
1. En la pestaña **Detalles adicionales**, haga clic en **Escribir detalles** en el campo Detalles de la solicitud.  

    ![Captura de pantalla que muestra la pestaña Detalles y el campo Escribir detalles.](../media/azure-netapp-files/quota-additional-details.png)

1. En la ventana Detalles de la cuota que aparece:  

    1. En Tipo de cuota, seleccione el tipo de recurso que desea aumentar.  
        Por ejemplo:  
        * *Cuota de capacidad regional por suscripción (TiB)*
        * *Número de cuentas de NetApp por región de Azure por suscripción*
        * *Número de volúmenes por suscripción*

    1. En Region Requested (Región solicitada), seleccione su región.   
        Los tamaños actual y predeterminado se muestran en Quota State (Estado de cuota).
    1. Escriba un valor para solicitar el aumento para el tipo de cuota especificado.
    
    ![Captura de pantalla que muestra cómo mostrar y solicitar el aumento de la cuota regional.](../media/azure-netapp-files/quota-details-regional-request.png)

1. Haga clic en **Siguiente** y en **Revisar y crear** para crear la solicitud.

## <a name="next-steps"></a>Pasos siguientes  

- [Información sobre la jerarquía del almacenamiento de Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Modelo de costo de Azure NetApp Files](azure-netapp-files-cost-model.md)
