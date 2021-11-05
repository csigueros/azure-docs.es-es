---
title: Grupos de conmutación por error automática
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Los grupos de conmutación por error automática permiten administrar la replicación geográfica y la conmutación por error automática o coordinada de un grupo de bases de datos en un servidor o de todas las bases de datos en una instancia administrada.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma
ms.date: 10/25/2021
ms.openlocfilehash: 8977af3468ce861efb9a2e97048b681c2b41d76d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069546"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-geo-failover-of-multiple-databases"></a>Uso de grupos de conmutación por error automática para permitir la conmutación por error geográfica de varias bases de datos de manera transparente y coordinada
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

La característica de los grupos de conmutación por error automática permite administrar la replicación y la conmutación por error en otra región de un grupo de bases de datos de un servidor o de todas las bases de datos de una instancia administrada. Se trata de una abstracción declarativa sobre la característica de [replicación geográfica activa](active-geo-replication-overview.md) existente, diseñada para simplificar la implementación y administración de bases de datos con replicación geográfica a escala. Puede iniciar la conmutación por error geográfica manualmente o puede delegarla en el servicio de Azure según una directiva definida por el usuario. La última opción le permite recuperar automáticamente varias bases de datos relacionadas en una región secundaria después de errores catastróficos u otros eventos no planeados que generen una pérdida total o parcial de la disponibilidad de SQL Database o Instancia administrada de SQL en la región primaria. Un grupo de conmutación por error puede incluir una o varias bases de datos, utilizadas normalmente por la misma aplicación. Además, puede usar las bases de datos secundarias legibles para descargar las cargas de trabajo de consulta de solo lectura.

> [!NOTE]
> Los grupos de conmutación por error automática admiten la replicación geográfica de todas las bases de datos en el grupo solo a una instancia o un servidor secundario en otra región. Si necesita crear varias réplicas geográficas secundarias de Azure SQL Database (en la misma o distintas regiones) para la misma réplica principal, use la [replicación geográfica activa](active-geo-replication-overview.md).
> 
> Los grupos elásticos no se admiten en el nivel de servicio [Hiperescala](service-tier-hyperscale.md). Para la conmutación por error geográfica de una base de datos de Hiperescala, use la [replicación geográfica activa](active-geo-replication-overview.md).

Cuando se usan grupos de conmutación por error automática con una directiva de conmutación automática por error, una interrupción que afecte a una o varias de las bases de datos del grupo tendrá como resultado una conmutación por error geográfica de manera automática. Normalmente, se trata de interrupciones que la infraestructura de alta disponibilidad integrada no puede mitigar automáticamente. Entre los ejemplos de desencadenadores de conmutación por error geográfica se incluyen los incidentes causados por un anillo de inquilino de SQL Database o un anillo de control que está inactivo debido a una fuga de memoria del kernel del sistema operativo en nodos de proceso, o bien los incidentes provocados por uno o varios anillos de inquilino que están inactivos porque se cortó accidentalmente un cable de red incorrecto durante la retirada rutinaria de hardware. Para más información, consulte [Alta disponibilidad y Azure SQL Database](high-availability-sla.md).

Además, los grupos de conmutación por error automática proporcionan puntos de conexión de clientes de escucha de lectura y escritura y de solo lectura que no se modifican durante las conmutaciones por error geográficas. Por lo tanto, ya sea que use la activación de conmutación por error automática o manual, la conmutación por error geográfica cambia todas las bases de datos secundarias del grupo al rol principal. Después de que la conmutación por error geográfica finaliza, el registro de DNS se actualiza automáticamente para redirigir los puntos de conexión a la nueva región. Para obtener información sobre RPO y RTO de la conmutación por error geográfica, consulte [Introducción a la continuidad empresarial](business-continuity-high-availability-disaster-recover-hadr-overview.md).

Cuando se usan grupos de conmutación por error automática con la directiva de conmutación automática por error, una interrupción que afecte a las bases de datos del servidor o de la instancia administrada tiene como resultado una conmutación por error geográfica de manera automática. 

Puede administrar el grupo de conmutación por error automática mediante:

- [Azure Portal](geo-distributed-application-configure-tutorial.md)
- [CLI de Azure: grupo de conmutación por error](scripts/add-database-to-failover-group-cli.md)
- [PowerShell: grupo de conmutación por error](scripts/add-database-to-failover-group-powershell.md)
- [API REST: grupo de conmutación por error](/rest/api/sql/failovergroups)

Al configurar un grupo de conmutación por error, asegúrese de que la autenticación y el acceso a la red de la base de datos secundaria estén configuradas para funcionar correctamente después de la conmutación por error geográfica, cuando la base de datos secundaria geográfica se convierte en la nueva principal. Para obtener más información, consulte [Administración de la seguridad de Azure SQL Database después de la recuperación ante desastres](active-geo-replication-security-configure.md).

Para lograr una continuidad empresarial completa, agregar redundancia de base de datos regional es solo una parte de la solución. Para recuperar una aplicación (un servicio) de un extremo a otro tras un error catastrófico, es necesario recuperar todos los componentes que constituyen el servicio y cualquier servicio dependiente. Algunos ejemplos de estos componentes son el software cliente (por ejemplo, un explorador con JavaScript personalizado), los front-end web, el almacenamiento y DNS. Es fundamental que todos los componentes sean resistentes a los mismos errores y que estén disponibles en el plazo del objetivo de tiempo de recuperación (RTO) de la aplicación. Por lo tanto, debe identificar todos los servicios dependientes y comprender las garantías y capacidades que ofrecen. A continuación, debe seguir los pasos adecuados para asegurarse de que el servicio funcione durante la conmutación por error de los servicios de los que depende. Para más información sobre el diseño de soluciones para la recuperación ante desastres, consulte [Diseño de soluciones de nube para la continuidad empresarial mediante la replicación geográfica activa](designing-cloud-solutions-for-disaster-recovery.md).

## <a name="failover-group-terminology-and-capabilities"></a><a name="terminology-and-capabilities"></a> Terminología y funcionalidades del grupo de conmutación por error

- **Grupo de conmutación por error (FOG)**

  Un grupo de conmutación por error es un grupo con nombre de bases de datos administradas por un único servidor o en una instancia administrada que puede conmutar por error como una unidad a otra región en caso de que algunas o todas las bases de datos principales estén deshabilitadas debido a una interrupción en la región primaria. Cuando se crea para Instancia administrada de SQL, un grupo de conmutación por error contiene todas las bases de datos de usuario de la instancia, por lo que solo se puede configurar un grupo de conmutación por error en una instancia.
  
  > [!IMPORTANT]
  > El nombre del grupo de conmutación por error debe ser único globalmente en el dominio `.database.windows.net`.

- **Servidores**

  Algunas o todas las bases de datos de usuario de un servidor lógico pueden colocarse en un grupo de conmutación por error. Además, un servidor admite varios grupos de conmutación por error en un único servidor.

- **Principal**

  Servidor o instancia administrada que hospeda las bases de datos principales del grupo de conmutación por error.

- **Secundario**

  Servidor o instancia administrada que hospeda las bases de datos secundarias del grupo de conmutación por error. La base de datos secundaria no puede estar en la misma región que la principal.

- **Incorporación de bases de datos únicas a un grupo de conmutación por error**

  Puede poner varias bases de datos únicas en el mismo servidor e incluirlas en el mismo grupo de conmutación por error. Si agrega una base de datos única al grupo de conmutación por error, automáticamente se creará una base de datos secundaria con la misma edición y el mismo tamaño de proceso del servidor secundario. Ese servidor es el que especificó cuando creó el grupo de conmutación por error. Si agrega una base de datos que ya tenga una base de datos secundaria en el servidor secundario, el grupo heredará el vínculo de replicación geográfica. Cuando se agrega una base de datos que ya tiene una base de datos secundaria en un servidor que no forma parte del grupo de conmutación por error, se crea otra base de datos secundaria en el servidor secundario.

  > [!IMPORTANT]
  > Asegúrese de que el servidor secundario no tiene una base de datos con el mismo nombre, a menos que sea una base de datos secundaria existente. En los grupos de conmutación por error de Instancia administrada de SQL se replican todas las bases de datos de usuario. No se puede seleccionar un subconjunto de bases de datos de usuario para la replicación en el grupo de conmutación por error.

- **Incorporación de bases de datos de un grupo elástico a un grupo de conmutación por error**

  Puede poner varias bases de datos en el mismo grupo elástico e incluirlas en el mismo grupo de conmutación por error. Si la base de datos principal está en un grupo elástico, la base de datos secundaria se creará automáticamente en un grupo elástico con el mismo nombre (grupo secundario). Debe asegurarse de que el servidor secundario contiene un grupo elástico con el mismo nombre exacto y suficiente capacidad libre para hospedar las bases de datos secundarias que va a crear el grupo de conmutación por error. Si agrega una base de datos en un grupo que ya tiene una base de datos secundaria en el grupo secundario, el grupo heredará el vínculo de replicación geográfica. Cuando se agrega una base de datos que ya tiene una base de datos secundaria en un servidor que no forma parte del grupo de conmutación por error, se crea otra base de datos secundaria en el grupo secundario.
  
- **Propagación inicial**

  Al agregar bases de datos, grupos elásticos o instancias administradas a un grupo de conmutación por error, hay una fase de propagación inicial antes de que se inicie la replicación de datos. Esta fase de propagación inicial es la operación más larga y costosa. Una vez completada, se sincronizan los datos y, luego, solo se replican los cambios de datos posteriores. El tiempo que tarda la propagación inicial en completarse depende del tamaño de sus datos, la cantidad de bases de datos replicadas, la carga en las bases de datos primarias y la velocidad del enlace entre la primaria y la secundaria. En circunstancias normales, la velocidad de inicialización típica es de hasta 500 GB por hora para SQL Database, y de hasta 360 GB por hora en el caso de SQL Managed Instance. La propagación se realiza en paralelo para todas las bases de datos.

  En el caso de SQL Managed Instance, al estimar el tiempo de la fase de inicialización inicial, tenga en cuenta la velocidad del vínculo de ExpressRoute entre las dos instancias. Si la velocidad del vínculo entre las dos instancias es más lenta de lo necesario, es probable que el tiempo de inicialización resulte muy afectado. Puede usar la velocidad de propagación indicada, el número de bases de datos, el tamaño total de los datos y la velocidad del vínculo para calcular cuánto tardará la fase de propagación inicial antes de que se inicie la replicación de los datos. Por ejemplo, en el caso de una sola base de datos de 100 GB, la fase de inicialización inicial tardaría aproximadamente 1,2 horas si el vínculo es capaz de insertar 84 GB por hora y si no hay otras bases de datos que se vayan a inicializar. Si el vínculo solo puede transferir 10 GB por hora, la propagación de una base de datos de 100 GB tardará aproximadamente 10 horas. Si hay que replicar varias bases de datos, la propagación se ejecutará en paralelo y, cuando se combina con una velocidad de vínculo baja, la fase de propagación inicial puede tardar mucho más tiempo, en especial si la propagación paralela de los datos de todas las bases de datos supera el ancho de banda de vínculo disponible. Si el ancho de banda de red entre dos instancias es limitado y va a agregar varias instancias administradas a un grupo de conmutación por error, considere la posibilidad de hacerlo de manera secuencial, una por una. Dada una SKU de puerta de enlace con el tamaño adecuado entre las dos instancias administradas, y si el ancho de banda de la red corporativa lo permite, se pueden lograr velocidades de hasta 360 GB.  

- **Zona DNS**

  Identificador único que se genera automáticamente cuando se crea una Instancia administrada de SQL. Se aprovisiona un certificado de varios dominios (SAN) para esta instancia a fin de autenticar las conexiones de cliente a cualquier instancia de la misma zona DNS. Las dos instancias administradas en el mismo grupo de conmutación por error deben compartir la zona DNS.
  
  > [!NOTE]
  > No se requiere un id. de zona DNS o no se usa para los grupos de conmutación por error creados para SQL Database.

- **Agente de escucha de lectura-escritura de grupo de conmutación por error**

  Un registro CNAME de DNS que apunta a la base de datos principal actual. Se crea automáticamente cuando se crea el grupo de conmutación por error y permite que la carga de trabajo de lectura y escritura se vuelva a conectar de forma transparente a la principal cuando esta cambia después de la conmutación por error. Cuando se crea el grupo de conmutación por error en un servidor, el registro CNAME de DNS para la dirección URL del cliente de escucha tiene el formato `<fog-name>.database.windows.net`. Cuando se crea el grupo de conmutación por error en Instancia administrada de SQL, el registro CNAME de DNS para la dirección URL del cliente de escucha tiene el formato `<fog-name>.<zone_id>.database.windows.net`.

- **Agente de escucha de solo lectura de grupo de conmutación por error**

  Un registro CNAME de DNS que apunta a la base de datos secundaria actual. Se crea automáticamente cuando se crea el grupo de conmutación por error y permite que la carga de trabajo de SQL de solo lectura se vuelva a conectar de forma transparente a la secundaria cuando la secundaria cambia después de la conmutación por error. Cuando se crea el grupo de conmutación por error en un servidor, el registro CNAME de DNS para la dirección URL del cliente de escucha tiene el formato `<fog-name>.secondary.database.windows.net`. Cuando se crea el grupo de conmutación por error en Instancia administrada de SQL, el registro CNAME de DNS para la dirección URL del cliente de escucha tiene el formato `<fog-name>.secondary.<zone_id>.database.windows.net`.

- **Directiva de conmutación por error automática**

  De manera predeterminada, un grupo de conmutación por error se configura con una directiva de conmutación por error automática. El sistema desencadena una conmutación por error geográfica una vez detectado el error y si el período de gracia ha expirado. El sistema debe comprobar que la interrupción no se pueda mitigar mediante la [infraestructura de alta disponibilidad](high-availability-sla.md) integrada, por ejemplo, debido a la escala del impacto. Si desea controlar el flujo de trabajo de la conmutación por error geográfica desde la aplicación o manualmente, puede desactivar la directiva de conmutación automática por error.
  
  > [!NOTE]
  > Dado que la comprobación de la escala de la interrupción y la rapidez con que se puede mitigar conllevan acciones humanas, el período de gracia no se puede establecer por debajo de una hora. Esta limitación se aplica a todas las bases de datos del grupo de conmutación por error, independientemente de su estado de sincronización de datos.

- **Directiva de conmutación por error de solo lectura**

  De forma predeterminada, se deshabilita la conmutación por error del agente de escucha de solo lectura. Se asegura de que el rendimiento de la réplica principal no se ve afectado cuando la base de datos secundaria está sin conexión. En cambio, también significa que las sesiones de solo lectura no podrán conectarse hasta que se recupere la base de datos secundaria. Si no puede tolerar tiempo de inactividad en las sesiones de solo lectura pero sí, usar la base de datos principal para el tráfico de solo lectura y el de lectura y escritura a costa de la posible degradación del rendimiento de esta, puede habilitar la conmutación por error para el agente de escucha de solo lectura mediante la configuración de la propiedad `AllowReadOnlyFailoverToPrimary`. En ese caso, el tráfico de solo lectura se redirigirá automáticamente a la base de datos principal si la secundaria no está disponible.

  > [!NOTE]
  > La propiedad `AllowReadOnlyFailoverToPrimary` solo surte efecto si la directiva de conmutación automática por error está habilitada y si se ha desencadenado una conmutación por error geográfica de manera automática. En ese caso, si la propiedad se establece en true, la nueva base de datos principal atenderá a las sesiones de lectura y escritura, y de solo lectura.

- **Conmutación por error planeada**

  La conmutación por error planeada realiza una sincronización de datos completa entre las bases de datos principales y secundarias antes de que el rol principal pase a la secundaria. De esta manera se garantiza que no hay pérdida de datos. La conmutación por error planeada se usa en los siguientes escenarios:

  - Realizar simulacros de recuperación ante desastres (DR) en producción cuando no es aceptable la pérdida de datos
  - Reubicar las bases de datos a una región diferente
  - Devolver las bases de datos a la región primaria después de que se ha corregido la interrupción (conmutación por recuperación)

- **Conmutación por error no planeada**

  La conmutación por error no planeada o forzada cambia inmediatamente el rol secundario al rol primario sin tener que esperar a que los cambios recientes se propaguen desde el rol primario. Esta operación puede ocasionar pérdida de datos. La conmutación por error no planeada se usa como método de recuperación durante las interrupciones cuando no es posible acceder a la base de datos principal. Cuando se mitiga la interrupción, la base de datos principal anterior se vuelve a conectar automáticamente y se convierte en una nueva base de datos secundaria. Asimismo, se puede ejecutar una conmutación por error planeada para realizar una conmutación por recuperación y devolver las réplicas a sus roles principal y secundario originales.

- **Conmutación por error manual**

  Puede iniciar manualmente una conmutación por error geográfica en cualquier momento, independientemente de la configuración de la conmutación automática por error. Durante una interrupción que afecta a la principal, si la directiva de conmutación automática por error no está configurada, se requiere una conmutación por error manual para promover la base de datos secundaria al rol principal. Puede iniciar una conmutación por error forzada (no planeada) o que sea sencilla (planeada). Una conmutación por error sencilla solo es posible cuando la base de datos principal anterior es accesible y se puede usar para reubicar la región principal en la secundaria sin perder los datos. Cuando se completa una conmutación por error, los registros de DNS se actualizan automáticamente para garantizar la conectividad a la nueva base de datos principal.

- **Período de gracia con pérdida de datos**

  Como las bases de datos secundarias se sincronizan con la replicación asincrónica, la conmutación por error geográfica de manera automática puede provocar la pérdida de datos. Puede personalizar la directiva de conmutación por error automática para que refleje la tolerancia de la aplicación a la pérdida de datos. Si configura `GracePeriodWithDataLossHours`, puede controlar durante cuánto tiempo espera el sistema antes de iniciar la conmutación por error forzada, aunque es probable que genere una pérdida de datos.

- **Varios grupos de conmutación por error**

  Puede configurar varios grupos de conmutación por error para el mismo par de servidores con el fin de controlar el ámbito de las conmutaciones por error geográficas. Cada grupo realiza la conmutación por error por separado. Si la aplicación de inquilino por base de datos se implementa en varias regiones y usa grupos elásticos, puede usar esta funcionalidad para combinar las bases de datos principales y secundarias en cada grupo. De este modo, puede reducir el impacto de una interrupción a solo algunas bases de datos de inquilino.

  > [!NOTE]
  > Instancia administrada de SQL no admite varios grupos de conmutación por error.
  
## <a name="permissions"></a>Permisos

Los permisos para un grupo de conmutación por error se administran a través del [control de acceso basado en rol de Azure (Azure RBAC)](../../role-based-access-control/overview.md). El rol [Colaborador de SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor) tiene todos los permisos necesarios para administrar grupos de conmutación por error.

### <a name="create-a-failover-group"></a><a name="create-failover-group"></a> Creación de un grupo de conmutación por error

Para crear un grupo de conmutación por error, necesita acceso de escritura de Azure RBAC a los servidores principales y secundarios, así como a todas las bases de datos del grupo de conmutación por error. Para una Instancia administrada de SQL, necesita acceso de escritura de Azure RBAC tanto a la instancia principal como a la secundaria de SQL Managed Instance, pero los permisos para las bases de datos individuales no son relevantes, puesto que no pueden agregarse bases de datos individuales de SQL Managed Instance a un grupo de conmutación por error ni eliminarse de este.

### <a name="update-a-failover-group"></a>Actualización de un grupo de conmutación por error

Para actualizar un grupo de conmutación por error, necesita acceso de escritura de Azure RBAC para el grupo de conmutación por error y para todas las bases de datos del servidor principal actual o instancia administrada.  

### <a name="fail-over-a-failover-group"></a>Conmutación por error de un grupo de conmutación por error

Para conmutar por error un grupo de conmutación por error, necesita acceso de escritura de Azure RBAC para el grupo de conmutación por error del nuevo servidor principal o instancia administrada.

## <a name="failover-group-best-practices-for-sql-database"></a><a name="best-practices-for-sql-database"></a> Procedimientos recomendados para los grupos de conmutación por error de SQL Database

El grupo de conmutación por error automática debe estar configurado en el servidor principal y se conectará al servidor secundario de una región de Azure diferente. Los grupos pueden incluir todas las bases de datos de estos servidores o algunas de ellas. En el siguiente diagrama se ilustra una configuración típica de una aplicación de nube con redundancia geográfica que usa varias bases de datos y un grupo de conmutación por error automática.

![En el siguiente diagrama se muestra una configuración típica de una aplicación de nube con redundancia geográfica que usa varias bases de datos y un grupo de conmutación por error automática.](./media/auto-failover-group-overview/auto-failover-group.png)

> [!NOTE]
> Consulte [Adición de SQL Database a un grupo de conmutación por error](failover-group-add-single-database-tutorial.md) para obtener un tutorial detallado paso a paso sobre la adición de una base de datos de SQL Database a un grupo de conmutación por error.

Al diseñar un servicio teniendo en cuenta la continuidad empresarial, siga estas directrices:

### <a name="use-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a><a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a> Use uno o varios grupos de conmutación por error para administrar la conmutación por error de varias bases de datos

Se pueden crear uno o más grupos de conmutación por error entre dos servidores en regiones distintas (servidor principal y servidor secundario). Cada grupo puede incluir una o varias bases de datos que se recuperan como unidad en caso de que una o todas las bases de datos principales dejen de estar disponibles debido a una interrupción en la región primaria. La creación de un grupo de conmutación por error crea bases de datos geográficas secundarias con el mismo objetivo de servicio que la principal. Si agrega una relación de replicación geográfica existente a un grupo de conmutación por error, asegúrese de que la base de datos geográfica secundaria esté configurada con el mismo nivel de servicio y tamaño de proceso que la principal.
  
### <a name="use-the-read-write-listener-to-connect-to-primary"></a><a name="using-read-write-listener-for-oltp-workload"></a> Uso del cliente de escucha de lectura y escritura para conectarse a la principal

Para cargas de trabajo de lectura y escritura, use `<fog-name>.database.windows.net` como nombre del servidor en la cadena de conexión. Las conexiones se dirigirán automáticamente a la principal. Este nombre no cambia después de la conmutación por error. Tenga en cuenta que la conmutación por error implica actualizar el registro DNS para que las conexiones de cliente se redirijan a la nueva base de datos principal cuando la caché DNS del cliente se haya actualizado.

### <a name="use-the-read-only-listener-to-connect-to-geo-secondary"></a><a name="using-read-only-listener-for-read-only-workload"></a> Uso del cliente de escucha de solo lectura para conectarse a la base de datos secundaria geográfica

Si tiene cargas de trabajo de solo lectura aisladas lógicamente que son tolerantes a la latencia de datos, puede ejecutarlas en la base de datos secundaria geográfica. Para sesiones de solo lectura, use `<fog-name>.secondary.database.windows.net` como nombre del servidor en la cadena de conexión. Las conexiones se dirigirán automáticamente a la secundaria geográfica. También se recomienda indicar la intención de lectura en la cadena de conexión mediante `ApplicationIntent=ReadOnly`.

> [!NOTE]
> En los niveles de servicio Prémium, Crítico para la empresa e Hiperescala, SQL Database admite el uso de [réplicas de solo lectura](read-scale-out.md) para descargar las cargas de trabajo de consulta de solo lectura, mediante el parámetro `ApplicationIntent=ReadOnly` de la cadena de conexión. Cuando se ha configurado una base de datos secundaria geográfica, puede usar esta funcionalidad para conectarse a una réplica de solo lectura de la ubicación principal o de la ubicación con replicación geográfica.
>
> - Para conectarse a una réplica de solo lectura en la ubicación principal, use `ApplicationIntent=ReadOnly` y `<fog-name>.database.windows.net`.
> - Para conectarse a una réplica de solo lectura en la ubicación secundaria, use `ApplicationIntent=ReadOnly`y `<fog-name>.secondary.database.windows.net`.

### <a name="potential-performance-degradation-after-geo-failover"></a><a name="preparing-for-performance-degradation"></a> Posible degradación del rendimiento después de la conmutación por error geográfica

Una aplicación de Azure típica usa varios servicios de Azure y consta de varios componentes. La conmutación por error geográfica de manera automática del grupo de conmutación por error se desencadena en función del estado de los componentes de Azure SQL. Es posible que otros servicios de Azure de la región primaria no se vean afectados por la interrupción y que sus componentes sigan estando disponibles en esa región. Una vez que las bases de datos principales cambien a la región secundaria de DR, la latencia entre los componentes dependientes puede aumentar. Para evitar el impacto de una mayor latencia en el rendimiento de la aplicación, asegúrese de la redundancia de todos los componentes de la aplicación en la región de DR, siga estas [directrices de seguridad de red](#failover-groups-and-network-security) y organice la conmutación por error geográfica de los componentes de aplicación pertinentes junto con la base de datos.

### <a name="potential-data-loss-after-geo-failover"></a><a name="preparing-for-data-loss"></a> Posible pérdida de datos después de la conmutación por error geográfica

Si se produce una interrupción en la región primaria, es posible que las transacciones recientes no se puedan replicar en la secundaria geográfica. Si se configura la directiva de conmutación automática por error, el sistema espera el período especificado por `GracePeriodWithDataLossHours` antes de iniciar una conmutación por error geográfica de manera automática. El valor predeterminado es 1 hora. Esto favorece la disponibilidad de la base de datos en lugar de la pérdida de datos. Establecer `GracePeriodWithDataLossHours` en un número mayor, como 24 horas, o deshabilitar la conmutación por error geográfica de manera automática le permite reducir la probabilidad de perder datos a costa de la disponibilidad de la base de datos.

> [!IMPORTANT]
> Los grupos elásticos con 800 o menos DTU, 8 o menos núcleos virtuales y más de 250 bases de datos pueden encontrar problemas, como conmutaciones por error planeadas más prolongadas y un menor rendimiento. Es más probable que estos problemas sucedan con cargas de trabajo intensivas de escritura, cuando las replicas geográficas están muy separadas por región geográfica, o cuando se utilizan varias réplicas geográficas secundarias para cada base de datos. Un síntoma de estos problemas es un aumento del retraso de replicación geográfica a lo largo del tiempo, lo que puede provocar una pérdida de datos más amplia en una interrupción. Este retardo puede supervisarse con [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database). Si se producen estos problemas, la mitigación incluye escalar verticalmente el grupo para tener más DTU o núcleos virtuales, o bien reducir el número de bases de datos con replicación geográfica del grupo.

### <a name="change-the-secondary-region-of-a-failover-group"></a><a name="changing-secondary-region-of-the-failover-group"></a> Cambio de la región secundaria de un grupo de conmutación por error

Para ilustrar la secuencia de cambios, supondremos que el servidor A es el principal, el servidor B es el servidor secundario existente y el servidor C es la nueva instancia secundaria de la tercera región. Para realizar la transición, siga estos pasos:

1. Cree instancias secundarias adicionales de cada base de datos del servidor A en el servidor C mediante la [replicación geográfica activa](active-geo-replication-overview.md). Cada base de datos del servidor A tendrá dos secundarios, uno en el servidor B y otro en el servidor C. Esto garantizará que las bases de datos principales permanezcan protegidas durante la transición.
2. Elimine el grupo de conmutación por error. En este momento, se produce un error en los intentos de inicio de sesión que usan los puntos de conexión del grupo de conmutación por error.
3. Vuelva a crear el grupo de conmutación por error con el mismo nombre entre los servidores A y C.
4. Agregue todas las bases de datos principales del servidor A al nuevo grupo de conmutación por error. En este momento, se dejarán de producir errores en los intentos de inicio de sesión.
5. Elimine el servidor B. Todas las bases de datos de dicho servidor se eliminarán automáticamente.

### <a name="change-the-primary-region-of-a-failover-group"></a><a name="changing-primary-region-of-the-failover-group"></a> Cambio de la región primaria de un grupo de conmutación por error

Para ilustrar la secuencia de cambios, supondremos que el servidor A es el servidor principal, el servidor B es el servidor secundario existente y el servidor C es la nueva instancia principal de la tercera región. Para realizar la transición, siga estos pasos:

1. Haga una conmutación por error geográfica planeada para cambiar el servidor principal al B. El servidor A se convertirá en el nuevo servidor secundario. La conmutación por error puede producir varios minutos de tiempo de inactividad. El tiempo real dependerá del tamaño del grupo de conmutación por error.
2. Cree instancias secundarias adicionales de cada base de datos del servidor B en el servidor C mediante la [replicación geográfica activa](active-geo-replication-overview.md). Cada base de datos del servidor B tendrá dos secundarios, uno en el servidor A y otro en el servidor C. Esto garantizará que las bases de datos principales permanezcan protegidas durante la transición.
3. Elimine el grupo de conmutación por error. En este momento, se produce un error en los intentos de inicio de sesión que usan los puntos de conexión del grupo de conmutación por error.
4. Vuelva a crear el grupo de conmutación por error con el mismo nombre entre los servidores B y C.
5. Agregue todas las bases de datos principales del servidor B al nuevo grupo de conmutación por error. En este momento, se dejarán de producir errores en los intentos de inicio de sesión.
6. Haga una conmutación por error geográfica planeada del grupo de conmutación por error para cambiar los servidores B y C. Ahora, el servidor C será el principal y el B será el secundario. Todas las bases de datos secundarias del servidor A se vincularán automáticamente a las instancias principales de C. Como en el paso 1, la conmutación por error puede producir varios minutos de tiempo de inactividad.
7. Elimine el servidor A. Todas las bases de datos de dicho servidor se eliminarán automáticamente.

> [!IMPORTANT]
> Cuando se elimina el grupo de conmutación por error, también se eliminan los registros de DNS de los puntos de conexión del agente de escucha. En ese momento, existe una probabilidad distinta de cero de que otra persona cree un grupo de conmutación por error o un alias DNS de servidor con el mismo nombre. Dado que los nombres de grupo de conmutación por error y los alias DNS deben ser únicos globalmente, esto impedirá que vuelva a usar el mismo nombre. Para minimizar este riesgo, no utilice nombres de grupo de conmutación por error genéricos.

## <a name="failover-group-best-practices-for-sql-managed-instance"></a><a name="best-practices-for-sql-managed-instance"></a> Procedimientos recomendados del grupo de conmutación por error para SQL Managed Instance

El grupo de conmutación por error automática debe estar configurado en la instancia principal y se conectará a la instancia secundaria de una región de Azure diferente.  Todas las bases de datos de usuario de la instancia se replicarán en la instancia secundaria. Las bases de datos del sistema como _maestra_ y _msdb_ no se replicarán.

En el siguiente diagrama se ilustra una configuración típica de una aplicación de nube con redundancia geográfica que usa instancia administrada y un grupo de conmutación por error automática.

![diagrama de conmutación por error automática](./media/auto-failover-group-overview/auto-failover-group-mi.png)

> [!NOTE]
> Consulte [Adición de una instancia administrada a un grupo de conmutación por error](../managed-instance/failover-group-add-instance-tutorial.md) para obtener un tutorial detallado paso a paso para la adición de una Instancia administrada de SQL para usar un grupo de conmutación por error.

Si la aplicación usa Instancia administrada de SQL como capa de datos, siga estas directrices generales al realizar el diseño para la continuidad empresarial:

### <a name="create-the-geo-secondary-managed-instance"></a><a name="creating-the-secondary-instance"></a> Creación de una instancia administrada secundaria geográfica

Para garantizar la conectividad sin interrupciones a la Instancia administrada de SQL principal después de la conmutación por error, las instancias principales y secundarias deben estar en la misma zona DNS. Esto garantizará que se pueda usar el mismo certificado de varios dominios (SAN) para autenticar las conexiones de cliente a cualquiera de las dos instancias del grupo de conmutación por error. Cuando la aplicación esté lista para la implementación en producción, cree una Instancia administrada de SQL secundaria en una región distinta y asegúrese de que comparte la zona DNS con la Instancia administrada de SQL principal. Puede hacerlo al especificar un parámetro opcional durante la creación. Si usa PowerShell o la API de REST, el nombre del parámetro opcional es `DNSZonePartner`. El nombre del campo opcional correspondiente en Azure Portal es *Primary Managed Instance* (Instancia administrada principal).

> [!IMPORTANT]
> La primera Instancia administrada creada en la subred determina la zona DNS de todas las instancias posteriores de la misma subred. Esto significa que dos instancias de la misma subred no pueden pertenecer a zonas DNS diferentes.

Para obtener más información sobre cómo crear la Instancia administrada de SQL secundaria en la misma zona DNS que la instancia principal, consulte [Creación de una instancia administrada secundaria](../managed-instance/failover-group-add-instance-tutorial.md#create-a-secondary-managed-instance).

### <a name="use-paired-regions"></a><a name="using-geo-paired-regions"></a> Uso de regiones emparejadas

De cara al rendimiento, implemente ambas instancias administradas en [regiones emparejadas](../../best-practices-availability-paired-regions.md). Los grupos de conmutación por error de SQL Managed Instance en regiones emparejadas tienen un mejor rendimiento en comparación con las regiones no emparejadas.

### <a name="enable-geo-replication-traffic-between-two-managed-instances"></a><a name="enabling-replication-traffic-between-two-instances"></a> Habilitación del tráfico de replicación geográfica entre dos instancias administradas

Dado que cada instancia administrada está aislada en su propia red virtual, se debe permitir el tráfico bidireccional entre estas redes virtuales. Consulte [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="create-a-failover-group-between-managed-instances-in-different-subscriptions"></a><a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a> Creación de un grupo de conmutación por error entre instancias administradas en distintas suscripciones

Puede crear un grupo de conmutación por error entre instancias de SQL Managed Instance en dos suscripciones diferentes, siempre que las suscripciones estén asociadas al mismo [inquilino de Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md#terminology). Al usar la API de PowerShell, puede hacerlo especificando el parámetro `PartnerSubscriptionId` de la instancia secundaria de SQL Managed Instance. Al usar la API REST, cada identificador de instancia incluido en el parámetro `properties.managedInstancePairs` puede tener su propio id. de suscripción.
  
> [!IMPORTANT]
> Azure Portal no admite la creación de grupos de conmutación por error en distintas suscripciones. Además, para los grupos de conmutación por error existentes en distintas suscripciones o grupos de recursos, no se puede iniciar manualmente la conmutación por error a través del portal desde la Instancia administrada de SQL principal. En su lugar, se inicia desde la instancia de la base de datos geográfica secundaria.

### <a name="manage-geo-failover-to-a-geo-secondary-instance"></a><a name="managing-failover-to-secondary-instance"></a> Administración de la conmutación por error geográfica en una instancia secundaria geográfica

El grupo de conmutación por error administrará la conmutación por error geográfica de todas las bases de datos de la instancia administrada principal. Cuando se crea un grupo, cada base de datos de la instancia se replicará geográficamente de manera automática a la instancia secundaria geográfica. No se pueden usar grupos de conmutación por error para iniciar una conmutación por error parcial de un subconjunto de bases de datos.

> [!IMPORTANT]
> Si se coloca una base de datos en la instancia administrada principal, también se colocará automáticamente en la instancia administrada secundaria de replicación geográfica.

### <a name="use-the-read-write-listener-to-connect-to-the-primary-managed-instance"></a><a name="using-read-write-listener-for-oltp-workload"></a> Uso del cliente de escucha de lectura y escritura para conectarse a la instancia administrada principal

Para cargas de trabajo de lectura y escritura, use `<fog-name>.zone_id.database.windows.net` como nombre del servidor. Las conexiones se dirigirán automáticamente a la principal. Este nombre no cambia después de la conmutación por error. La conmutación por error geográfica implica actualizar el registro DNS para que las conexiones de cliente se redirijan a la nueva base de datos principal cuando la caché DNS del cliente se haya actualizado. Dado que la instancia secundaria comparte la zona DNS con la principal, la aplicación cliente podrá volver a conectarse a ella con el mismo certificado de SAN del lado del servidor.

### <a name="use-the-read-only-listener-to-connect-to-the-geo-secondary-managed-instance"></a><a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a> Uso del cliente de escucha de solo lectura para conectarse a la instancia administrada secundaria geográfica

Si tiene cargas de trabajo de solo lectura aisladas lógicamente que son tolerantes a la latencia de datos, puede ejecutarlas en la base de datos secundaria geográfica. Para conectarse directamente a la base de datos secundaria geográfica, use `<fog-name>.secondary.<zone_id>.database.windows.net` como nombre del servidor.

> [!NOTE]
> En el nivel Crítico para la empresa, SQL Managed Instance admite el uso de [réplicas de solo lectura](read-scale-out.md) para descargar cargas de trabajo de consulta de solo lectura, mediante el parámetro `ApplicationIntent=ReadOnly` de la cadena de conexión. Cuando se ha configurado una base de datos secundaria con replicación geográfica, puede usar esta funcionalidad para conectarse a una réplica de solo lectura de la ubicación principal o de la ubicación con replicación geográfica.
>
> - Para conectarse a una réplica de solo lectura en la ubicación principal, use `ApplicationIntent=ReadOnly` y `<fog-name>.<zone_id>.database.windows.net`.
> - Para conectarse a una réplica de solo lectura en la ubicación secundaria, use `ApplicationIntent=ReadOnly`y `<fog-name>.secondary.<zone_id>.database.windows.net`.

### <a name="potential-performance-degradation-after-failover-to-the-geo-secondary-managed-instance"></a>Posible degradación del rendimiento después de la conmutación por error en la instancia administrada secundaria geográfica

Una aplicación de Azure típica usa varios servicios de Azure y consta de varios componentes. La conmutación por error geográfica de manera automática del grupo de conmutación por error se desencadena en función del estado de los componentes de Azure SQL. Es posible que otros servicios de Azure de la región primaria no se vean afectados por la interrupción y que sus componentes sigan estando disponibles en esa región. Una vez que las bases de datos principales cambien a la región secundaria, la latencia entre los componentes dependientes puede aumentar. Para evitar que la mayor latencia afecte al rendimiento de la aplicación, compruebe la redundancia de todos los componentes de la aplicación en la región secundaria y realice la conmutación por error de los componentes junto con la de la base de datos. En el momento de la configuración, siga las [instrucciones de seguridad de red](#failover-groups-and-network-security) para garantizar la conectividad a la base de datos en la región secundaria.

### <a name="potential-data-loss-after-failover-to-the-geo-secondary-managed-instance"></a>Posible pérdida de datos después de la conmutación por error en la instancia administrada secundaria geográfica

Si se produce una interrupción en la región primaria, es posible que las transacciones recientes no se puedan replicar en la secundaria geográfica. Si se configura la directiva de conmutación automática por error, se desencadena una conmutación por error geográfica si nos consta que no hay ninguna pérdida de datos. De lo contrario, la conmutación por error se aplazará durante el período que especifique mediante `GracePeriodWithDataLossHours`. Si configuró la directiva de conmutación automática por error, prepárese para la pérdida de datos. Por lo general, durante las interrupciones, Azure favorece la disponibilidad. Establecer `GracePeriodWithDataLossHours` en un número mayor, como 24 horas, o deshabilitar la conmutación por error geográfica de manera automática le permite reducir la probabilidad de perder datos a costa de la disponibilidad de la base de datos.

La actualización de DNS del agente de escucha de lectura y escritura sucederá inmediatamente después de que se inicie la conmutación por error. Esta operación no ocasionará pérdida de datos. Sin embargo, el proceso de conmutación de roles de base de datos puede tardar hasta 5 minutos en condiciones normales. Hasta que se complete, algunas bases de datos de la nueva instancia principal seguirán siendo de solo lectura. Si se inicia una conmutación por error mediante PowerShell, la operación para cambiar el rol de la réplica principal es sincrónica. Si se inicia mediante Azure Portal, la interfaz de usuario indicará el estado de finalización. Si se inicia mediante la API REST, use el mecanismo de sondeo estándar de Azure Resource Manager para supervisar la finalización.

> [!IMPORTANT]
> Use la conmutación por error planeada manual para volver a transferir la principal a la ubicación original una vez mitigada la interrupción que provocó la conmutación por error geográfica.
  
### <a name="change-the-secondary-region-of-the-managed-instance-failover-group"></a>Cambio de la región secundaria del grupo de conmutación por error de la instancia administrada

Supongamos que la instancia A es la principal, la B es la instancia secundaria existente y la C es la nueva instancia secundaria de la tercera región. Para realizar la transición, siga estos pasos:

1. Cree la instancia C con el mismo tamaño que la instancia A y en la misma zona DNS.
2. Elimine el grupo de conmutación por error entre las instancias A y B. En este momento, se producirá un error en los inicios de sesión porque se han eliminado los alias de SQL de los agentes de escucha del grupo de conmutación por error y la puerta de enlace no reconocerá el nombre del grupo de conmutación por error. Las bases de datos secundarias se desconectarán de las principales y se convertirán en bases de datos de lectura y escritura.
3. Cree un grupo de conmutación por error con el mismo nombre entre las instancias A y C. Siga las instrucciones del [tutorial sobre el grupo de conmutación por error con Instancia administrada de SQL](../managed-instance/failover-group-add-instance-tutorial.md). Se trata de una operación de tamaño de datos, que se completará cuando todas las bases de datos de la instancia A se inicialicen y se sincronicen.
4. Si no es necesario, elimine la instancia B para evitar cargos innecesarios.

> [!NOTE]
> Después del paso 2 y hasta que se complete el paso 3, las bases de datos de la instancia A permanecerán desprotegidas frente a un error catastrófico en la instancia A.

### <a name="change-the-primary-region-of-the-managed-instance-failover-group"></a>Cambio de la región primaria del grupo de conmutación por error de la instancia administrada

Supongamos que la instancia A es la principal, la B es la instancia secundaria existente y la C es la nueva instancia principal de la tercera región. Para realizar la transición, siga estos pasos:

1. Cree la instancia C con el mismo tamaño que la instancia B y en la misma zona DNS.
2. Conéctese a la instancia B y conmute por error manualmente para cambiar la instancia principal a B. La instancia A se convertirá en la nueva instancia secundaria automáticamente.
3. Elimine el grupo de conmutación por error entre las instancias A y B. En este momento, se produce un error en los intentos de inicio de sesión mediante el uso de los puntos de conexión del grupo de conmutación por error. Las bases de datos secundarias en A se desconectarán de las principales y se convertirán en bases de datos de lectura y escritura.
4. Cree un grupo de conmutación por error con el mismo nombre entre las instancias A y C. Siga las instrucciones del [tutorial sobre el grupo de conmutación por error con instancia administrada](../managed-instance/failover-group-add-instance-tutorial.md). Se trata de una operación de tamaño de datos, que se completará cuando todas las bases de datos de la instancia A se inicialicen y se sincronicen. En este momento, se dejarán de producir errores en los intentos de inicio de sesión.
5. Si no es necesario, elimine la instancia A para evitar cargos innecesarios.

> [!CAUTION]
> Después del paso 3 y hasta que se complete el paso 4, las bases de datos de la instancia A permanecerán desprotegidas frente a un error catastrófico en la instancia A.

> [!IMPORTANT]
> Cuando se elimina el grupo de conmutación por error, también se eliminan los registros de DNS de los puntos de conexión del agente de escucha. En ese momento, existe una probabilidad distinta de cero de que otra persona cree un grupo de conmutación por error con el mismo nombre. Dado que los nombres de grupo de conmutación por error deben ser únicos globalmente, esto impedirá que vuelva a usar el mismo nombre. Para minimizar este riesgo, no utilice nombres de grupo de conmutación por error genéricos.

### <a name="enable-scenarios-dependent-on-objects-from-the-system-databases"></a>Habilitación de escenarios que dependen de objetos de las bases de datos del sistema

Las bases de datos del sistema **no** se replican en la instancia secundaria de un grupo de conmutación por error. Para habilitar escenarios que dependen de los objetos de las bases de datos del sistema, asegúrese de crear los mismos objetos en la instancia secundaria y mantenerlos sincronizados con la instancia principal. 

Por ejemplo, si tiene previsto utilizar los mismos inicios de sesión en la instancia secundaria, asegúrese de crearlos con el SID idéntico. 

```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
``` 
### <a name="synchronize-instance-properties-and-retention-policies-between-primary-and-secondary-instance"></a>Sincronización de las propiedades de instancia y las directivas de retención entre la instancia principal y la secundaria

Las instancias de un grupo de conmutación por error siguen siendo recursos de Azure independientes y ningún cambio que se haga en la configuración de la instancia principal se replicará automáticamente en la instancia secundaria. Por ello, asegúrese de realizar todos los cambios relevantes tanto en la instancia principal _como en la_ secundaria. Por ejemplo, si cambia la redundancia del almacenamiento de copia de seguridad o la directiva de retención de copias de seguridad a largo plazo en la instancia principal, asegúrese de cambiarla también en la instancia secundaria.

## <a name="failover-groups-and-network-security"></a>Grupos de conmutación por error y la seguridad de red

En el caso de algunas aplicaciones, las reglas de seguridad requieren que el acceso de red a la capa de datos esté restringido a un componente o componentes específicos, como una VM, un servicio web, etc. Este requisito presenta algunos desafíos para el diseño de la continuidad empresarial y el uso de los grupos de conmutación por error. Considere las siguientes opciones al implementar dicho acceso restringido.

### <a name="use-failover-groups-and-virtual-network-service-endpoints"></a><a name="using-failover-groups-and-virtual-network-rules"></a> Uso de los grupos de conmutación por error y los puntos de conexión de servicio de red virtual

Si usa [reglas y puntos de conexión de servicio de Virtual Network](vnet-service-endpoint-rule-overview.md) para restringir el acceso a la base de datos en SQL Database o Instancia administrada de SQL, tenga en cuenta que cada punto de conexión de servicio de red virtual solo se aplica a una región de Azure. El punto de conexión no permite que otras regiones acepten la comunicación de la subred. Por lo tanto, solo las aplicaciones de cliente implementadas en la misma región pueden conectarse a la base de datos principal. Puesto que una conmutación por error geográfica provoca que las sesiones de cliente de SQL Database se vuelvan a enrutar al servidor en una región diferente (secundaria), se producirá un error en las sesiones si se originaron desde un cliente de fuera de esa región. Por ese motivo, la directiva de conmutación por error automática no se puede habilitar si se incluyen los servidores o las instancias implicados en las reglas de Virtual Network. Para admitir la conmutación por error manual, siga estos pasos:

1. Aprovisione las copias redundantes de los componentes front-end de la aplicación (servicio web, máquinas virtuales, etc.) en la región secundaria.
2. Configure las [reglas de red virtual](vnet-service-endpoint-rule-overview.md) individualmente para el servidor principal y secundario.
3. Habilite la [conmutación por error front-end con una configuración de Traffic Manager](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime).
4. Inicie la conmutación por error geográfica manualmente cuando se detecte la interrupción. Esta opción está optimizada para las aplicaciones que requieren una latencia coherente entre la capa de datos y de front-end, y admite la recuperación cuando se ven afectadas por la interrupción la capa de datos, la capa front-end o ambas.

> [!NOTE]
> Si usa el **agente de escucha de solo lectura** para equilibrar una carga de trabajo de solo lectura, asegúrese de que esta carga de trabajo se ejecuta en una máquina virtual o en otro recurso en la región secundaria de modo que pueda conectarse a la base de datos secundaria.

### <a name="use-failover-groups-and-firewall-rules"></a>Uso de grupos de conmutación por error y reglas de firewall

Si el plan de continuidad empresarial requiere un proceso de conmutación por error mediante el uso de grupos con conmutación automática por error, puede restringir el acceso a la base de datos de SQL Database con las reglas de firewall de IP públicas. Para admitir la conmutación por error automática, siga estos pasos:

1. [Cree una dirección IP pública](../../virtual-network/ip-services/virtual-network-public-ip-address.md#create-a-public-ip-address).
2. [Cree un equilibrador de carga público](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) y asígnele la dirección IP pública.
3. [Cree una red virtual y las máquinas virtuales](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) para los componentes front-end.
4. [Cree un grupo de seguridad de red](../../virtual-network/network-security-groups-overview.md) y configure las conexiones entrantes.
5. Asegúrese de que las conexiones salientes estén abiertas para Azure SQL Database en una región mediante el uso de una `Sql.<Region>` [etiqueta de servicio](../../virtual-network/network-security-groups-overview.md#service-tags).
6. Cree una [regla de firewall de SQL Database](firewall-configure.md) para permitir el tráfico entrante desde la dirección IP pública que creó en el paso 1.

Para más información acerca de cómo configurar el acceso de salida y qué dirección IP usar en las reglas de firewall, consulte [Conexiones salientes del equilibrador de carga](../../load-balancer/load-balancer-outbound-connections.md).

La configuración anterior garantizará que una conmutación automática por error geográfica no bloqueará las conexiones desde los componentes front-end y se da por supuesto que la aplicación puede tolerar la mayor latencia entre la capa de datos y el front-end.

> [!IMPORTANT]
> Para garantizar la continuidad empresarial durante las interrupciones regionales, debe garantizar la redundancia geográfica tanto para los componentes front-end como para las bases de datos.

## <a name="enabling-geo-replication-between-managed-instance-virtual-networks"></a><a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a> Habilitación de la replicación geográfica entre las redes virtuales de la instancia administrada

Al configurar un grupo de conmutación por error entre instancias administradas de SQL principales y secundarias en dos regiones diferentes, cada instancia se aísla mediante una red virtual independiente. Para permitir el tráfico de replicación entre estas redes virtuales, asegúrese de que se cumplen estos requisitos previos:

- Las dos instancias de Instancia administrada de SQL deben estar en diferentes regiones de Azure.
- Las dos instancias de Instancia administrada de SQL deben ser del mismo nivel de servicio y tener el mismo tamaño de almacenamiento.
- La instancia secundaria de Instancia administrada de SQL debe estar vacía (sin bases de datos de usuario).
- Las redes virtuales que se usan en las instancias de Instancia administrada de SQL deben estar conectadas mediante [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) o [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md). Si dos redes virtuales se conectan a través de una red local, asegúrese de que no haya ninguna regla de firewall bloqueando los puertos 5022 y 11000-11999. El emparejamiento de VNet global se admite con la limitación descrita en la nota siguiente.

   > [!IMPORTANT]
   > [El 22 de septiembre de 2020 se anunció el emparejamiento de red virtual global para clústeres virtuales recién creados](https://azure.microsoft.com/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Esto significa que el emparejamiento de red virtual global se admite en las instancias de SQL Managed Instance creadas en subredes vacías después de la fecha del anuncio, así como en todas las instancias administradas posteriores creadas en esas subredes. En el caso de todas las demás instancias de SQL Managed Instance, la compatibilidad con el emparejamiento se limita a las redes de la misma región, debido a las [restricciones del emparejamiento de red virtual global](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Consulte también la sección correspondiente del artículo [Preguntas más frecuentes (P+F) acerca de Azure Virtual Network](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) para más información. Para poder usar el emparejamiento de red virtual global en instancias de SQL Managed Instance desde clústeres virtuales creados antes de la fecha del anuncio, considere la posibilidad de configurar una [ventana de mantenimiento](./maintenance-window.md) no predeterminada en las instancias, ya que eso moverá las instancias a nuevos clústeres virtuales que admiten el emparejamiento de red virtual global.

- Las dos redes virtuales de Instancia administrada de SQL no pueden tener direcciones IP superpuestas.
- Debe configurar sus grupos de seguridad de red (NSG) de forma que los puertos 5022 y el intervalo 11000~12000 estén abiertos a las conexiones entrantes y salientes de la subred de la otra instancia administrada. Esto es para permitir el tráfico de replicación entre las instancias.

   > [!IMPORTANT]
   > Las reglas de seguridad de NSG mal configuradas dan lugar a operaciones de inicialización de base de datos bloqueadas.

- La Instancia administrada de SQL secundaria está configurada con el identificador de zona DNS correcto. La zona DNS es una propiedad de una Instancia administrada de SQL y un clúster virtual subyacente, cuyo identificador se incluye en la dirección del nombre de host. El identificador de zona se genera como una cadena aleatoria cuando se crea la primera Instancia administrada de SQL en cada red virtual, y este mismo identificador se asigna a todas las demás instancias de la misma subred. Una vez que se ha asignado, no se puede modificar la zona DNS. Las instancias de Instancia administrada de SQL incluidas en el mismo grupo de conmutación por error deben compartir la zona DNS. Para lograrlo, debe pasar el identificador de zona de la instancia principal como valor del parámetro DnsZonePartner al crear la instancia secundaria.

   > [!NOTE]
   > Consulte [Adición de una Instancia administrada de SQL a un grupo de conmutación por error](../managed-instance/failover-group-add-instance-tutorial.md) para obtener un tutorial detallado sobre la configuración de grupos de conmutación por error con Instancia administrada de SQL.

## <a name="scale-primary-database"></a><a name="upgrading-or-downgrading-primary-database"></a> Modificación de la escala de una base de datos principal

Puede escalar verticalmente o reducir verticalmente la base de datos principal a un tamaño de proceso diferente (en el mismo nivel de servicio) sin desconectar las secundarias. Cuando se escala verticalmente, se recomienda escalar verticalmente primero la secundaria geográfica y, después, escalar verticalmente la principal. Al reducir verticalmente, invierta el orden: primero escale verticalmente la principal y, a continuación, escale verticalmente la secundaria. Cuando se escala la base de datos a un nivel de servicio diferente, se aplica esta recomendación.

Esta secuencia se recomienda específicamente para evitar que la base de datos secundaria geográfica de una SKU inferior se sobrecargue y deba reinicializarse durante un proceso de actualización o degradación. También puede evitar este problema si hace que la base de datos principal sea de solo lectura, a costa de afectar a todas las cargas de trabajo de lectura y escritura en la réplica principal.

> [!NOTE]
> Si creó una base de datos secundaria geográfica como parte de la configuración del grupo de conmutación por error, no se recomienda reducir verticalmente la base de datos secundaria geográfica. De este modo, se garantiza que la capa de datos tiene la capacidad suficiente para procesar la carga de trabajo habitual después de una conmutación por error.

## <a name="prevent-loss-of-critical-data"></a><a name="preventing-the-loss-of-critical-data"></a> Evitación la pérdida de datos críticos

Debido a la elevada latencia de las redes de área extensa, la replicación geográfica usa un mecanismo de replicación asincrónica. La replicación asincrónica hace que la posibilidad de perder datos sea inevitable si se produce un error en la principal. Para proteger las transacciones críticas contra la pérdida de datos, un desarrollador de aplicaciones puede llamar al procedimiento almacenado [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) inmediatamente después de confirmar la transacción. La llamada a `sp_wait_for_database_copy_sync` bloquea el subproceso de llamada hasta que se transmite y protege la última transacción confirmada en el registro de transacciones de la base de datos secundaria. Sin embargo, no espera a que las transacciones transmitidas se reproduzcan (vuelvan a hacerse) en la base de datos secundaria. `sp_wait_for_database_copy_sync` está limitado a un vínculo de replicación geográfica específico. Cualquier usuario con derechos de conexión para la base de datos principal puede llamar a este procedimiento.

> [!NOTE]
> `sp_wait_for_database_copy_sync` evita la pérdida de datos después de la conmutación por error geográfica para transacciones específicas, pero no garantiza la sincronización completa para el acceso de lectura. El retraso provocado por una llamada al procedimiento `sp_wait_for_database_copy_sync` puede ser considerable y depende del tamaño del registro de transacciones que todavía no se transmiten en la principal en el momento de la llamada.

## <a name="failover-groups-and-point-in-time-restore"></a>Grupos de conmutación por error y restauración a un momento dado

Para información sobre el uso de la restauración a un momento dado con grupos de conmutación por error, consulte [Recuperación a un momento dado (PITR)](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations-of-failover-groups"></a>Limitaciones de los grupos de conmutación por error

Tenga en cuenta las siguientes limitaciones:

- No se pueden crear grupos de conmutación por error entre dos servidores o instancias en la misma región de Azure.
- No se puede cambiar el nombre de los grupos de conmutación por error. Tendrá que eliminar el grupo y volver a crearlo con un nombre diferente.
- No se admiten los cambios de nombre de la base de datos para las bases de datos del grupo de conmutación por error. Tendrá que eliminar temporalmente el grupo de conmutación por error para poder cambiar el nombre de una base de datos o quitar la base de datos del grupo de conmutación por error.
- Las bases de datos del sistema no se replican en la instancia secundaria de un grupo de conmutación por error. Por lo tanto, los escenarios que dependen de objetos de las bases de datos del sistema necesitan que estos se creen manualmente en las instancias secundarias y también que se mantengan sincronizados manualmente después de los cambios realizados en la instancia principal. La única excepción es la clave maestra de servicio (SMK) de SQL Managed Instance, que se replica automáticamente en la instancia secundaria durante la creación del grupo de conmutación por error. Sin embargo, los cambios posteriores de SMK en la instancia principal no se replicarán en la instancia secundaria.
- No se pueden crear grupos de conmutación por error entre instancias si alguna de ellas se encuentra en un grupo de instancias.

## <a name="programmatically-manage-failover-groups"></a><a name="programmatically-managing-failover-groups"></a> Administración mediante programación de grupos de conmutación por error

Como se ha mencionado antes, los grupos de conmutación por error automática también pueden administrarse mediante programación con Azure PowerShell, Azure CLI y la API de REST. En las tablas siguientes se describe el conjunto de comandos disponibles. La replicación geográfica activa incluye un conjunto de API de Azure Resource Manager para la administración, en el que se incluyen la [API REST de Azure SQL Database](/rest/api/sql/) y los [cmdlets de Azure PowerShell](/powershell/azure/). Estas API requieren que se usen grupos de recursos y admiten el control de acceso basado en rol de Azure (Azure RBAC). Para más información sobre cómo implementar los roles de acceso, consulte [Control de acceso basado en roles de Azure (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="manage-sql-database-geo-failover"></a><a name="manage-sql-database-failover"></a> Administración de la conmutación por error geográfica de SQL Database

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | Descripción |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Este comando crea un grupo de conmutación por error y lo registra en el servidor principal y el servidor secundario|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Quita un grupo de conmutación por error del servidor. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Recupera la configuración de un grupo de conmutación por error. |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Modifica la configuración de un grupo de conmutación por error. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Desencadena la conmutación por error de un grupo de conmutación por error en el servidor secundario. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Agrega una o varias bases de datos a un grupo de conmutación por error.|

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

| Get-Help | Descripción |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az_sql_failover_group_create) |Este comando crea un grupo de conmutación por error y lo registra en el servidor principal y el servidor secundario|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az_sql_failover_group_delete) | Quita un grupo de conmutación por error del servidor. |
| [az sql failover-group show](/cli/azure/sql/failover-group#az_sql_failover_group_show) | Recupera la configuración de un grupo de conmutación por error. |
| [az sql failover-group update](/cli/azure/sql/failover-group#az_sql_failover_group_update) |Modifica la configuración de un grupo de conmutación por error, o bien agrega una o varias bases de datos a un grupo de conmutación por error.|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az_sql_failover_group_set_primary) | Desencadena la conmutación por error de un grupo de conmutación por error en el servidor secundario. |

# <a name="rest-api"></a>[API DE REST](#tab/rest-api)

| API | Descripción |
| --- | --- |
| [Crear o actualizar grupo de conmutación por error](/rest/api/sql/failovergroups/createorupdate) | Crea o actualiza un grupo de conmutación por error. |
| [Eliminar grupo de conmutación por error](/rest/api/sql/failovergroups/delete) | Quita un grupo de conmutación por error del servidor. |
| [Conmutación por error (planeada)](/rest/api/sql/failovergroups/failover) | Desencadena la conmutación por error del servidor principal actual en el servidor secundario con sincronización de datos completa.|
| [Forzar la conmutación por error ocasiona la pérdida de datos](/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Desencadena la conmutación por error del servidor principal actual en el servidor secundario sin sincronización de datos. Esta operación puede ocasionar pérdida de datos. |
| [Obtener grupo de conmutación por error](/rest/api/sql/failovergroups/get) | Recupera la configuración de un grupo de conmutación por error. |
| [Enumerar grupos de conmutación por error por servidor](/rest/api/sql/failovergroups/listbyserver) | Enumera los grupos de conmutación por error en un servidor. |
| [Actualizar grupo de conmutación por error](/rest/api/sql/failovergroups/update) | Actualiza la configuración de un grupo de conmutación por error. |

---

### <a name="manage-sql-managed-instance-geo-failover"></a><a name="manage-sql-managed-instance-failover"></a> Administración de la conmutación por error geográfica de SQL Managed Instance

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | Descripción |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Este comando crea un grupo de conmutación por error y lo registra en las instancias principal y secundaria.|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Modifica la configuración de un grupo de conmutación por error.|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Recupera la configuración de un grupo de conmutación por error.|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Desencadena la conmutación por error de un grupo de conmutación por error en la instancia secundaria.|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Quita un grupo de conmutación por error.|


# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

| Get-Help | Descripción |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az_sql_failover_group_create) |Este comando crea un grupo de conmutación por error y lo registra en el servidor principal y el servidor secundario|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az_sql_failover_group_delete) | Quita un grupo de conmutación por error del servidor. |
| [az sql failover-group show](/cli/azure/sql/failover-group#az_sql_failover_group_show) | Recupera la configuración de un grupo de conmutación por error. |
| [az sql failover-group update](/cli/azure/sql/failover-group#az_sql_failover_group_update) |Modifica la configuración de un grupo de conmutación por error, o bien agrega una o varias bases de datos a un grupo de conmutación por error.|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az_sql_failover_group_set_primary) | Desencadena la conmutación por error de un grupo de conmutación por error en el servidor secundario. |

# <a name="rest-api"></a>[API DE REST](#tab/rest-api)

| API | Descripción |
| --- | --- |
| [Crear o actualizar grupo de conmutación por error](/rest/api/sql/instancefailovergroups/createorupdate) | Crea o actualiza la configuración de un grupo de conmutación por error. |
| [Eliminar grupo de conmutación por error](/rest/api/sql/instancefailovergroups/delete) | Quita un grupo de conmutación por error de la instancia. |
| [Conmutación por error (planeada)](/rest/api/sql/instancefailovergroups/failover) | Desencadena la conmutación por error del servidor principal actual en esta instancia con sincronización de datos completa. |
| [Forzar la conmutación por error ocasiona la pérdida de datos](/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Desencadena la conmutación por error de la instancia principal actual en la instancia secundaria sin sincronización de datos. Esta operación puede ocasionar pérdida de datos. |
| [Obtener grupo de conmutación por error](/rest/api/sql/instancefailovergroups/get) | Recupera la configuración de un grupo de conmutación por error. |
| [Lista de grupos de conmutación por error: por ubicación](/rest/api/sql/instancefailovergroups/listbylocation) | Enumera los grupos de conmutación por error de una ubicación. |

---

## <a name="next-steps"></a>Pasos siguientes

- Para ver tutoriales detallados, consulte
  - [Adición de SQL Database a un grupo de conmutación por error](failover-group-add-single-database-tutorial.md)
  - [Adición de un grupo elástico a un grupo de conmutación por error](failover-group-add-elastic-pool-tutorial.md)
  - [Adición de una Instancia administrada de SQL a un grupo de conmutación por error](../managed-instance/failover-group-add-instance-tutorial.md)
- Para los scripts de ejemplo, vea:
  - [Uso de PowerShell para configurar la replicación geográfica activa para Azure SQL Database](scripts/setup-geodr-and-failover-database-powershell.md)
  - [Uso de PowerShell para configurar la replicación geográfica activa para una base de datos agrupada en Azure SQL Database](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
  - [Uso de PowerShell para agregar una instancia de Azure SQL Database a un grupo de conmutación por error](scripts/add-database-to-failover-group-powershell.md)
- Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Continuidad empresarial con Base de datos SQL de Azure](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Para saber en qué consisten las copias de seguridad automatizadas de Azure SQL Database, consulte [Información general: copias de seguridad automatizadas de SQL Database](automated-backups-overview.md).
- Si quiere saber cómo usar las copias de seguridad automatizadas para procesos de recuperación, consulte [Recuperación de una base de datos a partir de copias de seguridad iniciadas por un servicio](recovery-using-backups.md).
- Para obtener información acerca de los requisitos de autenticación para un nuevo servidor principal y la base de datos, consulte [Administración de la seguridad de Azure SQL Database después de la recuperación ante desastres](active-geo-replication-security-configure.md).
