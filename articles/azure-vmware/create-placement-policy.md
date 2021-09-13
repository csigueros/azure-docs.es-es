---
title: Creación de una directiva de selección de ubicación (versión preliminar)
description: Aprenda a crear una directiva de selección de ubicación en Azure VMware Solution para controlar la selección de máquinas virtuales (VM) en hosts dentro de un clúster mediante Azure Portal.
ms.topic: how-to
ms.date: 8/16/2021
ms.openlocfilehash: 267c58382e0272ba6121ae762f48b1ef08973c61
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323312"
---
# <a name="create-a-placement-policy-in-azure-vmware-solution-preview"></a>Creación de una directiva de selección de ubicación en Azure VMware Solution (versión preliminar)

>[!IMPORTANT]
>La característica de directiva de selección de ubicación en Azure VMware Solution (versión preliminar) se encuentra actualmente en vista previa. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para usar la característica en vista previa, [deberá registrar las características de _directiva de selección de ubicación de DRS_ y de _acceso anticipado_](https://ms.portal.azure.com/#blade/Microsoft_Azure_Resources/PreviewFeaturesBlade).  Busque y seleccione las características y, después, haga clic en **Registrarse**.

En Azure VMware Solution, los clústeres de una nube privada son un recurso administrado. Como resultado, el rol cloudadmin no puede realizar determinados cambios en el clúster desde vSphere Client, incluida la administración de reglas de Distributed Resource Scheduler (DRS).

Las directivas de selección de ubicación en Azure VMware Solution le permiten controlar la selección de máquinas virtuales (VM) en hosts dentro de un clúster mediante Azure Portal. Al crear una directiva de selección de ubicación, esta incluye una regla de DRS en el clúster de vSphere especificado. También incluye lógica adicional para la interoperabilidad con operaciones de Azure VMware Solution.

Una directiva de selección de ubicación tiene al menos cinco componentes necesarios: 

- **Nombre**: define el nombre de la directiva y está sujeto a las restricciones de nomenclatura de los [recursos de Azure](../azure-resource-manager/management/resource-name-rules.md).

- **Tipo**: define el tipo de control que quiere aplicar a los recursos que incluye la directiva.

- **Clúster**: define el clúster de la directiva. El ámbito de una directiva de selección de ubicación es un clúster de vSphere, por lo que solo los recursos del mismo clúster pueden formar parte de la misma directiva de selección de ubicación.

- **Estado**: indica si la directiva está habilitada o deshabilitada. En determinados escenarios, una directiva podría deshabilitarse automáticamente cuando se crea una regla en conflicto. Para obtener más información, vea las [consideraciones](#considerations) que figuran más abajo.

- **Máquina virtual**: define las máquinas virtuales y los hosts de la directiva. En función del tipo de regla que cree, la directiva puede requerir que especifique un número determinado de máquinas virtuales y hosts. Para obtener más información, consulte más adelante los [tipos de directivas de selección de ubicación](#placement-policy-types).


## <a name="prerequisites"></a>Requisitos previos

- Debe tener acceso de nivel de _colaborador_ a la nube privada para administrar las directivas de selección de ubicación.

- Las características de _directiva de selección de ubicación de DRS_ y de _acceso anticipado_ [se registran](https://ms.portal.azure.com/#blade/Microsoft_Azure_Resources/PreviewFeaturesBlade).


## <a name="placement-policy-types"></a>Tipos de directivas de selección de ubicación

### <a name="vm-vm-policies"></a>Directivas de VM-VM

Las directivas de **VM-VM** especifican si las máquinas virtuales seleccionadas deben ejecutarse en el mismo host o en hosts independientes.  Además de elegir un nombre y un clúster para la directiva, las directivas de **VM-VM** requieren que seleccione al menos dos máquinas virtuales para asignar. La asignación de hosts no es necesaria y no se permite para este tipo de directiva.

- Las directivas de **afinidad de VM-VM** indican a DRS que intente mantener las máquinas virtuales especificadas juntas en el mismo host. Por ejemplo, esto podría ser útil por motivos de rendimiento.

- Las directivas de **antiafinidad de VM-VM** indican a DRS que intente mantener las máquinas virtuales especificadas separadas entre sí en hosts independientes. Esto es útil en escenarios en los que un problema con un host no afecta a varias máquinas virtuales incluidas en la misma directiva.


### <a name="vm-host-policies"></a>Directivas de VM-host

Las directivas de **VM-host** especifican si las máquinas virtuales seleccionadas se pueden ejecutar en los hosts seleccionados.  Para evitar interferencias con las operaciones administradas por la plataforma, como el modo de mantenimiento del host y el reemplazo del host, las directivas de **VM-host** en Azure VMware Solution siempre son preferentes (también conocidas como reglas "should"). Por tanto, es posible que las directivas de **VM-host** [no se respeten en determinados escenarios](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-793013E2-0976-43B7-9A00-340FA76859D0.html). Para obtener más información, consulte la sección [Supervisión del funcionamiento de una directiva](#monitor-the-operation-of-a-policy).

Ciertas operaciones de plataforma actualizan dinámicamente la lista de hosts definidos en las directivas de **VM-host**. Por ejemplo, cuando se elimina un host que es miembro de una directiva de selección de ubicación, si esa directiva incluye más de un host, el host se quita. Además, si un host incluido en una directiva debe reemplazarse como parte de una operación administrada por la plataforma, la directiva se actualiza dinámicamente con el nuevo host.

Además de elegir un nombre y un clúster para la directiva, una directiva de **VM-host** requiere que seleccione al menos una máquina virtual y un host que asignarle.

- Las directivas de **afinidad de VM-host** indican a DRS que intente ejecutar las máquinas virtuales especificadas en los hosts definidos.

- Las directivas de **antiafinidad de VM-host** indican a DRS que intente ejecutar las máquinas virtuales especificadas en hosts distintos de los definidos.




## <a name="considerations"></a>Consideraciones



### <a name="cluster-scale-in"></a>Reducción horizontal de clústeres

Azure VMware Solution intenta evitar que se produzcan determinadas infracciones de reglas de DRS al realizar operaciones de reducción horizontal de clústeres.

No se puede quitar el último host de una directiva de VM-host. Pero si necesita hacerlo, puede solucionarlo agregando otro host a la directiva antes de quitar el host del clúster. Como alternativa, puede eliminar la directiva de selección de ubicación antes de quitar el host.

No puede tener una directiva de antiafinidad de VM-VM con más máquinas virtuales que el número de hosts en un clúster. Si al quitar un host quedan menos hosts en el clúster en relación con la cantidad de máquinas virtuales, recibirá un error que impide la operación. Para corregir esto, quite primero las máquinas virtuales de la regla y, después, quite el host del clúster.


### <a name="rule-conflicts"></a>Conflictos de reglas

Si se detectan conflictos de reglas de DRS a la hora de crear una directiva de VM-VM, la directiva se creará con un estado deshabilitado de acuerdo con el [comportamiento estándar de las reglas de DRS de VMware](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-69C738B6-5FC8-4189-9CB5-DD90A5A05979.html). Para obtener más información sobre cómo consultar los conflictos de reglas, vea la sección [Supervisión del funcionamiento de una directiva](#monitor-the-operation-of-a-policy).



## <a name="create-a-placement-policy"></a>Creación de una directiva de selección de ubicación

Asegúrese de revisar primero los requisitos de cada [tipo de directiva](#placement-policy-types).

1. En la nube privada de Azure VMware Solution, en **Administrar**, seleccione **Placement policies** (Directivas de selección de ubicación) >  **+ Crear**.

   >[!TIP]
   >También puede seleccionar el clúster en el panel de información general de la directiva de selección de ubicación y, después, seleccionar **Crear**.
   >
   >:::image type="content" source="media/placement-policies/create-placement-policy-cluster.png" alt-text="Captura de pantalla en la que se muestra una opción alternativa para crear una directiva de selección de ubicación.":::



   :::image type="content" source="media/placement-policies/create-placement-policy.png" alt-text="Captura de pantalla en la que se muestra cómo iniciar el proceso de creación de una directiva de selección de ubicación VM-VM." lightbox="media/placement-policies/create-placement-policy.png":::


1. Proporcione un nombre descriptivo, seleccione el tipo de directiva y también el clúster donde se creará la directiva. Seleccione **Habilitar**.

   >[!WARNING]
   >Si deshabilita la directiva, se crearán la directiva y la regla de DRS subyacente, pero las acciones de la directiva se omitirán hasta que esta se habilite. 

   :::image type="content" source="media/placement-policies/create-placement-policy-vm-vm-affinity-1.png" alt-text="Captura de pantalla en la que se muestran las opciones de la directiva de selección de ubicación." lightbox="media/placement-policies/create-placement-policy-vm-vm-affinity-1.png":::   

1. Si selecciona los tipos de directiva de **afinidad de VM-host** o **antiafinidad de VM-host**, es necesario que seleccione un host para la directiva. Seleccione **+ Agregar host** y los hosts que se incluirán en la directiva. Puede seleccionar varios hosts.

   :::image type="content" source="media/placement-policies/create-placement-policy-vm-host-affinity-2.png" alt-text="Captura de pantalla en la que se muestra la lista de hosts que se pueden seleccionar.":::

1. Seleccione **+ Agregar máquina virtual** y las máquinas virtuales que se incluirán en la directiva. Puede seleccionar varias máquinas virtuales.

   :::image type="content" source="media/placement-policies/create-placement-policy-vm-vm-affinity-2.png" alt-text="Captura de pantalla en la que se muestra la lista de máquinas virtuales que se pueden seleccionar.":::

   >[!NOTE]
   >El panel Seleccionar hosts muestra cuántas directivas de VM-host están asociadas con el host y el número total de máquinas virtuales que contienen esas directivas asociadas.

1. Cuando haya terminado de agregar las máquinas virtuales que quiera, seleccione **Agregar máquinas virtuales**. 

1. Seleccione **Siguiente: Revisar y crear** para revisar la directiva. 

1. Seleccione **Crear una directiva**. Si quiere realizar cambios, seleccione **Atrás: Aspectos básicos**.

   :::image type="content" source="media/placement-policies/create-placement-policy-vm-vm-affinity-3.png" alt-text="Captura de pantalla en la que se muestra la configuración de la directiva de selección de ubicación antes de crearla.":::

1. Una vez creada la directiva de selección de ubicación, seleccione **Actualizar** para verla en la lista.

   :::image type="content" source="media/placement-policies/create-placement-policy-8.png" alt-text="Captura de pantalla en la que se muestra la directiva de selección de ubicación como Habilitada después de crearla." lightbox="media/placement-policies/create-placement-policy-8.png":::



## <a name="edit-a-placement-policy"></a>Edición de una directiva de selección de ubicación

Puede cambiar el estado de una directiva, agregar un nuevo recurso o cancelar la asignación de un recurso existente.

### <a name="change-the-policy-state"></a>Modificación del estado de la directiva

Puede cambiar el estado de una directiva a **Habilitado** o **Deshabilitado**. 

1. En la nube privada de Azure VMware Solution, en **Administrar**, seleccione **Placement policies** (Directivas de selección de ubicación).

1. Seleccione **Más** (…) junto a la directiva que quiere editar y, después, seleccione **Editar**.

   >[!TIP]
   >Puede deshabilitar una directiva desde el panel de información general de la directiva de selección de ubicación si selecciona **Deshabilitar** en la lista desplegable de configuración. No se puede habilitar una directiva desde la lista desplegable de configuración.

   :::image type="content" source="media/placement-policies/edit-placement-policy.png" alt-text="Captura de pantalla en la que se muestra cómo editar una directiva de selección de ubicación." lightbox="media/placement-policies/edit-placement-policy.png":::

1.  Si la directiva está habilitada y quiere deshabilitarla, seleccione **Deshabilitada** y, después, vuelva a seleccionar **Deshabilitada** en el mensaje de confirmación. Por el contrario, si la directiva está deshabilitada y quiere habilitarla, seleccione **Habilitar**.

1.  Seleccione **Revisión y actualización**. 
 
1.  Revise los cambios y seleccione **Actualizar directiva**. Si quiere realizar cambios, seleccione **Atrás: Aspectos básicos**.


### <a name="update-the-resources-in-a-policy"></a>Actualización de los recursos de una directiva

Puede agregar nuevos recursos, como una máquina virtual o un host, a una directiva, así como quitar los recursos existentes. 

1. En la nube privada de Azure VMware Solution, en **Administrar**, seleccione **Placement policies** (Directivas de selección de ubicación).

1. Seleccione **Más** (…) junto a la directiva que quiere editar y, después, seleccione **Editar**.

   :::image type="content" source="media/placement-policies/edit-placement-policy.png" alt-text="Captura de pantalla en la que se muestra cómo editar los recursos de una directiva de selección de ubicación." lightbox="media/placement-policies/edit-placement-policy.png":::

   - Para quitar un recurso existente, seleccione el recurso o los recursos que quiere quitar. Después, seleccione **Cancelar asignación** para quitar el recurso o los recursos de la lista.

      :::image type="content" source="media/placement-policies/edit-placement-policy-unassign.png" alt-text="Captura de pantalla en la que se muestra cómo quitar un recurso existente de una directiva de selección de ubicación.":::

   - Para agregar un nuevo recurso, seleccione **Edit virtual machine** (Editar máquina virtual) o **Edit host** (Editar host), seleccione el recurso que quiere agregar y, después, seleccione **Guardar**. 

1. Seleccione **Next: Review and update** (Siguiente: Revisar y actualizar). 

1. Revise los cambios y seleccione **Actualizar directiva**.  Si quiere realizar cambios, seleccione **Atrás: Aspectos básicos**.



## <a name="delete-a-policy"></a>Eliminar una directiva

Puede eliminar una directiva de selección de ubicación y su regla de DRS correspondiente. 

1. En la nube privada de Azure VMware Solution, en **Administrar**, seleccione **Placement policies** (Directivas de selección de ubicación).

1. Seleccione **Más** (…) junto a la directiva que quiere editar y, después, seleccione **Eliminar**.

   :::image type="content" source="media/placement-policies/delete-placement-policy.png" alt-text="Captura de pantalla en la que se muestra cómo eliminar una directiva de selección de ubicación." lightbox="media/placement-policies/delete-placement-policy.png":::

1. Seleccione **Eliminar** en el mensaje de confirmación.



## <a name="monitor-the-operation-of-a-policy"></a>Supervisión del funcionamiento de una directiva

Use vSphere Client para supervisar el funcionamiento de la regla de DRS correspondiente de una directiva de selección de ubicación. 

Como titular del rol cloudadmin, puede ver, pero no editar, las reglas de DRS creadas por una directiva de selección de ubicación en la pestaña Configurar del clúster en Reglas de host/máquina virtual. Desde aquí, puede ver información adicional, por ejemplo, si las reglas de DRS están en conflicto.

Además, puede supervisar varias operaciones de reglas de DRS, como recomendaciones y errores, desde la pestaña Supervisar del clúster.
