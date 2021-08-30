---
title: Administración de máquinas virtuales con SQL Server en Azure mediante Azure Portal | Microsoft Docs
description: Aprenda a acceder al recurso de máquina virtual de SQL en Azure Portal en una VM con SQL Server hospedada en Azure para modificar la configuración de SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2021
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 37b4f063f2d08c137c44784c55cc3c47569a3c56
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113434595"
---
# <a name="manage-sql-server-vms-by-using-the-azure-portal"></a>Administración de máquinas virtuales con SQL Server mediante Azure Portal
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

En [Azure Portal](https://portal.azure.com), el recurso [**Máquinas virtuales SQL**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) es un servicio de administración independiente para administrar SQL Server en las máquinas virtuales de Azure que se han registrado con la extensión Agente de IaaS de SQL Server. Puede usar el recurso para ver todas las máquinas virtuales con SQL Server a la vez y modificar la configuración dedicada a SQL Server: 

![Recurso máquinas virtuales SQL](./media/manage-sql-vm-portal/sql-vm-manage.png)

El punto de administración del recurso **Máquinas virtuales SQL** es diferente al del recurso **Máquina virtual** usado para administrar la máquina virtual, por ejemplo, iniciarla, detenerla o reiniciarla. 


## <a name="prerequisite"></a>Requisito previo 

El recurso **Máquinas virtuales SQL** solo está disponible para las máquinas virtuales con SQL Server que se hayan [registrado con la extensión Agente de IaaS de SQL](sql-agent-extension-manually-register-single-vm.md). 


## <a name="access-the-resource"></a>Acceso al recurso

Para acceder al recurso **Máquinas virtuales SQL**, haga lo siguiente:

1. Abra [Azure Portal](https://portal.azure.com). 
1. Seleccione **Todos los servicios**. 
1. Escriba **Máquinas virtuales SQL** en el cuadro de búsqueda.
1. (Optional): Seleccione la estrella que hay junto a **Máquinas virtuales SQL** para agregar esta opción al menú **Favoritos**. 
1. Seleccione **Máquinas virtuales SQL**. 

   ![Búsqueda de máquinas virtuales con SQL Server en todos los servicios](./media/manage-sql-vm-portal/sql-vm-search.png)

1. En el portal se enumeran todas las máquinas virtuales con SQL Server disponibles en la suscripción. Seleccione la que desee administrar para iniciar el recurso **Máquinas virtuales SQL**. Utilice el cuadro de búsqueda si la máquina virtual con SQL Server no aparece. 

   ![Todas las máquinas virtuales con SQL Server disponibles](./media/manage-sql-vm-portal/all-sql-vms.png)

   Al seleccionar la máquina virtual con SQL Server, se abrirá el recurso **Máquinas virtuales SQL**: 


   ![Visualización de recurso de máquinas virtuales SQL](./media/manage-sql-vm-portal/sql-vm-resource.png)

> [!TIP]
> El recurso **máquinas virtuales de SQL** es para la configuración de SQL Server dedicada. Seleccione el nombre de la máquina virtual en el campo **Máquina virtual** para ir a los valores específicos de la máquina virtual, no a los exclusivos de SQL Server. 


## <a name="license-and-edition"></a>Licencia y edición 

Use la página **Configurar** del recurso de máquina virtual de SQL para cambiar los metadatos de la licencia de SQL Server por **Pago por uso**, **Ventaja híbrida de Azure** o **Alta disponibilidad/recuperación ante desastres** en la [réplica de Azure gratuita para la recuperación ante desastres](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).



![Cambio de la versión y la edición de los metadatos de VM con SQL Server en Azure Portal mediante el recurso Máquinas virtuales SQL](./media/manage-sql-vm-portal/sql-vm-license-edition.png)

También puede modificar la edición de SQL Server en la página **Configurar**, por ejemplo, **Enterprise**, **Standard** o **Developer**. 

El cambio de los metadatos de licencia y edición en Azure Portal solo se admite una vez que la versión y la edición de SQL Server se han modificado internamente en la máquina virtual. Para más información, cambie la [versión](change-sql-server-version.md) y la [edición](change-sql-server-edition.md) de SQL Server en las máquinas virtuales de Azure. 

## <a name="storage"></a>Almacenamiento 

Use la página **Configurar** del recurso Máquinas virtuales SQL para ampliar las unidades de datos, registro y tempdb. 

![Ampliación del almacenamiento en Azure Portal mediante el recurso Máquinas virtuales SQL](./media/manage-sql-vm-portal/sql-vm-storage-configuration.png)

## <a name="patching"></a>Aplicación de revisiones

Use la página **Aplicación de revisión** del recurso Máquinas virtuales SQL para permitir la aplicación automática de revisiones de la máquina virtual e instalar automáticamente las actualizaciones de Windows y SQL Server marcadas como importantes. También puede configurar aquí una programación de mantenimiento, por ejemplo, ejecutar la aplicación de revisiones diariamente, así como una hora de inicio local para el mantenimiento y una ventana de mantenimiento. 


![Configuración de la aplicación automatizada de revisiones y programación en Azure Portal mediante el recurso Máquinas virtuales SQL](./media/manage-sql-vm-portal/sql-vm-automated-patching.png)


Para más información, consulte [Aplicación automatizada de revisiones](automated-patching.md). 



## <a name="backups"></a>Copias de seguridad

Use la página **Copias de seguridad** del recurso Máquinas virtuales SQL para configurar las opciones de copia de seguridad automatizada, como el período de retención, la cuenta de almacenamiento que se va a usar, el cifrado, la copia de seguridad o no de las bases de datos del sistema y una programación de copia de seguridad. 

![Configuración de la copia de seguridad automatizada y programación en Azure Portal mediante el recurso Máquinas virtuales SQL](./media/manage-sql-vm-portal/sql-vm-automated-backup.png)

Para más información, consulte [Aplicación automatizada de revisiones](automated-backup.md). 


## <a name="high-availability-preview"></a>Alta disponibilidad (versión preliminar)

Use la página **Alta disponibilidad** del recurso Máquinas virtuales SQL para crear un clúster de conmutación por error de Windows Server y configurar un grupo de disponibilidad Always On, una escucha de grupo de disponibilidad y Azure Load Balancer. La configuración de alta disponibilidad mediante Azure Portal está actualmente en versión preliminar. 


![Configuración de un clúster de conmutación por error de Windows Server y un grupo de disponibilidad Always On en Azure Portal mediante el recurso Máquinas virtuales SQL](./media/manage-sql-vm-portal/sql-vm-high-availability.png)


Para más información, consulte [Configuración de grupos de disponibilidad mediante Azure Portal](availability-group-azure-portal-configure.md).

## <a name="security-configuration"></a>Configuración de seguridad 

Use la página **Configuración de seguridad** del recurso Máquinas virtuales SQL para realizar la configuración de seguridad de SQL Server, como el puerto que se usará o si se habilitará o no la autenticación de SQL, y para habilitar la integración con Azure Key Vault. 

![Configuración de la seguridad de SQL Server en Azure Portal mediante el recurso Máquinas virtuales SQL](./media/manage-sql-vm-portal/sql-vm-security-configuration.png)

Para más información, consulte [Procedimientos recomendados de seguridad](security-considerations-best-practices.md).


## <a name="security-center"></a>Security Center 

Use la página **Security Center** del recurso Máquinas virtuales SQL para ver recomendaciones de Security Center directamente en la hoja Máquina virtual SQL. Habilite [Azure Defender para SQL](../../../security-center/defender-for-sql-usage.md) para usar esta característica. 

![Configuración de Security Center de SQL Server en Azure Portal mediante el recurso Máquinas virtuales SQL](./media/manage-sql-vm-portal/sql-vm-security-center.png)


## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte los siguientes artículos. 

* [Introducción a SQL Server en máquinas virtuales Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Preguntas más frecuentes de SQL Server en máquinas virtuales Windows](frequently-asked-questions-faq.yml)
* [Orientación de precios de SQL Server para máquinas virtuales de Azure](pricing-guidance.md)
* [Notas de la versión de SQL Server en Azure Virtual Machines](doc-changes-updates-release-notes.md)


