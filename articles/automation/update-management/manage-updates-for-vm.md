---
title: Administración de actualizaciones y revisiones para las máquinas virtuales en Azure Automation
description: En este artículo se indica cómo usar Update Management para administrar las actualizaciones y revisiones de las máquinas virtuales de Azure y las que no lo son.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 08/25/2021
ms.openlocfilehash: 932f5d93c5fa67de486ddb9cabaafd68384f0db8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357481"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>Administración de actualizaciones y revisiones para las máquinas virtuales

Las actualizaciones de software de Update Management de Azure Automation proporcionan un conjunto de herramientas y recursos que pueden ayudar a administrar la compleja tarea de realizar un seguimiento de las actualizaciones de software y aplicarlas a las máquinas en Azure y en la nube híbrida. Se requiere un proceso de administración de actualizaciones de software eficaz para mantener la eficiencia operativa, superar los problemas de seguridad y reducir los riesgos de las cada vez mayores amenazas de seguridad en la red. Sin embargo, debido a la naturaleza cambiante de la tecnología y la aparición continua de nuevas amenazas de seguridad, la administración de las actualizaciones de software, para ser efectiva, requiere atención constante y continua.

> [!NOTE]
> Update Management permite implementar actualizaciones de terceros y descargarlas con antelación. Para ello, es necesario realizar cambios en los sistemas que se van a actualizar. Consulte [Configuración de los parámetros de Windows Update para Azure Automation Update Management](configure-wuagent.md) para aprender a configurar estas opciones en sus sistemas.

Antes de intentar administrar las actualizaciones de las máquinas virtuales, asegúrese de que ha habilitado Update Management en ellas con uno de estos métodos:

* [Habilitación de Update Management desde una cuenta de Automation](enable-from-automation-account.md)
* [Habilitación de Update Management desde Azure Portal](enable-from-portal.md)
* [Habilitación de Update Management desde un runbook](enable-from-runbook.md)
* [Habilitación de Update Management desde una maquina virtual de Azure](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Limitación del ámbito de implementación

Update Management usa una configuración de ámbito dentro del área de trabajo para dirigirse a los equipos que tienen que recibir actualizaciones. Para más información, consulte [Uso de configuraciones de ámbito para Update Management](scope-configuration.md).

## <a name="compliance-assessment"></a>Evaluación del cumplimiento

Antes de implementar las actualizaciones de software en las máquinas, revise los resultados de la evaluación del cumplimiento de las actualizaciones para las máquinas habilitadas. Para cada actualización de software, se registra su estado de cumplimiento y, una vez completada la evaluación, se recopila y reenvía de forma masiva a los registros de Azure Monitor.

En una máquina Windows, el examen de cumplimiento se ejecuta cada 12 horas de manera predeterminada y se inicia en los 15 minutos posteriores al reinicio del agente de Log Analytics para Windows. Luego, los datos de la valoración se reenvían al área de trabajo y actualizan la tabla **Actualizaciones**. Antes y después de la instalación de actualizaciones se realiza un examen de cumplimiento de actualizaciones para identificar las que faltan, pero los resultados no se usan para actualizar los datos de la valoración en la tabla.

Es importante revisar las recomendaciones sobre cómo [configurar el cliente de Windows Update](configure-wuagent.md) con Update Management para evitar problemas que impidan su correcta administración.

En una máquina Linux, el examen de cumplimiento se realiza cada hora de manera predeterminada. Si se reinicia el agente de Log Analytics para Linux, se inicia un examen de cumplimiento al cabo de 15 minutos.

Los resultados de cumplimiento se presentan en Update Management para cada máquina evaluada. Pueden transcurrir hasta 30 minutos antes de que el panel muestre los datos actualizados de una nueva máquina habilitada para administración.

Revise [Supervisión de las actualizaciones de software](view-update-assessments.md) para obtener información sobre cómo ver los resultados de cumplimiento.

## <a name="deploy-updates"></a>Implementación de actualizaciones

Después de revisar los resultados de cumplimiento, la fase de implementación de las actualizaciones de software es el proceso de implementación de las actualizaciones de software. Para instalar actualizaciones, programe una implementación que esté en consonancia con su ventana de programación y servicio de versiones. Puede elegir los tipos de actualizaciones que quiere incluir en la implementación. Por ejemplo, puede incluir actualizaciones de seguridad o críticas y excluir paquetes acumulativos de actualizaciones.

Revise [implementación de actualizaciones de software](deploy-updates.md) para aprender a programar una implementación de actualizaciones.

## <a name="exclude-updates"></a>Excluir actualizaciones

En algunas variantes de Linux, como Red Hat Enterprise Linux, las actualizaciones de nivel de sistema operativo pueden producirse mediante paquetes. Esto puede llevar a ejecuciones de Update Management en las que cambia el número de versión del sistema operativo. Como Update Management usa los mismos métodos para actualizar paquetes que un administrador utiliza localmente en la máquina Linux, este comportamiento es deliberado.

Para evitar la actualización de la versión del sistema operativo con las implementaciones de Update Management, use la característica **Exclusión**.

En Red Hat Enterprise Linux, el nombre del paquete para excluir es `redhat-release-server.x86_64`.

## <a name="linux-update-classifications"></a>Clasificaciones de actualizaciones de Linux

Al implementar actualizaciones en una máquina Linux, puede seleccionar clasificaciones de actualizaciones. Esta opción filtra las actualizaciones que cumplen los criterios especificados. Este filtro se aplica localmente en el equipo cuando se implementa la actualización.

Dado que Update Management realiza el enriquecimiento de actualizaciones en la nube, puede marcar algunas actualizaciones en Update Management como de impacto para la seguridad, aunque la máquina local no tenga esa información. Si aplica actualizaciones críticas a una máquina Linux, puede haber actualizaciones que no estén marcadas como de impacto para la seguridad en esa máquina y que, por lo tanto, no se apliquen. Sin embargo, es posible que Update Management todavía notifique que esa máquina no es compatible porque tiene información adicional acerca de la actualización pertinente.

La implementación de actualizaciones mediante la clasificación de actualizaciones no funciona en las versiones RTM de CentOS. Para implementar correctamente actualizaciones para CentOS, seleccione todas las clasificaciones para asegurarse de que se aplican las actualizaciones. Para SUSE, al seleccionar SOLO **Otras actualizaciones** como clasificación, puede que se instalen algunas actualizaciones de seguridad si están relacionadas con Zypper (administrador de paquetes) o se requieren sus dependencias en primer lugar. Este comportamiento es una limitación de zypper. En algunos casos, puede que se le pida volver a ejecutar la implementación de actualizaciones y, a continuación, comprobar la implementación a través del registro de actualizaciones.

## <a name="review-update-deployments"></a>Visualización de las implementaciones de actualizaciones

Una vez completada la implementación, revise el proceso para determinar el éxito de la implementación de actualizaciones según la máquina o el grupo de destino. Consulte [Revisión del estado de implementación](deploy-updates.md#check-deployment-status) para obtener información sobre cómo puede supervisar el estado de la implementación.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información sobre cómo crear alertas que le notifiquen los resultados de la implementación de actualizaciones, consulte [Creación de alertas para Update Management](configure-alerts.md).

* Puede [consultar los registros de Azure Monitor](query-logs.md) para analizar las evaluaciones de las actualizaciones, las implementaciones y otras tareas de administración relacionadas. Incluye consultas predefinidas para ayudarle a empezar.