---
title: Más información sobre las herramientas y utilidades de código abierto de Azure Purview
description: En este tutorial se enumeran las diversas herramientas y utilidades disponibles en Azure Purview y se describe su uso.
author: abandyop
ms.author: arindamba
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 10/10/2021
ms.openlocfilehash: 8c87adc48a9a717a6ca9796b6d3bb2ec780220f7
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007643"
---
# <a name="azure-purview-open-source-tools-and-utilities"></a>Herramientas y utilidades de código abierto de Azure Purview

En este artículo se enumeran varias herramientas y utilidades de código abierto (línea de comandos, Python e interfaces de PowerShell) que le ayudarán a empezar a trabajar rápidamente con el servicio Azure Purview en cuestión de minutos. Estas herramientas se han creado y desarrollado gracias al esfuerzo colectivo del grupo de productos de Azure Purview y la comunidad de código abierto. El objetivo de estas herramientas es hacer que el aprendizaje, la puesta en marcha, el uso regular y la adopción a largo plazo de Purview sean fáciles y muy rápidos.

### <a name="intended-audience"></a>Destinatarios

- La comunidad de Azure Purview, que incluye clientes, desarrolladores, ISV, asociados, evangelistas y entusiastas. 

- El catálogo de Azure Purview se basa en [Apache Atlas](https://atlas.apache.org/) y amplía la compatibilidad total con las API de Apache Atlas. Damos nuestra más sincera bienvenida a la comunidad de Apache Atlas, a los entusiastas y a los desarrolladores para que trabajen sobre Azure Purview y lo den a conocer.

### <a name="purview-customer-journey-stages"></a>Fases del recorrido del cliente de Purview

- *Aprendices de Purview*: personas que se inician en el servicio Azure Purview y están interesados en comprender y explorar cómo funciona una solución de gobernanza de datos unificada de varias nubes. Una sección de los aprendices incluye a los usuarios que quieren comparar y contrastar Purview con otras soluciones de la competencia del mercado de gobernanza de los datos y probarlo antes de adoptarlo para su uso a largo plazo.

- *Innovadores de Purview*: personas con ganas de entender las funciones existentes y las más recientes, e idear y conceptualizar las próximas funciones de Purview. Son expertos en la construcción y el desarrollo de soluciones para los clientes, y tienen ideas futuristas para el producto de gobernanza de datos de última generación.

- *Entusiastas/evangelistas de Purview*: personas que son una combinación de aprendices e innovadores. Han desarrollado conocimientos sólidos de Purview, por lo que se sienten optimistas sobre su adopción. Pueden ayudar a dar a conocer Purview como servicio y educar a otros usuarios y probables clientes de Purview en todo el mundo.

- *Adoptantes de Purview*: personas que han pasado de iniciar y explorar Purview a utilizarlo sin problemas durante más de unos meses.

- *Usuarios habituales a largo plazo de Purview*: personas que han estado utilizando Purview durante más de un año y ahora están seguros y cómodos con la mayoría de los casos de uso avanzados de Purview en Azure Portal y Purview Studio; además tienen un conocimiento casi perfecto de las API REST de Purview y los casos de uso adicionales admitidos a través de las API de Purview.


## <a name="azure-purview-open-source-tools-and-utilities-list"></a>Lista de herramientas y utilidades de código abierto de Azure Purview

1. [Purview-API-via-PowerShell](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/README.md) 

    - **Fases recomendadas del recorrido del cliente**: *aprendices, innovadores, entusiastas, adoptantes, usuarios habituales a largo plazo*
    - **Descripción**: esta utilidad se basa en el conjunto entero de documentos de Microsoft de [referencia de la API REST de Azure Purview](/rest/api/purview/). [Puede descargarla e instalarla desde la Galería de PowerShell](https://aka.ms/purview-api-ps). Le ayuda a ejecutar todas las API REST de Purview documentadas mediante una interfaz de PowerShell muy rápida y fácil de usar. Use y automatice las API de Purview para el uso normal y a largo plazo a través de los métodos de script y de línea de comandos. Se trata de una alternativa para los clientes que buscan realizar tareas masivas de forma automatizada, en modo lote o trabajos cron programados, frente al método GUI de utilizar Azure Portal y Purview Studio. Puede encontrar documentación detallada, una guía de uso de muestra, autoayuda y ejemplos en [GitHub:Azure-Purview-API-PowerShell](https://github.com/Azure/Azure-Purview-API-PowerShell).

1. [Purview-Starter-Kit](https://aka.ms/PurviewKickstart)

    - **Fases recomendadas del recorrido del cliente**: *aprendices, innovadores, entusiastas*
    - **Descripción**: script de PowerShell para realizar la configuración inicial de la cuenta de Purview. Resulta útil para todo aquel que quiera configurar varias cuentas nuevas de Purview en menos de 5 minutos.

1. [Laboratorio de Purview](https://aka.ms/purviewlab)

    - **Fases recomendadas del recorrido del cliente**: *aprendices, innovadores, entusiastas*
    - **Descripción**: un laboratorio práctico que introduce las innumerables características de Purview y le ayuda a aprender los conceptos en un enfoque práctico y manual en el que ejecuta cada paso por sí mismo a mano para desarrollar la mejor comprensión posible de Purview.

1. [CLI de Purview](https://aka.ms/purviewcli)

    - **Fases recomendadas del recorrido del cliente**: *innovadores, entusiastas, adoptantes, usuarios habituales a largo plazo*
    - **Descripción**: herramienta basada en Python para ejecutar las API de Purview. Se parece a [Purview-API-via-PowerShell](https://aka.ms/purview-api-ps), pero tiene una funcionalidad limitada o menor que el marco basado en PowerShell.

1. [Demostración de Purview](https://aka.ms/pvdemo)

    - **Fases recomendadas del recorrido del cliente**: *aprendices, innovadores, entusiastas*
    - **Descripción**: una herramienta basada en plantillas de Azure Resource Manager (ARM) para configurar e implementar automáticamente una nueva cuenta de Azure Purview de forma rápida y segura con tan solo emitir un comando. Se parece a [Purview-Starter-Kit](https://aka.ms/PurviewKickstart), con la característica adicional de que implementa algunos orígenes de datos más previamente configurados: Azure SQL Database, cuenta de Azure Data Lake Storage Gen2, Azure Data Factory, área de trabajo de Azure Synapse Analytics.

1. [PyApacheAtlas: interfaz entre Azure Purview y Apache Atlas](https://github.com/wjohnson/pyapacheatlas) que usa API de Atlas

    - **Fases recomendadas del recorrido del cliente**: *innovadores, entusiastas, adoptantes, usuarios habituales a largo plazo*
    - **Descripción**: un paquete de Python para trabajar con Azure Purview y la API de Apache Atlas. Admite la carga masiva y el linaje personalizado (entre otras funciones) a partir de un conjunto de Python de clases y plantillas de Excel. El paquete admite la interacción mediante programación y una plantilla de Excel para cargas de poco código.

1. [Purview EventHub Notifications Reader](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/purview_atlas_eventhub_sample.py)

    - **Fases recomendadas del recorrido del cliente**: *innovadores, entusiastas, adoptantes, usuarios habituales a largo plazo*
    - **Descripción**: esta herramienta muestra cómo leer EventHub de Purview y detectar notificaciones de Kafka en tiempo real desde EventHub en formato de notificaciones de Atlas (https://atlas.apache.org/2.0.0/Notifications.html). Además, genera una hoja CSV de Excel de las entidades y recursos sobre la marcha que se detectan en vivo durante un examen, y cualquier otra notificación de interés que Purview genere.


## <a name="feedback-and-disclaimer"></a>Comentarios y declinación de responsabilidades

Ninguna de las herramientas viene con una garantía expresa de Microsoft que verifique su eficacia o garantice su funcionalidad. Se garantiza que están libres de cualquier actividad maliciosa o virus, y que no recopilan datos privados o confidenciales.

Si tiene alguna pregunta o duda sobre la eficacia y la funcionalidad durante el uso, póngase en contacto con los propietarios y autores de las herramientas correspondientes en los detalles de contacto mencionados en el repositorio de GitHub correspondiente.


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"] 
> [Purview-API-PowerShell](https://aka.ms/purview-api-ps) 
