---
title: 'Continuidad empresarial en Azure Database for PostgreSQL: servidor único'
description: En este artículo se describe la continuidad empresarial (restauración a un momento dado, interrupción del centro de datos, restauración geográfica, réplicas) al usar Azure Database for PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/07/2020
ms.openlocfilehash: 68e046d0a19af2d8a4f6a413b527c9bf49c0e6ec
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137146"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Introducción a la continuidad empresarial con Azure Database for PostgreSQL con un único servidor

En este artículo de introducción se describen las funcionalidades de continuidad empresarial y recuperación ante desastres que proporciona Azure Database for PostgreSQL. Conozca más información sobre opciones para recuperarse de eventos de interrupción que podrían provocar la pérdida de información o que su base de datos y aplicación dejen de estar disponibles. Sepa qué hacer en caso de que un error del usuario o la aplicación afecte a la integridad de los datos, se produzca una interrupción en una región de Azure o su aplicación requiera mantenimiento.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Características que puede utilizar para proporcionar continuidad empresarial

A medida que desarrolle el plan de continuidad empresarial, tendrá que saber el tiempo máximo aceptable para que la aplicación se recupere por completo tras un evento de interrupción. A esto se le denomina "objetivo de tiempo de recuperación" (RTO). También debe conocer la cantidad máxima de actualizaciones de datos recientes (intervalo de tiempo) que la aplicación puede tolerar perder al recuperarse después de un evento de interrupción. A esto se le conoce como "objetivo de punto de recuperación" (RPO).

Azure Database for PostgreSQL proporciona características de continuidad empresarial que incluyen copias de seguridad con redundancia geográfica con la capacidad de iniciar la restauración geográfica y la implementación de réplicas de lectura en otra región. Cada una de ellas posee distintas características que abarcan los conceptos de tiempo de recuperación y pérdida de datos potencial. Con la característica de [restauración geográfica](concepts-backup.md), se crea un servidor con los datos de copia de seguridad que se replican desde otra región. El tiempo total que se tarda en la restauración y la recuperación depende del tamaño de la base de datos y de la cantidad de registros que se van a recuperar. El tiempo total para establecer el servidor varía de unos minutos a pocas horas. Con las [réplicas de lectura](concepts-read-replicas.md), los registros de transacciones de la principal se transmiten de forma asincrónica a la réplica. En caso de que se produzca una interrupción de la base de datos principal debido a un error de nivel de zona o de región, la conmutación por error a la réplica proporciona un RTO más corto y una pérdida de datos reducida.

> [!NOTE]
> El intervalo entre la base de datos principal y la réplica depende de la latencia entre los sitios, la cantidad de datos que se van a transmitir y, sobre todo, de la carga de trabajo de escritura del servidor principal. Las cargas de trabajo de escritura intensivas pueden generar retrasos significativos. 
>
> Debido a la naturaleza asincrónica de la replicación que se usa para las réplicas de lectura, **no se deben** considerar una solución de alta disponibilidad, ya que los retrasos de mayor duración pueden significar un mayor RTO y RPO. Solo en el caso de las cargas de trabajo en las que el retraso sigue siendo poco en las horas de poca y mucha actividad de la carga de trabajo, las réplicas de lectura pueden actuar como una alternativa de alta disponibilidad. De lo contrario, las réplicas de lectura están diseñadas para una escala de lectura verdadera para las cargas de trabajo pesadas y para los escenarios de recuperación ante desastres.

En la tabla siguiente se comparan el RTO y el RPO en un escenario de **carga de trabajo típica**:

| **Funcionalidad** | **Basic** | **Uso general** | **Memoria optimizada** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Restauración a un momento dado a partir de una copia de seguridad | Cualquier punto de restauración dentro del período de retención <br/> RTO: varía <br/>RPO < 15 minutos| Cualquier punto de restauración dentro del período de retención <br/> RTO: varía <br/>RPO < 15 minutos | Cualquier punto de restauración dentro del período de retención <br/> RTO: varía <br/>RPO < 15 minutos |
| Restauración geográfica de las copias de seguridad con replicación geográfica | No compatible | RTO: varía <br/>RPO < 1 hora | RTO: varía <br/>RPO < 1 hora |
| Réplicas de lectura | RTO: minutos* <br/>RPO < 5 minutos* | RTO: minutos* <br/>RPO < 5 minutos*| RTO: minutos* <br/>RPO < 5 minutos*|

 \* El RTO y el RPO **pueden ser muy superiores** en algunos casos, en función de varios factores, como la latencia entre sitios, la cantidad de datos que se transmitirá y, sobre todo, la carga de trabajo de escritura de la base de datos principal. 

## <a name="recover-a-server-after-a-user-or-application-error"></a>Recuperación de un servidor tras un error del usuario o la aplicación

Puede utilizar las copias de seguridad del servicio para recuperar un servidor a partir de diversos eventos de interrupción. Un usuario podría eliminar de forma involuntaria algunos datos, una tabla importante o, incluso, una base de datos entera. O bien, una aplicación podría sobrescribir accidentalmente datos correctos por otros no válidos debido a un defecto en esta, etc.

Puede realizar una **restauración a un momento dado** para crear una copia del servidor en un momento dado conocido correcto. Este momento dado debe estar incluido en el período de retención de la copia de seguridad que configuró para el servidor. Después de restaurar los datos en el nuevo servidor, puede reemplazar el servidor original por el servidor recientemente restaurado o copiar los datos necesarios del servidor restaurado en el servidor original.

Se recomienda que use el [bloqueo de recursos de Azure](../azure-resource-manager/management/lock-resources.md) para evitar la eliminación accidental del servidor. Si eliminó accidentalmente el servidor, es posible que pueda restaurarlo si la eliminación se produjo en los últimos 5 días. Para más información, consulte [Restauración de un servidor descartado de Azure Database for PostgreSQL](howto-restore-dropped-server.md).

## <a name="recover-from-an-azure-data-center-outage"></a>Recuperación de una interrupción del centro de datos de Azure

Aunque es poco habitual, en los centros de datos de Azure pueden producirse interrupciones. Cuando esto ocurre, provoca también una interrupción en el negocio que podría extenderse solo unos pocos minutos o, incluso, horas.

Una opción consiste en esperar a que el servidor vuelva a estar en línea cuando termine la interrupción del centro de datos. Esta opción es válida para aquellas aplicaciones que se pueden permitir tener el servidor sin conexión durante un período de tiempo, por ejemplo las aplicaciones de un entorno de producción. Cuando se produce una interrupción en un centro de datos, no sabe cuánto durará, por lo que esta opción solo es útil si no necesita utilizar el servidor durante un tiempo.

## <a name="geo-restore"></a>Geo-restore

La característica de restauración geográfica restaura el servidor mediante copias de seguridad con redundancia geográfica. Las copias de seguridad se hospedan en la [región emparejada](../best-practices-availability-paired-regions.md) del servidor. Puede realizar la restauración a partir de estas copias de seguridad en cualquier otra región. La restauración geográfica crea un nuevo servidor con los datos de las copias de seguridad. Obtenga más información sobre la restauración geográfica en el [artículo sobre los conceptos de copia de seguridad y restauración](concepts-backup.md).

> [!IMPORTANT]
> La restauración geográfica solo es posible si se ha aprovisionado el servidor con almacenamiento de copia de seguridad con redundancia geográfica. Si desea cambiar de copias de seguridad con redundancia local a copias de seguridad con redundancia geográfica para un servidor existente, debe realizar un volcado mediante pg_dump del servidor existente y restaurarlo en un servidor recién creado, configurado con copias de seguridad con redundancia geográfica.

## <a name="cross-region-read-replicas"></a>Réplicas de lectura entre regiones
Puede usar réplicas de lectura entre regiones para mejorar el planeamiento de la continuidad empresarial y recuperación ante desastres. Las réplicas de lectura se actualizan de manera asincrónica mediante la tecnología de replicación física de PostgreSQL y pueden generar retrasos en la principal. Obtenga más información sobre las réplicas de lectura, las regiones disponibles y la conmutación por error en el [artículo sobre los conceptos de las réplicas de lectura](concepts-read-replicas.md). 

## <a name="faq"></a>Preguntas más frecuentes
### <a name="where-does-azure-database-for-postgresql-store-customer-data"></a>¿Dónde Azure Database for PostgreSQL almacena los datos de los clientes?
De manera predeterminada Azure Database for PostgreSQL no mueve ni almacena los datos de los clientes fuera de la región en la que se ha implementado. Sin embargo, los clientes tienen la opción de elegir habilitar las [copias de seguridad con redundancia geográfica](concepts-backup.md#backup-redundancy-options) o crear [réplicas de lectura entre regiones](concepts-read-replicas.md#cross-region-replication) para almacenar los datos en otra región.


## <a name="next-steps"></a>Pasos siguientes
- Obtenga más información acerca de las [copias de seguridad automatizadas en Azure Database for PostgreSQL](concepts-backup.md). 
- Obtenga información sobre cómo restaurar mediante [Azure Portal](howto-restore-server-portal.md) o la [CLI de Azure](howto-restore-server-cli.md).
- Obtenga información sobre las [réplicas de lectura en Azure Database for PostgreSQL](concepts-read-replicas.md).
