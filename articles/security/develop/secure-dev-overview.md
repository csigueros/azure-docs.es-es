---
title: Procedimientos recomendados para un desarrollo seguro en Microsoft Azure
description: Procedimientos recomendados que le ayudarán a desarrollar un código más seguro e implementar una aplicación más segura en la nube.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 3b0ebb1989e350962317adce64c0542b4a296924
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000771"
---
# <a name="secure-development-best-practices-on-azure"></a>Procedimientos recomendados para un desarrollo seguro en Azure
En esta serie de artículos se presentan las actividades y controles de seguridad que hay que tener en cuenta al desarrollar aplicaciones para la nube. Se abarcan las fases del Ciclo de vida de desarrollo de seguridad (SDL) de Microsoft así como conceptos y preguntas de seguridad que se han de tener en cuenta durante cada fase del ciclo de vida. El objetivo es ayudarle a definir actividades y servicios de Azure que pueden usarse en cada fase del ciclo de vida para diseñar, desarrollar e implementar una aplicación más segura.

Las recomendaciones de este artículo proceden de nuestra experiencia con la seguridad de Azure y de las experiencias de nuestros clientes. Puede usar estos artículos como referencia sobre lo que ha de tener presente durante una fase específica del proyecto de desarrollo, pero también se recomienda leer todos los artículos de principio a fin por lo menos una vez. La lectura de todos los artículos le presentará conceptos que podría haber omitido en fases anteriores del proyecto. La implementación de estos conceptos antes de lanzar el producto puede ayudarle a compilar software seguro, abordar los requisitos de cumplimiento de seguridad y reducir los costos de desarrollo.

Estos artículos se proponen ser un recurso para diseñadores, desarrolladores y evaluadores de software de cualquier nivel que compilan e implementan soluciones seguras de Azure.

## <a name="overview"></a>Información general

La seguridad es uno de los aspectos más importantes de cualquier aplicación, y no es algo fácil de conseguir. Afortunadamente, Azure ofrece muchos servicios que pueden ayudarle a proteger su aplicación en la nube. En estos artículos se abordan actividades y servicios de Azure que pueden implementarse en cada etapa del ciclo de vida de desarrollo de software que ayudan a desarrollar un código más seguro e implementar una aplicación más segura en la nube.

## <a name="security-development-lifecycle"></a>Ciclo de vida de desarrollo de seguridad

El seguimiento de procedimientos recomendados para el desarrollo de software seguro requiere la integración de seguridad en cada fase del ciclo de vida de desarrollo de software, del análisis de requisitos al mantenimiento, independientemente de la metodología de proyecto ([cascada](https://en.wikipedia.org/wiki/Waterfall_model), [ágil](https://en.wikipedia.org/wiki/Agile_software_development) o [DevOps](https://en.wikipedia.org/wiki/DevOps)). Como resultado de infracciones de datos de alto perfil y el aprovechamiento de brechas de seguridad operativa, cada vez más desarrolladores comprenden que la seguridad se debe abordar a lo largo de todo el proceso de desarrollo.

Cuanto más tarde corrija un problema en su ciclo de vida de desarrollo, más le costará la corrección. Los problemas de seguridad no son una excepción. Si pasa por alto los problemas de seguridad en las primeras fases del desarrollo de software, es posible que cada una de las fases siguientes herede las vulnerabilidades de la fase anterior. El producto final habrá acumulado varios problemas de seguridad y una posible infracción de seguridad. La incorporación de seguridad en cada fase del ciclo de vida de desarrollo le ayuda a detectar problemas antes y a reducir los costos de desarrollo.

Seguimos las fases del [ciclo de vida de desarrollo de seguridad (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) de Microsoft para introducir actividades y servicios de Azure que pueden usarse para cumplir las prácticas de desarrollo de software seguro en cada fase del ciclo de vida.

Las fases del SDL son:

  - Cursos
  - Requisitos
  - Diseño
  - Implementación
  - Comprobación
  - Release
  - Response

![Ciclo de vida de desarrollo de seguridad](./media/secure-dev-overview/01-sdl-phase.png)

En estos artículos se agrupan las fases del SDL por diseño, desarrollo e implementación.

## <a name="engage-your-organizations-security-team"></a>Interacción con el equipo de seguridad de la organización

Es posible que la organización tenga un programa formal de seguridad de aplicaciones que le ayude con las actividades de seguridad de principio a fin durante el ciclo de vida de desarrollo. Si la organización tiene equipos de cumplimiento y seguridad, asegúrese de interactuar con ellos antes de empezar a desarrollar la aplicación. Pregúnteles en cada fase del SDL si ha omitido alguna tarea.

Entendemos que es posible que muchos de los lectores no tengan un equipo de seguridad o de cumplimiento con el que interactuar. Estos artículos pueden guiarle en las preguntas y decisiones de seguridad que debe tener en cuenta en cada fase del SDL.

## <a name="resources"></a>Recursos

Utilice los siguientes recursos para obtener más información sobre el desarrollo de aplicaciones seguras y como ayuda para proteger sus aplicaciones en Azure:

[Ciclo de vida de desarrollo de seguridad (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)): el SDL es un proceso de desarrollo de software de Microsoft que ayuda a los desarrolladores a compilar software más seguro. Le ayuda a abordar requisitos de cumplimiento de seguridad al tiempo que reduce los costos de desarrollo.

[Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page): OWASP es una comunidad en línea que produce artículos, metodologías, documentación, herramientas y tecnologías disponibles gratuitamente en el ámbito de seguridad de aplicaciones web.

[Pushing Left, Like a Boss](https://wehackpurple.com/pushing-left-like-a-boss-part-1/): serie de artículos en línea donde se describen los distintos tipos de actividades de seguridad de aplicaciones que los desarrolladores deben seguir para crear código más seguro.

[Plataforma de identidad de Microsoft](../../active-directory/develop/index.yml) esta plataforma es una evolución de la plataforma de desarrolladores y de servicio de identidad de Azure AD. Se trata de una plataforma completa que consiste en un servicio de autenticación, bibliotecas de código abierto, registro y configuración de aplicaciones, documentación completa para desarrolladores, ejemplos de código y otro contenido para desarrolladores. La plataforma de identidad de Microsoft admite protocolos estándar del sector tales como OAuth 2.0 y OpenID Connect.

[Security best practices for Azure solutions](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) (Procedimientos recomendados de seguridad para soluciones de Azure): colección de procedimientos recomendados de seguridad que se pueden usar al diseñar, implementar y administrar soluciones en la nube mediante Azure. Este documento se propone ser un recurso para profesionales de TI. Aquí podrían caber diseñadores, arquitectos, desarrolladores y evaluadores que compilen e implementen soluciones seguras de Azure.

[Planos técnicos de seguridad y cumplimiento en Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview): estos planos técnicos son recursos que pueden ayudarle a compilar y lanzar aplicaciones basadas en la nube que cumplan los estándares y reglamentaciones más exigentes.

## <a name="next-steps"></a>Pasos siguientes
En los siguientes artículos, se recomiendan controles de seguridad y actividades que pueden ayudarle a diseñar, desarrollar e implementar aplicaciones seguras.

- [Diseño de aplicaciones seguras](secure-design.md)
- [Desarrollo de aplicaciones seguras](secure-develop.md)
- [Implementación de aplicaciones seguras](secure-deploy.md)
