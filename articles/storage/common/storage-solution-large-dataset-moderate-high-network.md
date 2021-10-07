---
title: Opciones de transferencia de datos de Azure para grandes conjuntos de datos con ancho de banda de red moderado a elevado | Microsoft Docs
description: Obtenga información sobre cómo elegir una solución de Azure para la transferencia de datos cuando tenga ancho de banda de red moderado a elevado en su entorno y vaya a transferir grandes conjuntos de datos.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 5c06de5c1d466db26e756029d2046286bd55d4e7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128674535"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Transferencia de datos para grandes conjuntos de datos con ancho de banda de red moderado a elevado

En este artículo encontrará información general sobre las soluciones para la transferencia de datos cuando tiene un ancho de banda de red moderado a elevado en su entorno y va a transferir grandes conjuntos de datos. En el artículo también se describen las opciones recomendadas de transferencia de datos y la matriz de funcionalidades clave para este escenario.

Para una visión general de todas las opciones de transferencia de datos disponibles, vaya a [Choose an Azure data transfer solution](storage-choose-data-transfer-solution.md) (Elección de una solución de transferencia de datos de Azure).

## <a name="scenario-description"></a>Descripción del escenario

Los conjuntos de datos grandes hacen referencia a tamaños de datos en el orden de TB a PB. El ancho de banda de moderado a elevado comprende de 100 Mbps a 10 Gbps.

## <a name="recommended-options"></a>Opciones recomendadas

Las opciones recomendadas en este escenario dependen de si tiene un ancho de banda de red moderado o elevado.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Ancho de banda de red moderado (100 Mbps - 1 Gbps)

Con un ancho de banda de red moderado, necesita planear el tiempo para la transferencia de datos a través de la red.

Use la siguiente tabla para estimar el tiempo y, en base a eso, elija entre una transferencia sin conexión o una transferencia de red. La tabla muestra el tiempo previsto para la transferencia de datos de red, teniendo en cuenta los diversos anchos de banda disponibles (suponiendo un uso del 90 %).

![Transferencia de red o transferencia sin conexión](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Si está previsto que la transferencia de red sea demasiado lenta, debe usar un dispositivo físico. Las opciones recomendadas en este caso son los dispositivos de transferencia sin conexión de la familia de Azure Data Box o Azure Import/Export que usan sus propios discos.

  - **Familia Azure Data Box para transferencias sin conexión:** use los dispositivos Data Box que proporciona Microsoft para mover grandes volúmenes de datos a Azure cuando tenga limitación de tiempo, disponibilidad de red o costes. Copie los datos locales con herramientas como Robocopy. Según el tamaño de los datos previstos para la transferencia, puede elegir entre Data Box Disk, Data Box o Data Box Heavy.
  - **Azure Import/Export**: use el servicio Azure Import/Export para enviar sus propias unidades de disco y así importar de forma segura grandes cantidades de datos a Azure Blob Storage y Azure Files. También se puede usar este servicio para transferir datos desde Azure Blob Storage hasta las unidades de disco y enviarlas al sitio local.

- Si se prevé que la transferencia de red sea razonable, entonces puede usar cualquiera de las siguientes herramientas detalladas en [Ancho de banda de red elevado](#high-network-bandwidth).

### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Ancho de banda de red elevado (1 Gbps - 100 Gbps)

Si el ancho de banda de red disponible es elevado, use una de las siguientes herramientas.

- **AzCopy**: use esta herramienta de la línea de comandos para copiar fácilmente datos desde y hacia Azure Blobs, Files y Table Storage con un rendimiento óptimo. AzCopy admite la simultaneidad y el paralelismo, y permite reanudar operaciones de copia cuando si se interrumpen.
- **API REST/SDK de Azure Storage**: al compilar una aplicación, puede desarrollar las API REST de Azure Storage y usar los SDK de Azure que se ofrecen en varios lenguajes.
- **Familia de Azure Data Box para transferencias en línea**: Data Box Edge y Data Box Gateway son dispositivos de red en línea que pueden mover datos dentro y fuera de Azure. Use el dispositivo físico Data Box Edge cuando exista la necesidad simultánea de ingerir y procesar de forma previa los datos antes de cargarlos. Data Box Gateway es una versión virtual del dispositivo con las mismas funcionalidades de transferencia de datos. En cada caso, es el dispositivo quien se encarga de administrar la transferencia de datos.
- **Azure Data Factory**: se debe usar Data Factory para escalar horizontalmente una operación de transferencia y saber si existe la necesidad de tener funcionalidades de orquestación y supervisión a nivel empresarial. Use Data Factory para transferir archivos regularmente entre varios servicios de Azure, en las instalaciones o mediante una combinación de ambos. Con Data Factory puede crear y programar flujos de trabajo basados en datos (llamados canalizaciones) que ingieren datos de distintos almacenes de datos y automatizar el movimiento de datos y la transformación de datos.

## <a name="comparison-of-key-capabilities"></a>Comparación de funcionalidades clave

En las siguientes tablas se resumen las diferencias de las funcionalidades clave para las opciones recomendadas.

### <a name="moderate-network-bandwidth"></a>Ancho de banda de red moderado

Si usa la transferencia de datos sin conexión, use la siguiente tabla para comprender las diferencias de las funcionalidades clave.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy            |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    **Tamaño de los datos**                    |    Hasta 35 TB                 |    Hasta 80 TB por dispositivo                       |    Hasta 800 TB por dispositivo               |    Variable                            |
|    **Tipo de datos**                    |    Azure Blobs                  |    Azure Blobs<br>Azure Files                    |    Azure Blobs<br>Azure Files            |    Azure Blobs<br>Azure Files          |
|    **Factor de forma**                  |    5 SSD por pedido             |    1 x 50 libras dispositivo de tamaño de escritorio por pedido    |    1 x ~500 libras dispositivo grande por pedido    |    Hasta 10 HDD/SSD por pedido        |
|    **Tiempo de instalación inicial**               |    Bajo <br>(15 min)            |    De bajo a moderado <br> (<30 min)               |    Moderado<br>(De 1 a 2 horas)               |    De moderado a difícil<br>(variable) |
|    **Envío de datos a Azure**           |    Sí                          |    Sí                                           |    Sí                                   |    Sí                                 |
|    **Exportación de datos de Azure**           |    No                           |    No                                            |    No                                    |    Sí                                 |
|    **Cifrado**                   |    AES de 128 bits                  |    AES de 256 bits                                   |    AES de 256 bits                           |    AES de 128 bits                         |
|    **Hardware**                     |     Proporcionado por Microsoft          |    Proporcionado por Microsoft                            |    Proporcionado por Microsoft                    |    Proporcionado por el cliente                   |
|    **Interfaz de red**            |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    **Integración de asociados**          |    Algunos                         |    [Elevado](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [Elevado](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    Algunos                                |
|    **Envío**                     |    Administrado por Microsoft            |    Administrado por Microsoft                             |    Administrado por Microsoft                     |    Administrado por el cliente                    |
| **Uso cuando se mueven los datos**     |Dentro de un límite de comercio|Dentro de un límite de comercio|Dentro de un límite de comercio|Traspasando fronteras geográficas; por ejemplo, de EE. UU. a la UE|
|    **Precios**                          |    [Precios](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Precios](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Precios](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Precios](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |

Si usa la transferencia de datos en línea, use la tabla en la sección siguiente para el ancho de banda de red elevado.

### <a name="high-network-bandwidth"></a>Ancho de banda de red elevado

|                                     |    Herramientas de AzCopy, <br>Azure PowerShell, <br>Azure CLI             |    API REST y SDK de Azure Storage                   |    Data Box Edge o Data Box Gateway          |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    **Tipo de datos**              |    Azure Blobs, Azure Files, Azure Tables    |    Azure Blobs, Azure Files, Azure Tables    |    Azure Blobs, Azure Files                           |   Admite más de 70 conectores de datos para almacenes y formatos de datos    |
|    **Factor de forma**            |    Herramientas de línea de comandos                        |    Interfaz programática                    |    Microsoft proporciona un dispositivo <br>virtual o físico     |    Servicio en Azure Portal                                            |
|    **Instalación única inicial** |    Fácil               |    Moderado                       |    Fácil (< 30 minutos) a moderada (entre 1 y 2 horas)            |    Amplia                                                          |
|    **Procesamiento previo de los datos**          |    No                                        |    No                                        |    Sí (con proceso perimetral)                               |    Sí                                                                |
|    **Transferencia desde otras nubes**   |    No                                        |    No                                        |    No                                                    |    Sí                                                                |
|    **Tipo de usuario**                    |    Profesional de TI o desarrollador                                       |    Desarrollo                                       |    Profesional de TI                                                |    Profesional de TI                                                             |
|    **Precios**                      |    Gratis, se aplican los cargos de salida         |    Gratis, se aplican los cargos de salida         |    [Precios](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Precios](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>Pasos siguientes

- [Aprenda a transferir datos con Import/Export](../../import-export/storage-import-export-data-to-blobs.md).

- Más información:
  - [Transferir datos con Data Box Disk](../../databox/data-box-disk-quickstart-portal.md).
  - [Transferir datos con Data Box](../../databox/data-box-quickstart-portal.md).
  - [Transferencia de datos con AzCopy](./storage-use-azcopy-v10.md).
  - [Transferencia de datos con Data Box Gateway](../../databox-gateway/data-box-gateway-deploy-add-shares.md).
  - [Transformación de datos con Data Box Edge antes del envío a Azure](../../databox-online/azure-stack-edge-deploy-configure-compute.md).

- [Transferencia de datos con Azure Data Factory](../../data-factory/quickstart-create-data-factory-portal.md).

- Use las API REST para transferir datos:
  - [En .NET](/dotnet/api/overview/azure/storage)
  - [En Java](/java/api/overview/azure/storage)
