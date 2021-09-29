---
title: Comprobar las métricas de rendimiento y latencia de una cuenta de almacenamiento en Azure Portal | Microsoft Docs
description: Descubra cómo puede comprobar las métricas de rendimiento y latencia de una cuenta de almacenamiento en el portal.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: ced5b6493753baab64f5390b7dee815227334796
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128549524"
---
# <a name="verify-throughput-and-latency-metrics-for-a-storage-account"></a>Comprobar las métricas de rendimiento y latencia de una cuenta de almacenamiento

Este tutorial es la parte número cuatro y última de una serie. En los tutoriales anteriores, aprendió a cargar y descargar grandes cantidades de datos aleatorios en una cuenta de almacenamiento de Azure. En este tutorial le mostraremos cómo puede usar las métricas para ver el rendimiento y la latencia en Azure Portal.

En la parte número cuatro de la serie, se aprende a:

> [!div class="checklist"]
> - Configurar diagramas en Azure Portal
> - Comprobar las métricas de rendimiento y latencia

[Las métricas de almacenamiento de Azure](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) utilizan el monitor de Azure para proporcionar una vista unificada del rendimiento y la disponibilidad de la cuenta de almacenamiento.

## <a name="configure-metrics"></a>Configuración de métricas

Vaya a la opción **Métricas** en la sección **CONFIGURACIÓN** de la cuenta de almacenamiento.

Elija "Blob" en la lista desplegable **SUBSERVICIO**.

En **MÉTRICA**, seleccione una de las métricas que se encuentran en la tabla siguiente:

Las siguientes métricas le proporcionan una idea de la latencia y el rendimiento de la aplicación. Las métricas que configure en el portal tendrán promedios de 1 minuto. Si una transacción termina en mitad de un minuto, esos datos del minuto se dividen por la mitad para hallar la media. En la aplicación, las operaciones de carga y descarga se calculan y le proporcionan el resultado de la cantidad real de tiempo necesario para cargar y descargar los archivos. Esta información se puede utilizar junto con las métricas del portal para así comprender el rendimiento.

|Métrica|Definición|
|---|---|
|**Latencia de E2E correcta**|La latencia media de un extremo a otro de las solicitudes correctas realizadas a un servicio de almacenamiento o a la operación de API especificada. Este valor incluye el tiempo de procesamiento requerido dentro de Azure Storage para leer la solicitud, enviar la respuesta y recibir la confirmación de la respuesta.|
|**Latencia del servidor correcta**|El tiempo medio que se usa para que Azure Storage procese una solicitud correcta . Este valor no incluye la latencia de red especificada en SuccessE2ELatency. |
|**Transactions**|El número de solicitudes realizadas a un servicio de almacenamiento o la operación de API especificada. Este número incluye solicitudes correctas y con errores, así como las solicitudes que generaron errores. En el ejemplo, el tamaño de bloque se estableció en 100 MB. En este caso, cada bloque de 100 MB se considera una transacción.|
|**Entrada**|La cantidad de datos de entrada. Este número incluye la entradas desde un cliente externo en Azure Storage, así como la entrada dentro de Azure. |
|**Salida**|La cantidad de datos de salida. Este número incluye la salida de un cliente externo en Azure Storage, así como la salida dentro de Azure. En consecuencia, este número no refleja las salidas facturables. |

Seleccione la opción **Últimas 24 horas (automático)** junto a **Tiempo**. Elija **Última hora** y **Minuto** en **Granularidad del tiempo** y haga clic en **Aplicar**.

![Métricas de la cuenta de almacenamiento](./media/storage-blob-scalable-app-verify-metrics/figure1.png)

Los gráficos pueden tener más de una métrica asignada, pero asignar más de una métrica deshabilita la capacidad de grupo por dimensiones.

## <a name="dimensions"></a>Dimensions

Las [Dimensiones](./monitor-blob-storage-reference.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#metrics-dimensions) se utilizan para obtener los pormenores de los gráficos y obtener información más detallada. Las métricas diferentes tienen distintas dimensiones. Una dimensión que está disponible es la dimensión **Nombre de API**. Esta dimensión se sale del gráfico en cada llamada de API independiente. La primera imagen que tiene a continuación muestra un gráfico de ejemplo con las transacciones de una cuenta de almacenamiento. En la segunda imagen se muestra el mismo gráfico, pero con la dimensión Nombre de API seleccionada. Tal como puede ver, cada transacción aparece en una lista que proporciona más detalles sobre cuántas llamadas se realizaron por nombre de API.

![Métricas de la cuenta de almacenamiento: transacciones sin una dimensión](./media/storage-blob-scalable-app-verify-metrics/transactionsnodimensions.png)

![Métricas de la cuenta de almacenamiento: transacciones](./media/storage-blob-scalable-app-verify-metrics/transactions.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, elimine el grupo de recursos, la máquina virtual y todos los recursos relacionados. Para ello, seleccione el grupo de recursos de la máquina virtual y haga clic en Eliminar.

## <a name="next-steps"></a>Pasos siguientes

En la parte cuatro de la serie, aprendió a ver las métricas de la solución de ejemplo y a realizar lo siguiente:

> [!div class="checklist"]
> - Configurar diagramas en Azure Portal
> - Comprobar las métricas de rendimiento y latencia

Siga este vínculo para ver ejemplos de almacenamiento previamente creados.

> [!div class="nextstepaction"]
> [Ejemplos de script de almacenamiento de Azure](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-blob-scalable-app-download-files.md
