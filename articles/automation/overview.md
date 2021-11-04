---
title: Información general sobre Azure Automation
description: En este artículo se indica qué es Azure Automation y cómo utilizarlo para automatizar el ciclo de vida de la infraestructura y las aplicaciones.
services: automation
keywords: Azure Automation, DSC, PowerShell, State Configuration, Update Management, Change Tracking, DSC, inventario, runbooks, Python, gráfico
ms.date: 10/25/2021
ms.topic: overview
ms.openlocfilehash: e641908666c081ca66a76999c63a29e73befe6aa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030991"
---
# <a name="what-is-azure-automation"></a>¿Qué es Azure Automation?

Automation es necesario en tres áreas generales de operaciones en la nube:

* Implementación y administración: proporcione una infraestructura repetible y coherente en forma de código.
* Respuesta: cree una automatización basada en eventos para diagnosticar y resolver problemas.
* Organización: organice e integre la automatización con otros servicios y productos de Azure o de terceros.

Azure Automation ofrece un servicio basado en la nube de automatización, actualización de sistemas operativos y configuración, que posibilita una administración coherente en sus entornos, sean o no de Azure. Incluye automatización de procesos, administración de configuración, administración de actualizaciones, funcionalidades compartidas y características heterogéneas.

:::image type="content" source="./media/overview/automation-overview.png" alt-text="Diagrama de funcionalidades de Automation" border="true":::

Hay varios servicios de Azure capaces de satisfacer los requisitos anteriores, donde cada servicio incluye un conjunto de funcionalidades y hace las veces de plataforma programable para crear soluciones en la nube. Por ejemplo, Azure Bicep y Resource Manager proporcionan un lenguaje para desarrollar plantillas de implementación repetibles y coherentes dirigidas a recursos de Azure. Azure Automation puede procesar dichas plantillas con el fin de implementar un recurso de Azure y, a continuación, procesar un conjunto de tareas de configuración posteriores a la implementación.

Automation proporciona control total durante la implementación, las operaciones y la retirada de las cargas de trabajo y los recursos empresariales.

## <a name="process-automation"></a>Automatización de procesos

La automatización de procesos de Azure Automation permite automatizar tareas de administración frecuentes, laboriosas y propensas a errores. Este servicio le ayuda a centrarse en el trabajo que agrega valor empresarial. Al reducir los errores y aumentar la eficacia, también contribuye en la reducción de los costos operativos. El entorno operativo de automatización de procesos se describe con detalle en [Ejecución de un runbook en Azure Automation](automation-runbook-execution.md).

Con la automatización de procesos puede integrar servicios de Azure y otros sistemas de terceros que sean necesarios en la implementación, configuración y administración de los procesos de un extremo a otro. El servicio le permite [crear runbooks](automation-runbook-types.md) gráficos o de PowerShell o Python. Para ejecutar runbooks directamente en la máquina Windows o Linux o con relación a recursos del entorno local u otro entorno en la nube, con el fin de administrar dichos recursos locales, puede implementar una [instancia de Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) en la máquina.

Los [webhooks](automation-webhooks.md) le permiten responder a solicitudes y garantizar una entrega y operaciones continuas. Para ello, desencadenan la automatización desde Azure Logic Apps, Azure Function, un producto o servicio de ITSM, DevOps y sistemas de supervisión.

## <a name="configuration-management"></a>Administración de la configuración

La administración de configuración de Azure Automation admite dos funcionalidades:

* Change Tracking e Inventario
* State Configuration de Azure Automation

### <a name="change-tracking-and-inventory"></a>Change Tracking e Inventario

Seguimiento de cambios e inventario combina varias funciones que permiten realizar un seguimiento de los cambios en la infraestructura de servidores y máquinas virtuales Linux y Windows. El servicio admite el seguimiento de cambios en el registro, servicios, demonios, software y archivos del entorno para ayudarle a diagnosticar cambios no deseados y generar alertas. La compatibilidad con inventario le permite consultar recursos de los invitados para obtener una visualización de las aplicaciones instaladas y otros elementos de configuración. Para más información sobre esta característica, consulte [Change Tracking e Inventario](change-tracking/overview.md).

### <a name="azure-automation-state-configuration"></a>State Configuration de Azure Automation

[State Configuration de Azure Automation](automation-dsc-overview.md) es una característica basada en la nube de Desired State Configuration (DSC) de PowerShell que proporciona servicios para entornos empresariales. Con esta característica, puede administrar los recursos de DSC en Azure Automation y aplicar las configuraciones a las máquinas virtuales o físicas desde un servidor de extracción DSC en la nube de Azure.

## <a name="update-management"></a>Administración de actualizaciones

Azure Automation incluye la característica [Update Management](./update-management/overview.md) para sistemas Windows y Linux en entornos híbridos. Update Management le proporciona visibilidad sobre el cumplimiento de las actualizaciones en Azure y otras nubes, así como en el entorno local. Esta característica le permite crear implementaciones programadas para organizar la instalación de actualizaciones en una ventana de mantenimiento definida. Si no es necesario instalar una actualización en una máquina, puede usar la funcionalidad Update Management para excluirla de una implementación.

## <a name="shared-capabilities"></a>Funcionalidades compartidas

Azure Automation ofrece una serie de funcionalidades compartidas, como recursos compartidos, control de acceso basado en roles, programación flexible, integración de control de código fuente, auditoría y etiquetado.

### <a name="shared-resources"></a><a name="shared-resources"></a>Recursos compartidos

Azure Automation consta de un conjunto de recursos compartidos que le facilitan la automatización y configuración de los entornos a escala.

* **[Programaciones](./shared-resources/schedules.md)** : desencadene operaciones de automatización a horas predefinidas.
* **[Módulos](./shared-resources/modules.md)** : administre Azure y otros sistemas. Puede importar módulos a la cuenta de Automation para Microsoft, recursos de DSC y cmdlets definidos personalizados, de la comunidad o de terceros.
* **[Galería de módulos](automation-runbook-gallery.md)** : admite la integración nativa con la Galería de PowerShell para ver runbooks e importarlos a la cuenta de Automation. La galería le permite integrar y crear rápidamente los procesos desde la Galería de PowerShell y el Centro de scripts de Microsoft.
* **[Paquetes de Python 2 y 3:](python-packages.md)** admita runbooks de Python 2 y 3 para su cuenta de Automation.
* **[Credenciales](./shared-resources/credentials.md)** : almacene de forma segura la información confidencial que los runbooks y las configuraciones pueden usar en tiempo de ejecución.
* **[Conexiones](automation-connections.md)** : almacenan pares nombre-valor de información común para las conexiones a sistemas. El autor del módulo define las conexiones en runbooks y configuraciones para usarlas en tiempo de ejecución.
* **[Certificados](./shared-resources/certificates.md)** : definen información que se usará en la autenticación y protección de recursos implementados cuando accedan a ellos runbooks o configuraciones de DSC en tiempo de ejecución.
* **[Variables](./shared-resources/variables.md)** : almacenan contenido que puede usarse en runbooks y configuraciones. Puede cambiar los valores de las variables sin tener que modificar los runbooks ni las configuraciones que hacen referencia a ellos.

### <a name="role-based-access-control"></a>Control de acceso basado en rol

Azure Automation admite el control de acceso basado en roles (RBAC) para regular el acceso a la cuenta de Automation y a sus recursos. Para más información sobre la configuración de Azure RBAC en su cuenta de Automation, runbooks y trabajos, consulte [Control de acceso basado en roles en Azure Automation](automation-role-based-access-control.md).

### <a name="source-control-integration"></a>Integración de control de código fuente

Azure Automation permite la [integración del control de código fuente](source-control-integration.md). Esta característica promueve la configuración como código donde los runbooks o las configuraciones pueden insertarse en un sistema de control de código fuente.

## <a name="heterogeneous-support-windows-and-linux"></a>Compatibilidad heterogénea (Windows y Linux)

Automation se ha diseñado para funcionar en servidores físicos y máquinas virtuales Windows y Linux fuera de Azure, en su red corporativa o en otro proveedor en la nube. Le ofrece una manera coherente de automatizar y configurar cargas de trabajo implementadas y los sistemas operativos en los que se ejecutan. Hybrid Runbook Worker de Azure Automation permite ejecutar runbooks directamente en el servidor físico o la máquina virtual que no son de Azure y en los que se hospeda el rol, así como con relación a los recursos del entorno para administrar dichos recursos locales.

A través de [servidores habilitados para Arc](../azure-arc/servers/overview.md), la característica proporciona una experiencia de implementación y administración coherente para las máquinas que no sean de Azure. Además, posibilita la integración con el servicio Automation mediante el marco de extensión de máquina virtual, para implementar el rol de Hybrid Runbook Worker y simplificar la incorporación en Update Management y Seguimiento de cambios e inventario.

## <a name="common-scenarios"></a>Escenarios frecuentes

Azure Automation permite la administración de la infraestructura y las aplicaciones a lo largo de su ciclo de vida. Entre los escenarios habituales se incluyen los siguientes:

* **Programar tareas:** detenga máquinas virtuales o servicios por la noche y active durante el día flujos de trabajo de mantenimiento periódicos de carácter semanal o mensual.
* **Escribir runbooks:** cree runbooks gráficos y de PowerShell, flujo de trabajo de PowerShell, Python 2 y 3 y DSC en lenguajes comunes.
* **Compilar e implementar recursos**: implemente máquinas virtuales en un entorno híbrido mediante runbooks y plantillas de Azure Resource Manager. Integre en las herramientas de desarrollo, como Jenkins y Azure DevOps.
* **Configurar máquinas virtuales**: evalúe y configure máquinas Windows y Linux con configuraciones para la infraestructura y la aplicación.
* **Compartir conocimientos**: transfiera conocimientos al sistema sobre cómo la organización entrega y mantiene las cargas de trabajo.
* **Recuperar inventario**: obtenga un inventario completo de los recursos implementados con fines de compatibilidad, creación de informes y cumplimiento.
* **Buscar cambios:** identifique y aísle cambios que puedan provocar una configuración incorrecta y mejorar el cumplimiento operativo. Corríjalos o escálelos a sistemas de administración.
* **Mantenimiento periódico:** para ejecutar tareas que deben realizarse a intervalos de tiempo establecidos, como purgar datos obsoletos o antiguos o volver a indexar una base de datos SQL.
* **Responder a alertas:** organice una respuesta cuando se generen alertas de uso de recursos o basadas en costes, sistemas o servicios.
* **Automatización híbrida:** administre o automatice servidores y servicios locales como SQL Server, Active Directory, SharePoint Server, etc.
* **Administración del ciclo de vida de los recursos de Azure:** para los servicios IaaS y PaaS.
* **Escenarios de automatización de desarrollo y pruebas:** inicie y escale recursos, entre otras acciones.
* **Automatización relacionada con la gobernanza:** aplique o actualice automáticamente etiquetas, bloqueos, etc.
* **Azure Site Recovery:** organice scripts previos o posteriores que estén definidos en un flujo de trabajo de recuperación ante desastres de Site Recovery.
* **Windows Virtual Desktop:** organice el escalado de máquinas virtuales, o bien inícielas o deténgalas en función del uso.

Dependiendo de sus requisitos, uno o varios de los siguientes servicios de Azure se integran con Azure Automation o lo complementan para ayudarle a cumplirlos:

* Los [servidores habilitados para Azure Arc](../azure-arc/servers/overview.md) simplifican la incorporación de máquinas híbridas en Update Management, Seguimiento de cambios e inventario y el rol de Hybrid Runbook Worker.
* Los [grupos de acciones de Alertas de Azure](../azure-monitor/alerts/action-groups.md) pueden iniciar un runbook de Automation cuando se genera una alerta.
* [Azure Monitor](../azure-monitor/overview.md) recopila métricas y datos de registro de su cuenta de Automation para realizar análisis adicionales y tomar medidas con relación a la telemetría. Algunas características de Automation, como Update Management y Seguimiento de cambios e inventario, dependen del área de trabajo de Log Analytics para ofrecer elementos de su funcionalidad.
* [Azure Policy](../governance/policy/samples/built-in-policies.md) incluye definiciones de iniciativa para ayudar a establecer y mantener el cumplimiento con diferentes estándares de seguridad en relación con su cuenta de Automation.
* [Azure Site Recovery](../site-recovery/site-recovery-runbook-automation.md) puede usar runbooks de Azure Automation para automatizar planes de recuperación.

Estos servicios de Azure pueden funcionar con recursos de runbook y trabajo de Automation mediante un webhook HTTP o un método de API:

* [Azure Logic Apps](/connectors/built-in.md)
* [Azure Power Apps](/connectors/azureautomation.md)
* [Azure Event Grid](../event-grid/handler-webhooks.md)
* [Azure Power Automate](/connectors/azureautomation.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-azure-automation"></a>Precios de Azure Automation

Puede revisar el precio asociados a Azure Automation en la página de [precios](https://azure.microsoft.com/pricing/details/automation/).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Creación de una cuenta de Automation](./quickstarts/create-account-portal.md)