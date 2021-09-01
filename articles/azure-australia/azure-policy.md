---
title: Cumplimiento de seguridad con Azure Policy y Azure Blueprints
description: Garantizar el cumplimiento y la seguridad con Azure Policy y Azure Blueprints para las agencias gubernamentales australianas en lo que se refiere a los estándares ASD ISM y Essential 8
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: 79ee7b4a0d21322e8c2e497eecd1704356282eba
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "117029242"
---
# <a name="security-compliance-with-azure-policy-and-azure-blueprints"></a>Cumplimiento de seguridad con Azure Policy y Azure Blueprints

El reto de aplicar la gobernanza dentro de su entorno de TI, ya sea un entorno local, nativo de la nube o híbrido, existe en todas las organizaciones. Es necesario disponer de un marco de gobernanza técnica sólido para asegurarse de que el entorno de Microsoft Azure se ajusta a los requisitos de diseño, normativa y seguridad.

En el caso de las agencias gubernamentales australianas, los principales controles que hay que tener en cuenta al evaluar el riesgo se detallan en el [manual de seguridad de la información (ISM) del Centro Australiano de Seguridad Cibernética (ACSC)](https://acsc.gov.au/infosec/ism/index.htm). La mayoría de los controles que se detallan en el manual requieren la aplicación de la gobernanza técnica para que se pueda administrar y aplicar de forma eficaz. Es importante que tenga las herramientas adecuadas para evaluar y aplicar la configuración en sus entornos.

Microsoft Azure proporciona dos servicios complementarios para ayudar con estos desafíos, Azure Policy y Azure Blueprints.

## <a name="azure-policy"></a>Azure Policy

Azure Policy habilita la aplicación de los elementos técnicos de la gobernanza de TI de una organización. Azure Policy contiene una biblioteca en constante crecimiento de directivas integradas. Cada directiva aplica reglas y efectos en los recursos de Azure de destino.

Una vez que se asigna una directiva a los recursos, se puede evaluar el cumplimiento general de la directiva y corregirse si es necesario.

Esta biblioteca de directivas integradas de Azure permite a una organización aplicar rápidamente los tipos de controles que se encuentran en el manual del ACSC. Entre los ejemplos de controles se incluyen:

* Supervisión de las máquinas virtuales para detectar las actualizaciones del sistema que faltan
* Auditoría de las cuentas con permisos elevados para la autenticación multifactor
* Identificación de las bases de datos SQL sin cifrar
* Supervisión del uso del control de acceso basado en rol (Azure RBAC) de Azure personalizado
* Restricción de las regiones de Azure en las que se pueden crear recursos

Si una definición de Azure Policy integrada no cumple los controles normativos o de gobernanza, se puede crear y asignar una definición personalizada. Todas las definiciones de Azure Policy se definen en JSON y siguen una [estructura de definición](../governance/policy/concepts/definition-structure.md) estándar. Las definiciones de Azure Policy existentes también se pueden duplicar y usar para formar la base de una definición de directiva personalizada.

La asignación de directivas individuales de Azure a los recursos, especialmente en entornos complejos o en entornos con estrictos requisitos normativos, puede suponer una gran sobrecarga para los administradores. Para ayudar a solucionar estos desafíos, se puede agrupar un conjunto de directivas de Azure para formar una iniciativa de Azure Policy. Las iniciativas de Azure Policy se usan para combinar directivas de Azure relacionadas que, cuando se aplican juntas como un grupo, forman la base de un objetivo de seguridad o cumplimiento específico. Microsoft está agregando definiciones de iniciativa de Azure Policy integradas, incluidas las definiciones diseñadas para cumplir los requisitos normativos específicos:

![Iniciativas de Azure Policy para el cumplimiento normativo](media/regulatory-initiatives.png)

Todas las iniciativas de Azure Policy se asignan a un ámbito de asignación. Este ámbito se define en los niveles de suscripción de Azure, grupo de administración de Azure o grupo de recursos de Azure. Una vez que se han asignado las iniciativas de directiva o directivas de Azure, una organización podrá aplicar los requisitos de configuración en todos los recursos de Azure recién creados.

La asignación de una nueva iniciativa o directiva de Azure no afectará a los recursos existentes. Sin embargo, Azure Policy puede permitir que una organización vea el cumplimiento de los recursos existentes de Azure. Los recursos que se hayan identificado como no conformes se pueden corregir a discreción de la organización.

### <a name="azure-policy-and-initiatives-in-action"></a>Iniciativas y directivas de Azure en acción

Las definiciones de iniciativas y directivas de Azure integradas se pueden encontrar en el nodo Definiciones de la sección Directiva de Azure Portal:

![Definiciones de Azure Policy integradas](media/policy-definitions.png)

Con la biblioteca de definiciones integradas, puede buscar rápidamente las directivas que cumplan un requisito organizativo, revisar la definición de la directiva y asignar la directiva a los recursos adecuados. Por ejemplo, el ISM requiere Multi-Factor Authentication (MFA) para todos los usuarios con privilegios y para todos los usuarios con acceso a repositorios de datos importantes. En Azure Policy, puede buscar "MFA" entre las definiciones de Azure Policy:

![Directivas de Azure AD MFA](media/mfa-policies.png)

Una vez identificada una directiva adecuada, asígnela al ámbito deseado. Si no hay ninguna directiva integrada que cumpla sus requisitos, puede duplicar la directiva existente y realizar los cambios deseados:

![Duplicación de la instancia existente de Azure Policy](media/duplicate-policy.png)

Microsoft también proporciona una colección de ejemplos de Azure Policy en [GitHub](https://github.com/Azure/azure-policy) como una "Guía de inicio rápido" para crear directivas personalizadas de Azure Policy. Estos ejemplos de Policy se pueden copiar directamente en el editor de Azure Policy en Azure Portal.

Al crear iniciativas de Azure Policy, puede ordenar la lista de definiciones de directivas disponibles, tanto integradas como personalizadas, con la adición de las definiciones necesarias.

Por ejemplo, puede buscar en la lista de definiciones de Azure Policy disponibles todas las directivas relacionadas con las máquinas virtuales Windows. Después, estas definiciones se aplican a una iniciativa diseñada para aplicar las prácticas recomendadas de protección de las máquinas virtuales:

![Lista de directivas de Azure](media/initiative-definitions.png)

A la vez que asigna una iniciativa de directiva o una directiva de Azure Policy a un ámbito de asignación, es posible excluir recursos de Azure de los efectos de las directivas mediante la exclusión de los grupos de recursos o grupos de administración de Azure.

### <a name="real-time-enforcement-and-compliance-assessment"></a>Evaluación de cumplimiento y aplicación en tiempo real

Los análisis de cumplimiento de Azure Policy de los recursos de Azure incluidos en el ámbito se realizan cuando se cumplen las siguientes condiciones:

* Cuando se asigna una iniciativa o directiva de Azure Policy
* Cuando se cambia el ámbito de una iniciativa o directiva de Azure Policy existente
* A petición mediante la API, hasta un máximo de 10 exámenes por hora
* Una vez cada 24 horas (comportamiento predeterminado)

El examen de cumplimiento de una directiva en un único recurso de Azure se lleva a cabo 15 minutos después de que se haya realizado un cambio en el recurso.

Se puede consultar una introducción del cumplimiento de Azure Policy de los recursos en Azure Portal, en el panel Cumplimiento de directiva:

![Puntuación de cumplimiento de Azure Policy](media/simple-compliance.png)

La cifra total del porcentaje de cumplimiento de los recursos es un agregado del cumplimiento de todos los recursos implementados en el ámbito con todas las directivas de Azure Policy asignadas. Esto le permite identificar los recursos de un entorno que no son conformes y diseñar el plan para corregirlos mejor.

El panel Cumplimiento de directiva también incluye el historial de cambios de cada recurso. Si un recurso se identifica como que ya no cumple con la directiva asignada y la corrección automática no está habilitada, puede ver quién realizó el cambio, qué ha cambiado y cuándo se realizaron los cambios en ese recurso.

## <a name="azure-blueprints"></a>Azure Blueprint

Azure Blueprints amplía la funcionalidad de Azure Policy mediante su combinación con:

* Azure RBAC
* Grupos de recursos de Azure
* [Plantillas del Administrador de recursos de Azure](../azure-resource-manager/templates/syntax.md)

Blueprints permite crear diseños de entorno que implementan los recursos de Azure a partir de las plantillas de Resource Manager, configurar Azure RBAC y aplicar y auditar la configuración mediante la asignación de directivas de Azure Policy. Blueprints crea una plantilla de entorno que se puede modificar y volver a implementar. Una vez creado el plano técnico, se puede asignar a una suscripción de Azure. Una vez asignado, se crearán todos los recursos de Azure definidos en el plano técnico y se aplicarán las directivas de Azure Policy. La implementación y configuración de los recursos definidos en un plano técnico de Azure Blueprints se pueden supervisar desde la consola de Azure Blueprints en Azure Portal.

Los planos técnicos de Azure Blueprints que se han editado deben volver a publicarse en Azure Portal. Cada vez que se vuelve a publicar un plano técnico, se incrementa el número de versión del plano técnico. El número de versión le permite determinar qué versión específica de un plano técnico se ha implementado en las suscripciones de Azure de una organización. Si lo desea, la versión asignada actualmente del plano técnico se puede actualizar a la última versión.

Los recursos implementados con una instancia de Azure Blueprints pueden configurarse con los [bloqueos de los recursos de Azure](../azure-resource-manager/management/lock-resources.md) en el momento de la implementación. Los bloqueos de los recursos impiden que los recursos se modifiquen o eliminen de forma accidental.

Microsoft está desarrollando plantillas de Azure Blueprints para una variedad de sectores y requisitos normativos. La biblioteca actual de definiciones de Azure Blueprints disponibles se puede ver en Azure Portal o en la página [Plano técnico de seguridad y cumplimiento de Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview/) del Portal de confianza de servicios.

### <a name="azure-blueprint-artifacts"></a>Artefactos de Azure Blueprints

Para crear un plano técnico de Azure Blueprints, puede empezar con una plantilla de plano técnico en blanco o usar uno de los planos técnicos de ejemplo existentes como punto de partida. Puede agregar artefactos al plano técnico que se configurarán como parte de la implementación:

![Artefactos de Azure Blueprints](media/blueprint-artifacts.png)

Estos artefactos podrían incluir los recursos y el grupo de recursos de Azure y las iniciativas de directiva y las directivas de Azure Policy para aplicar la configuración necesaria a fin de que su entorno cumpla los requisitos normativos, como por ejemplo los controles del ISM para la protección del sistema.

Cada uno de estos artefactos también puede configurarse con parámetros. Estos valores se proporcionan cuando el plano técnico se ha asignado a una suscripción de Azure y se ha implementado. Los parámetros permiten crear y usar un solo plano técnico para implementar recursos en entornos diferentes sin tener que editar el plano técnico subyacente.

Microsoft está desarrollando cmdlets de Azure PowerShell y la CLI para crear y administrar Azure Blueprints con la intención de que una organización pueda mantener e implementar un plano técnico a través de una canalización de CI/CD.

## <a name="next-steps"></a>Pasos siguientes

En este artículo se explica cómo se puede aplicar la gobernanza y la seguridad con Azure Policy y Azure Blueprints. Ahora que se ha expuesto a un nivel alto, aprenda a usar cada servicio con más detalle:

* [Introducción a Azure Policy](../governance/policy/overview.md)
* [Introducción a Azure Blueprints](https://azure.microsoft.com/services/blueprints/)
* [Ejemplos de Azure Policy](../governance/policy/samples/index.md)
* [Repositorio de ejemplos de Azure Policy](https://github.com/Azure/azure-policy)
* [Estructura de definición de Azure Policy](../governance/policy/concepts/definition-structure.md)
* [Comprender los efectos de Azure Policy](../governance/policy/concepts/effects.md)