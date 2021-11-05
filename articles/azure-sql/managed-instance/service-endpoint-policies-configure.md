---
title: Configuración de directivas de punto de conexión de servicio
description: Configure directivas de punto de conexión de servicio de Azure Storage para proteger Azure SQL Managed Instance frente a la filtración a cuentas de Azure Storage no autorizadas.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: references_regions, ignite-fall-2021
ms.devlang: ''
ms.topic: how-to
author: zoran-rilak-msft
ms.author: zoranrilak
ms.reviewer: mathoma
ms.date: 11/02/2021
ms.openlocfilehash: 9760285da827e884c5dfa49ca1da58b3e76292de
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091176"
---
# <a name="configure-service-endpoint-policies-preview-for-azure-sql-managed-instance"></a>Configuración de directivas de punto de conexión de servicio (versión preliminar) para Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Las [directivas de punto de conexión de servicio](../../virtual-network/virtual-network-service-endpoint-policies-overview.md) de Azure Storage de red virtual (VNet) le permiten filtrar el tráfico de red virtual de salida a Azure Storage, lo que restringe las transferencias de datos a cuentas de almacenamiento específicas.

La capacidad de configurar las directivas de punto de conexión y asociarlas a SQL Managed Instance está actualmente en versión preliminar. 

## <a name="key-benefits"></a>Ventajas principales

La configuración de directivas de punto de conexión de servicio de Azure Storage de red virtual para Azure SQL Managed Instance proporciona las siguientes ventajas:

- __Mejor seguridad para el tráfico de Azure SQL Managed Instance a Azure Storage__: las directivas de punto de conexión establecen un control de seguridad que evita la filtración errónea o malintencionada de datos críticos para la empresa. El tráfico se puede limitar solo a las cuentas de almacenamiento que cumplen los requisitos de gobernanza de datos.

- __Control granular sobre las cuentas de almacenamiento a las que se puede acceder__: las directivas de punto de conexión de servicio pueden permitir el tráfico a las cuentas de almacenamiento en un nivel de suscripción, grupo de recursos y cuenta de almacenamiento individual. Los administradores pueden usar directivas de punto de conexión de servicio para aplicar el cumplimiento de la arquitectura de seguridad de datos de la organización en Azure.

- __El tráfico del sistema no se ve afectado__: las directivas de punto de conexión de servicio nunca impiden el acceso al almacenamiento necesario para que Azure SQL Managed Instance funcione. Esto incluye el almacenamiento de copias de seguridad, archivos de datos, archivos de registro de transacciones y otros recursos.

> [!IMPORTANT]
> Las directivas de punto de conexión de servicio solo controlan el tráfico que se origina en la subred SQL Managed Instance y finaliza en Azure Storage. Las directivas no afectan, por ejemplo, a la exportación de la base de datos a un archivo BACPAC local, la integración de Azure Data Factory, la recopilación de información de diagnóstico a través de Configuración de diagnóstico de Azure u otros mecanismos de extracción de datos que no tienen como destino Azure Storage.

## <a name="limitations"></a>Limitaciones

La habilitación de directivas de punto de conexión de servicio para Azure SQL Managed Instance tiene las siguientes limitaciones:

- Mientras está en versión preliminar, esta característica está disponible en todas las regiones de Azure en las que se admite SQL Managed Instance, excepto en **Este de China 2**, **Norte de China 2**, **Centro de EE. UU. EUAP**, **Este de EE. UU. 2 EUAP**, **US Gov Arizona**, **US Gov Texas**, **US Gov Virginia** y **Centro-oeste de EE. UU.** .
- Esta característica solo está disponible en las redes virtuales implementadas con el modelo de implementación de Azure Resource Manager.
- La característica solo está disponible en subredes que tienen [puntos de conexión de servicio](../../virtual-network/virtual-network-service-endpoints-overview.md) habilitados para Azure Storage.
- La habilitación de puntos de conexión de servicio para Azure Storage también se amplía para incluir las regiones emparejadas en las que se implementa la red virtual para admitir el tráfico del almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) y el del almacenamiento con redundancia geográfica (GRS).
- La asignación de una directiva de punto de conexión de servicio a un punto de conexión de servicio actualiza el punto de conexión de ámbito regional a global. Es decir, todo el tráfico a Azure Storage pasará por el punto de conexión de servicio independientemente de la región en la que resida la cuenta de almacenamiento.

## <a name="prepare-storage-inventory"></a>Preparación del inventario de almacenamiento

Antes de empezar a configurar directivas de punto de conexión de servicio en una subred, cree una lista de cuentas de almacenamiento a las que la instancia administrada debe tener acceso en esa subred.

A continuación, se muestra una lista de flujos de trabajo que pueden ponerse en contacto con Azure Storage:

- [Auditoría](auditing-configure.md) de Azure Storage.
- Realización de una [copia de seguridad de solo copia](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) en Azure Storage.
- [Restauración](restore-sample-database-quickstart.md) de una base de datos desde Azure Storage.
- Importación de datos con [BULK INSERT o OPENROWSET(BULK ...)](/sql/relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server).
- Registro de [eventos extendidos](../database/xevent-db-diff-from-svr.md) en un destino de archivo de eventos en Azure Storage.
- [Migración de sin conexión de Azure DMS](../../dms/tutorial-sql-server-to-managed-instance.md) a Azure SQL Managed Instance.
- [Migración del servicio de repetir registro](log-replay-service-migrate.md) a Azure SQL Managed Instance.
- Sincronización de tablas con la [replicación transaccional](replication-transactional-overview.md).

Anote el nombre de la cuenta, el grupo de recursos y la suscripción de cualquier cuenta de almacenamiento que participe en estos o en cualquier otro flujo de trabajo que acceda al almacenamiento. 


## <a name="configure-policies"></a>Configurar directivas

Primero deberá crear la directiva de punto de conexión de servicio y, a continuación, asociarla a la subred SQL Managed Instance. Modifique el flujo de trabajo de esta sección para adaptarlo a sus necesidades empresariales.


> [!NOTE]
> - Las subredes de SQL Managed Instance requieren directivas que contengan el alias de servicio /Services/Azure/ManagedInstance (consulte el paso 5). 
> - Las instancias administradas implementadas en una subred que ya contiene directivas de punto de conexión de servicio se actualizarán automáticamente con el alias de servicio /Services/Azure/ManagedInstance.

### <a name="create-a-service-endpoint-policy"></a>Creación de una directiva de punto de conexión de servicio

Para crear una directiva de punto de conexión de servicio, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com). 
1. Seleccione **+ Crear un recurso**. 
1. En el panel de búsqueda, escriba _directiva de punto de conexión de servicio_, seleccione **Directiva del punto de conexión de servicio** y, a continuación, seleccione **Crear**.

   ![Creación de una directiva de punto de conexión de servicio](../../virtual-network/media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

1. Rellene los siguientes valores en la página **Datos básicos**:

   - Suscripción: seleccione la suscripción de la directiva en la lista desplegable.
   - Grupo de recursos: seleccione el grupo de recursos donde se encuentra la instancia administrada o seleccione **Crear nuevo** y rellene el nombre de un nuevo grupo de recursos.
   - Nombre: proporcione un nombre para la directiva, por ejemplo, **mySEP**.
   - Ubicación: seleccione la región de la red virtual que hospeda la instancia administrada. 

   ![Conceptos básicos de Crear una directiva de punto de conexión de servicio](../../virtual-network/media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

1. En **Definiciones de directivas**, seleccione **Agregar un alias** y escriba la siguiente información en el panel **Agregar un alias**:
   - Alias de servicio: seleccione /Services/Azure/ManagedInstance.
   - Haga clic en **Agregar** para terminar de agregar la nueva directiva de acceso.

   ![Adición de un alias a una directiva de punto de conexión de servicio](./media/service-endpoint-policies-configure/add-an-alias.png)

1. En Definiciones de directivas, seleccione **+ Agregar** en **Recursos** y escriba o seleccione la siguiente información en el panel **Agregar un recurso**:
   - Servicio: seleccione **Microsoft.Storage**.
   - Ámbito: seleccione **Todas las cuentas de la suscripción**.
   - Suscripción: seleccione una suscripción que contenga las cuentas de almacenamiento que desea permitir. Consulte el [inventario de cuentas de almacenamiento de Azure](#prepare-storage-inventory) creadas anteriormente.
   - Seleccione **Agregar** para terminar de agregar el recurso.
   - Repita este paso para agregar suscripciones adicionales. 

   ![Adición de un recurso a una directiva de punto de conexión de servicio](./media/service-endpoint-policies-configure/add-a-resource.png)

1. Opcional: puede configurar etiquetas en la directiva de punto de conexión de servicio en **Etiquetas**.
1.  Seleccione **Revisar + crear**. Valide la información y seleccione **Crear**. Para hacer más ediciones, seleccione **Anterior**.

   > [!TIP]
   > En primer lugar, configure las directivas para permitir el acceso a suscripciones completas. Valide la configuración asegurándose de que todos los flujos de trabajo funcionan con normalidad. A continuación, si lo desea, puede volver a configurar las directivas para permitir cuentas de almacenamiento individuales o cuentas en un grupo de recursos. Para ello, seleccione **Cuenta única** o **Todas las cuentas del grupo de recursos** en el campo _Ámbito:_ en su lugar y rellene los demás campos en consecuencia.

### <a name="associate-policy-with-subnet"></a>Asociación de una directiva a una subred

Una vez creada la directiva de punto de conexión de servicio, asóciela a la subred de SQL Managed Instance.

Para asociar la directiva, siga estos pasos:

1. En el cuadro _Todos los servicios_ de Azure Portal, busque _Redes virtuales_. Seleccione **Redes virtuales**.
1. Busque y seleccione la red virtual que hospeda la instancia administrada.
1. Seleccione **Subredes** y elija la subred dedicada a la instancia administrada. Escriba la siguiente información en el panel de subred:
    - Servicio: seleccione **Microsoft.Storage**. Si este campo está vacío, debe configurar el punto de conexión de servicio para Azure Storage en esta subred.
    - Directivas de punto de conexión de servicio: seleccione las directivas de punto de conexión de servicio que quiera aplicar a la subred de SQL Managed Instance.

   ![Asociación una directiva de punto de conexión de servicio a una subred](./media/service-endpoint-policies-configure/associate-service-endpoint-policy.png)

1. Seleccione **Guardar** para terminar de configurar la red virtual.

> [!WARNING]
> Si las directivas de esta subred no tienen el alias `/Services/Azure/ManagedInstance`, es posible que vea el siguiente error: ` Failed to save subnet 'subnet'. Error: 'Found conflicts with NetworkIntentPolicy.`
> `Details: Service endpoint policies on subnet are missing definitions`
> Para resolver este problema, actualice todas las directivas de la subred para incluir el alias `/Services/Azure/ManagedInstance`.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre la [protección de cuentas de Azure Storage](../../storage/common/storage-network-security.md).
- Lea sobre las [capacidades de seguridad de SQL Managed Instance](../database/security-overview.md).
- Explore la [arquitectura de conectividad](connectivity-architecture-overview.md) de Azure SQL Managed Instance.
