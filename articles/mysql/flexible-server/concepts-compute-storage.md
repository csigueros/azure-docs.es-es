---
title: 'Opciones de proceso y almacenamiento de Azure Database for MySQL: servidor flexible'
description: 'En este artículo se explican las opciones de proceso y almacenamiento de Azure Database for MySQL: servidor flexible.'
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: 9b8699598a9bac4781346ff939736b2bd6ee72f2
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113429951"
---
# <a name="compute-and-storage-options-in-azure-database-for-mysql---flexible-server-preview"></a>Opciones de proceso y almacenamiento de Azure Database for MySQL: servidor flexible (versión preliminar)

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Actualmente, Azure Database for MySQL: servidor flexible se encuentra en versión preliminar pública.

Puede crear un servidor flexible de Azure Database for MySQL en uno de los tres niveles de proceso diferentes: flexible, de uso general y optimizado para memoria. Los niveles de proceso se diferencian en la SKU de la máquina virtual subyacente que utiliza la serie B, la serie D y la serie E. La elección del tamaño y el nivel de proceso determina la memoria y los núcleos virtuales disponibles en el servidor. Se usa la misma tecnología de almacenamiento en todos los niveles de proceso. Todos los recursos se aprovisionan en el nivel de servidor MySQL. Un servidor puede tener una o varias bases de datos.

| Recurso/nivel | **Flexible** | **Uso general** | **Memoria optimizada** |
|:---|:----------|:--------------------|:---------------------|
| Series de máquinas virtuales| Serie B | Serie Ddsv4 | Serie Edsv4|
| Núcleos virtuales | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Memoria por núcleo virtual | Variable | 4 GiB | 8 GiB * |
| Tamaño de almacenamiento | De 20 GiB a 16 TiB | De 20 GiB a 16 TiB | De 20 GiB a 16 TiB |
| Período de retención de copias de seguridad de base de datos | De 1 a 35 días | De 1 a 35 días | De 1 a 35 días |

\* Con la excepción de la SKU de E64ds_v4 (optimizada para memoria), que tiene 504 GB de memoria

Para elegir un nivel de proceso, use la siguiente tabla como punto de partida.

| Nivel de proceso | Carga de trabajo objetivo |
|:-------------|:-----------------|
| Flexible | Ideal para cargas de trabajo que no necesitan toda la CPU continuamente. |
| Uso general | La mayoría de las cargas de trabajo de empresa que requieren un equilibrio entre proceso y memoria con rendimiento de E/S escalable. Por ejemplo, servidores para hospedar aplicaciones web y móviles, y otras aplicaciones empresariales.|
| Memoria optimizada | Cargas de trabajo de base de datos de alto rendimiento que requieren rendimiento en memoria para un procesamiento de transacciones más rápido y una mayor simultaneidad. Por ejemplo, servidores para procesar datos en tiempo real y aplicaciones de análisis y transacciones de alto rendimiento.|

Después de crear un servidor, cambia el nivel y el tamaño de proceso, así como el tamaño de almacenamiento. El escalado de proceso requiere un reinicio y tarda entre 60 y 120 segundos, mientras que el escalado de almacenamiento no requiere un reinicio. También se puede aumentar o reducir de forma independiente el período de retención de la copia de seguridad. Para más información, consulte la sección [Escalado de recursos](#scale-resources).

## <a name="compute-tiers-size-and-server-types"></a>Niveles de proceso, tamaño y tipos de servidor

Los recursos de proceso se pueden seleccionar en función del nivel y el tamaño. Esto determina los núcleos virtuales y el tamaño de la memoria. Los núcleos virtuales representan la CPU lógica del hardware subyacente.

Las especificaciones detalladas de los tipos de servidores disponibles son las siguientes:

| Tamaño de proceso         | Núcleos virtuales | Tamaño de memoria (GiB) | IOPS máximas admitidas | Ancho de banda de E/S máximo admitido (MBps)| Conexiones máximas
|----------------------|--------|-------------------| ------------------ |-----------------------------------|------------------
| **Flexible**        |        |                   | 
| Standard_B1s         | 1      | 1                 | 320                | 10                                | 171
| Standard_B1ms        | 1      | 2                 | 640                | 10                                | 341
| Standard_B2s         | 2      | 4                 | 1280               | 15                                | 683
| **Uso general**  |        |                   |                    |                                   | 
| Standard_D2ds_v4     | 2      | 8                 | 3200               | 48                                | 1365
| Standard_D4ds_v4     | 4      | 16                | 6400               | 96                                | 2731
| Standard_D8ds_v4     | 8      | 32                | 12800              | 192                               | 5461
| Standard_D16ds_v4    | 16     | 64                | 20000              | 384                               | 10923
| Standard_D32ds_v4    | 32     | 128               | 20000              | 768                               | 21845
| Standard_D48ds_v4    | 48     | 192               | 20000              | 1152                              | 32 768
| Standard_D64ds_v4    | 64     | 256               | 20000              | 1200                              | 43691
| **Memoria optimizada** |        |                   |                    |                                   |
| Standard_E2ds_v4     | 2      | 16                | 3200               | 48                                | 2731
| Standard_E4ds_v4     | 4      | 32                | 6400               | 96                                | 5461
| Standard_E8ds_v4     | 8      | 64                | 12800              | 192                               | 10923
| Standard_E16ds_v4    | 16     | 128               | 20000              | 384                               | 21845
| Standard_E32ds_v4    | 32     | 256               | 20000              | 768                               | 43691
| Standard_E48ds_v4    | 48     | 384               | 20000              | 1152                              | 65536
| Standard_E64ds_v4    | 64     | 504               | 20000              | 1200                              | 86016

Para obtener más detalles acerca de la serie de proceso disponible, consulte la documentación de la máquina virtual de Azure para [Flexible (serie B)](../../virtual-machines/sizes-b-series-burstable.md), [De uso general (serie Ddsv4)](../../virtual-machines/ddv4-ddsv4-series.md) y [Optimizada para memoria (serie Edsv4)](../../virtual-machines/edv4-edsv4-series.md).

>[!NOTE]
>Para [nivel de proceso ampliable (serie B)](../../virtual-machines/sizes-b-series-burstable.md), si la VM se inicia, detiene o reinicia, es posible que se pierdan los créditos. Para más información, vea [Preguntas más frecuentes sobre las máquinas virtuales ampliables (serie B)](../../virtual-machines/sizes-b-series-burstable.md#q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart).

## <a name="storage"></a>Storage

El almacenamiento que se aprovisiona es la cantidad de capacidad de almacenamiento disponible para el servidor flexible. El almacenamiento se usa para los archivos de base de datos, los archivos temporales, los registros de transacciones y los registros del servidor MySQL. En todos los niveles de proceso, el almacenamiento mínimo admitido es de 20 GiB y el máximo son 16 TiB. El almacenamiento se escala en incrementos de 1 GiB y se puede escalar verticalmente después de crear el servidor.

>[!NOTE]
> El almacenamiento solo se puede escalar verticalmente, no reducir.

Puede supervisar el consumo de almacenamiento en el Azure Portal (con Azure Monitor) mediante el límite de almacenamiento, el porcentaje de almacenamiento y las métricas usadas en el almacenamiento. Consulte el artículo [Supervisión](./concepts-monitoring.md) para obtener información sobre las métricas. 

### <a name="reaching-the-storage-limit"></a>Alcance del límite de almacenamiento

Cuando el almacenamiento consumido en el servidor está cerca de alcanzar el límite aprovisionado, el servidor se pone en modo de solo lectura para proteger las escrituras perdidas en el servidor. Los servidores con un almacenamiento aprovisionado menor o igual a 100 GiB se marcan como de solo lectura si el almacenamiento disponible es inferior al 5 % del tamaño del almacenamiento aprovisionado. Los servidores con más de 100 GiB de almacenamiento aprovisionado se marcan como solo de lectura cuando el almacenamiento libre es inferior a 5 GiB.

Por ejemplo, si ha aprovisionado 110 GiB de almacenamiento y el uso real supera los 105 GiB, el servidor se marca como de solo lectura. También, si ha aprovisionado 5 GiB de almacenamiento, el servidor se marca como de solo lectura cuando quedan menos de 256 MB de almacenamiento disponible.

Mientras el servicio intenta hacer que el servidor sea de solo lectura, se bloquean todas las nuevas solicitudes de transacción de escritura, y las transacciones activas existentes continuarán ejecutándose. Cuando el servidor se establece en solo lectura, todas las operaciones de escritura y confirmaciones de transacción posteriores generarán errores. Las consultas de lectura seguirán funcionando sin interrupciones. 

Para sacar el servidor del modo de solo lectura, debe aumentar el almacenamiento aprovisionado en el servidor. Esto puede hacerse mediante el Azure Portal o la CLI de Azure. Después del aumento, el servidor estará listo para aceptar las transacciones de escritura de nuevo.

Recomendamos que configure una alerta que le envíe una notificación cada vez que su almacenamiento en servidor esté cerca del umbral para que pueda evitar entrar en el estado de solo lectura. Consulte el artículo [Supervisión](./concepts-monitoring.md) para obtener información sobre las métricas disponibles. 

Se recomienda que <!--turn on storage auto-grow or to--> configure una alerta que le envíe una notificación cada vez que su almacenamiento en servidor esté cerca del umbral para que pueda evitar entrar en el estado de solo lectura. Para obtener más información, consulte [cómo configurar una alerta](how-to-alert-on-metric.md) en la documentación sobre alertas.

### <a name="storage-auto-grow"></a>Crecimiento automático del almacenamiento

El crecimiento automático del almacenamiento impide que el servidor se quede sin almacenamiento y se vuelva de solo lectura. Si el crecimiento automático del almacenamiento está habilitado, el almacenamiento crece automáticamente sin afectar a la carga de trabajo. El crecimiento automático del almacenamiento está habilitado de manera predeterminada para todos los nuevos servidores creados. En cuanto a los servidores con un almacenamiento aprovisionado menor o igual a 100 GB, el tamaño del almacenamiento aprovisionado se incrementa en 5 GB cuando el almacenamiento disponible es inferior al 10 % del almacenamiento aprovisionado. En los servidores con más de 100 GB de almacenamiento aprovisionado, el tamaño del almacenamiento aprovisionado se incrementa en un 5 % cuando el espacio de almacenamiento disponible está por debajo de 10 GB del tamaño del almacenamiento aprovisionado. Se aplican los límites máximos de almacenamiento según lo especificado anteriormente.

Por ejemplo, si ha aprovisionado 1000 GB de almacenamiento y el uso real supera los 990 GB, el tamaño del almacenamiento del servidor se incrementa a 1050 GB. Como alternativa, si ha aprovisionado 10 GB de almacenamiento, el tamaño del almacenamiento aumenta a 15 GB cuando queda menos de 1 GB de almacenamiento.

Recuerde que el almacenamiento solo se puede escalar verticalmente, no reducir.

## <a name="iops"></a>E/S

Azure Database for MySQL: el servidor flexible admite el aprovisionamiento de IOPS adicionales. Esta característica permite aprovisionar IOPS adicionales por encima del límite gratuito de IOPS. Con esta característica puede aumentar o disminuir el número de IOPS aprovisionadas en función de los requisitos de la carga de trabajo en cualquier momento. 

El número mínimo de IOPS es de 360 en todos los tamaños de proceso y el máximo viene determinado por el tamaño de proceso seleccionado. En la versión preliminar, el número máximo de IOPS admitido es de 20 000.

A continuación se muestra el número máximo de IOPS por tamaño de proceso: 

| Tamaño de proceso         | Número máximo de IOPS        | 
|----------------------|---------------------|
| **Flexible**        |                     |
| Standard_B1s         | 320                 |
| Standard_B1ms        | 640                 |
| Standard_B2s         | 1280                | 
| **Uso general**  |                     |
| Standard_D2ds_v4     | 3200                |
| Standard_D4ds_v4     | 6400                |
| Standard_D8ds_v4     | 12800               |
| Standard_D16ds_v4    | 20000               |
| Standard_D32ds_v4    | 20000               |
| Standard_D48ds_v4    | 20000               | 
| Standard_D64ds_v4    | 20000               | 
| **Memoria optimizada** |                     | 
| Standard_E2ds_v4     | 3200                | 
| Standard_E4ds_v4     | 6400                | 
| Standard_ E8ds_v4    | 12800               | 
| Standard_ E16ds_v4   | 20000               | 
| Standard_E32ds_v4    | 20000               | 
| Standard_E48ds_v4    | 20000               | 
| Standard_E64ds_v4    | 20000               |  

El número máximo de IOPS depende del máximo de IOPS disponible por tamaño de proceso. Vea la columna *Rendimiento máximo del disco sin almacenamiento en la caché: IOPS/MBps* en la documentación de la [serie B](../../virtual-machines/sizes-b-series-burstable.md), [serie Ddsv4](../../virtual-machines/ddv4-ddsv4-series.md) y [serie Edsv4](../../virtual-machines/edv4-edsv4-series.md).

> [!Important]
> Las **IOPS complementarias** son iguales a MÍNIMO("rendimiento de disco no almacenado en caché máximo: IOPS/MBps" del tamaño de proceso, 300 + almacenamiento aprovisionado en GiB * 3)<br>
> El **número mínimo de IOPS** es de 360 en todos los tamaños de proceso.<br>
> El **número de IOPS máximo** viene determinado por el tamaño de proceso seleccionado. En la versión preliminar, el número máximo de IOPS admitido es de 20 000.

Puede supervisar el consumo de E/S en el Azure Portal (con Azure Monitor) mediante la métrica [Porcentaje de E/S](./concepts-monitoring.md). Si necesita más IOPS que el número máximo de IOPS basado en el proceso, debe escalar el proceso del servidor.

## <a name="backup"></a>Copia de seguridad

El servicio realiza automáticamente copias de seguridad del servidor. Puede seleccionar un período de retención de entre 1 y 35 días. Obtenga más información sobre las copias de seguridad en el [artículo sobre los conceptos de copia de seguridad y restauración](concepts-backup-restore.md).

## <a name="scale-resources"></a>Escalado de recursos

Después de crear el servidor, puede cambiar el nivel de proceso, el tamaño de proceso (núcleos virtuales y memoria) y la cantidad de almacenamiento y el período de retención de la copia de seguridad de manera independiente. El tamaño de proceso se puede escalar o reducir verticalmente. El período de retención de la copia de seguridad se puede escalar o reducir verticalmente de 1 a 35 días. El tamaño de almacenamiento solo se puede aumentar. El escalado de los recursos puede realizarse a través del portal o la CLI de Azure.

> [!NOTE]
> El tamaño de almacenamiento solo se puede aumentar. Tras aplicar el aumento del tamaño de almacenamiento, no puede volver a otro más pequeño.

Al cambiar el nivel o el tamaño de proceso, el servidor se reinicia para que se aplique el nuevo tipo de servidor. Durante el breve espacio de tiempo en que el sistema cambia al nuevo servidor, no se puede establecer ninguna nueva conexión y todas las transacciones no confirmadas se revierten. Este intervalo de tiempo varía, pero en la mayoría de los casos está entre 60 y 120 segundos. 

El escalado del almacenamiento y el cambio del período de retención de la copia de seguridad son operaciones en línea y no requieren el reinicio de un servidor.

## <a name="pricing"></a>Precios

Para conocer la información más actualizada sobre precios, consulte la [página de precios](https://azure.microsoft.com/pricing/details/MySQL/) del servicio. Para ver el coste de la configuración deseada, en [Azure Portal](https://portal.azure.com/#create/Microsoft.MySQLServer/flexibleServers) se muestra el coste mensual en la pestaña **Proceso + Almacenamiento** según las opciones que seleccione. Si no tiene una suscripción de Azure, puede usar la calculadora de precios de Azure para obtener un precio estimado. En el sitio web [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/), seleccione **Agregar elementos**, expanda la categoría **Bases de datos** y elija **Azure Database for MySQL** y **Servidor flexible** como tipo de implementación para personalizar las opciones.

Si quiere optimizar el coste del servidor, puede considerar las siguientes sugerencias:

- Reduzca verticalmente el nivel o el tamaño de proceso (núcleos virtuales) si el proceso está infrautilizado.
- Le recomendamos que cambie al nivel de proceso flexible si la carga de trabajo no necesita la capacidad de proceso completa de forma continua de los niveles De uso general y Optimizada para memoria.
- Detenga el servidor cuando no esté en uso.
- Reduzca el período de retención de la copia de seguridad si no se requiere una retención más larga de la copia de seguridad.

## <a name="next-steps"></a>Pasos siguientes

- Aprenda cómo [crear un servidor MySQL en el portal](quickstart-create-server-portal.md).
- Más información sobre las [limitaciones del servicio](concepts-limitations.md).
