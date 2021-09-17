---
title: Visualización de las valoraciones de actualizaciones de Azure Automation
description: En este artículo se explica cómo ver las valoraciones de actualizaciones para las implementaciones de Update Management.
services: automation
ms.subservice: update-management
ms.date: 06/10/2021
ms.topic: conceptual
ms.openlocfilehash: 20df340df025ceada8a4b254f317fe2f5d8eecbc
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768900"
---
# <a name="view-update-assessments-in-update-management"></a>Visualización de las evaluaciones de las actualizaciones en Update Management

En Update Management, puede ver información sobre las máquinas, las actualizaciones que faltan, las implementaciones de actualizaciones y las implementaciones de actualizaciones programadas. Puede ver la información sobre la evaluación en el ámbito de la máquina virtual de Azure seleccionada, desde el servidor habilitado para Arc seleccionado o la cuenta de Automation en todos los equipos y servidores configurados.

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en el [Portal de Azure](https://portal.azure.com)

## <a name="view-update-assessment"></a>Ver evaluación de la actualización

Para ver la evaluación de las actualizaciones desde una máquina virtual de Azure, vaya a **Máquinas virtuales** y seleccione la máquina virtual en la lista. En el menú de la izquierda, seleccione **Actualizaciones de invitado y host** y, a continuación, seleccione **Ir a Update Management** en la página **Actualizaciones de invitado y host**.

En Update Management, puede ver información sobre las máquinas, las actualizaciones que faltan, las implementaciones de actualizaciones y las implementaciones de actualizaciones programadas.

[ ![Vista de la evaluación de Update Management de las máquinas virtuales de Azure](./media/view-update-assessments/update-assessment-azure-vm.png)](./media/view-update-assessments/update-assessment-azure-vm-expanded.png#lightbox)

Para ver la evaluación de la actualización desde un servidor habilitado para Arc, vaya a **Servidores: Azure Arc** y seleccione el servidor en la lista. En el menú de la izquierda, seleccione **Actualizaciones de invitado y host**. En la página **Actualizaciones de invitado y host**, seleccione **Ir a Update Management**.

En Update Management, puede ver información sobre la máquina habilitada para Arc, las actualizaciones que faltan, las implementaciones de actualizaciones y las implementaciones de actualizaciones programadas.

[ ![Vista de la evaluación de Update Management de los servidores habilitados para Arc](./media/view-update-assessments/update-assessment-arc-server.png)](./media/view-update-assessments/update-assessment-arc-server-expanded.png#lightbox)

Para ver la evaluación de las actualizaciones en todos los equipos, incluidos los servidores habilitados para Arc de la cuenta de Automation, vaya a **Cuentas de Automation** y, en la lista, seleccione su cuenta de Automation con Update Management habilitado. En su cuenta de Automation, seleccione **Administración de actualizaciones** en el menú de la izquierda.

Las actualizaciones de su entorno se muestran en la página **Administración de actualizaciones**. Si alguna actualización se identifica como ausente, verá una lista de las actualizaciones que faltan en la pestaña **Actualizaciones que faltan**.

[ ![Vista predeterminada de Update Management](./media/overview/update-management-view.png)](./media/overview/update-management-view-expanded.png#lightbox)

En la columna **CUMPLIMIENTO**, puede ver la última vez que se evaluó la máquina. En la columna **PREPARACIÓN DE ACTUALIZACIONES DEL AGENTE**, puede ver el estado del agente de actualización. Si hay un problema, seleccione el vínculo para dirigirse a la documentación de solución de problemas, que le ayudará a corregirlo.

En **Vínculo información**, seleccione el vínculo de una actualización para abrir el artículo de soporte técnico que le proporciona información importante acerca de la actualización.

[ ![Visualización del estado de la actualización](./media/view-update-assessments/missing-updates.png)](./media/view-update-assessments/missing-updates-expanded.png#lightbox)

> [!NOTE]
> La información que se muestra sobre el estado de actualización de la definición de Windows Defender se basa en los últimos datos resumidos en el área de trabajo de Log Analytics, y es posible que no sean actuales. Consulte [Siempre se muestra que falta la actualización de Windows Defender](../troubleshoot/update-management.md#windows-defender-update-missing-status) para obtener más información sobre este comportamiento.
 
Haga clic en cualquier otro lugar de la actualización para abrir el panel de búsqueda de registros. La consulta para la búsqueda de registros está predefinida para esa actualización específica. Puede modificar esta consulta o crear su propia consulta para ver información detallada.

[ ![Visualización de resultados de consultas de registro](./media/view-update-assessments/logsearch-results.png)](./media/view-update-assessments/logsearch-results-expanded.png#lightbox)

## <a name="view-missing-updates"></a>Visualización de actualizaciones que faltan

Haga clic en **Actualizaciones que faltan** para ver la lista de actualizaciones que faltan en las máquinas. Se muestran todas las actualizaciones, y se pueden seleccionar. Se muestran los datos sobre el número de máquinas que requieren la actualización, los detalles del sistema operativo y un vínculo para obtener más información. El panel Búsqueda de registros también muestra más información sobre las actualizaciones.

![Actualizaciones que faltan](./media/view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="work-with-update-classifications"></a>Trabajo con las clasificaciones de actualizaciones

En las siguientes tablas, se muestran las clasificaciones de las actualizaciones de Update Management admitidas, junto con una definición de cada una de ellas.

### <a name="windows"></a>Windows

|clasificación  |Descripción  |
|---------|---------|
|Actualizaciones críticas     | Actualizaciones para problemas específicos que resuelven un error crítico no relacionado con la seguridad.        |
|Actualizaciones de seguridad     | Actualizaciones para problemas específicos del producto relacionados con la seguridad.        |
|Paquetes acumulativos de actualizaciones     | Conjunto de revisiones que se empaquetan para facilitar la implementación.        |
|Feature Packs     | Nuevas características del producto que se distribuyen fuera de una versión del producto.        |
|Service Packs     | Conjuntos de revisiones que se aplican a una aplicación.        |
|Actualizaciones de definiciones     | Actualizaciones de archivos de definiciones de virus o de otra índole.        |
|Herramientas     | Utilidades o características que ayudan a realizar una o más tareas.        |
|Actualizaciones     | Actualizaciones de aplicaciones o archivos instalados actualmente.        |

### <a name="linux"></a>Linux

|clasificación  |Descripción  |
|---------|---------|
|Actualizaciones críticas y de seguridad     | Actualizaciones para un problema específico o un problema de un producto específico relacionado con la seguridad.         |
|Otras actualizaciones     | Todas las demás actualizaciones que ni son críticas por naturaleza ni son actualizaciones de seguridad.        |

En Linux, Update Management puede distinguir entre actualizaciones críticas y de seguridad en la nube y mostrar al mismo tiempo datos de evaluación. (Esta granularidad es posible gracias al enriquecimiento de los datos en la nube). Para aplicar revisiones, Update Management se basa en los datos de clasificación disponibles en la máquina. A diferencia de otras distribuciones, CentOS no dispone de esta información en las versiones RTM del producto. Si tiene máquinas de CentOS configuradas para devolver datos de seguridad para el siguiente comando, Update Management puede aplicar revisiones basadas en clasificaciones:

```bash
sudo yum -q --security check-update
```

Actualmente no hay ningún método compatible para habilitar la disponibilidad de datos de clasificación nativos en CentOS. En este momento, solo se proporciona soporte técnico a clientes que han habilitado esta funcionalidad por su cuenta.

Para clasificar las actualizaciones de la versión 6 de Red Hat Enterprise, debe instalar el complemento de seguridad de yum. En Red Hat Enterprise Linux 7, el complemento ya forma parte de yum, por lo que no es necesario instalar nada. Para más información, consulte el siguiente [artículo de conocimientos](https://access.redhat.com/solutions/10021) de Red Hat.

## <a name="next-steps"></a>Pasos siguientes

La siguiente fase del proceso es [implementar actualizaciones](deploy-updates.md) en máquinas no compatibles y revisar los resultados de la implementación.
