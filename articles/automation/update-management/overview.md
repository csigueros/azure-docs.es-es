---
title: Introducción a Update Management en Azure Automation
description: En este artículo se ofrece información general de la característica Update Management que implementa las actualizaciones de las máquinas Windows y Linux.
services: automation
ms.subservice: update-management
ms.date: 09/27/2021
ms.topic: conceptual
ms.openlocfilehash: fed1ce7f236b568458f1eb1b25ce5420c2ad5501
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092174"
---
# <a name="update-management-overview"></a>Introducción a Update Management

Puede usar Update Management en Azure Automation para administrar las actualizaciones del sistema operativo de las máquinas virtuales Windows y Linux en Azure, máquinas virtuales o físicas en entornos locales, y en otros entornos en la nube. Puede evaluar rápidamente el estado de las actualizaciones disponibles y administrar el proceso de instalación de las actualizaciones necesarias para las máquinas que informan a Update Management. 

Como proveedor de servicios, es posible que haya incorporado varios inquilinos de cliente para [Azure Lighthouse](../../lighthouse/overview.md). Update Management se puede usar para evaluar y programar implementaciones de actualizaciones en máquinas de varias suscripciones en el mismo inquilino de Azure Active Directory (Azure AD) o distintos inquilinos mediante Azure Lighthouse.

Microsoft ofrece otras funcionalidades que le ayudarán a administrar las actualizaciones de las máquinas virtuales de Azure o los conjuntos de escalado de máquinas virtuales de Azure que debe tener en cuenta como parte de la estrategia general de administración de actualizaciones. 

- Si desea mantener el cumplimiento de la seguridad y le interesa evaluar y actualizar automáticamente las máquinas virtuales de Azure con las actualizaciones *Crítica* y *Seguridad* que se publican cada mes, consulte [Aplicación de revisiones automática a invitados de las máquinas virtuales](../../virtual-machines/automatic-vm-guest-patching.md) (versión preliminar). Se trata de una solución alternativa de administración de actualizaciones para que las máquinas virtuales de Azure se actualicen automáticamente durante las horas de poca actividad, incluidas las máquinas virtuales que están dentro de un conjunto de disponibilidad; este proceso es diferente de la administración de implementaciones de actualizaciones en máquinas virtuales con Update Management en Azure Automation. 

- Si administra conjuntos de escalado de máquinas virtuales de Azure, consulte cómo realizar [actualizaciones automáticas de imágenes de sistema operativo](../../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) para actualizar de forma segura y automática el disco del sistema operativo de todas las instancias del conjunto de escalado. 

Antes de implementar Update Management y habilitar las máquinas para su administración, asegúrese de que comprende la información de las secciones siguientes.

## <a name="about-update-management"></a>Acerca de Update Management

El siguiente diagrama muestra cómo Update Management evalúa y aplica las actualizaciones de seguridad a todos los servidores Windows Server y Linux conectados.

![Flujo de trabajo de Update Management](./media/overview/update-mgmt-workflow.png)

Update Management se integra en los registros de Azure Monitor para almacenar las evaluaciones de las actualizaciones y los resultados de la implementación de actualizaciones como datos de registro, desde máquinas asignadas de Azure y no de Azure. Para recopilar estos datos, la cuenta de Automation y el área de trabajo de Log Analytics se vinculan entre sí, y se necesita el agente de Log Analytics para Windows y Linux en la máquina, configurado para informar a esta área de trabajo. 

Update Management permite recopilar información sobre las actualizaciones del sistema de los agentes de un grupo de administración de System Center Operations Manager que esté conectado al área de trabajo. No se permite tener registrada una máquina para Update Management en más de un área de trabajo de Log Analytics (también conocido como hospedaje múltiple).

En la tabla siguiente se resumen los orígenes conectados que se admiten con Update Management.

| Origen conectado | Compatible | Descripción |
| --- | --- | --- |
| Windows |Sí |Update Management recopila información acerca de las actualizaciones del sistema de las máquinas Windows con el agente de Log Analytics y la instalación de las actualizaciones necesarias.<br> Las máquinas deben informar a Microsoft Update o Windows Server Update Services (WSUS). |
| Linux |Sí |Update Management recopila información acerca de las actualizaciones del sistema de las máquinas Linux con el agente de Log Analytics y la instalación de las actualizaciones necesarias en las distribuciones admitidas.<br> Las máquinas deben informar a un repositorio local o remoto. |
| Grupo de administración de Operations Manager |Sí |Update Management recopila información sobre las actualizaciones de software de los agentes de un grupo de administración conectado.<br/><br/>No se requiere ninguna conexión directa entre el agente de Operations Manager y los registros de Azure Monitor. Los datos de registro se reenvían desde el grupo de administración al área de trabajo de Log Analytics. |

Las máquinas asignadas a Update Management informan de su actualización en función del origen con el que tienen configurada la sincronización. Las máquinas Windows tienen que estar configuradas para informar a [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) o [Microsoft Update](https://www.update.microsoft.com), y las máquinas Linux, para informar a un repositorio local o público. También puede usar Update Management con Microsoft Endpoint Configuration Manager. Para obtener más información, consulte [Integración de Update Management con Windows Endpoint Configuration Manager](mecmintegration.md). 

Si el agente de Windows Update (WUA) en la máquina Windows está configurado para informar a WSUS, los resultados pueden diferir de lo que se muestra en Microsoft Update en función de cuándo WSUS se sincronizó por última vez con Microsoft Update. Este comportamiento es el mismo para las máquinas Linux que están configuradas para informar a un repositorio local en lugar de a un repositorio público. En una máquina Windows, el examen de cumplimiento se ejecuta cada 12 horas de forma predeterminada. En una máquina Linux, el examen de cumplimiento se realiza cada hora de manera predeterminada. Si se reinicia el agente de Log Analytics, se inicia un examen de cumplimiento al cabo de 15 minutos. Después de que una máquina finalice un examen de cumplimiento de actualizaciones, el agente reenvía la información de forma masiva a los registros de Azure Monitor. 

Puede implementar e instalar las actualizaciones de software en las máquinas que requieren las actualizaciones mediante la creación de una implementación programada. Las actualizaciones clasificadas como *Opcional* no se incluyen en el ámbito de implementación en máquinas Windows. Solo se incluyen las actualizaciones necesarias.

La implementación programada define qué máquina de destino reciben las actualizaciones aplicables. Lo hace mediante la especificación explícita de determinadas máquinas o por medio de la selección de un [grupo de equipos](../../azure-monitor/logs/computer-groups.md) que se basa en las búsquedas de registros de un conjunto determinado de máquinas (o en una [consulta de Azure](query-logs.md) que selecciona de forma dinámica las máquinas virtuales de Azure en función de los criterios especificados). Estos grupos difieren de la [configuración de ámbito](../../azure-monitor/insights/solution-targeting.md), que se usa para controlar los destinos de las máquinas que reciben la configuración para habilitar Update Management. Esto evita que realicen la comprobación de actualizaciones e informen sobre la misma, además de instalar las actualizaciones necesarias aprobadas.

Al definir una implementación, también se especifica una programación para aprobar y establecer un período de tiempo durante el que se pueden instalar actualizaciones. Este período se denomina ventana de mantenimiento. Un intervalo de 20 minutos de la ventana de mantenimiento se reserva para los reinicios, suponiendo que sea necesario reiniciar y que haya seleccionado la opción de reinicio adecuada. Si la aplicación de revisiones tarda más de lo esperado y la ventana de mantenimiento dura menos de 20 minutos, no se producirá un reinicio.

Una vez que se programa un paquete de actualización para su implementación, la actualización tarda entre 2 y 3 horas en mostrarse para su evaluación para las máquinas Linux. En el caso de las máquinas Windows, la revisión tarda de 12 a 15 horas en aparecer para su evaluación tras su publicación. Antes y después de la instalación de la actualización, se evalúa el cumplimiento de las actualizaciones y los resultados de los datos de registro se reenvían al área de trabajo.

Los Runbooks instalan las actualizaciones en Azure Automation. No puede ver estos runbooks, que no requieren ninguna configuración. Cuando se crea una implementación de actualizaciones, esta crea una programación que inicia un runbook de actualización maestro a la hora especificada para las máquinas incluidas. El runbook maestro inicia un runbook secundario en cada agente que inicia la instalación de las actualizaciones necesarias con el agente de Windows Update en Windows, o con el comando correspondiente en la distribución de Linux admitida.

En la fecha y hora especificadas en la implementación de actualizaciones, las máquinas de destino ejecutan la implementación en paralelo. Antes de la instalación, se ejecuta un examen para comprobar que las actualizaciones siguen siendo necesarias. En las máquinas cliente de WSUS, si no se aprueban las actualizaciones en WSUS, se produce un error en la implementación de actualizaciones.

## <a name="limits"></a>Límites

Para ver los límites que se aplican a Update Management, consulte los [límites del servicio Azure Automation](../../azure-resource-manager/management/azure-subscription-service-limits.md#update-management).

## <a name="permissions"></a>Permisos

Para crear y administrar implementaciones de actualizaciones, necesita permisos concretos. Para más información sobre estos permisos, consulte [Acceso basado en rol: Update Management](../automation-role-based-access-control.md#update-management-permissions).

## <a name="update-management-components"></a>Componentes de Update Management

Update Management usa los recursos descritos en esta sección. Estos recursos se agregan automáticamente a la cuenta de Automation al habilitar Update Management.

### <a name="hybrid-runbook-worker-groups"></a>Grupos de Trabajos híbridos de runbook

Después de habilitar Update Management, las máquinas Windows conectadas directamente al área de trabajo de Log Analytics se configuran de modo automático como un sistema Hybrid Runbook Worker para admitir los runbooks que admiten a su vez Update Management.

Cada máquina Windows administrada por Update Management se muestra en el panel Grupos de Hybrid Worker como un grupo Hybrid Worker del sistema para la cuenta de Automation. Los grupos usan la convención de nomenclatura `Hostname FQDN_GUID`. No puede usar estos grupos como destino con runbooks de su cuenta. Si lo intenta, se producirá un error. Estos grupos están diseñados únicamente para admitir Update Management. Para más información sobre la visualización de la lista de máquinas Windows configuradas como Hybrid Runbook Worker, consulte [Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md#view-system-hybrid-runbook-workers).

Puede agregar la máquina Windows a un grupo de Hybrid Runbook Worker de usuario en la cuenta de Automation para admitir runbooks de Automation si usa la misma cuenta para Update Management y la pertenencia a grupos de Hybrid Runbook Worker. Esta funcionalidad se agregó en la versión 7.2.12024.0 de Hybrid Runbook Worker.

### <a name="external-dependencies"></a>Dependencias externas

La solución Update Management de Azure Automation depende de las siguientes dependencias externas para ofrecer actualizaciones de software.

* Se necesita Windows Server Update Services (WSUS) o Microsoft Update para los paquetes de actualizaciones de software y para el análisis de su aplicabilidad en máquinas basadas en Windows.
* Se necesita el cliente de Agente de Windows Update (WUA) para que las máquinas basadas en Windows puedan conectarse al servidor de WSUS o a Microsoft Update.
* Un repositorio local o remoto para recuperar e instalar las actualizaciones del sistema operativo en máquinas basadas en Linux.

### <a name="management-packs"></a>Módulos de administración

Los siguientes módulos de administración se instalan en las máquinas administradas por Update Management. Si el grupo de administración de Operations Manager está [conectado a un área de trabajo de Log Analytics](../../azure-monitor/agents/om-agents.md), se instalarán los siguientes módulos de administración en dicho grupo. No es necesario configurar ni administrar dichos módulos.

* Intelligence Pack Update Assessment de Microsoft System Center Advisor (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Módulo de administración de Update Deployment

> [!NOTE]
> Si tiene un grupo de administración de Operations Manager 1807 o 2019 conectado a un área de trabajo de Log Analytics con agentes configurados en el grupo de administración para recopilar los datos de registro, debe invalidar el parámetro `IsAutoRegistrationEnabled` y establecerlo en `True` en la regla **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init**.

Para más información sobre las actualizaciones de los módulos de administración, consulte [Conexión de Operations Manager con registros de Azure Monitor](../../azure-monitor/agents/om-agents.md).

> [!NOTE]
> Para que Update Management administre totalmente las máquinas que tengan el agente de Log Analytics, debe actualizar al agente de Log Analytics para Windows o Linux. Para aprender a actualizar el agente, consulte [Actualización de Operations Manager Agent](/system-center/scom/deploy-upgrade-agents). En entornos que usan Operations Manager, debe ejecutar System Center Operations Manager 2012 R2 UR 14 o posterior.

## <a name="data-collection-frequency"></a>Frecuencia de recopilación de datos

Update Management examina los datos de las máquinas administradas con las siguientes reglas. Puede que transcurran entre 30 minutos y 6 horas antes de que se muestren los datos actualizados de las máquinas administradas.

* Cada máquina Windows: Update Management realiza un examen dos veces al día de cada máquina.

* Cada máquina Linux: Update Management realiza un examen cada hora.

El uso medio de datos de los registros de Azure Monitor para una máquina que utiliza Update Management es aproximadamente de 25 MB al mes. Este valor es solo una aproximación y está sujeto a cambios en función de su entorno. Se recomienda supervisar el entorno para realizar un seguimiento del uso exacto. Para obtener más información sobre cómo analizar el uso de los datos de los registros de Azure Monitor, consulte [Administrar el uso y los costos](../../azure-monitor/logs/manage-cost-storage.md).

## <a name="update-classifications"></a>Clasificaciones de actualizaciones

En la tabla siguiente se definen las clasificaciones que Update Management admite para las actualizaciones de Windows.

|clasificación  |Descripción  |
|---------|---------|
|Actualizaciones críticas     | Actualización para un problema específico que resuelve un error crítico no relacionado con la seguridad.        |
|Actualizaciones de seguridad     | Actualización para un problema específico del producto relacionado con la seguridad.        |
|Paquetes acumulativos de actualizaciones     | Conjunto acumulativo de revisiones que se empaquetan para facilitar la implementación.        |
|Feature Packs     | Nuevas características del producto que se distribuyen fuera de una versión del producto.        |
|Service Packs     | Un conjunto acumulativo de revisiones que se aplican a una aplicación.        |
|Actualizaciones de definiciones     | Una actualización de archivos de definiciones de virus o de otra índole.        |
|Herramientas     | Utilidad o característica que ayuda a realizar una o más tareas.        |
|Actualizaciones     | Actualización de una aplicación o archivo que están instalados actualmente.        |

En la tabla siguiente se definen las clasificaciones admitidas para las actualizaciones de Linux.

|clasificación  |Descripción  |
|---------|---------|
|Actualizaciones críticas y de seguridad     | Actualizaciones para un problema específico o un problema de un producto específico relacionado con la seguridad.         |
|Otras actualizaciones     | Todas las demás actualizaciones que ni son críticas por naturaleza ni son actualizaciones de seguridad.        |

> [!NOTE]
> La clasificación de actualizaciones para máquinas Linux solo está disponible en las regiones de nube pública de Azure admitidas. No hay clasificación de actualizaciones de Linux si se usa Update Management en las siguientes regiones de nube nacional:
>
>* Azure US Government
>* 21Vianet en China
>
> En lugar de clasificarse, las actualizaciones se muestran en la categoría **Otras actualizaciones**.
>
> Update Management usa los datos publicados por las distribuciones admitidas, en concreto, sus archivos de [OVAL](https://oval.mitre.org/) (Open Vulnerability and Assessment Language). Dado que el acceso a Internet está restringido desde estas nubes nacionales, Update Management no puede acceder a los archivos.

En Linux, Update Management puede distinguir entre actualizaciones críticas y de seguridad en la nube en la clasificación **Seguridad** y en **Otras**, y mostrar al mismo tiempo datos de evaluación debido al enriquecimiento de datos en la nube. Para aplicar revisiones, Update Management se basa en los datos de clasificación disponibles en la máquina. A diferencia de otras distribuciones, CentOS no dispone de esta información en la versión RTM. Si tiene máquinas de CentOS configuradas para devolver datos de seguridad para el siguiente comando, Update Management puede aplicar revisiones basadas en clasificaciones.

```bash
sudo yum -q --security check-update
```

Actualmente no hay ningún método compatible para habilitar la disponibilidad de datos de clasificación nativos en CentOS. En este momento, se proporciona soporte técnico limitado a clientes que puedan haber habilitado esta característica por su cuenta.

Para clasificar las actualizaciones de la versión 6 de Red Hat Enterprise, debe instalar el complemento de seguridad de yum. En Red Hat Enterprise Linux 7, el complemento ya forma parte de yum y no es necesario instalar nada. Para más información, consulte el siguiente [artículo de conocimientos](https://access.redhat.com/solutions/10021) de Red Hat.

Cuando se programa una actualización para que se ejecute en una máquina Linux que, por ejemplo, está configurada para instalar solo las actualizaciones que coincidan con la clasificación **Seguridad**, las actualizaciones instaladas podrían ser diferentes de las actualizaciones que coinciden con esa clasificación o constituir un subconjunto de estas. Cuando se realiza una evaluación de las actualizaciones del sistema operativo pendientes para la máquina Linux, Update Management utiliza los archivos [Open Vulnerability and Assessment Language ](https://oval.mitre.org/) (OVAL) proporcionados por el proveedor de distribución de Linux para la clasificación.

La categorización se realiza para las actualizaciones de Linux en función de las clasificaciones **Seguridad** u **Otras**, según los archivos OVAL, y estas clasificaciones incluyen las actualizaciones que tratan problemas de seguridad o vulnerabilidades. Sin embargo, cuando se ejecuta la programación de actualización, lo hace en la máquina Linux mediante el administrador de paquetes adecuado, como YUM, APT o ZYPPER, para instalarlas. El administrador de paquetes para la distribución de Linux puede tener un mecanismo diferente para clasificar las actualizaciones, donde los resultados pueden diferir de los que se obtienen de los archivos OVAL mediante Update Management. Para comprobar manualmente la máquina y saber qué actualizaciones son de seguridad relevantes para el administrador de paquetes, consulte [Solución de problemas de implementación de actualizaciones de Linux](../troubleshoot/update-management.md#updates-linux-installed-different).

>[!NOTE]
> Es posible que la implementación de actualizaciones por clasificación de actualizaciones no funcione correctamente para las distribuciones de Linux compatibles con Update Management. Esto se debe a un problema identificado con el esquema de nomenclatura del archivo OVAL, lo que impide que Update Management establezca correctamente la coincidencia de las clasificaciones en función de las reglas de filtrado. Dada la lógica diferente que se usa en las evaluaciones de las actualizaciones de seguridad, los resultados pueden diferir de las actualizaciones de seguridad que se aplican durante la implementación. Si ha establecido la clasificación como **Crítico** y **Seguridad**, la implementación de actualizaciones funcionará según lo previsto. Solo se ve afectada la *clasificación de las actualizaciones* durante una evaluación.
>
> Update Management para máquinas con Windows Server no se ve afectado; la clasificación de actualizaciones y las implementaciones no cambian.

## <a name="integrate-update-management-with-configuration-manager"></a>Integración de Update Management con Configuration Manager.

Los clientes que han invertido en Microsoft Endpoint Configuration Manager para administrar equipos, servidores y dispositivos móviles también dependen de la fuerza y la madurez de Configuration Manager para que les ayude a administrar las actualizaciones de software. Para aprender a integrar Update Management con Configuration Manager, consulte este artículo sobre la [integración de Update Management con Endpoint Configuration Manager de Windows](mecmintegration.md).

## <a name="third-party-updates-on-windows"></a>Actualizaciones de terceros en Windows

Update Management se basa en el repositorio de actualización configurado localmente para actualizar los sistemas Windows compatibles, ya sea WSUS o Windows Update. Herramientas como [System Center Updates Publisher](/configmgr/sum/tools/updates-publisher) le permiten importar y publicar actualizaciones personalizadas con WSUS. Este escenario permite que Update Management actualice las máquinas que usan Configuration Manager como repositorio de actualizaciones con software de terceros. Para obtener información sobre cómo configurar Updates Publisher, consulte [Instalar Updates Publisher](/configmgr/sum/tools/install-updates-publisher).

## <a name="next-steps"></a>Pasos siguientes

* Antes de habilitar y usar Update Management, consulte [Planeamiento de la implementación de Update Management](plan-deployment.md).

* Revise las preguntas más frecuentes sobre Update Management en [Preguntas más frecuentes sobre Azure Automation](../automation-faq.md).