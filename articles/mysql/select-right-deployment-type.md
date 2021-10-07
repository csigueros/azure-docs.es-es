---
title: 'Selección del tipo de implementación adecuado: Azure Database for MySQL'
description: En este artículo se describen los factores que deben tenerse en cuenta antes de implementar Azure Database for MySQL como infraestructura como servicio (IaaS) o plataforma como servicio (PaaS).
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: ee18a405ca6c6a9d2e6a3a6cceb5c0ff89b5cf73
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124748595"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Selección de la opción adecuada de MySQL Server en Azure

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Con Azure, las cargas de trabajo del servidor MySQL se pueden ejecutar en una infraestructura como servicio (IaaS) de máquina virtual hospedada o como una plataforma como servicio (PaaS) hospedada. PaaS dispone de varias opciones de implementación y, dentro de cada una, hay niveles de servicio. Al decidir entre PaaS o IaaS, debe decidir si quiere administrar la base de datos, aplicar revisiones y realizar copias de seguridad, o si quiere delegar estas operaciones en Azure.

Cuando tome la decisión, tenga en cuenta las dos siguientes opciones:

- **Azure Database for MySQL**. Esta opción es un motor de base de datos MySQL totalmente administrado basado en la versión estable de la edición Community de MySQL. Esta base de datos relacional como servicio (DBaaS), hospedada en plataforma en la nube de Azure, se engloba en la categoría del sector denominada PaaS.

  Con una instancia administrada de MySQL en Azure, puede usar características integradas, por ejemplo, revisiones automatizadas, alta disponibilidad, copias de seguridad automatizadas, escalado elástico, seguridad de nivel empresarial, cumplimiento y gobernanza, supervisión y alertas que, de otro modo, necesitan una configuración exhaustiva cuando el servidor MySQL está en el entorno local o en una máquina virtual de Azure. Al usar MySQL como servicio, se paga por el uso de opciones de escalado vertical u horizontal a fin de aumentar el control de forma ininterrumpida.
  
  [Azure Database for MySQL](overview.md), con tecnología de MySQL Community Edition, está disponible en dos modos de implementación:

  - [Servidor único](single-server-overview.md) es un servicio de base de datos totalmente administrado con requisitos mínimos para las personalizaciones de la base de datos. La plataforma de servidor único está diseñada para administrar la mayoría de las funciones de administración de bases de datos, como las de revisión, copias de seguridad, alta disponibilidad y seguridad con una configuración y un control mínimos para el usuario. La arquitectura está optimizada para proporcionar una disponibilidad del 99,99 % en una zona de disponibilidad única. Los servidores únicos son más adecuados para las aplicaciones nativas en la nube diseñadas para controlar la aplicación de revisiones automatizadas sin necesidad de un control granular sobre la programación de aplicación de revisiones y la configuración personalizada de MySQL.

  - [Servidor flexible (versión preliminar)](flexible-server/overview.md) es un servicio de base de datos totalmente administrado diseñado para proporcionar un control más granular y una mayor flexibilidad sobre las funciones de administración de bases de datos y las opciones de configuración. En general, el servicio proporciona más flexibilidad y personalizaciones de configuración del servidor en comparación con la implementación de servidor único en función de los requisitos del usuario. La arquitectura de servidor flexible permite a los usuarios optar por la alta disponibilidad dentro de una única zona de disponibilidad o entre varias. Los servidores flexibles también proporcionan mejores controles de optimización de costes con la capacidad de iniciar o detener el servidor, y SKU flexibles, idóneas para cargas de trabajo que no necesitan una capacidad de proceso completa de forma continuada.

Los servidores flexibles son idóneos para:

  - Desarrollo de aplicaciones que necesitan un mejor control y personalizaciones del motor de MySQL.
  - Alta disponibilidad con redundancia de zona
  - Ventanas de mantenimiento administradas
 
  - **MySQL en máquinas virtuales de Azure**. Esta opción pertenece a la categoría del sector de IaaS. Con este servicio, puede ejecutar MySQL Server dentro de una máquina virtual administrada en la plataforma en la nube de Azure. Todas las versiones y ediciones recientes de MySQL se pueden instalar en la máquina virtual.

## <a name="comparing-the-mysql-deployment-options-in-azure"></a>Comparación de las opciones de implementación de MySQL en Azure

Las principales diferencias entre estas opciones se mencionan en la siguiente tabla:

| Atributo          | Azure Database for MySQL<br/>Servidor único |Azure Database for MySQL<br/>Servidor flexible  |MySQL en máquinas virtuales de Azure |
|:-------------------|:-------------------------------------------|:---------------------------------------------|:------------------|
| [**General**](flexible-server/overview.md)  | | | |
| Disponibilidad general | GA desde 2018 | Vista previa pública | GA |
| Acuerdo de Nivel de Servicio (SLA) | Acuerdo de Nivel de Servicio con disponibilidad del 99,99 % |No hay Acuerdo de Nivel de Servicio en la versión preliminar| 99.99 % con Availability Zones|
| Sistema operativo subyacente | Windows | Linux  | Administrado por el usuario |
| Edición de MySQL | Community Edition | Community Edition | Community o Enterprise Edition |
| Compatibilidad con versiones de MySQL | 5.6 (retirado), 5.7 y 8.0| 5.7 y 8.0 | Cualquier versión|
| Selección de zona de disponibilidad para la coubicación de aplicaciones | No | Sí | Sí |
| Nombre de usuario en la cadena de conexión | `<user_name>@server_name`. Por ejemplo, `mysqlusr@mypgServer`. | Solo nombre de usuario. Por ejemplo, `mysqlusr`. | Solo nombre de usuario. Por ejemplo, `mysqlusr`. | 
| [**Escalado de almacenamiento y proceso**](flexible-server/concepts-compute-storage.md) | | | |
| Niveles de proceso | Básico, De uso general y Optimizado para memoria | Ampliable, De uso general y Optimizado para memoria | Ampliable, De uso general y Optimizado para memoria |
| Escalado de proceso | Compatible (no se admite el escalado desde y hasta el nivel Básico)| Compatible | Compatible|
| Tamaño de almacenamiento | De 5 GiB a 16 TiB| De 20 GiB a 16 TiB | De 32 GiB a 32 767 GiB|
| Escalado de almacenamiento en línea | Compatible| Compatible| No compatible|
| Escalado de almacenamiento automático | Compatible| Compatible| No compatible|
| Escalado de IOPS | No compatible| Compatible| No compatible|
| [**Optimización de costos**](https://azure.microsoft.com/pricing/details/mysql/flexible-server/) | | | |
| Precios de instancias reservadas | Compatible | Compatible | Compatible |
| Detener o iniciar el servidor para el desarrollo | El servidor se puede detener hasta 7 días | El servidor se puede detener hasta 30 días | Compatible |
| SKU ampliable de bajo costo | No compatible | Compatible | Compatible |
| [**Redes y seguridad**](concepts-security.md) | | | |
| Conectividad de red | - Puntos de conexión públicos con firewall de servidor.<br/> - Acceso privado con compatibilidad con Private Link.|- Puntos de conexión públicos con firewall de servidor.<br/> - Acceso privado con integración de Virtual Network.| - Puntos de conexión públicos con firewall de servidor.<br/> - Acceso privado con compatibilidad con Private Link.|
| SSL/TLS | Habilitado de forma predeterminada con compatibilidad con TLS v1.2, 1.1 y 1.0 | Habilitado de forma predeterminada con compatibilidad con TLS v1.2, 1.1 y 1.0| Compatible con TLS v1.2, 1.1 y 1.0 |
| Cifrado de datos en reposo | Compatible con claves administradas por el cliente (BYOK) | Compatible con claves administradas por el servicio | No compatible|
| Autenticación de Azure AD | Compatible | No compatible | No compatible|
| Compatibilidad con Azure Defender | Sí | No | No |
| Auditoría de servidor | Compatible | Compatible | Administrado por el usuario |
| [**Aplicación de revisión y mantenimiento**](flexible-server/concepts-maintenance.md) | | |
| Aplicación de revisiones del sistema operativo| Automático  | Automático  | Usuario administrado |
| Actualización de la versión secundaria de MySQL  | Automático  | Automático | Usuario administrado |
| Actualización local de la versión principal de MySQL | Compatible de 5.6 a 5.7 | No compatible | Administrado por el usuario |
| Control de mantenimiento | Administrada por el sistema | Administrado por el cliente | Usuario administrado |
| Ventana de mantenimiento | En cualquier momento en un plazo de 15 horas | Período de 1 hora | Usuario administrado |
| Notificación de mantenimiento planeado | 3 días | 5 días | Usuario administrado |
| [**Alta disponibilidad**](flexible-server/concepts-high-availability.md) | | | |
| Alta disponibilidad | Alta disponibilidad integrada (sin espera activa)| Alta disponibilidad integrada (sin espera activa), alta disponibilidad con redundancia de zona y de misma zona con espera activa | Usuario administrado |
| Redundancia de zona | No compatible | Compatible | Compatible|
| Colocación de zona en espera | No compatible | Compatible | Compatible|
| Conmutación por error automática | Sí (gira otro servidor)| Sí | Administrado por el usuario|
| conmutación por error forzada | No | Sí | Administrado por el usuario |
| Conmutación por error transparente de la aplicación | Sí | Sí | Administrado por el usuario|
| [**Replicación**](flexible-server/concepts-read-replicas.md) | | | |
| Compatibilidad para réplicas de lectura | Sí | Sí | Administrado por el usuario |
| Número de réplicas de lectura compatibles | 5 | 10 | Administrado por el usuario |
| Modo de replicación | Asincrónica | Asincrónica | Administrado por el usuario |
| Compatibilidad de GTID para réplicas de lectura | Compatible | Compatible | Administrado por el usuario |
| Compatibilidad entre regiones (replicación geográfica) | Yes | No compatible | Administrado por el usuario |
| Escenarios híbridos | Compatible con [Replicación de datos de entrada](./concepts-data-in-replication.md)| Compatible con [Replicación de datos de entrada](./flexible-server/concepts-data-in-replication.md) | Administrado por el usuario |
| Compatibilidad de GTID con la replicación de datos de entrada | Compatible | No compatible | Administrado por el usuario |
| Replicación de datos de salida | No compatible | En versión preliminar | Compatible |
| [**Copia de seguridad y recuperación**](flexible-server/concepts-backup-restore.md) | | | |
| Copias de seguridad automatizadas | Sí | Sí | No |
| Retención de copias de seguridad | Entre 7 y 35 días | 1-35 días | Administrado por el usuario |
| Retención a largo plazo de copias de seguridad | Administrado por el usuario | Administrado por el usuario | Administrado por el usuario |
| Exportación de copias de seguridad | Compatible con copias de seguridad lógicas | Compatible con copias de seguridad lógicas | Compatible |
| Funcionalidad de recuperación a un momento dado en cualquier momento dentro del período de retención | Sí | Sí | Administrado por el usuario |
| Capacidad de restauración en una zona diferente | No compatible | Sí | Sí |
| Capacidad de restauración en una red virtual diferente | No | Sí | Sí |
| Capacidad de restauración a una región diferente | Sí (Con redundancia geográfica) | No | Administrado por el usuario |
| Capacidad de restaurar un servidor eliminado | Sí | No | No |
| [**Recuperación ante desastres**](flexible-server/concepts-business-continuity.md) | | | | 
| DR en todas las regiones de Azure | Uso entre regiones de réplicas de lectura, copia de seguridad con redundancia geográfica | No compatible | Administrado por el usuario |
| Conmutación por error automática | No | No compatible | No |
| Puede usar el mismo punto de conexión de l/e | No | No compatible | No |
| [**Supervisión**](flexible-server/concepts-monitoring.md) | | | |
| Integración y alertas de Azure Monitor | Compatible | Compatible | Administrado por el usuario |
| Supervisión de las operaciones de base de datos | Compatible | Compatible | Administrado por el usuario |
| Información del rendimiento de las consultas | Compatible | No compatible | Administrado por el usuario |
| Registros del servidor | Compatible | Compatible (mediante registros de diagnóstico) | Administrado por el usuario |
| Registros de auditoría | Compatible | Compatible | Compatible | 
| Registros de errores | No compatible | Compatible | Compatible |
| Soporte técnico de Azure Advisor | Compatible | No compatible | No compatible |
| **Complementos** | | | |
| validate_password | No compatible | En versión preliminar | Compatible |
| caching_sha2_password | No compatible | En versión preliminar | Compatible |
| [**Productividad de los desarrolladores**](flexible-server/quickstart-create-server-cli.md) | | | |
| Administración de flota | Compatible con la CLI de Azure, PowerShell, REST y Azure Resource Manager | Compatible con la CLI de Azure, PowerShell, REST y Azure Resource Manager  | Compatible para máquinas virtuales con la CLI de Azure, PowerShell, REST y Azure Resource Manager |
| Compatibilidad con Terraform | Compatible | No compatible | Compatible |
| Acciones de GitHub | Compatible | Compatible | Administrado por el usuario |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motivaciones empresariales para elegir PaaS o IaaS

Hay varios factores que pueden influir a la hora de elegir PaaS o IaaS para hospedar las bases de datos MySQL.

### <a name="cost"></a>Coste

La reducción de costes suele ser la consideración principal que determina la mejor solución para hospedar las bases de datos. Esto suele darse en el caso de las startups con poca disponibilidad de efectivo o el equipo de una empresa bien establecida que opera con presupuestos ajustados. En esta sección se describen los conceptos básicos de facturación y licencias en Azure tal como se aplican a Azure Database for MySQL y MySQL en las máquinas virtuales de Azure.

#### <a name="billing"></a>Facturación

Azure Database for MySQL está disponible como servicio en varios niveles con recursos de distintos precios. Todos los recursos se facturan por hora a una tarifa fija. Para obtener la información más reciente sobre los niveles de servicio, los tamaños de proceso y las cantidades de almacenamiento admitidos actualmente, vea la [página de precios](https://azure.microsoft.com/pricing/details/mysql/). Los niveles de servicio y los tamaños de proceso se pueden ajustar de forma dinámica para satisfacer las necesidades variables de rendimiento de la aplicación. Se le factura por el tráfico saliente de Internet según las [tarifas de transferencia de datos](https://azure.microsoft.com/pricing/details/data-transfers/) habituales.

Con Azure Database for MySQL, Microsoft configura el software de base de datos, aplica revisiones en él y lo actualiza de forma automática. Estas acciones automatizadas reducen los costos de administración. Además, Azure Database for MySQL tiene funcionalidades de [copia de seguridad automatizadas](./concepts-backup.md). Estas funcionalidades le ayudan a obtener un ahorro significativo, sobre todo, cuando se tiene gran cantidad de base de datos. Por el contrario, con MySQL en las máquinas virtuales de Azure puede elegir y ejecutar cualquiera de las versiones de MySQL. Con independencia de la versión de MySQL que use, se paga por la máquina virtual aprovisionada, el coste de almacenamiento asociado a los datos, la copia de seguridad, la supervisión de los datos y el almacenamiento de registros, y los costes del tipo de licencia de MySQL específico que se utilice (si existe).

Azure Database for MySQL proporciona alta disponibilidad integrada para cualquier tipo de interrupción de nivel de nodo y mantiene al mismo tiempo la garantía del 99,99 % establecida en el SLA para el servicio. Pero para la alta disponibilidad de la base de datos en las máquinas virtuales, se usan las opciones de alta disponibilidad disponibles en una base de datos de MySQL, como la [replicación de MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication.html). El uso de una opción de alta disponibilidad compatible no proporciona un SLA adicional, pero permite lograr una disponibilidad de base de datos del 99,99 % a cambio de un costo adicional y sobrecarga administrativa.

Para más información sobre los precios, consulte los siguientes artículos:

- [Precios de Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/)
- [Precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administración

En muchas empresas, la decisión de pasar a un servicio en la nube está tan relacionada con la posibilidad de reducir la carga de complejidad de administración como con el costo.

Con IaaS, Microsoft:

- Administra la infraestructura subyacente.
- Proporciona revisiones automatizadas para el hardware y el sistema operativo subyacentes.
  
Con PaaS, Microsoft:

- Administra la infraestructura subyacente.
- Proporciona una aplicación automatizada de revisiones para el hardware, el sistema operativo y el motor de base de datos subyacentes.
- Administra la alta disponibilidad de la base de datos.
- Realiza copias de seguridad y replicaciones automáticas de todos los datos para proporcionar recuperación ante desastres.
- Cifra los datos en reposo y en movimiento de forma predeterminada.
- Supervisa el servidor y proporciona características para obtener información de rendimiento de consultas y recomendaciones de rendimiento

En la lista siguiente se describen las consideraciones administrativas para cada opción:

- Con Azure Database for MySQL, puede seguir administrando la base de datos. Sin embargo, ya no necesita administrar el motor de base de datos, el sistema operativo o el hardware. Entre los ejemplos de elementos que puede seguir administrando se incluyen:

  - Bases de datos
  - Inicio de sesión
  - Ajuste del índice
  - Ajuste de las consultas
  - Auditoría
  - Seguridad

  Además, la configuración de alta disponibilidad en otro centro de datos no requiere configuración y administración o, si acaso, ajustes mínimos.

- Con MySQL en las máquinas virtuales de Azure tiene un control completo sobre la configuración del sistema operativo y la instancia de MySQL Server. Con una máquina virtual, puede decidir cuándo actualizar o renovar el sistema operativo y el software de la base de datos, y qué revisiones aplicar. También puede decidir cuándo instalar cualquier software adicional, como una aplicación antivirus. Se proporcionan algunas características automatizadas para simplificar considerablemente la aplicación de revisiones, las copias de seguridad y la alta disponibilidad. Puede controlar el tamaño de la máquina virtual, el número de discos y sus configuraciones de almacenamiento. Para más información, consulte [Tamaños de las máquinas virtuales y los servicios en la nube de Azure](../virtual-machines/sizes.md).

### <a name="time-to-move-to-azure"></a>Es hora de cambiar a Azure

- Azure Database for MySQL es la solución adecuada para las aplicaciones diseñadas para la nube cuando la productividad del desarrollador y un plazo de comercialización rápido de las nuevas soluciones son factores críticos. Con una funcionalidad mediante programación de tipo DBA, el servicio es idóneo para arquitectos y desarrolladores de la nube, puesto que reduce la necesidad de administrar el sistema operativo y la base de datos subyacentes.

- Si quiere evitar el tiempo y los gastos de adquirir nuevo hardware local, MySQL en máquinas virtuales de Azure es la solución adecuada para las aplicaciones que necesitan un control granular y la personalización del motor de MySQL que no admite el servicio o que requiere acceso al sistema operativo subyacente. Esta solución también es idónea para migrar aplicaciones y bases de datos locales existentes a Azure tal cual en casos en los que Azure Database for MySQL no sea una buena solución.

Dado que no es necesario cambiar las capas de presentación, aplicación y datos, se ahorra tiempo y presupuesto a la hora de renovar la arquitectura de la solución existente. Así, puede centrarse en migrar todas las soluciones a Azure y en abordar algunas optimizaciones de rendimiento que puede requerir la plataforma Azure.

## <a name="next-steps"></a>Pasos siguientes

- Consulte [Precios de Azure Database for MySQL](https://azure.microsoft.com/pricing/details/MySQL/).
- Comenzar por [crear el primer servidor](./quickstart-create-mysql-server-database-using-azure-portal.md).
