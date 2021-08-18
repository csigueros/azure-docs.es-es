---
title: Implementación inicial mínima de hosts
description: La implementación inicial mínima es de tres hosts.
ms.topic: include
ms.date: 04/28/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: d0efae1a8f3bf30360d2649b9d638f5cc800179f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747616"
---
<!-- Used in plan-private-cloud-deployment.md and concepts-private-clouds-clusters.md -->

De manera predeterminada, hay un clúster vSAN para cada nube privada que se crea. Los clústeres se pueden agregar, eliminar y escalar.  El número mínimo de hosts por clúster y la implementación inicial es tres. 

Los clústeres de prueba están disponibles con fines de evaluación y están limitados a tres hosts por nube privada.

Se usa vSphere y NSX-T Manager para administrar la mayoría de los otros aspectos de la configuración o del funcionamiento de los clústeres. Todo el almacenamiento local de cada host de un clúster está bajo el control de vSAN.

>[!TIP]
>Si necesita aumentar el número de implementación inicial, siempre puede extender el clúster y agregar clústeres adicionales más adelante.
