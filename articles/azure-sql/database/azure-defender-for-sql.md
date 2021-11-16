---
title: Microsoft Defender para SQL
description: Conozca la funcionalidad para administrar los puntos vulnerables de la base de datos y detectar actividades anómalas que podrían indicar una amenaza para la base de datos en Azure SQL Database, Azure SQL Managed Instance o Azure Synapse.
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 06/07/2021
ms.openlocfilehash: 77c9e9fc02ba5fdb5f67cf14495103b352196f0c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347891"
---
# <a name="microsoft-defender-for-sql"></a>Microsoft Defender para SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Microsoft Defender para SQL es un paquete unificado de funcionalidades avanzadas de seguridad de SQL. Microsoft Defender for Cloud está disponible para Azure SQL Database, Azure SQL Managed Instance y Azure Synapse Analytics. Incluye funcionalidades para buscar y mitigar las posibles vulnerabilidades de la base de datos, así como para detectar actividades anómalas que puedan indicar una amenaza para ella. Proporciona una ubicación única para habilitar y administrar estas funcionalidades.

## <a name="what-are-the-benefits-of-microsoft-defender-for-sql"></a>¿Cuáles son las ventajas de Microsoft Defender para SQL?

Microsoft Defender for Cloud proporciona un conjunto de funcionalidades avanzadas de seguridad de SQL, entre las que se incluyen Evaluación de vulnerabilidad de SQL y Advanced Threat Protection.
- [Evaluación de vulnerabilidad](sql-vulnerability-assessment.md) es un servicio fácil de configurar que puede detectar, realizar el seguimiento y ayudarle a corregir vulnerabilidades de la base de datos. Permite ver el estado de la seguridad e incluye pasos interactivos para resolver problemas de seguridad y mejorar las defensas de la base de datos.
- [Advanced Threat Protection](threat-detection-overview.md) permite detectar actividades anómalas que indica intentos poco habituales y posiblemente dañinos de acceder a sus bases de datos o aprovecharse de ellas. Supervisa de forma continuada la base de datos para detectar actividades sospechosas y proporciona de forma inmediata alertas de seguridad sobre posibles vulnerabilidades, ataques por inyección de código SQL de Azure y patrones anómalos de acceso a la base de datos. Las alertas de Advanced Threat Protection proporcionan detalles de la actividad sospechosa y recomiendan acciones para investigar y mitigar la amenaza.

Si habilita Microsoft Defender para SQL una vez, habilitará todas estas características incluidas. Con un solo clic, puede habilitar Microsoft Defender para todas las bases de datos en el [servidor](logical-servers.md) en Azure o en SQL Managed Instance. Para habilitar o administrar la configuración de Microsoft Defender for Cloud es necesario pertenecer al rol [administrador de seguridad SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager), o bien a uno de los roles de administrador de base de datos o de servidor.

Para obtener más información sobre los precios de Microsoft Defender para SQL, consulte la [página de precios de Microsoft Defender for Cloud](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-microsoft-defender-for-cloud"></a>Habilitación de Microsoft Defender for Cloud

Hay varias maneras de habilitar los planes de Microsoft Defender. Puede habilitarlo en el nivel de suscripción (**recomendado**) desde:

- [Microsoft Defender for Cloud](#enable-microsoft-defender-for-azure-sql-database-at-the-subscription-level-from-microsoft-defender-for-cloud)
- [Habilite los planes de Microsoft Defender mediante programación con la API REST, la CLI de Azure, PowerShell o Azure Policy](#enable-microsoft-defender-plans-programatically).

Como alternativa, puede habilitarlo en el nivel de recursos, tal y como se describe en [Habilitación de Microsoft Defender para Azure SQL Database en el nivel de recursos](#enable-microsoft-defender-for-azure-sql-database-at-the-resource-level).

### <a name="enable-microsoft-defender-for-azure-sql-database-at-the-subscription-level-from-microsoft-defender-for-cloud"></a>Habilitación de Microsoft Defender para Azure SQL Database en el nivel de suscripción desde Microsoft Defender for Cloud
Para habilitar Microsoft Defender para Azure SQL Database en el nivel de suscripción desde dentro de Microsoft Defender for Cloud:

1. En [Azure Portal](https://portal.azure.com), abra **Defender for Cloud**.
1. En el menú de Defender for Cloud, seleccione **Pricing and settings** (Precios y configuración).
1. Seleccione la suscripción correspondiente.
1. Cambie la configuración del plan a **Activado**.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender-sql-subscription-level.png" alt-text="Habilitación de Microsoft Defender para Azure SQL Database en el nivel de suscripción.":::

1. Seleccione **Guardar**.


### <a name="enable-microsoft-defender-plans-programatically"></a>Habilitación de planes de Microsoft Defender mediante programación 

La flexibilidad de Azure permite una serie de métodos de programación para habilitar los planes de Microsoft Defender. 

Use cualquiera de las siguientes herramientas para habilitar Microsoft Defender para su suscripción: 

| Método       | Instructions                                                                                                                                       |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| API DE REST     | [API de precios](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI    | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell   | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy | [Precios de conjunto](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|              |                                                                                                                                                    |

### <a name="enable-microsoft-defender-for-azure-sql-database-at-the-resource-level"></a>Habilitación de Microsoft Defender para Azure SQL Database en el nivel de recursos

Se recomienda habilitar los planes de Microsoft Defender en el nivel de suscripción, y esto puede ayudar a crear recursos sin protección. Pero, si tiene una razón organizativa para habilitar Microsoft Defender for Cloud a nivel de servidor, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com), abra el servidor o la instancia administrada.
1. En el encabezado **Seguridad**, seleccione **Defender for Cloud**.
1. Seleccione **Enable Microsoft Defender for SQL** (Habilitar Microsoft Defender para SQL).

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Habilitación de Microsoft Defender para SQL desde bases de datos de Azure SQL.":::

> [!NOTE]
> Una cuenta de almacenamiento se crea y se configura automáticamente para almacenar los resultados del examen de **evaluación de vulnerabilidades**. Si ya ha habilitado Microsoft Defender para otro servidor del mismo grupo de recursos y la misma región, se usa la cuenta de almacenamiento existente.
>
> El costo de Microsoft Defender para SQL se alinea con los precios por nodo del nivel estándar de Microsoft Defender for Cloud, donde un nodo es todo el servidor o la instancia administrada. Por tanto, se paga una sola vez por proteger con Microsoft Defender for Cloud todas las bases de datos del servidor o la instancia administrada. Puede evaluar Microsoft Defender for Cloud a través de una prueba gratuita.

## <a name="manage-microsoft-defender-for-cloud-settings"></a>Administración de la configuración de Microsoft Defender for Cloud

Para ver y administrar la configuración de Microsoft Defender for Cloud:

1. En el área **Seguridad** del servidor o la instancia administrada seleccione **Defender for Cloud**.

    En esta página se ve el estado de Microsoft Defender para SQL:

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="Comprobación del estado de Microsoft Defender para SQL en bases de datos de Azure SQL.":::

1. Si Microsoft Defender para SQL está habilitado, se ve un vínculo **Configurar**, como se muestra en el gráfico anterior. Para editar la configuración de Microsoft Defender para SQL, seleccione **Configurar**.

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="Configuración de Microsoft Defender para SQL.":::

1. Realice los cambios necesarios y seleccione **Guardar**.


## <a name="next-steps"></a>Pasos siguientes

- Más información acerca de [Evaluación de vulnerabilidad](sql-vulnerability-assessment.md)
- Más información sobre [Advanced Threat Protection](threat-detection-configure.md)
- Obtenga más información sobre [Microsoft Defender for Cloud](../../security-center/security-center-introduction.md).
