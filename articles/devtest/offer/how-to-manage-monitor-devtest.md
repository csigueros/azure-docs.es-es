---
title: Administración y supervisión de las suscripciones de Desarrollo/pruebas de Azure
description: Administre las suscripciones de Desarrollo/pruebas de Azure con la flexibilidad del entorno en la nube de Azure. Esta guía también cubre Azure Monitor para ayudar a maximizar la disponibilidad y el rendimiento de aplicaciones y servicios.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: 22fa6c747d7c15cebe1d36228965c3119fea25ea
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093209"
---
# <a name="managing-azure-devtest-subscriptions"></a>Administración de suscripciones de desarrollo/pruebas de Azure

Es importante administrar las suscripciones de Desarrollo/pruebas de Azure. La administración de costos, la supervisión de las fases de producción, los recursos que implemente y los procesos que configure es una prioridad máxima en la administración de suscripciones.  

El entorno en la nube de Azure ofrece flexibilidad para administrar los costos y las cargas de trabajo. La administración local le permite administrar la capacidad frente a los gastos.  

Los servicios dentro de los niveles de suscripción y grupo de recursos son de costo cero: solo los propios recursos tienen costo para ellos.  

![Diagrama de organizaciones y gobernanza de Azure](media/how-to-manage-monitor-devtest/orgs-and-governance.png "Organizaciones y gobernanza de Azure.")

Al administrar desde una perspectiva de gastos operativos, solo paga por lo que usa. Hay varias herramientas con suscripciones de desarrollo/pruebas de Azure que le ayudan a administrar los costos durante la implementación.  

## <a name="monitoring-through-a-different-lens"></a>Supervisión a través de una lente diferente

[Azure Monitor](../../azure-monitor/overview.md) ayuda a maximizar la disponibilidad y el rendimiento de las aplicaciones y servicios. Ofrezca soluciones completas para recopilar, analizar y actuar sobre los datos de los entornos locales y en la nube. Comprenda cómo funcionan las aplicaciones. Azure Monitor identifica de forma proactiva los problemas que afectan a las aplicaciones y a los recursos de los que dependen.  

En Azure, use la supervisión para acelerar el tiempo de comercialización y garantizar los datos de rendimiento en los servicios de producción. Puede agregar y analizar métricas, registros y seguimientos. A través de la supervisión, también puede activar alertas y enviar notificaciones o llamar a soluciones automatizadas.  

Azure Monitor permite usar las ventajas de desarrollo/pruebas para optimizar el tiempo de comercialización de las aplicaciones y entregar esas aplicaciones.  

La supervisión permite maximizar las ventajas de desarrollo/pruebas con nuevas aplicaciones netas y aplicaciones existentes.  

- Preproducción con aplicaciones Net New Green Field: con nuevas aplicaciones, se crean y se habilitan métricas personalizadas con análisis de registros o alertas inteligentes en preproducción que usará en producción. El uso de la supervisión temprana refina los datos de rendimiento de los servicios de producción  
- Postproducción con aplicaciones existentes: al implementar nuevas funciones o agregar nuevas características con una API a aplicaciones existentes, puede implementar esta característica en preproducción y ajustar su supervisión para garantizar la fuente de distribución de datos correcta desde el principio. El uso del seguimiento en la preproducción de la nueva característica le proporciona una visibilidad clara y le permite combinar esta supervisión con el sistema de supervisión general después de la producción. Integra los datos de rendimiento recientes con los registros existentes para asegurarse de que se usa la supervisión  
- La supervisión de diferentes fases de la implementación que no es de producción refleja la supervisión durante la producción. Los costos se administran y se analizan los gastos antes de producción y de postproducción.  

## <a name="cost-management"></a>Cost Management

[Azure Cost Management](../../cost-management-billing/cost-management-billing-overview.md) permite mejorar significativamente el rendimiento técnico de las cargas de trabajo empresariales. También puede reducir los costos y la sobrecarga necesarios para administrar los recursos de la organización.  

Mediante la supervisión, puede usar herramientas de análisis de costos en preproducción antes de implementar los servicios para ejecutar un análisis del proceso actual de preproducción a la previsión de los costos de producción y, potencialmente, ahorrar dinero.  

## <a name="performance-management"></a>Administración del rendimiento

Además de la supervisión y la administración de costos, también querrá probar el rendimiento del proceso de preproducción con las cargas de trabajo esperadas.  

En preproducción, puede configurar el escalado para expandirlo en función de la carga. Al probar la aplicación en un entorno de desarrollo/pruebas bajo carga, se obtienen mejores cifras de supervisión y análisis de costos. Tendrá una idea mejor de si necesita empezar a una escala mayor o inferior.  

El análisis de carga y coacción proporciona otros datos en preproducción para que pueda seguir optimizando el tiempo de comercialización y la entrega de la aplicación.  

A medida que realiza pruebas de carga y coacción con la aplicación o el servicio, el método para escalar vertical u horizontalmente depende de las cargas de trabajo. Puede obtener más información sobre el escalado de las aplicaciones en Azure:  

- [Escalado de una aplicación en Azure App Service](../../app-service/manage-scale-up.md)  
- [Introducción al escalado automático en Azure](../../azure-monitor/platform/autoscale-get-started.md?toc=/azure/app-service/toc.json)  

Habilite la supervisión de la aplicación con [Application Insights](../../azure-monitor/app/app-insights-overview.md) para recopilar información detallada, como vistas de página, solicitudes de aplicación y excepciones.  

## <a name="azure-automation"></a>Azure Automation

[Azure Automation](../../automation/automation-intro.md) ofrece un servicio de configuración y de automatización basado en la nube que facilita una administración coherente en los entornos que se encuentren dentro y fuera de Azure. Esta herramienta proporciona control durante la implementación, las operaciones y la retirada de cargas de trabajo y recursos. Azure Automation está siempre activa. Funciona con los recursos existentes. Azure Automation permite crear recursos o suscripciones a petición. Pague solo por lo que usa.  

Ejemplo: si está siguiendo una implementación de producción de desarrollo/pruebas, algunos de los recursos y fases deben estar en funcionamiento todo el tiempo. Otros solo necesitan actualizarse y ejecutarse varias veces al año.  

Azure Automation es importante en este escenario. Cuando vaya a una nueva fase de desarrollo de aplicaciones y envíe su primera solicitud de extracción (PR), puede iniciar un trabajo de automatización. El trabajo implementará la infraestructura como código a través de una plantilla de Azure Resource Manager (ARM) para crear todos los recursos en la suscripción de desarrollo/pruebas de Azure durante la preproducción.  

## <a name="azure-resource-manager"></a>Azure Resource Manager

[Las plantillas de Azure Resource Manager (ARM)](../../azure-resource-manager/templates/overview.md) implementan la infraestructura como código para las soluciones de Azure. La plantilla define la infraestructura y la configuración del proyecto. Puede automatizar las implementaciones.  

Puede implementar las configuraciones tantas veces como desee actualizar el entorno de preproducción y realizar un seguimiento de los costos. Con Azure Automation puede ejecutar y eliminar las plantillas de ARM según sea necesario.  

Cuando un servicio o recurso solo necesita actualizarse dos veces al año, use las herramientas de DevOps para implementar la plantilla de ARM. Deje que el trabajo de automatización desactive el recurso y, a continuación, vuelva a implementarlo según sea necesario.  
