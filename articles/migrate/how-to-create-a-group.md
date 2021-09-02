---
title: Agrupación de servidores para su evaluación con Azure Migrate | Microsoft Docs
description: En este artículo se describe cómo agrupar servidores antes de ejecutar una evaluación con el servicio Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/17/2019
ms.openlocfilehash: 9ffa031a9058eb6864298c5c9387402955a84968
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747250"
---
# <a name="create-a-group-for-assessment"></a>Creación de un grupo para su valoración

En este artículo se describe cómo crear grupos de servidores para su valoración mediante Azure Migrate: Detección y evaluación.

[Azure Migrate](migrate-services-overview.md) le ayuda a migrar a Azure. Azure Migrate proporciona un centro principal para realizar el seguimiento de la detección, valoración y migración de infraestructuras, aplicaciones y datos locales a Azure. El centro proporciona herramientas de Azure para la valoración y migración, así como ofertas de proveedores de software independientes (ISV) externos.

## <a name="grouping-servers"></a>Agrupación de servidores

Los servidores se reúnen en grupos para evaluar si son adecuados para la migración a Azure y para obtener estimaciones sobre sus tamaños y costes en Azure. Hay un par de formas de crear grupos:

- Si sabe qué servidores deben migrarse juntos, puede crear manualmente el grupo en Azure Migrate.
- Si no está seguro de qué servidores deben agruparse, puede usar la funcionalidad de visualización de dependencias de Azure Migrate para crear grupos. 

> [!NOTE]
> La funcionalidad de visualización de dependencias no está disponible en Azure Government.

## <a name="create-a-group-manually"></a>Creación de un grupo manualmente

Puede crear un grupo al mismo tiempo que [crea una valoración](how-to-create-assessment.md).

Si quiere crear un grupo manualmente fuera de la creación de una valoración, haga lo siguiente:

1. En el proyecto de Azure Migrate > **Información general**, haga clic en **Evaluar y migrar servidores**. En **Azure Migrate: Detección y evaluación**, haga clic en **Grupos**
    - Si aún no ha agregado la herramienta Azure Migrate: Detección y evaluación, haga clic para agregarla. [Más información](how-to-assess.md).
    - Si aún no ha creado un proyecto de Azure Migrate, [obtenga más información](./create-manage-projects.md).

    ![Selección de grupos](./media/how-to-create-a-group/select-groups.png)

2. Haga clic en el icono **Grupos**.
3. En **Crear grupo**, especifique un nombre de grupo y, en **Nombre del dispositivo**, seleccione el dispositivo de Azure Migrate que está usando para la detección de servidor.
4. En la lista de servidor, seleccione los servidores que quiere agregar al grupo > **Crear**.

    ![Crear grupo](./media/how-to-create-a-group/create-group.png)

Este grupo ya se puede usar al [crear la valoración de una máquina virtual de Azure](how-to-create-assessment.md), [una valoración de Azure VMware Solution (AVS)](how-to-create-azure-vmware-solution-assessment.md), [una valoración de Azure SQL](how-to-create-azure-sql-assessment.md) o [una valoración de Azure App Service](how-to-create-azure-app-service-assessment.md).

## <a name="refine-a-group-with-dependency-mapping"></a>Restricción de un grupo con la asignación de dependencias

La asignación de dependencias ayuda a visualizar las dependencias entre los servidores. La asignación de dependencias suele usarse cuando quiere evaluar grupos de servidores con niveles de confianza más altos.

- Le ayuda a comprobar las dependencias de los servidores antes de llevar a cabo una valoración.
- También le ayuda a planear eficazmente la migración a Azure, ya que le permite asegurarse de que no omite nada, con lo que evitará interrupciones inesperadas durante la migración.
- Puede detectar los sistemas interdependientes que tienen que migrarse juntos e identificar si un sistema en ejecución sigue ofreciendo servicio a los usuarios o si es un candidato para la retirada, en lugar de la migración.

Si ya [ha configurado la asignación de dependencias](how-to-create-group-machine-dependencies.md) y quiere restringir un grupo existente, haga lo siguiente:

1. En la pestaña **Servidores**, en el icono **Azure Migrate: Detección y evaluación**, haga clic en **Grupos**.
2. Haga clic en el grupo que quiere restringir.
    - Si aún no ha configurado la asignación de dependencias, en la columna **Dependencias** aparecerá el estado **Requiere instalación**. En cada servidor cuyas dependencias quiera que se visualicen, haga clic en **Requiere instalación**. Instale un par de agentes en cada servidor para poder asignar las dependencias del servidor. [Más información](how-to-create-group-machine-dependencies.md).

        ![Adición de la asignación de dependencias](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Si ya ha configurado la asignación de dependencias, en la página del grupo, haga clic en **Ver dependencias** para abrir la asignación de dependencias del grupo.

3. Después de hacer clic en **Ver dependencias**, en la asignación de dependencias del grupo se mostrará lo siguiente:

    - Las conexiones TCP de entrada (clientes) y de salida (servidores) hacia y desde todos los servidores del grupo que tienen instalados agentes de dependencias.
    - Los servidores dependientes que no tienen instalados agentes de dependencias se agrupan en función de los números de puerto.
    - Los servidores dependientes con los agentes de dependencias instalados se muestran como cuadros independientes.
    - Los procesos que se ejecutan en el servidor. Expanda el cuadro de cada servidor para ver los procesos.
    - Propiedades del servidor (incluido el FQDN, el sistema operativo y la dirección MAC). Haga clic en el cuadro de cada servidor para ver los detalles.

4. Para ver las dependencias de un intervalo de tiempo de su elección, modifique el intervalo de tiempo (de forma predeterminada, una hora). Para ello, especifique las fechas de inicio y de finalización, o bien la duración.

    > [!NOTE]
    > El intervalo de tiempo puede ser de hasta una hora. Si necesita un intervalo más amplio, use [Azure Monitor para consultar los datos dependientes](how-to-create-group-machine-dependencies.md) durante un período más prolongado.

5. Una vez identificadas las dependencias que quiere agregar al grupo o quitar de este, puede modificar el grupo. Use CTRL+clic para agregar servidores al grupo o quitarlos de este.

    - Solo puede agregar servidores que se han detectado.
    - Al agregar y quitar servidores, se invalidan las valoraciones anteriores del grupo.
    - Si lo desea, puede crear una evaluación cuando se modifica el grupo.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a configurar y a usar la [asignación de dependencias](how-to-create-group-machine-dependencies.md) para crear grupos de confianza alta.