---
title: 'Mover datos hacia y desde Azure Blob Storage: proceso de ciencia de datos en equipos'
description: Movimiento de los datos hacia y desde Azure Blob Storage con el Explorador de Azure Storage, AzCopy, Python y SSIS
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 151375a37b80567aecf100ec3cd576882d06e3f3
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111902256"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Movimiento de datos hacia y desde Azure Blob Storage

El proceso de ciencia de datos en equipos requiere que los datos se introduzcan o se cargue en varios entornos de almacenamiento diferentes para que se procesen o analicen de la manera más adecuada en cada fase del proceso.

## <a name="different-technologies-for-moving-data"></a>Distintas tecnologías para mover datos

Los artículos siguientes describen cómo mover datos hacia y desde Azure Blob storage mediante diferentes tecnologías.

* [Explorador de Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
  * El Explorador de Storage es una herramienta gratuita de Microsoft que permite trabajar con los datos de Azure Storage en Windows, macOS y Linux.
  * Si está usando la máquina virtual que se configuró con los scripts ofrecidos por Máquinas virtuales de ciencia de datos en Azure,el Explorador de Azure Storage ya está instalado en la máquina virtual.
* [AzCopy](../../storage/common/storage-use-azcopy-v10.md)
  * AzCopy es una utilidad de línea de comandos que puede usar para copiar blobs o archivos a una cuenta de almacenamiento o desde una cuenta de almacenamiento. 
* [SDK de Python](../../storage/blobs/storage-quickstart-blobs-python.md)
  * Use la biblioteca cliente de Azure Blob Storage para Python para mover blobs.
* [Feature Pack de SQL Server Integration Services (SSIS) para Azure](/sql/integration-services/azure-feature-pack-for-integration-services-ssis)
  * SSIS ofrece componentes para conectarse a Azure, transferir datos entre Azure y orígenes de datos locales y procesar datos almacenados en Azure. Si desea ver un análisis de los escenarios canónicos que usan SSIS para satisfacer las necesidades de negocio comunes en escenarios de integración de datos híbridos, visite el blog [Aprovechar más el Feature Pack de SQL Server Integration Services para Azure](https://techcommunity.microsoft.com/t5/sql-server-integration-services/doing-more-with-sql-server-integration-services-feature-pack-for/ba-p/388238).
  * Para obtener materiales de aprendizaje sobre SSIS, vea [Aprendizaje práctico para SSIS](https://www.microsoft.com/sql-server/training-certification).
  * Si desea obtener información sobre cómo trabajar con SISS para compilar paquetes de extracción, transformación y carga (ETL) sencillos, consulte [Tutorial de SSIS: Creación de un paquete ETL sencillo](/sql/integration-services/ssis-how-to-create-an-etl-package).

El método más adecuado para usted dependerá de su escenario. El artículo [Escenarios para análisis avanzado en Azure Machine Learning](plan-sample-scenarios.md) lo ayudará a determinar los recursos que necesita para varios flujos de trabajo de ciencia de datos utilizados en un proceso de análisis avanzado.

> [!NOTE]
> Para ver una introducción completa a Azure Blob Storage, consulte [Aspectos básicos de Azure Blob](../../storage/blobs/storage-quickstart-blobs-dotnet.md) y [Azure Blob Service](/rest/api/storageservices/Blob-Service-Concepts).
> 
> 

## <a name="using-azure-data-factory"></a>Uso de Azure Data Factory

Como alternativa, puede usar [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para: 

* crear y programar una canalización que descarga los datos desde Azure Blob Storage,
* pasarla a un servicio web Azure Machine Learning publicado, 
* recibir los resultados de análisis predictivo y 
* cargar los resultados al almacenamiento. 

Consulte [Creación de canalizaciones predictivas mediante Factoría de datos de Azure y Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md)para obtener más información.

## <a name="prerequisites"></a>Prerrequisitos
En este artículo se presupone que tiene una suscripción de Azure, una cuenta de almacenamiento y la clave de almacenamiento correspondiente para dicha cuenta. Antes de cargar o descargar datos, debe conocer su nombre de cuenta de Azure Storage y la clave de cuenta.

* Para configurar una suscripción a Azure, consulte [Prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).
* Para obtener instrucciones sobre cómo crear una cuenta de almacenamiento y cómo obtener información sobre la cuenta y la clave, consulte [Acerca de las cuentas de Azure Storage](../../storage/common/storage-account-create.md).