---
title: Azure Automanage para máquinas virtuales
description: Conozca Azure Automanage para máquinas virtuales.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: b93169ced916f5d16adcbd11fcc2d2217a07b643
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451186"
---
# <a name="preview-azure-automanage-for-machine-best-practices"></a>Versión preliminar: procedimientos recomendados de Azure Automanage para máquinas

En este artículo se incluye información sobre los procedimientos recomendados de Azure Automanage para máquinas. Este servicio presenta las siguientes ventajas:

- Incorpora máquinas virtuales de forma inteligente a servicios de Azure de procedimientos recomendados.
- Configura automáticamente cada servicio según los procedimientos recomendados de Azure.
- Admite la personalización de los servicios de procedimientos recomendados.
- Supervisa las desviaciones y las corrige cuando se detectan.
- Proporciona una experiencia sencilla (punto, clic, establecer, olvidar)

## <a name="overview"></a>Información general

Procedimientos recomendados de Azure Automanage para máquinas es un servicio que elimina la necesidad de detectar, saber incorporar y configurar determinados servicios en Azure que serían beneficiosos para una máquina virtual. Se considera que estos son servicios de procedimientos recomendados de Azure y que ayudan a mejorar la confiabilidad, seguridad y administración de máquinas virtuales. Algunos ejemplos de estos servicios son [Azure Update Management](../automation/update-management/overview.md) y [Azure Backup](../backup/backup-overview.md).

Después de incorporar las máquinas a Azure Automanage, se usa la configuración recomendada en todos los servicios de procedimientos recomendados. Sin embargo, si desea personalizar los servicios y la configuración de procedimientos recomendados, puede usar la opción [Perfil personalizado](#custom-profiles). 

Azure Automanage también supervisa automáticamente las desviaciones y las corrige cuando se detectan. Lo que esto significa es que si su máquina virtual o servidor habilitado para Arc se incorpora a Azure Automanage, supervisaremos su máquina para garantizar que sigue cumpliendo su [perfil de configuración](#configuration-profile) durante todo su ciclo de vida. Si la máquina virtual se desvía de ese perfil (por ejemplo, si se retira un servicio), se corregirá y se la devolverá al estado deseado.

Automanage no almacena ni procesa los datos de clientes fuera de la geografía en la que se encuentran las máquinas virtuales. En la región Sudeste Asiático, Automanage no almacena ni procesa datos fuera de esa región.

> [!NOTE]
> Automanage se puede habilitar en máquinas virtuales de Azure, así como en servidores habilitados para Azure Arc. Automanage no está disponible en la nube del Gobierno de EE. UU. en este momento.

## <a name="prerequisites"></a>Requisitos previos

Antes de intentar habilitar Azure Automanage en las máquinas virtuales, debe tener en cuenta varios requisitos previos.

- [Versiones de Windows Server](automanage-windows-server.md#supported-windows-server-versions) y [distribuciones de Linux](automanage-linux.md#supported-linux-distributions-and-versions) compatibles
- Las máquinas virtuales deben estar en una región admitida (véase a continuación)
- El usuario debe tener los permisos correctos (véase a continuación)
- Automanage no admite suscripciones de espacio aislado en este momento
- Automanage no admite Windows 10 en este momento.

### <a name="supported-regions"></a>Regiones admitidas
Automanage solo admite máquinas virtuales que se encuentran en las siguientes regiones:
* Oeste de Europa
* Norte de Europa
* Centro de EE. UU.
* Este de EE. UU.
* Este de EE. UU. 2
* Oeste de EE. UU.
* Oeste de EE. UU. 2
* Centro de Canadá
* Centro-Oeste de EE. UU.
* Centro-sur de EE. UU.
* Japón Oriental
* Sur de Reino Unido 2
* Este de Australia
* Sudeste de Australia
* Sudeste de Asia

### <a name="required-rbac-permissions"></a>Permisos de RBAC necesarios
Para la incorporación, Automanage requiere roles RBAC ligeramente diferentes en función de si va a habilitar Automanage por primera vez en una suscripción.

Si va a habilitar Automanage por primera vez en una suscripción:
* Rol de **Propietario** en las suscripciones que contienen las máquinas; _**o**_
* Roles de **Colaborador** y **Administrador de acceso de usuario** en las suscripciones que contienen las máquinas.

Si va a habilitar Automanage en una máquina de una suscripción que ya tiene máquinas de Automanage:
* Rol de **Colaborador** en el grupo de recursos que contiene las máquinas virtuales.

El servicio Automanage concederá permiso de **Colaborador** a esta aplicación de primera entidad (id. de aplicación de API de Automanage: d828acde-4b48-47f5-a6e8-52460104a052) para realizar acciones en máquinas administradas automáticamente.

> [!NOTE]
> Si quiere usar Automanage en una máquina virtual que está conectada a un área de trabajo en una suscripción diferente, debe tener los permisos descritos anteriormente en cada suscripción.

## <a name="participating-services"></a>Servicios participantes

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services-1.png" alt-text="Incorporación inteligente de servicios.":::

Para obtener una lista completa de los servicios de Azure participantes, así como su perfil compatible, consulte estos temas:
- [Automanage para Linux](automanage-linux.md)
- [Automanage para Windows Server](automanage-windows-server.md)

 Le incorporaremos automáticamente a estos servicios participantes cuando use los perfiles de configuración de procedimientos recomendados. Estos servicios son fundamentales para nuestra nota del producto de procedimientos recomendados, que puede encontrar en nuestro marco [Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management).


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Habilitación de Automanage para máquinas virtuales en Azure Portal

En Azure Portal, puede habilitar Automanage en una máquina virtual existente. Para conocer los pasos resumidos de este proceso, consulte el [inicio rápido de Automanage para máquinas virtuales](quick-create-virtual-machines-portal.md).

Si es la primera vez que habilita Automanage en la máquina virtual, puede buscar **Automanage: procedimientos recomendados de máquinas de Azure** en Azure Portal. Haga clic en **Habilitación en una máquina virtual existente**, seleccione el [perfil de configuración](#configuration-profile) que desea usar y, a continuación, seleccione las máquinas que desea incorporar. Haga clic en **Habilitar** y ya ha terminado.

El único caso en que podría tener que interactuar con esta máquina para administrar estos servicios es si intentamos corregir la máquina virtual, pero no lo conseguimos. Si hemos corregido la máquina virtual correctamente, la devolveremos a su estado de conformidad sin avisarle. Para más información, consulte [Estado de las máquinas virtuales](#status-of-vms).

## <a name="enabling-automanage-for-vms-using-azure-policy"></a>Habilitación de Automanage para las máquinas virtuales mediante Azure Policy
También puede habilitar Automanage en las máquinas virtuales a escala mediante la instancia de Azure Policy integrada. La directiva tiene un efecto de DeployIfNotExists, lo que significa que todas las máquinas virtuales que se encuentren dentro del ámbito de la directiva se incorporarán automáticamente a los procedimientos recomendados de VM de Automanage.

[Este es un vínculo directo ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3) a la directiva.

Para obtener más información, consulte cómo habilitar la [directiva integrada de Automanage](virtual-machines-policy-enable.md). 

## <a name="configuration-profile"></a>Perfil de configuración

Para habilitar Automanage en una máquina, se requiere un perfil de configuración. Los perfiles de configuración son la base de este servicio. Definen qué servicios se incorporan a las máquinas y, hasta cierto punto, cuál será la configuración de esos servicios.

### <a name="best-practice-configuration-profiles"></a>Perfiles de configuración de procedimientos recomendados

Actualmente, hay dos perfiles de configuración disponibles de procedimientos recomendados.

- El perfil de **desarrollo/pruebas** está diseñado para máquinas de desarrollo y pruebas.
- El perfil de **producción** es para producción.

La razón de esta diferencia es que algunos servicios se recomiendan en función de la carga de trabajo en ejecución. Por ejemplo, en una máquina de producción le incorporaremos automáticamente a Azure Backup. Sin embargo, en el caso de una máquina de desarrollo/pruebas, un servicio de copia de seguridad sería un costo innecesario, ya que las máquinas de desarrollo/pruebas suelen tener un menor efecto para la empresa.

### <a name="custom-profiles"></a>Perfiles personalizados

Los perfiles personalizados permiten personalizar los servicios y la configuración que desea aplicar a las máquinas. Esta es una excelente opción si sus requisitos de TI difieren de los procedimientos recomendados. Por ejemplo, si no desea usar la solución Microsoft Antimalware porque su organización de TI requiere que use una solución antimalware diferente, puede simplemente desactivar Microsoft Antimalware al crear un perfil personalizado.

> [!NOTE]
> En el perfil de configuración de desarrollo/pruebas de los procedimientos recomendados, no se realizará una copia de seguridad de la máquina virtual.

> [!NOTE]
> Si desea cambiar el perfil de configuración de una máquina, simplemente puede volver a configurarlo con el perfil de configuración deseado. Sin embargo, si el estado de la máquina es "Necesita actualización", primero deberá deshabilitar y volver a habilitar Automanage. 

Aquí puede obtener una lista completa de los servicios de Azure que participan y consultar si admiten preferencias:
- [Automanage para Linux](automanage-windows-server.md)
- [Automanage para Windows Server](automanage-windows-server.md)


## <a name="status-of-vms"></a>Estado de las máquinas virtuales

En Azure Portal, vaya a la página **Automanage: procedimientos recomendados para máquinas de Azure**, que muestra todas las máquinas administradas automáticamente. Aquí verá el estado general de cada máquina.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Lista de máquinas virtuales configuradas.":::

Para cada máquina de la lista, se muestran los detalles siguientes: Nombre, Perfil de configuración, Estado, Tipo de recurso, Grupo de recursos, Suscripción.

La columna **Status** (Estado) puede mostrar los siguientes estados:
- *En curso*: la máquina virtual se acaba de habilitar y se está configurando.
- *Conforme*: la máquina virtual está configurada y no se detecta ninguna desviación.
- *No conforme*: la máquina virtual se ha desviado y no se pudo corregir o la máquina está apagada y Automanage intentará incorporar o corregir la máquina virtual cuando se ejecute la siguiente vez.
- *Necesita actualización*: la máquina virtual se ha incorporado a una versión anterior de Automanage y debe [actualizarse](automanage-upgrade.md) a la versión más reciente.

Si ve el **Estado** como *No conforme*, puede solucionar problemas haciendo clic en el estado en el portal y usando los vínculos de solución de problemas proporcionados.


## <a name="disabling-automanage-for-vms"></a>Deshabilitación de Automanage para máquinas virtuales

Puede decidir un día deshabilitar Automanage en determinadas máquinas virtuales. Por ejemplo, la máquina está ejecutando una carga de trabajo segura muy confidencial y debe bloquearla incluso por encima de lo que Azure haría de forma natural, por lo que debe configurar la máquina fuera de los procedimientos recomendados de Azure.

Para ello, en Azure Portal, vaya a la página **Automanage: procedimientos recomendados para máquinas de Azure**, que muestra todas las máquinas virtuales administradas automáticamente. Active la casilla situada junto a la máquina virtual en la que quiere deshabilitar Automanage y, luego, haga clic en el botón **Disable automanagment** (Deshabilitar la administración automática).

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Deshabilitación de Automanage en una máquina virtual.":::

Lea detenidamente los mensajes del elemento emergente resultante antes de aceptar **Disable** (Deshabilitar).

> [!NOTE]
> Al deshabilitar la administración en una VM, se produce el comportamiento siguiente:
>
> - La configuración de la VM y los servicios que se incorporan no cambian.
> - Los cargos en los que incurran esos servicios se seguirán facturando y se continuarán generando.
> - La supervisión del desfase de datos de Automanage se detiene inmediatamente.


En primer lugar, no se desactivará la máquina virtual de ninguno de los servicios que hayamos incorporado y configurado. Los cargos que generen esos servicios se seguirán facturando. Si es necesario, debe desactivarlos. Cualquier acción de Automanage se detendrá inmediatamente. Por ejemplo, ya no se supervisarán las desviaciones de la máquina virtual.

## <a name="automanage-and-azure-disk-encryption"></a>Automanage y Azure Disk Encryption
Automanage es compatible con máquinas virtuales que tienen Azure Disk Encryption (ADE) habilitado.

Si usa el entorno de producción, también se le incorporará a Azure Backup. Hay un requisito previo para usar correctamente ADE y Azure Backup:
* Antes de incorporar la máquina virtual habilitada para ADE al entorno de producción de Automanage, asegúrese de que ha seguido los pasos que se encuentran en la sección **Antes de comenzar** de [este documento](../backup/backup-azure-vms-encryption.md#before-you-start).

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido que Automanage para máquinas proporciona un medio para eliminar la necesidad de conocer, incorporar y configurar servicios de Azure de procedimientos recomendados. Además, si una máquina que se ha incorporado a Automanage para máquinas virtuales se desvía del perfil de configuración, la devolveremos automáticamente a su estado de conformidad.

Pruebe a habilitar Automanage para máquinas virtuales de Azure o servidores habilitados para Arc en Azure Portal.

> [!div class="nextstepaction"]
> [Habilitación de Automanage para máquinas virtuales en Azure Portal](quick-create-virtual-machines-portal.md)
