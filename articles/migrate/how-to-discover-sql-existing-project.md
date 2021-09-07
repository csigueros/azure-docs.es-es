---
title: Detección de instancias de SQL Server en un proyecto existente de Azure Migrate
description: Aprenda a detectar instancias de SQL Server en un proyecto existente de Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 38169324211a22012426b895d66bc6d0015f5587
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740354"
---
# <a name="discover-web-apps-and-sql-server-instances-in-an-existing-project"></a>Detección de aplicaciones web e instancias de SQL Server en un proyecto existente

En este artículo se describe cómo detectar aplicaciones web e instancias de SQL Server en un proyecto de [Azure Migrate](./migrate-services-overview.md) creado antes de la versión preliminar de la característica de evaluación de Azure SQL o antes de la versión preliminar de la característica de evaluación de Azure App Service.

La detección de aplicaciones web de ASP.NET y bases de datos e instancias de SQL Server que se ejecutan en máquinas locales ayuda a identificar y personalizar una ruta de migración a Azure. Para realizar la detección, el dispositivo de Azure Migrate usa credenciales del sistema operativo Windows de dominio y que no son de dominio, o bien credenciales de autenticación de SQL Server que tienen acceso a las bases de datos e instancias de SQL Server que se ejecutan en los servidores de destino.
Este proceso de detección se realiza sin agente, es decir, no se instalada nada en los servidores de destino.

## <a name="before-you-start"></a>Antes de comenzar

- Asegúrese de que ha:
    - Creado un [proyecto de Azure Migrate](./create-manage-projects.md) antes de anunciar la característica de evaluación de SQL y aplicaciones web para su región.
    - Agregado la herramienta para [Azure Migrate: detección y valoración](./how-to-assess.md) a un proyecto
- Revise [la compatibilidad y los requisitos de la detección de aplicaciones](./migrate-support-matrix-vmware.md#vmware-requirements).
-  Asegúrese de que los servidores en los que se ejecuta la detección de aplicaciones tengan instaladas la versión de PowerShell 2.0 o posterior y las herramientas de VMware (posterior a 10.2.0).
- Compruebe los [requisitos ](./migrate-appliance.md) para implementar el dispositivo de Azure Migrate.
- Compruebe que tiene los [roles necesarios](./create-manage-projects.md#verify-permissions) en la suscripción para crear los recursos.
- Asegúrese de que el dispositivo tenga acceso a Internet.

## <a name="enable-discovery-of-aspnet-web-apps-and-sql-server-instances-and-databases"></a>Habilitación de la detección de aplicaciones web de ASP.NET y bases de datos e instancias de SQL Server

1. En el proyecto de Azure Migrate, realice una de las acciones siguientes:
    - Seleccione **Sin habilitar** en el mosaico del centro, o bien :::image type="content" source="./media/how-to-discover-sql-existing-project/hub-not-enabled.png" alt-text="Mosaico del centro de Azure Migrate con la detección de SQL y aplicaciones web no habilitada":::.
    - Seleccione **Sin habilitar** en cualquiera de las entradas de la página de detección de servidores, en la columna de instancias de SQL o Aplicaciones web :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-not-enabled.png" alt-text="Hoja de servidores detectados de Azure Migrate con la detección de SQL aplicaciones web sin habilitar":::.
2. Para detectar aplicaciones web de ASP.NET y bases de datos e instancias de SQL Server, siga los pasos que se indican:
    - Seleccione la opción de **Actualizar** para crear el recurso necesario.
        :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-upgrade-appliance.png" alt-text="Botón para actualizar el dispositivo de Azure Migrate":::
    - Compruebe que los servicios que se ejecutan en el dispositivo estén actualizados a las últimas versiones. Para ello, inicie el administrador de configuración del dispositivo desde el servidor de este y seleccione la opción para ver los servicios del dispositivo en el panel de configuración de requisitos previos.
        - El dispositivo y sus componentes se actualizan de forma automática :::image type="content" source="./media/how-to-discover-sql-existing-project/appliance-services-version.png" alt-text="Comprobar la versión del dispositivo":::
    - En el panel de administración de credenciales y orígenes de detección del administrador de configuración del dispositivo, agregue las credenciales de autenticación de SQL Server o de dominio que tengan acceso de administrador del sistema a las bases de datos y a la instancia de SQL Server que se van a detectar.
    - La detección de aplicaciones web de ASP.NET funciona con credenciales del sistema operativo Windows de dominio y que no son de dominio, siempre que la cuenta usada tenga privilegios de administrador local en los servidores.
    Puede aprovechar la característica de asignación automática de credenciales del dispositivo, como se resalta [aquí](./tutorial-discover-vmware.md#start-continuous-discovery).

    Algunos puntos a tener en cuenta:
    - Asegúrese de que el inventario de software ya esté habilitado o proporcione credenciales que sean de dominio o no para habilitarlo. Debe realizarse el inventario de software para detectar las instancias de SQL Server y las aplicaciones web de ASP.NET.
    - El dispositivo intentará validar las credenciales de dominio con AD, a medida que se agreguen. Asegúrese de que el servidor del dispositivo tenga una línea de visión de red al servidor de AD asociado a las credenciales. Las credenciales que no son de dominio y las credenciales asociadas con la autenticación de SQL Server no se validan.

3. Una vez agregadas las credenciales que desee, seleccione Iniciar la detección para comenzar el examen.

> [!Note]
>Permita que la detección de aplicaciones web y SQL se ejecute durante algún tiempo antes de crear evaluaciones de Azure App Service o Azure SQL. Si no se permite que se complete la detección de aplicaciones web y bases de datos e instancias de SQL Server, las instancias respectivas se marcan como **Desconocidas** en el informe de evaluación.

## <a name="next-steps"></a>Pasos siguientes

- Aprender a crear una [valoración de Azure SQL](./how-to-create-azure-sql-assessment.md)
- Obtener más información sobre las [valoraciones de Azure SQL](./concepts-azure-sql-assessment-calculation.md)
- Obtenga información sobre cómo crear una [evaluación de Azure App Service](./how-to-create-azure-app-service-assessment.md).
- Obtenga más información sobre las [evaluaciones de Azure App Service](./concepts-azure-webapps-assessment-calculation.md).