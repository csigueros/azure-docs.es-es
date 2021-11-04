---
title: Implementación de aplicaciones seguras en Microsoft Azure
description: En este artículo se describen los procedimientos recomendados que hay que tener en cuenta durante las fases de versión y de respuesta del proyecto de aplicación web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: f8094fc0eaf646a026371db3726706f92c763f6d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460553"
---
# <a name="deploy-secure-applications-on-azure"></a>Implementación de aplicaciones seguras en Azure
En esta serie de artículos se presentan las actividades y controles de seguridad que hay que tener en cuenta al implementar aplicaciones para la nube. Se tratan los conceptos y preguntas de seguridad que se deben tener en cuenta durante las fases de versión y de respuesta del [Ciclo de vida de desarrollo de seguridad (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) de Microsoft. El objetivo es ayudarle a definir actividades y servicios de Azure que pueden usarse para implementar una aplicación más segura.

En este artículo se tratan las siguientes fases del SDL:

- Release
- Response

## <a name="release"></a>Release
El enfoque de la fase de lanzamiento es preparar un proyecto para el lanzamiento público. Esto incluye la planeación de formas de realizar eficazmente las tareas de servicio posteriores a la versión y de abordar las vulnerabilidades de seguridad que puedan producirse más adelante.

### <a name="check-your-applications-performance-before-you-launch"></a>Comprobación del rendimiento de la aplicación antes de iniciar

Compruebe el rendimiento de su aplicación antes de iniciarla o de implementar actualizaciones en la producción. Ejecute las [pruebas de carga](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) basadas en la nube mediante Visual Studio para encontrar problemas de rendimiento en la aplicación, mejorar la calidad de la implementación, asegurarse de que la aplicación esté siempre activa o disponible y de que la aplicación pueda tratar el tráfico para el lanzamiento.

### <a name="install-a-web-application-firewall"></a>Instalar un firewall de aplicaciones web

Las aplicaciones web son cada vez más los objetivos de ataques malintencionados que aprovechan vulnerabilidades comunes conocidas, como Entre estas vulnerabilidades, son comunes los ataques por inyección de código SQL o ataques de scripts de sitios. Evitar estos ataques en el código de la aplicación puede ser todo un desafío. Puede requerir un mantenimiento riguroso, revisión y supervisión en varias capas de la topología de la aplicación. Un WAF centralizado ayuda a simplificar la administración de seguridad. Las soluciones de WAF también pueden reaccionar ante una amenaza de la seguridad aplicando revisiones que aborden una vulnerabilidad conocida en una ubicación central en lugar de proteger cada una de las aplicaciones web por separado.

La característica de [Azure Application Gateway WAF](../../web-application-firewall/ag/ag-overview.md) que ofrece una protección centralizada de las aplicaciones web contra las vulnerabilidades de seguridad más habituales. La solución WAF se basa en las reglas contenidas en [OWASP Core Rule Set](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 o 2.2.9.

### <a name="create-an-incident-response-plan"></a>Creación de un plan de respuesta ante incidentes

La preparación de un plan de respuesta a incidentes es fundamental para ayudarle a hacer frente a las nuevas amenazas que puedan surgir con el tiempo. La preparación de un plan de respuesta a incidentes incluye la identificación de contactos de emergencia de seguridad apropiados y el establecimiento de planes de servicio de seguridad para el código heredado de otros grupos de la organización y para el código de terceros con licencia.

### <a name="conduct-a-final-security-review"></a>Realización de una revisión de seguridad final

La revisión deliberada de todas las actividades de seguridad que se llevaron a cabo ayuda a garantizar la preparación para la versión de software o la aplicación. La revisión de seguridad final (FSR) suele incluir el examen de los modelos de amenazas, las salidas de las herramientas y el rendimiento frente a las puertas de calidad y las barras de error que se han definido en la fase de requisitos.

### <a name="certify-release-and-archive"></a>Certificación de la versión y del archivo

La certificación de software antes de una versión ayuda a garantizar que se cumplen los requisitos de seguridad y privacidad. El archivo de todos los datos pertinentes es esencial para realizar las tareas de mantenimiento posteriores a la versión. El archivado también ayuda a reducir los costos a largo plazo asociados con la ingeniería de software sostenida.

## <a name="response"></a>Response

La fase de respuesta posterior al lanzamiento se centra en que el equipo de desarrollo sea capaz y esté disponible para responder adecuadamente a cualquier informe de amenazas y vulnerabilidades de software emergentes.

### <a name="execute-the-incident-response-plan"></a>Ejecución del plan de respuesta ante incidentes

Ser capaz de implementar el plan de respuesta a incidentes instituido en la fase de lanzamiento es esencial para ayudar a proteger a los clientes de la seguridad del software o de las vulnerabilidades de privacidad que surjan.

### <a name="monitor-application-performance"></a>Supervisión del rendimiento de las aplicaciones

La supervisión continua de la aplicación una vez implementada puede ayudarle a detectar problemas de rendimiento y vulnerabilidades de seguridad.

Los servicios de Azure que ayudan con la supervisión de aplicaciones son los siguientes:

  - Azure Application Insights
  - Azure Security Center

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) es un servicio de Application Performance Management (APM) extensible para desarrolladores web en varias plataformas. Úselo para supervisar la aplicación web en directo. Application Insights detecta automáticamente anomalías en el rendimiento. Incluye herramientas de análisis eficaces que le ayudan a diagnosticar problemas y comprender lo que hacen realmente los usuarios con la aplicación. Está diseñado para ayudarle a mejorar continuamente el rendimiento y la facilidad de uso.

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-introduction.md) ayuda a evitar, detectar y responder a amenazas con más visibilidad y control sobre la seguridad de sus recursos de Azure, incluidas las aplicaciones web. Azure Security Center ayuda a detectar amenazas que de otro modo podrían pasar desapercibidas. Funciona con varias soluciones de seguridad.

El nivel Gratis de Security Center ofrece seguridad limitada solo para los recursos de Azure. El [nivel Estándar de Security Center](../../security-center/security-center-get-started.md) amplía estas funcionalidades a los recursos locales y otras nubes.
El nivel Estándar de Security Center le ayuda a:

  - Buscar y corregir vulnerabilidades de seguridad
  - Aplicar controles de acceso y de aplicación para bloquear actividad malintencionada
  - Detectar amenazas mediante análisis e inteligencia
  - Responder rápidamente cuando esté siendo atacado

## <a name="next-steps"></a>Pasos siguientes
En los siguientes artículos, se recomiendan controles de seguridad y actividades que pueden ayudarle a diseñar y desarrollar aplicaciones seguras.

- [Diseño de aplicaciones seguras](secure-design.md)
- [Desarrollo de aplicaciones seguras](secure-develop.md)
