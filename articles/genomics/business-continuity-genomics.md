---
title: Información general sobre la continuidad empresarial
titleSuffix: Microsoft Genomics
description: En este artículo de introducción se describen las funcionalidades que Microsoft Genomics proporciona a la continuidad empresarial y la recuperación ante desastres.
keywords: Continuidad empresarial, recuperación ante desastres
services: genomics
author: vigunase
manager: cgronlun
ms.author: vigunase
ms.service: genomics
ms.topic: conceptual
ms.date: 04/06/2018
ms.openlocfilehash: 0684f8ed53c850ea3fe8adda33aa06e3a90b184c
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255786"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Introducción a la continuidad empresarial con Microsoft Genomics
En este artículo de introducción se describen las funcionalidades que Microsoft Genomics proporciona a la continuidad empresarial y la recuperación ante desastres. Obtenga información acerca de las opciones para la recuperación de eventos potencialmente perjudiciales, como una interrupción de la región de Azure, que podrían provocar la pérdida de datos. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics presenta esa asistencia a la continuidad empresarial 
Aunque es poco frecuente, un centro de datos de Azure puede tener una interrupción que a su vez puede provocar una interrupción del negocio, que puede durar desde unos minutos a unas pocas horas. Cuando se produce una interrupción, todos los trabajos que se están ejecutando actualmente en el centro de datos darán un error, y el servicio de Microsoft Genomics no volverá a enviar automáticamente los trabajos a una región secundaria. 

* Una opción consiste en esperar a que el centro de datos vuelva a estar en línea cuando termine la interrupción. Si la interrupción es breve, el servicio de Microsoft Genomics detectará automáticamente los trabajos con errores y se reiniciará automáticamente el flujo de trabajo.

* Otra opción es en enviar el flujo de trabajo en otra región de datos de forma proactiva. Microsoft Genomics implementa instancias en varias [regiones de Azure](https://azure.microsoft.com/regions/services/), y cada instancia específica de región es independiente. Si una de las instancias de Microsoft Genomics experimenta un error local en la región, otras regiones que estén ejecutando instancias de Microsoft Genomics continuarán procesando trabajos. Esta transferencia a una región alternativa está bajo el control del usuario y está disponible en cualquier momento.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Flujos de trabajo de conmutación por error de Microsoft Genomics a otra región
Si se produce una interrupción del centro de datos de una región, puede decidir el envío de flujos de trabajo de Microsoft Genomics en una región secundaria, según la soberanía de datos individuales y los requisitos de continuidad empresarial. Para realizar una conmutación por error manual de los flujos de trabajo de Microsoft Genomics, se usa una cuenta de Genomics específica para una región y se envía el trabajo con las credenciales de una cuenta de almacenamiento y de Genomics adecuadas y específicas para una región.

En concreto, tendrá que:
* Crear una cuenta de Genomics en la región secundaria mediante Azure Portal. 
* Migrar los datos de entrada a una cuenta de almacenamiento en la región secundaria y configurar una carpeta de salida en la región secundaria.
* Enviar el flujo de trabajo en la región secundaria.

Cuando se restaure la región original, el servicio de Microsoft Genomics no migra los datos de la región secundaria a la región original. Puede mover los archivos de entrada y salida de la región secundaria de nuevo a la región original.  Si elige mover los datos, esto está fuera del servicio de Genomic y todos los cargos relacionados con el movimiento de los datos serán su responsabilidad. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Preparación para una posible interrupción específica de región
Si le preocupa la una recuperación rápida en el caso de una interrupción del centro de datos, hay algunos pasos que puede seguir para reducir el tiempo necesario para que pueda reenviar manualmente los flujos de trabajo de Microsoft Genomics a una región secundaria:

* Identifique una región secundaria adecuada y cree proactivamente una cuenta de Genomics en dicha región
* Duplique los datos en la región primaria y secundaria para que los datos están disponibles inmediatamente en la región secundaria. Esto se puede realizar de forma manual o mediante la función de [almacenamiento con redundancia geográfica](../storage/common/storage-redundancy.md) que está disponible en Azure Storage. 

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido acerca de las opciones de continuidad empresarial y recuperación ante desastres cuando se utiliza el servicio Microsoft Genomics. Para más información acerca de la continuidad empresarial y recuperación ante desastres en Azure en general, consulte la [Guía técnica sobre resistencia en Azure.](/azure/architecture/resiliency/recovery-loss-azure-region)