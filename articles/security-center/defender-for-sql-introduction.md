---
title: 'Microsoft Defender para SQL: ventajas y características'
description: Obtenga información sobre las ventajas y características de Microsoft Defender para SQL.
author: memildin
ms.author: memildin
ms.date: 05/27/2021
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 51e031034e344de6e1e8b12874a443fbf51afb42
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057468"
---
# <a name="introduction-to-microsoft-defender-for-sql"></a>Introducción a Microsoft Defender para SQL

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender para SQL incluye dos planes de Microsoft Defender que amplían el [paquete de seguridad de datos](../azure-sql/database/azure-defender-for-sql.md) de Microsoft Defender for Cloud para proteger las bases de datos y sus datos dondequiera que se encuentren. Microsoft Defender para SQL incluye funcionalidades para detectar y mitigar las posibles vulnerabilidades de las bases de datos, así como para detectar actividades anómalas que puedan indicar una amenaza para ellas.

## <a name="availability"></a>Disponibilidad

|Aspecto|Detalles|
|----|:----|
|Estado de la versión:|**Microsoft Defender para servidores de base de datos de Azure SQL**: disponible con carácter general (GA)<br>**Microsoft Defender para servidores SQL Server en máquinas**: disponible con carácter general (GA) |
|Precios:|Los dos planes que forman **Microsoft Defender para SQL** se facturan como se muestra en [la página de precios](https://azure.microsoft.com/pricing/details/security-center/).|
|Versiones de SQL protegidas:|[SQL en máquinas virtuales de Azure](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)<br>[SQL Server en servidores habilitados para Azure Arc](/sql/sql-server/azure-arc/overview)<br>Servidores SQL Server locales en máquinas Windows sin Azure Arc<br>[Bases de datos únicas](../azure-sql/database/single-database-overview.md) y [grupos elásticos](../azure-sql/database/elastic-pool-overview.md) de Azure SQL<br>[Instancia administrada de Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Grupo de SQL dedicado de Azure Synapse Analytics (anteriormente SQL DW)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|Nubes:|:::image type="icon" source="./media/icons/yes-icon.png"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure China 21Vianet (**parcial**: subconjunto de alertas y evaluación de vulnerabilidades en servidores SQL. No hay disponible protección contra amenazas de comportamiento).|
|||

## <a name="what-does-microsoft-defender-for-sql-protect"></a>¿Qué protege Microsoft Defender para SQL?

**Microsoft Defender para SQL** consta de dos planes de Microsoft Defender independientes:

- **Microsoft Defender para servidores de base de datos de Azure SQL** protege:
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
    - [Instancia administrada de Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
    - [Grupo de SQL dedicado en Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **Microsoft Defender para servidores SQL Server en máquinas** amplía las protecciones de los servidores SQL Server nativos de Azure para admitir totalmente los entornos híbridos y proteger los servidores SQL Server (todas las versiones compatibles) hospedados en Azure, otros entornos en la nube e incluso en máquinas locales:
    - [SQL Server en Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/)
    - Servidores SQL Server locales:
        - [SQL Server habilitado para Azure Arc (versión preliminar)](/sql/sql-server/azure-arc/overview)
        - [Servidores SQL Server que se ejecutan en máquinas Windows sin Azure Arc](../azure-monitor/agents/agent-windows.md)


## <a name="what-are-the-benefits-of-microsoft-defender-for-sql"></a>¿Cuáles son las ventajas de Microsoft Defender para SQL?

Estos dos planes incluyen funcionalidad para detectar y mitigar posibles vulnerabilidades de las bases de datos, además de detectar actividades anómalas que puedan suponer alguna amenaza para ellas:

Un servicio de evaluación de vulnerabilidades detecta posibles vulnerabilidades de la base de datos, las sigue y ayuda a corregirlas. Los exámenes de evaluación proporcionan información general sobre el estado de seguridad de las máquinas SQL y detalles de las conclusiones de seguridad.

- Obtenga más información sobre la [evaluación de vulnerabilidades para Azure SQL Database](../azure-sql/database/sql-vulnerability-assessment.md).
- Obtenga más información sobre la [evaluación de vulnerabilidades para servidores de Azure SQL en máquinas](defender-for-sql-on-machines-vulnerability-assessment.md).

Un servicio de protección contra amenazas avanzada supervisa continuamente los servidores SQL Server para detectar amenazas tales como inyección de código SQL, ataques por fuerza bruta y abuso de privilegios. Este servicio proporciona alertas de seguridad orientadas a acciones en Microsoft Defender for Cloud con detalles de la actividad sospechosa, instrucciones sobre cómo mitigar las amenazas y opciones para continuar las investigaciones con Microsoft Sentinel. Más información sobre la [protección contra amenazas avanzada](../azure-sql/database/threat-detection-overview.md).

 > [!TIP]
 > Consulte la lista de alertas de seguridad de los servidores de SQL Server [en la página de referencia de alertas](alerts-reference.md#alerts-sql-db-and-warehouse).


## <a name="is-there-a-performance-impact-from-deploying-microsoft-defender-for-sql-on-machines"></a>La implementación de Microsoft Defender para SQL en las máquinas, ¿tiene un impacto en el rendimiento?

Evidentemente, el foco de **Microsoft Defender para SQL en máquinas** es la seguridad. Pero también nos interesa su actividad comercial y, por tanto, hemos priorizado el rendimiento para garantizar el impacto mínimo en los servidores SQL. 

El servicio tiene una arquitectura dividida para equilibrar la carga y la velocidad de los datos con el rendimiento: 

- Algunos de nuestros detectores se ejecutan en la máquina para obtener ventajas de velocidad en tiempo real.
- Otros se ejecutan en la nube para evitar cargas de cálculo pesadas en la máquina.

Las pruebas de laboratorio de nuestra solución, que la comparaban con las cargas de referencia, mostraron un promedio de uso de CPU del 3 % para los segmentos máximos. Un análisis de la telemetría de nuestros usuarios actuales muestra un impacto insignificante en el uso de CPU y memoria.

Por supuesto, el rendimiento siempre varía en función de los entornos, máquinas y cargas. Las declaraciones y números anteriores se brindan como guía general, no como garantía para ninguna implementación individual.


## <a name="what-kind-of-alerts-does-microsoft-defender-for-sql-provide"></a>¿Qué tipos de alertas ofrece Microsoft Defender para SQL?

Las alertas de seguridad enriquecidas con inteligencia sobre amenazas se desencadenan en estos casos:

- **Posibles ataques por inyección de código SQL**, incluidas las vulnerabilidades detectadas cuando las aplicaciones generan una instrucción SQL defectuosa en la base de datos.
- **Acceso a bases de datos y patrones de consulta anómalos**: por ejemplo, un número anormalmente alto de intentos de inicio de sesión incorrectos con credenciales diferentes (un intento por fuerza bruta).
- **Actividad sospechosa en las bases de datos**: por ejemplo, un usuario legítimo que accede a un servidor SQL Server desde un equipo vulnerado que se comunica con un servidor de comando y control de minería de datos de cifrado.

Las alertas incluyen detalles acerca del incidente que las desencadenó, así como recomendaciones acerca de cómo investigar y corregir las amenazas.



## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha obtenido información sobre Microsoft Defender para SQL. Para usar los servicios que se han descrito:

- Use servidores de Microsoft Defender para SQL en las máquinas para [examinar las vulnerabilidades de los servidores SQL Server](defender-for-sql-usage.md).
- Para una presentación de Microsoft Defender para SQL, vea cómo [Microsoft Defender para SQL puede proteger servidores SQL Server en cualquier lugar](https://www.youtube.com/watch?v=V7RdB6RSVpc).
