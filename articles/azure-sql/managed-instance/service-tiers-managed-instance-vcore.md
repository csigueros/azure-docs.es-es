---
title: Modelo de compra de núcleo virtual
description: El modelo de compra de núcleo virtual permite escalar los recursos de proceso y de almacenamiento de manera independiente, igualar el rendimiento local y optimizar el precio para Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sashan, moslake
ms.date: 05/18/2021
ms.openlocfilehash: 16782538918c0477b969f95b7730b38221f97f82
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2021
ms.locfileid: "111415217"
---
# <a name="azure-sql-managed-instance---compute-hardware-in-the-vcore-service-tier"></a>Azure SQL Managed Instance: hardware de proceso en el nivel de servicio de núcleo virtual
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

En este artículo se revisa el modelo de compra de núcleo virtual para [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md). Para más información sobre cómo elegir entre los modelos de compra de núcleos virtuales y DTU, consulte [Elección entre los modelos de compra de núcleo virtual y DTU: Azure SQL Database y SQL Managed Instance](../database/purchasing-models.md).

El modelo de compra de núcleo virtual usado por Azure SQL Managed Instance tiene las características siguientes:

- Control sobre la generación de hardware para satisfacer mejor los requisitos de proceso y memoria de la carga de trabajo.
- Descuentos en los precios de [Ventaja híbrida de Azure (AHB)](../azure-hybrid-benefit.md) e [Instancia reservada (RI)](../database/reserved-capacity-overview.md).
- Mayor transparencia en los detalles de hardware que potencian el proceso, lo que facilita la planeación de las migraciones desde implementaciones locales.
- [Los precios de las instancias reservadas](../database/reserved-capacity-overview.md) solo están disponibles para el modelo de compra de núcleo virtual. 

## <a name="service-tiers"></a><a id="compute-tiers"></a>Niveles de servicio

Entre las opciones de nivel de servicio del modelo de compra de núcleo virtual se incluyen Uso general y Crítico para la empresa. El nivel de servicio define generalmente la arquitectura de almacenamiento, los límites de espacio y de E/S y las opciones de continuidad empresarial relacionadas con la disponibilidad y la recuperación ante desastres.

|**Caso de uso**|**Uso general**|**Crítico para la empresa**|
|---|---|---|
|Más adecuado para|La mayoría de las cargas de trabajo empresariales. Ofrece opciones de proceso y almacenamiento equilibradas y escalables pensando en el presupuesto. |Ofrece a las aplicaciones empresariales la mayor resistencia a los errores mediante el uso de varias réplicas aisladas y proporciona el mayor rendimiento de E/S.|
|Storage|Usa el almacenamiento remoto. 32 GB - 8 TB |Usa almacenamiento local de SSD. 32 GB - 4 TB |
|IOPS y rendimiento (aproximado)|consulte [Introducción a los límites de recursos de Instancia administrada de Azure SQL](../managed-instance/resource-limits.md#service-tier-characteristics).|consulte [Introducción a los límites de recursos de Instancia administrada de Azure SQL](../managed-instance/resource-limits.md#service-tier-characteristics).|
|Disponibilidad|1 réplica, sin réplicas de escalado de lectura|3 réplicas, 1 [réplica de escalado de lectura](../database/read-scale-out.md),<br/>Alta disponibilidad (HA) con redundancia de zona|
|Copias de seguridad|[Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS)](../../storage/common/geo-redundant-design.md), de 1 a 35 días (7 días de forma predeterminada)|[RA-GRS](../../storage/common/geo-redundant-design.md), de 1 a 35 días (7 días de forma predeterminada)|
|En memoria|No compatible|Compatible|
||||

### <a name="choosing-a-service-tier"></a>Selección de un nivel de servicio

Para obtener información sobre cómo seleccionar un nivel de servicio para una carga de trabajo determinada, consulte los siguientes artículos:

- [Cuándo elegir el nivel de servicio De uso general](../database/service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Cuándo elegir el nivel de servicio Crítico para la empresa](../database/service-tier-business-critical.md#when-to-choose-this-service-tier)

## <a name="compute"></a>Compute

El proceso de SQL Managed Instance proporciona una cantidad específica de recursos de proceso que se aprovisionan continuamente con independencia de la actividad de carga de trabajo y factura la cantidad de proceso aprovisionado a un precio fijo por hora.

## <a name="hardware-generations"></a>Generaciones de hardware

Entre las opciones de generación de hardware del modelo de núcleo virtual se incluye la serie de hardware Gen 5. En general, la generación de hardware define los límites de proceso y de memoria, así como otras características que afectan al rendimiento de la carga de trabajo.

### <a name="compute-and-memory-specifications"></a>Especificaciones de memoria y proceso

|Generación de hardware  |Proceso  |Memoria  |
|:---------|:---------|:---------|
|Gen4     |- Procesadores Intel&reg; E5-2673 v3 (Haswell) de 2,4 GHz<br>- Aprovisionamiento de hasta 24 núcleos virtuales (1 núcleo virtual = 1 núcleo físico)  |- 7 GB por núcleo virtual<br>- Aprovisionamiento de hasta 168 GB|
|Gen5     |- Procesadores Intel&reg; E5-2673 v4 (Broadwell) de 2,3 GHz, Intel&reg; SP-8160 (Skylake)\* e Intel&reg; 8272CL (Cascade Lake) de 2,5 GHz\*<br>- Aprovisionamiento de hasta 80 núcleos virtuales (1 núcleo virtual = 1 hiperproceso)|5,1 GB por núcleo virtual<br>- Aprovisionamiento de hasta 408 GB|

\* En la vista de administración dinámica [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database), la generación de hardware para bases de datos que usan procesadores Intel&reg; SP-8160 (Skylake) aparece como Gen6, mientras que la generación de hardware para las instancias que usan procesadores Intel&reg; 8272CL (Cascade Lake) aparece como Gen7. Los límites de recursos en todas las instancias Gen5 son los mismos, independientemente del tipo de procesador (Broadwell, Skylake o Cascade Lake).

### <a name="selecting-a-hardware-generation"></a>Selección de una generación de hardware

En Azure Portal, puede seleccionar la generación de hardware en el momento de la creación o puede cambiar la generación de hardware de una instancia de SQL Managed Instance existente.

**Selección de una generación de hardware al crear una instancia administrada de SQL**

Para obtener información detallada, consulte [Creación de una instancia administrada de SQL](../managed-instance/instance-create-quickstart.md).

En la pestaña **Aspectos básicos**, seleccione el vínculo **Configurar base de datos** en la sección **Compute + storage** (Proceso y almacenamiento) y, a continuación, seleccione la generación de hardware deseada:

:::image type="content" source="../database/media/service-tiers-vcore/configure-managed-instance.png" alt-text="configurar la instancia administrada de SQL"  loc-scope="azure-portal":::
  
**Cambio de la generación de hardware de una instancia administrada de SQL existente**

#### <a name="the-azure-portal"></a>[Portal de Azure](#tab/azure-portal)

En la página de instancia administrada de SQL, seleccione el vínculo **Plan de tarifa** situado en la sección Configuración.

:::image type="content" source="../database/media/service-tiers-vcore/change-managed-instance-hardware.png" alt-text="cambiar el hardware de instancia administrada de SQL"  loc-scope="azure-portal":::

En la página Plan de tarifa, podrá cambiar la generación de hardware tal y como se describe en los pasos anteriores.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Use el siguiente script de PowerShell:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Para más información, vea el comando [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance).

#### <a name="the-azure-cli"></a>[La CLI de Azure](#tab/azure-cli)

Use el siguiente comando de la CLI:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Para más información, vea el comando [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update).

---

### <a name="hardware-availability"></a>Disponibilidad de hardware

#### <a name="gen4gen5"></a><a id="gen4gen5-1"></a> Gen4/Gen5

El hardware de Gen4 está [en proceso de eliminación gradual](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) y ya no está disponible para las nuevas implementaciones. Todas las nuevas instancias administradas deben implementarse en hardware Gen5.

Gen5 está disponible en todas las regiones públicas de todo el mundo.

## <a name="next-steps"></a>Pasos siguientes

- Para empezar, vea [Creación de una instancia SQL Managed Instance mediante Azure Portal](instance-create-quickstart.md).
- Para ver los detalles de precios, consulte: 
    - [Página de precios de instancia única de Azure SQL Managed Instance](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/single/)
    - [Página de precios de grupos de Azure SQL Managed Instance](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/pools/)
- Para más información sobre los tamaños de proceso y almacenamiento específicos que están disponibles en los niveles de servicio de uso general y crucial para la empresa, consulte [Límites de recursos basados en núcleo virtual de Azure SQL Managed Instance](resource-limits.md).
