---
title: Detección de inventario de software en servidores locales con Azure Migrate
description: 'Aprenda a detectar inventarios de software en servidores locales con Azure Migrate: Detección y evaluación.'
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: c337229a8e9a4fa98e80c955c317813950c51bfb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732655"
---
# <a name="discover-installed-software-inventory-web-apps-and-sql-server-instances-and-databases"></a>Detección del inventario de software instalado, las aplicaciones web y las instancias y bases de datos de SQL Server

En este artículo se explica cómo detectar el inventario de software instalado, las aplicaciones web y las instancias y bases de datos de SQL Server en servidores que se ejecutan en el entorno de VMware mediante la herramienta Azure Migrate: Detección y evaluación.

La realización del inventario de software ayuda a identificar y adaptar una ruta de migración a Azure para las cargas de trabajo. El inventario de software usa el dispositivo de Azure Migrate para realizar la detección, con las credenciales de servidor. Se realiza totalmente sin agente: no hay agentes instalados en los servidores para recopilar los datos.

## <a name="before-you-start"></a>Antes de comenzar

- Asegúrese de que ha [creado un proyecto](./create-manage-projects.md) con la herramienta Azure Migrate: Detección y evaluación agregada.
- Revise los [requisitos de VMware](migrate-support-matrix-vmware.md#vmware-requirements) para realizar el inventario de software.
- Revise los [requisitos del dispositivo](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) antes de configurarlo.
- Revise los [requisitos de detección de aplicaciones](migrate-support-matrix-vmware.md#software-inventory-requirements) antes de iniciar el inventario de software en los servidores.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Implementación y configuración del dispositivo de Azure Migrate

1. [Revise](migrate-appliance.md#appliance---vmware) los requisitos para implementar el dispositivo de Azure Migrate.
2. Revise las direcciones URL de Azure a las que el dispositivo necesitará acceder en las nubes [públicas](migrate-appliance.md#public-cloud-urls) y [gubernamentales](migrate-appliance.md#government-cloud-urls).
3. [Revise los datos](migrate-appliance.md#collected-data---vmware) que la aplicación recopila durante la detección y valoración.
4. [Tenga en cuenta](migrate-support-matrix-vmware.md#port-access-requirements) los requisitos de acceso a puertos del dispositivo.
5. [Implemente el dispositivo de Azure Migrate](how-to-set-up-appliance-vmware.md) para iniciar la detección. Para implementar el dispositivo, descargue e importe una plantilla OVA a VMware para crear un servidor que se ejecute en vCenter Server. Después de implementar el dispositivo, debe registrarlo en el proyecto y configurarlo para iniciar la detección.
6. Al configurar el dispositivo, debe especificar lo siguiente en el administrador de configuración del dispositivo:
    - Los detalles de la instancia de vCenter Server a la que se quiere conectar.
    - Las credenciales de vCenter Server en el ámbito para detectar los servidores del entorno de VMware.
    - Las credenciales del servidor, que pueden ser credenciales de dominio, Windows (no de dominio) o Linux (no de dominio). [Obtenga más información](add-server-credentials.md) sobre cómo proporcionar credenciales y cómo administrarlas.

## <a name="verify-permissions"></a>Comprobar los permisos

- Debe [crear una cuenta de vCenter Server de solo lectura](./tutorial-discover-vmware.md#prepare-vmware) para la detección y evaluación. La cuenta de solo lectura necesita tener habilitados los privilegios para **Virtual Machines** > **Guest Operations** (Operaciones de invitado), con el fin de interactuar con los servidores para realizar el inventario de software.
- Puede agregar varias credenciales de dominio y no de dominio (Windows o Linux) en el administrador de configuración del dispositivo para la detección de aplicaciones. Se necesita una cuenta de usuario invitado para las instancias de Windows Server y una cuenta de usuario normal (sin acceso sudo) para todas las máquinas virtuales Linux.[Aprenda](add-server-credentials.md) cómo especificar credenciales y cómo administrarlas.

### <a name="add-credentials-and-initiate-discovery"></a>Adición de credenciales e inicio de la detección

1. Abra el administrador de configuración del dispositivo, complete las comprobaciones de requisitos previos y el registro del dispositivo.
2. Navegue hasta el panel **Administrar credenciales y orígenes de detección**.
1.  En **Step 1: Provide vCenter Server credentials** (Paso 1: Proporcionar las credenciales de vCenter Server), haga clic en **Agregar credenciales** para proporcionar las credenciales de la cuenta de vCenter Server que el dispositivo usará para detectar los servidores que se ejecutan en vCenter Server.
1. En el **Paso 2: Proporcionar los detalles de vCenter Server**, haga clic en **Agregar origen de detección** para seleccionar el nombre descriptivo de las credenciales en la lista desplegable, especifique **Dirección IP/FQDN** de la instancia de vCenter Server. :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Panel 3 del administrador de configuración del dispositivo para los detalles de vCenter Server":::
1. En **Step 3: Provide server credentials to perform software inventory, agentless dependency analysis, discovery of SQL Server instances and databases and discovery of ASP.NET web apps in your VMware environment** (Paso 3: Proporcionar credenciales de servidor para realizar el inventario de software, el análisis de dependencias sin agente, la detección de instancias y bases de datos de SQL Server y la detección de aplicaciones web de ASP.NET en el entorno de VMware), haga clic en **Agregar credenciales** para proporcionar varias credenciales de servidor a fin de iniciar el inventario de software.
1. Haga clic en **Iniciar detección** para iniciar la detección de vCenter Server.

 Una vez completada la detección de vCenter Server, el dispositivo inicia la detección de las aplicaciones instaladas, roles y características (inventario de software). Todo depende del número de servidores que se detecten. En el caso de 500 servidores, el inventario de detección tarda aproximadamente una hora en aparecer en el portal de Azure Migrate.

## <a name="review-and-export-the-inventory"></a>Revisión y exportación del inventario

Una vez completado el inventario de software, puede revisarlo y exportarlo a Azure Portal.

1. En **Azure Migrate: Servidores, bases de datos y aplicaciones web** > **Azure Migrate: Detección y evaluación**, haga clic en el recuento que se muestra para abrir la página **Servidores detectados**.

    > [!NOTE]
    > En esta fase también puede habilitar el análisis de dependencias de los servidores detectados, para poder visualizar las dependencias entre los servidores que quiere evaluar. [Obtener más información](concepts-dependency-visualization.md) sobre el análisis de dependencias.

2. En la columna **Inventario de software**, haga clic en el recuento mostrado para revisar las aplicaciones, los roles y las características detectados.
4. Para exportar el inventario, en **Servidores detectados**, haga clic en **Export software inventory** (Exportar inventario de software).

El inventario de software se exporta y descarga en formato de Excel. En la hoja **Inventario de software** se muestran todas las aplicaciones detectadas en todos los servidores.

## <a name="discover-sql-server-instances-and-databases"></a>Detección de instancias y bases de datos de SQL Server

- El inventario de software también identifica las instancias de SQL Server que se ejecutan en el entorno de VMware.
- Si no ha proporcionado las credenciales de autenticación de Windows o SQL Server en el administrador de configuración del dispositivo, agregue las credenciales para que el dispositivo pueda usarlas para conectarse a las instancias de SQL Server correspondientes.

    > [!NOTE]
    > El dispositivo solo se puede conectar a aquellas instancias de SQL Server cuya red tenga en el campo de visión, mientras que el inventario de software por sí mismo puede no necesitar tener la red en el campo de visión.

Una vez conectado, el dispositivo recopila datos de configuración y rendimiento de las instancias y de las bases de datos de SQL Server. Los datos de configuración de SQL Server se actualizan una vez cada 24 horas y los datos de rendimiento se capturan cada 30 segundos. Por lo tanto, cualquier cambio en las propiedades de la instancia y bases de datos de SQL Server, como el estado de la base de datos, el nivel de compatibilidad, etc. puede tardar hasta 24 horas en actualizarse en el portal.

## <a name="discover-aspnet-web-apps"></a>Detección de aplicaciones web de ASP.NET

El inventario de software identifica el rol de servidor web existente en los servidores detectados. Si se detecta que un servidor tiene habilitado el rol de servidor web, Azure Migrate realiza la detección de aplicaciones web en el servidor.
El usuario puede agregar credenciales de dominio y que no son de dominio al dispositivo. Asegúrese de que la cuenta usada tenga privilegios de administrador local en los servidores de origen. Azure Migrate asigna automáticamente las credenciales a los servidores respectivos, así que no es necesario hacerlo manualmente. Lo más importante es que estas credenciales nunca se envían a Microsoft y permanecen en el dispositivo que se ejecuta en el entorno de origen.
Una vez conectado el dispositivo, recopila datos de configuración del servidor web de IIS y las aplicaciones web de ASP.NET. Los datos de configuración de las aplicaciones web se actualizan una vez cada 24 horas.

## <a name="next-steps"></a>Pasos siguientes

- [Cree una valoración](how-to-create-assessment.md) para los servidores detectados.
- [Evalúe las aplicaciones web](how-to-create-azure-app-service-assessment.md) para su migración a Azure App Service.
