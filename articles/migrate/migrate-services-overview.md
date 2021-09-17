---
title: Acerca de Azure Migrate
description: Más información sobre el servicio de Azure Migrate
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: b67239a973f3ba435aeeb7021d8d4f2faea9c98f
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015640"
---
# <a name="about-azure-migrate"></a>Acerca de Azure Migrate

En este artículo se proporciona una rápida descripción general del servicio Azure Migrate.

Azure Migrate proporciona un centro centralizado para evaluar y migrar a Azure servidores, infraestructuras, aplicaciones y datos locales. Ofrece lo siguiente:

- **Plataforma de migración unificada**: un único portal para iniciar, ejecutar y realizar un seguimiento de la migración a Azure.
- **Rango de herramientas**: Rango de herramientas para la evaluación y migración Las herramientas de Azure Migrate incluyen Azure Migrate: Discovery y assessment y Azure Migrate: Server Migration. Azure Migrate también se integra con otros servicios y herramientas de Azure, así como con ofertas de proveedores de software independientes (ISV).
- **Evaluación y migración** En el centro de Azure Migrate, puede evaluar y migrar:
    - **Servidores, bases de datos y aplicaciones web**: evalúe servidores locales, incluidas las aplicaciones web e instancias de SQL Server, y migrarlos a máquinas virtuales de Azure o a Azure VMware Solution (versión preliminar).
    - **Bases de datos**: Evalúe bases de datos locales y mígrelas a Azure SQL Database o a una instancia administrada de SQL.
    - **Aplicaciones web**: evalúe las aplicaciones web locales y mígrelas a Azure App Service.
    - **Escritorios virtuales**: Evalúe la infraestructura de escritorio virtual (VDI) local y mígrela a Windows Virtual Desktop en Azure.
    - **Data**: Migre grandes cantidades de datos a Azure de manera rápida y rentable gracias a los productos de Azure Data Box.

## <a name="integrated-tools"></a>Herramientas integradas

El centro de Azure Migrate incluye estas herramientas:

**Herramienta** | **Evaluación y migración** | **Detalles**
--- | --- | ---
**Azure Migrate: Discovery and assessment** | Descubrimiento y evaluación de servidores, incluidos SQL y aplicaciones web | Detecte y evalúe servidores locales que se ejecutan en VMware, Hyper-V y servidores físicos para preparar la migración a Azure.
**Azure Migrate: Server Migration** | Migrar servidores | Migre máquinas virtuales de VMware, máquinas virtuales de Hyper-V, servidores físicos, otros servidores virtualizados y máquinas virtuales de la nube pública a Azure.
**Data Migration Assistant** | Evalúe las bases de datos de SQL Server para la migración a Azure SQL Database, Instancia administrada de Azure SQL o máquinas virtuales de Azure que ejecutan SQL Server. | Data Migration Assistant es una herramienta independiente para evaluar servidores de SQL Server. Ayuda a identificar posibles problemas que bloquean la migración. Identifica características no admitidas, nuevas características que puede aprovechar después de la migración y la ruta de acceso correcta para la migración de la base de datos. [Más información](/sql/dma/dma-overview).
**Azure Database Migration Service** | Migre bases de datos locales a máquinas virtuales de Azure en las que se ejecutan SQL Server, Azure SQL Database o SQL Managed Instances. | [Más información](../dms/dms-overview.md) sobre Database Migration Service.
**Movere** | Evaluar servidores | [Más información](#movere) acerca de Movere.
**Migration Assistant para aplicaciones web** | Evalúe aplicaciones web locales y mígrelas a Azure. |  Azure App Service Migration Assistant es una herramienta independiente para evaluar sitios web locales para la migración a Azure App Service.<br/><br/> Use Migration Assistant para migrar aplicaciones web de .NET y PHP a Azure. [Más información](https://appmigration.microsoft.com/) sobre Azure App Service Migration Assistant.
**Azure Data Box** | Migración de datos sin conexión | Use los productos de Azure Data Box para trasladar grandes cantidades de datos sin conexión a Azure. [Más información](../databox/index.yml).

> [!NOTE]
> Si se encuentra en Azure Government, las herramientas externas integradas y las ofertas del ISV no pueden enviar datos a Azure Migrate. Las herramientas se pueden usar de forma independiente.

## <a name="isv-integration"></a>Integración de ISV

Azure Migrate se integra en varias ofertas de ISV. 

**ISV**    | **Característica**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migre servidores.
[Cloudamize](https://www.cloudamize.com/platform) | Evalúe servidores.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Evalúe y migre servidores.
[Device42](https://docs.device42.com/) | Evalúe servidores.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Evalúe VDI.
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Migre servidores.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Evalúe servidores.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Evalúe servidores y bases de datos.
[Zerto](https://go.microsoft.com/fwlink/?linkid=2152102) | Migre servidores.

## <a name="azure-migrate-discovery-and-assessment-tool"></a>Herramienta Azure Migrate: Discovery and assessment

La herramienta Azure Migrate: Discovery and assessment detecta y evalúa máquinas virtuales VMware locales, máquinas virtuales Hyper-V y servidores físicos para su migración a Azure.

Esto es lo que hace la herramienta:

- **Preparación para Azure**: evalúa si los servidores locales, servidores de SQL Server y aplicaciones web están preparados para la migración a Azure.
- **Dimensionamiento de Azure**: calcula el tamaño de las máquinas virtuales de Azure, la configuración de Azure SQL o el número de nodos de Azure VMware Solution después de la migración.
- **Estimación de costos de Azure**: Calcule el costo de la ejecución de servidores locales en Azure.
- **Análisis de dependencias**: Identifica las dependencias entre servidores y las estrategias de optimización para mover servidores interdependientes a Azure. Más información sobre Discovery and assessment con [análisis de dependencias](concepts-dependency-visualization.md).

La herramienta de detección y evaluación usa un [dispositivo de Azure Migrate](migrate-appliance.md) ligero que se implementa en el entorno local.

- El dispositivo se ejecuta en una máquina virtual o en un servidor físico. Puede instalarlo fácilmente mediante una plantilla descargada.
- El dispositivo detecta servidores locales. También envía continuamente metadatos del servidor y datos de rendimiento a Azure Migrate.
- La detección del dispositivo es sin agente. No se instala nada en los servidores detectados.
- Después de la detección del dispositivo, puede recopilar los servidores detectados en grupos y ejecutar evaluaciones para cada uno de ellos.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: Herramienta Server Migration

La herramienta Azure Migrate: Server Migration le ayuda a migrar a Azure:

**Migrar** | **Detalles**
--- | ---
Máquinas virtuales de VMware locales | Migre máquinas virtuales a Azure mediante la migración basada en agente o sin agente.<br/><br/> Para la migración sin agente, Server Migration usa el mismo dispositivo de Azure Migrate que también puede usar la herramienta de detección y evaluación para la detección y la evaluación de servidores.<br/><br/> En el caso de la migración basada en agente, Server Migration usa un dispositivo de replicación.
VM de Hyper-V locales | Migre máquinas virtuales a Azure.<br/><br/> Server Migration usa agentes de proveedor instalados en el host de Hyper-V para la migración.
Servidores físicos locales o servidores hospedados en otras nubes | Los servidores físicos se pueden migrar a Azure. También se pueden migrar otros servidores virtualizados y máquinas virtuales de otras nubes públicas. Para ello, hay que tratarlos como servidores físicos con el fin de realizar la migración. Server Migration usa un dispositivo de replicación para la migración.

## <a name="selecting-assessment-and-migration-tools"></a>Selección de herramientas de valoración y migración

En el centro de Azure Migrate, seleccione la herramienta que desea usar para la evaluación o migración, y agréguela a un proyecto. Si agrega una herramienta de ISV o a Movere:

- Para empezar, obtenga una licencia o regístrese para obtener una evaluación gratuita, para lo que debe seguir las instrucciones de la herramienta. Cada ISV o herramienta especifica las licencias.
- Cada herramienta tiene una opción para conectarse a Azure Migrate. Siga las instrucciones de la herramienta para conectarse.
- Realice un seguimiento de la migración en todas las herramientas desde el proyecto.

## <a name="movere"></a>Movere

Movere es una plataforma de software como servicio (SaaS). Aumenta la inteligencia empresarial, ya que presentar con precisión entornos de TI completos en un solo día. Las organizaciones y las empresas crecen, cambian y se optimizan digitalmente. Cuando lo hacen, Movere les proporciona la confianza necesaria para ver y controlar sus entornos, independientemente de la plataforma, la aplicación o la geografía.

Microsoft ha [adquirido](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) Movere y ha dejado de comercializarse como una oferta independiente. Movere está disponible mediante los programas Microsoft Solution Assessment y Microsoft Cloud Economics. [Más información](https://www.movere.io) acerca de Movere.

Le recomendamos que consulte también Azure Migrate, nuestro servicio de migración integrado. Azure Migrate proporciona un centro para simplificar la migración a la nube. Este centro admite totalmente cargas de trabajo, como los servidores físicos y virtuales, las bases de datos y las aplicaciones. La visibilidad de un extremo a otro permite hacer un seguimiento del progreso en la detección, la evaluación y la migración de forma sencilla.

Con las herramientas de Azure y de los ISV asociados integradas, Azure Migrate tiene una amplio rango de características, entre las que se incluyen:

- Detección de servidores virtuales y físicos.
- Elección del tamaño adecuado en función del rendimiento.
- Planeamiento de costos.
- Valoraciones basadas en la importación.
- Análisis de dependencias de aplicaciones sin agente.

Si buscando ayuda experta para empezar, Microsoft cuenta con [proveedores de servicios administrados expertos de Azure](https://azure.microsoft.com/partners) que le guiarán. Consulte el [sitio web de Azure Migrate](https://azure.microsoft.com/services/azure-migrate/).

## <a name="azure-migrate-versions"></a>Versiones de Azure Migrate

Hay dos versiones del servicio Azure Migrate.

- **Versión actual**: use esta versión para crear proyectos, detectar servidores locales y organizar valoraciones y migraciones. [Obtenga más información](whats-new.md) sobre las novedades de esta versión.
- **Versión anterior**: la versión anterior de Azure Migrate, también conocida como Azure Migrate clásico, solo admite la valoración de servidores locales que se ejecutan en VMware. Azure Migrate clásico se retira en febrero de 2024. Después de febrero de 2024, ya no se admitirá la versión clásica de Azure Migrate y se eliminarán los metadatos de inventario del proyecto clásico. No se pueden actualizar proyectos ni componentes de la versión anterior a la nueva versión. Necesita [crear un proyecto](create-manage-projects.md) y [agregarle herramientas de evaluación y migración](./create-manage-projects.md). Use los tutoriales para aprender a usar las herramientas de valoración y migración disponibles. Si tiene un área de trabajo de Log Analytics asociada a un proyecto clásico y desea adjuntarla a uno de la versión actual, no tiene más que eliminar el proyecto clásico.

    Para acceder a los proyectos existentes en Azure Portal, busque **Azure Migrate** y selecciónelo. El panel de **Azure Migrate** tiene una notificación y un vínculo para acceder a proyectos antiguos.

## <a name="next-steps"></a>Pasos siguientes

- Pruebe nuestros tutoriales para evaluar [máquinas virtuales de VMware](./tutorial-discover-vmware.md), [máquinas virtuales de Hyper-V](./tutorial-discover-hyper-v.md) o [servidores físicos](./tutorial-discover-physical.md).
- [Repase las preguntas más frecuentes](resources-faq.md) sobre Azure Migrate.