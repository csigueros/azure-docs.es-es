---
title: 'Planes de tarifa: Azure Database for MySQL'
description: Obtenga información sobre los distintos planes de tarifa para Azure Database for MySQL, incluidas las generaciones de procesos, los tipos de almacenamiento, el tamaño de almacenamiento, los núcleos virtuales, la memoria y los períodos de retención de copias de seguridad.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: c9e726e53d40ac90aec6bbbaaf41399698b11209
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748207"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Planes de tarifa de Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Puede crear un servidor de Azure Database for MySQL en tres planes de tarifa diferentes: Básico, De uso general y Optimizado para memoria. Los planes de tarifa se diferencian por la cantidad de proceso en núcleos virtuales que se puede aprovisionar, la cantidad de memoria por núcleo virtual y la tecnología de almacenamiento usada para almacenar los datos. Todos los recursos se aprovisionan en el nivel de servidor MySQL. Un servidor puede tener una o varias bases de datos.

| Atributo   | **Basic** | **Uso general** | **Memoria optimizada** |
|:---|:----------|:--------------------|:---------------------|
| Generación de procesos | Gen 4, Gen 5 | Gen 4, Gen 5 | Gen 5 |
| Núcleos virtuales | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memoria por núcleo virtual | 2 GB | 5 GB | 10 GB |
| Tamaño de almacenamiento | De 5 GB a 1 TB | De 5 GB a 16 TB | De 5 GB a 16 TB |
| Período de retención de copias de seguridad de base de datos | De 7 a 35 días | De 7 a 35 días | De 7 a 35 días |

Para elegir un plan de tarifa, use la siguiente tabla como punto de partida.

| Plan de tarifa | Carga de trabajo objetivo |
|:-------------|:-----------------|
| Básica | Cargas de trabajo que requieren proceso y rendimiento de E/S ligeros. Algunos ejemplos son los servidores que se usan para desarrollo o prueba, o bien las aplicaciones a pequeña escala que se usan con poca frecuencia. |
| De uso general | La mayoría de las cargas de trabajo de empresa que requieren un equilibrio entre proceso y memoria con rendimiento de E/S escalable. Por ejemplo, servidores para hospedar aplicaciones web y móviles, y otras aplicaciones empresariales.|
| Memoria optimizada | Cargas de trabajo de base de datos de alto rendimiento que requieren rendimiento en memoria para un procesamiento de transacciones más rápido y una mayor simultaneidad. Por ejemplo, servidores para procesar datos en tiempo real y aplicaciones de análisis y transacciones de alto rendimiento.|

Después de crear un servidor, el número de núcleos virtuales, la generación de hardware y el plan de tarifa (excepto hacia y desde Básico) se puede aumentar o reducir en cuestión de segundos. También puede ajustar de forma independiente la cantidad de almacenamiento y aumentar o reducir el período de retención sin que las aplicaciones experimenten tiempo de inactividad. No puede cambiar el tipo de almacenamiento de copia de seguridad. Para más información, consulte la sección [Escalado de recursos](#scale-resources).

## <a name="compute-generations-and-vcores"></a>Procesar generaciones y núcleos virtuales

Los recursos de proceso se proporcionan como núcleos virtuales, que representan la CPU lógica del hardware subyacente. Este de China 1, Norte de China 1, US DoD (centro) y US DoD (este) usan CPU lógicas Gen 4 que se basan en los procesadores Intel E5-2673 v3 (Haswell) de 2,4 GHz. Todas las demás regiones utilizan CPU lógicas Gen 5 que se basan en procesadores Intel E5-2673 v4 (Broadwell) de 2,3 GHz.

## <a name="storage"></a>Storage

El almacenamiento que se aprovisiona es la cantidad de capacidad de almacenamiento disponible para el servidor de Azure Database for MySQL. El almacenamiento se usa para los archivos de base de datos, los archivos temporales, los registros de transacciones y los registros del servidor MySQL. La cantidad total de almacenamiento que se aprovisiona también define la capacidad de E/S disponible para su servidor.

Azure Database for MySQL: servidor único que admite el siguiente almacenamiento de back-end para los servidores. 

| Tipo de almacenamiento   | Básica | Uso general v1 | Uso general v2 |
|:---|:----------|:--------------------|:---------------------|
| Tamaño de almacenamiento | De 5 GB a 1 TB | De 5 GB a 4 TB | De 5 GB a 16 TB |
| Tamaño de incremento de almacenamiento | 1 GB | 1 GB | 1 GB |
| E/S | Variable |3 IOPS/GB<br/>100 IOPS mín.<br/>6000 IOPS máx. | 3 IOPS/GB<br/>100 IOPS mín.<br/>20 000 IOPS máx. |

>[!NOTE]
> El almacenamiento básico no ofrece garantía de IOPS. En el almacenamiento De uso general, las IOPS oscilan en un ratio de 3:1 con el tamaño de almacenamiento aprovisionado.

### <a name="basic-storage"></a>Almacenamiento básico 
El almacenamiento básico es el almacenamiento de back-end que admite los servidores de nivel de precio Básico. El almacenamiento básico aprovecha el almacenamiento estándar de Azure en el back-end, donde no se garantizan las IOPS aprovisionadas y la latencia es variable. El nivel Básico es más adecuado para cargas de trabajo que requieren proceso ligero, bajo coste y rendimiento de E/S para el desarrollo o aplicaciones de uso poco frecuente a pequeña escala.

### <a name="general-purpose-storage"></a>Almacenamiento de uso general 
El almacenamiento de uso general es el almacenamiento de back-end que admite el servidor De uso general y el servidor de nivel Optimizado para memoria. En el almacenamiento De uso general, las IOPS oscilan en un ratio de 3:1 con el tamaño de almacenamiento aprovisionado. Hay dos generaciones de almacenamiento de uso general, como se describe a continuación:

#### <a name="general-purpose-storage-v1-supports-up-to-4-tb"></a>Almacenamiento de uso general v1 (admite hasta 4 TB)
El almacenamiento de uso general v1 se basa en la tecnología de almacenamiento heredada, que puede admitir hasta 4 TB de almacenamiento y 6000 IOPS por servidor. El almacenamiento de uso general v1 está optimizado para aprovechar la memoria de los nodos de proceso que ejecutan el motor MySQL para el almacenamiento en caché local y las copias de seguridad. El proceso de copia de seguridad en el almacenamiento de uso general v1 lee los archivos de datos y registro en la memoria de los nodos de proceso y los copia en el almacenamiento de copia de seguridad de destino para su retención hasta 35 días. Como resultado, el consumo de memoria y E/S del almacenamiento durante las copias de seguridad es relativamente mayor. 

Todas las regiones de Azure admiten el almacenamiento de uso general v1

Para el servidor De uso general u Optimizado para memoria en el almacenamiento de uso general v1, le recomendamos que considere:

*   Planificar el nivel de SKU de proceso teniendo en cuenta un exceso de memoria del 10 al 30 % para el almacenamiento en caché y los búferes de copia de seguridad 
*   Aprovisionar un 10 % más IOPS de lo requerido por la carga de trabajo de la base de datos para tener en cuenta las E/S de copia de seguridad 
*   También puede migrar a un almacenamiento de uso general v2 que se describe a continuación y que admite hasta 16 TB si la infraestructura de almacenamiento subyacente está disponible en las regiones de Azure de su preferencia indicadas a continuación. 

#### <a name="general-purpose-storage-v2-supports-up-to-16-tb-storage"></a>Almacenamiento de uso general v2 (admite hasta 16 TB de almacenamiento)
El almacenamiento de uso general v2 está basado en la infraestructura de almacenamiento más reciente, que puede admitir hasta 16 TB y 20 000 IOPS. En un subconjunto de regiones de Azure donde la infraestructura está disponible, todos los servidores recién aprovisionados se encuentran en el almacenamiento de uso general v2 de manera predeterminada. El almacenamiento de uso general v2 no consume memoria del nodo de ejecución de MySQL y proporciona mejores latencias de E/S predecibles en comparación con el almacenamiento de uso general v1. Las copias de seguridad de los servidores de almacenamiento de uso general v2 están basadas en instantáneas sin sobrecarga adicional de E/S. En el almacenamiento de uso general v2, se espera que el rendimiento del servidor MySQL sea mayor en comparación con el almacenamiento de uso general v1 para el mismo almacenamiento e IOPS aprovisionados. No hay ningún coste adicional por el almacenamiento de uso general que admite almacenamiento de hasta 16 TB. Si necesita ayuda para migrar a un almacenamiento de 16 TB, abra una incidencia de soporte técnico en Azure Portal.

El almacenamiento de uso general v2 se admite en las siguientes regiones de Azure: 

| Region | Disponibilidad de almacenamiento de uso general v2 | 
| --- | --- | 
| Este de Australia | :heavy_check_mark: | 
| Sudeste de Australia | :heavy_check_mark: | 
| Sur de Brasil | :heavy_check_mark: | 
| Centro de Canadá | :heavy_check_mark: |
| Este de Canadá | :heavy_check_mark: |
| Centro de EE. UU. | :heavy_check_mark: | 
| Este de EE. UU. | :heavy_check_mark: | 
| Este de EE. UU. 2 | :heavy_check_mark: |
| Este de Asia | :heavy_check_mark: | 
| Japón Oriental | :heavy_check_mark: | 
| Japón Occidental | :heavy_check_mark: | 
| Centro de Corea del Sur | :heavy_check_mark: |
| Corea del Sur | :heavy_check_mark: |
| Norte de Europa | :heavy_check_mark: | 
| Centro-Norte de EE. UU | :heavy_check_mark: | 
| Centro-sur de EE. UU. | :heavy_check_mark: | 
| Sudeste de Asia | :heavy_check_mark: | 
| Sur de Reino Unido | :heavy_check_mark: | 
| Oeste de Reino Unido | :heavy_check_mark: | 
| Centro-Oeste de EE. UU. | :heavy_check_mark: | 
| Oeste de EE. UU. | :heavy_check_mark: | 
| Oeste de EE. UU. 2 | :heavy_check_mark: | 
| Oeste de Europa | :heavy_check_mark: | 
| Centro de la India* | :heavy_check_mark: | 
| Centro de Francia* | :heavy_check_mark: | 
| Norte de Emiratos Árabes Unidos* | :heavy_check_mark: | 
| Norte de Sudáfrica* | :heavy_check_mark: |

> [!Note] 
> *Regiones donde Azure Database for MySQL tiene Almacenamiento de uso general v2 en la versión preliminar pública. <br /> *Para estas regiones de Azure, tendrá la opción de crear un servidor en Almacenamiento de uso general v1 y v2. En el caso de los servidores creados con almacenamiento de uso general v2 en versión preliminar pública, estas son las limitaciones: <br /> 
> * No se admiten copias de seguridad con almacenamiento con redundancia geográfica.<br /> 
> * El servidor Réplica debe estar en las regiones que admiten el almacenamiento de uso general v2. <br /> 
    

### <a name="how-can-i-determine-which-storage-type-my-server-is-running-on"></a>¿Cómo puedo determinar en qué tipo de almacenamiento se ejecuta mi servidor?

Para encontrar el tipo de almacenamiento del servidor, vaya a la hoja Plan de tarifa del portal. 
* Si el servidor se aprovisiona mediante SKU básica, el tipo de almacenamiento es almacenamiento básico.
* Si el servidor se aprovisiona mediante SKU de uso general u optimizada para memoria, el tipo de almacenamiento es almacenamiento de uso general.
   *  Si el almacenamiento máximo que se puede aprovisionar en el servidor es de hasta 4 TB, el tipo de almacenamiento es almacenamiento de uso general v1.
   *  Si el almacenamiento máximo que se puede aprovisionar en el servidor es de hasta 16 TB, el tipo de almacenamiento es almacenamiento de uso general v2.

### <a name="can-i-move-from-general-purpose-storage-v1-to-general-purpose-storage-v2-if-yes-how-and-is-there-any-additional-cost"></a>¿Puedo pasar del almacenamiento de uso general v1 al almacenamiento de uso general v2? En caso afirmativo, ¿cómo se hace? ¿Hay algún coste adicional?
Sí, se admite la migración al almacenamiento de uso general v2 desde v1 si la infraestructura de almacenamiento subyacente está disponible en la región de Azure del servidor de origen. La migración y el almacenamiento v2 están disponibles sin coste adicional.

### <a name="can-i-grow-storage-size-after-server-is-provisioned"></a>¿Puedo aumentar el tamaño de almacenamiento después de aprovisionar el servidor?
Puede agregar capacidad de almacenamiento adicional durante y después de la creación del servidor y permitir que el sistema aumente el almacenamiento automáticamente en función del consumo de almacenamiento de su carga de trabajo. 

>[!IMPORTANT]
> El almacenamiento solo se puede escalar verticalmente, no reducir.

### <a name="monitoring-io-consumption"></a>Supervisión del consumo de E/S
Puede supervisar el consumo de E/S en Azure Portal o mediante los comandos de la CLI de Azure. Las métricas pertinentes que se van a supervisar son el [límite de almacenamiento, el porcentaje de almacenamiento, el almacenamiento usado y el porcentaje de E/S](concepts-monitoring.md). Las métricas de supervisión del servidor MySQL con almacenamiento de uso general v1 notifican la memoria y la E/S consumidas por el motor de MySQL, pero es posible que no capturen la memoria y el consumo de E/S de la capa de almacenamiento, lo cual es una limitación.

### <a name="reaching-the-storage-limit"></a>Alcance del límite de almacenamiento

Los servidores con un almacenamiento aprovisionado menor o igual a 100 GB se marcan como de solo lectura si el almacenamiento disponible es inferior al 5 % del tamaño del almacenamiento aprovisionado. Los servidores con más de 100 GB de almacenamiento aprovisionado se marcan como solo de lectura cuando el almacenamiento libre es inferior a 5 GB.

Por ejemplo, si ha aprovisionado 110 GB de almacenamiento y el uso real supera los 105 GB, el servidor se marca como de solo lectura. También, si ha aprovisionado 5 GB de almacenamiento, el servidor se marca como de solo lectura cuando quedan menos de 256 MB de almacenamiento disponible.

Mientras el servicio intenta hacer que el servidor sea de solo lectura, se bloquean todas las nuevas solicitudes de transacción de escritura, y las transacciones activas existentes continuarán ejecutándose. Cuando el servidor se establece en solo lectura, todas las operaciones de escritura y confirmaciones de transacción posteriores generarán errores. Las consultas de lectura seguirán funcionando sin interrupciones. Después de aumentar el almacenamiento aprovisionado, el servidor estará listo para aceptar las transacciones de escritura de nuevo.

Le recomendamos que active el almacenamiento automático o que configure una alerta que le envíe una notificación cada vez que su almacenamiento en servidor esté cerca del umbral para que pueda evitar entrar en el estado de solo lectura. Para obtener más información, consulte la documentación sobre [cómo configurar una alerta](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Crecimiento automático del almacenamiento

El crecimiento automático del almacenamiento impide que el servidor se quede sin almacenamiento y se vuelva de solo lectura. Si el crecimiento automático del almacenamiento está habilitado, el almacenamiento crece automáticamente sin afectar a la carga de trabajo. En cuanto a los servidores con un almacenamiento aprovisionado menor o igual a 100 GB, el tamaño del almacenamiento aprovisionado se incrementa en 5 GB cuando el almacenamiento disponible es inferior al 10 % del almacenamiento aprovisionado. En los servidores con más de 100 GB de almacenamiento aprovisionado, el tamaño del almacenamiento aprovisionado se incrementa en un 5 % cuando el espacio de almacenamiento disponible está por debajo de 10 GB del tamaño del almacenamiento aprovisionado. Se aplican los límites máximos de almacenamiento según lo especificado anteriormente.

Por ejemplo, si ha aprovisionado 1000 GB de almacenamiento y el uso real supera los 990 GB, el tamaño del almacenamiento del servidor se incrementa a 1050 GB. Como alternativa, si ha aprovisionado 10 GB de almacenamiento, el tamaño del almacenamiento aumenta a 15 GB cuando queda menos de 1 GB de almacenamiento.

Recuerde que el almacenamiento solo se puede escalar verticalmente, no reducir.

## <a name="backup-storage"></a>Almacenamiento de copia de seguridad 

Azure Database for MySQL proporciona hasta un 100 % del almacenamiento del servidor aprovisionado como almacenamiento de copia de seguridad, sin costos adicionales. El almacenamiento de copia de seguridad que use que supere esta cantidad se cobrará en GB por mes. Por ejemplo, si aprovisiona un servidor con 250 GB de almacenamiento, tiene 250 GB de almacenamiento adicional disponible para las copias de seguridad del servidor sin ningún cargo. El almacenamiento de copias de seguridad que supere los 250 GB se cobra según el [modelo de precios](https://azure.microsoft.com/pricing/details/mysql/). Para comprender los factores que influyen en el uso del almacenamiento de copia de seguridad, la supervisión y el control del costo del almacenamiento de copia de seguridad, puede consultar la [documentación de copia de seguridad](concepts-backup.md).

## <a name="scale-resources"></a>Escalado de recursos

Después de crear el servidor, puede cambiar los núcleos virtuales, la generación de hardware, el plan de tarifa (excepto hacia y desde Básico), la cantidad de almacenamiento y el período de retención de copia de seguridad de manera independiente. No puede cambiar el tipo de almacenamiento de copia de seguridad. El número de núcleos virtuales se pueden escalar o reducir verticalmente. El período de retención de copia de seguridad se puede escalar o reducir verticalmente de 7 a 35 días. El tamaño de almacenamiento solo se puede aumentar. El escalado de los recursos puede realizarse a través del portal o la CLI de Azure. Para ver un ejemplo de escalado con la CLI de Azure, consulte [Supervisión y escalado de un servidor de Azure Database for MySQL mediante la CLI de Azure](scripts/sample-scale-server.md).

Al cambiar el número de núcleos virtuales, la generación de hardware o el plan de tarifa, se crea una copia del servidor original con la nueva asignación de recursos de proceso. Una vez que el nuevo servidor está en funcionamiento, las conexiones se transfieren a él. Durante el breve espacio de tiempo en que el sistema cambia al nuevo servidor, no se puede establecer ninguna nueva conexión y todas las transacciones no confirmadas se revierten. Este tiempo de inactividad durante el escalado puede ser de aproximadamente 60-120 segundos. El tiempo de inactividad durante el escalado depende del tiempo de recuperación de la base de datos, lo que puede hacer que la base de datos se ponga en línea más tiempo si tiene mucha actividad transaccional en el servidor en el momento de la operación de escalado. Para evitar un tiempo de reinicio más largo, se recomienda realizar operaciones de escalado durante períodos de poca actividad transaccional en el servidor.

El escalado del almacenamiento y el cambio del período de retención de copia de seguridad son operaciones verdaderamente en línea. No hay ningún tiempo de inactividad y la aplicación no se ve afectada. A medida que el valor de IOPS se escala con el tamaño del almacenamiento aprovisionado, puede aumentar el número de IOPS disponibles para el servidor mediante el escalado vertical del almacenamiento.

## <a name="pricing"></a>Precios

Para conocer la información más actualizada sobre precios, consulte la [página de precios](https://azure.microsoft.com/pricing/details/mysql/) del servicio. Para ver el costo de la configuración deseada, en [Azure Portal](https://portal.azure.com/#create/Microsoft.MySQLServer) se muestra el costo mensual en la pestaña **Plan de tarifa** según las opciones que seleccione. Si no tiene una suscripción de Azure, puede usar la calculadora de precios de Azure para obtener un precio estimado. En el sitio web [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/), seleccione **Agregar elementos**, expanda la categoría **Bases de datos** y elija **Azure Database for MySQL** para personalizar las opciones.

## <a name="next-steps"></a>Pasos siguientes

- Aprenda cómo [crear un servidor MySQL en el portal](howto-create-manage-server-portal.md).
- Obtenga información acerca de los [límites de servicio](concepts-limits.md).
- Obtenga información sobre cómo [escalar horizontalmente con réplicas de lectura](howto-read-replicas-portal.md).
