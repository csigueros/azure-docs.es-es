---
title: 'Representación de Azure: arquitecturas de referencia'
description: Arquitecturas para el uso de Azure Batch y otros servicios de Azure para ampliar una granja de representación local llevándola a la nube
ms.date: 02/07/2019
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: abd67312c9ff8d74cc2a73d9750daca80f28391b
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110481387"
---
# <a name="reference-architectures-for-azure-rendering"></a>Arquitecturas de referencia para la representación de Azure

Este artículo muestra detallados diagramas de arquitectura para los escenarios de ampliación de una granja de representación local en Azure. Los ejemplos muestran opciones diferentes para los servicios de proceso, redes y almacenamiento de Azure.

## <a name="hybrid-with-nfs-or-cfs"></a>Híbrido con NFS o CFS

El siguiente diagrama muestra un escenario híbrido que incluye los siguientes servicios de Azure:

* **Proceso**: grupo de Azure Batch o conjunto de escalado de máquinas virtuales.

* **Red**: local; Azure ExpressRoute o VPN. Azure: red virtual de Azure.

* **Almacenamiento**: archivos de entrada y salida; NFS o CFS que usan VM de Azure, sincronizados con el almacenamiento local mediante Azure File Sync o RSync. De forma alternativa: Avere vFXT para archivos de entrada o salida desde dispositivos NAS locales mediante NFS.

  ![Ampliación a la nube: híbrido con NFS o CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Híbrido con Blobfuse

El siguiente diagrama muestra un escenario híbrido que incluye los siguientes servicios de Azure:

* **Proceso**: grupo de Azure Batch o conjunto de escalado de máquinas virtuales.

* **Red**: local; Azure ExpressRoute o VPN. Azure: red virtual de Azure.

* **Almacenamiento**: archivos de entrada y salida; Blob Storage, montados para procesar recursos a través de Azure Blobfuse.

  ![Ampliación a la nube: híbrido con Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Proceso y almacenamiento híbrido

El siguiente diagrama muestra un escenario híbrido completamente conectado de proceso y almacenamiento e incluye los siguientes servicios de Azure:

* **Proceso**: grupo de Azure Batch o conjunto de escalado de máquinas virtuales.

* **Red**: local; Azure ExpressRoute o VPN. Azure: red virtual de Azure.

* **Almacenamiento**: entre locales; Avere vFXT. Archivado opcional de archivos locales a través de Azure Data Box en Blob Storage, o instancia local de Avere FXT para aceleración NAS.

  ![Ampliación en la nube: proceso y almacenamiento híbrido](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre las opciones de [representación en Azure](batch-rendering-service.md).
* Más información sobre el [uso de aplicaciones de representación con Batch](batch-rendering-applications.md).
