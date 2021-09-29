---
title: 'Migración de directivas de acceso condicional: Azure Active Directory'
description: Aprenda todo lo necesario sobre cómo migrar las directivas clásicas en Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88244a527825a8cbaaa81dea64dd0c2f49a5fb72
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618179"
---
# <a name="conditional-access-classic-policy-migration"></a>Migración de directivas clásicas de acceso condicional

El acceso condicional es la herramienta que usa Azure Active Directory para reunir las señales, tomar decisiones y aplicar las directivas de la organización. El acceso condicional está en el centro del nuevo plano de control basado en identidades. Aunque el propósito sigue siendo el mismo, la nueva versión de Azure Portal presenta mejoras importantes acerca de cómo funciona Acceso condicional.

Considere la posibilidad de migrar las directivas que no haya creado en Azure Portal porque:

- Ahora puede resolver situaciones que antes no podía controlar.
- Puede reducir el número de directivas que tiene que administrar mediante su consolidación.
- Puede administrar todas las directivas de Acceso condicional en una ubicación central.
- Se retirará el Portal de Azure clásico.

En este artículo se explica lo que necesita saber para migrar las directivas de Acceso condicional existentes al marco nuevo.

## <a name="classic-policies"></a>Directivas clásicas

En [Azure Portal](https://portal.azure.com), las directivas de acceso condicional se pueden encontrar en **Azure Active Directory** > **Seguridad** > **Acceso condicional**. Es posible que su organización también tenga directivas de acceso condicional antiguas que no se hayan creado con esta página. Estas directivas se conocen como *directivas clásicas*. Las directivas de clásicas son directivas de Acceso condicional que creó con:

- El Portal de Azure clásico
- El Portal de Intune clásico
- El Portal de Intune App Protection

En la página **Acceso condicional**, puede acceder a las directivas clásicas si hace clic en [**Directivas clásicas**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) en la sección **Administrar**. 

![Acceso condicional en Azure AD con la vista de directivas clásicas](./media/policy-migration/71.png)

La vista **Directivas clásicas** le permite:

- Filtrar las directivas clásicas.
- Deshabilitar las directivas clásicas.
- Revisar la configuración de una directiva clásica y deshabilitarla.

   ![Detalles de una directiva clásica, incluida la configuración de directiva existente](./media/policy-migration/74.png)

> [!WARNING]
> Una vez deshabilitada una directiva clásica, no se puede volver a habilitar.

La vista de detalles de una directiva clásica permite documentar la configuración, modificar los grupos incluidos o excluidos y deshabilitar la directiva.

![Detalles de la directiva: grupos que se van a incluir o excluir](./media/policy-migration/75.png)

Si cambia los grupos seleccionados o excluye grupos específicos, puede probar el efecto de una directiva clásica deshabilitada para algunos usuarios de prueba antes de deshabilitar la directiva para todos los usuarios y grupos.
 
## <a name="migration-considerations"></a>Consideraciones sobre la migración

En este artículo, las directivas de Acceso condicional de Azure AD también se conocen como *directivas nuevas*.
Las directivas clásicas seguirán funcionando con las directivas nuevas hasta que las deshabilite o las elimine. 

Los aspectos siguientes son importantes en el contexto de una consolidación de directiva:

- Aunque las directivas clásicas están asociadas a una aplicación de nube específica, puede seleccionar tantas como necesite en una directiva nueva.
- Los controles de una directiva clásica y una directiva nueva para una aplicación de nube requieren que se cumplan todos ellos (*AND*). 
- En una directiva nueva puede:
   - Combinar varias condiciones, si la situación lo requiere. 
   - Seleccionar varios requisitos de concesión como control de acceso y combinarlos con un valor lógico *OR* (se requiere uno de los controles seleccionados) o con un valor lógico *AND* (se requieren todos los controles seleccionados).

### <a name="exchange-online"></a>Intercambio en línea

Si quiere migrar las directivas clásicas para **Exchange Online** que incluyen **Exchange Active Sync** como condición de aplicaciones de cliente, es posible que no pueda consolidarlas en una directiva nueva. 

Esto sucede en caso de que quiera admitir todos los tipos de aplicación de cliente. En una directiva nueva que tiene **Exchange Active Sync** como condición de aplicaciones de cliente no puede seleccionar otras aplicaciones cliente.

![Acceso condicional con selección de aplicaciones cliente](./media/policy-migration/64.png)

Tampoco es posible una consolidación en una directiva nueva si las directivas clásicas contienen varias condiciones. Una directiva nueva que tiene **Exchange Active Sync** como condición de aplicaciones de cliente no admite otras condiciones:   

![Exchange ActiveSync no es compatible con las condiciones seleccionadas](./media/policy-migration/08.png)

Si existe una directiva nueva que tiene **Exchange Active Sync** como condición de aplicaciones de cliente configurada, debe asegurarse de que todas las demás condiciones no están configuradas. 

![Condiciones de acceso condicional](./media/policy-migration/16.png)
 
Las directivas clásicas basadas en aplicaciones para Exchange Online que incluyen **Exchange Active Sync** como condición de aplicaciones de cliente permiten plataformas de dispositivos **admitidas** y **no admitidas**. Aunque no se pueda configurar plataformas de dispositivos individuales en una directiva nueva relacionada, puede limitar la compatibilidad solo para [plataformas de dispositivos compatibles](concept-conditional-access-conditions.md#device-platforms). 

![Acceso condicional con selección de Exchange ActiveSync](./media/policy-migration/65.png)

Puede consolidar varias directivas clásicas que incluya **Exchange Active Sync** como condición de aplicaciones de cliente si tienen:

- **Exchange Active Sync** como única condición 
- Varios requisitos configurados para la concesión de acceso

Una situación habitual es la consolidación de:

- Una directiva clásica basada en dispositivos desde el Portal de Azure clásico 
- Una directiva clásica basada aplicaciones desde el Portal de Intune App Protection 
 
En este caso, puede consolidar las directivas clásicas en una nueva directiva que tenga seleccionados ambos requisitos.

![Controles de concesión de acceso condicional](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Plataformas de dispositivo

Las directivas clásicas con controles basados en aplicaciones están preconfiguradas con iOS y Android como la condición de la plataforma del dispositivo. 

En una directiva nueva, debe seleccionar las [plataformas de dispositivo](concept-conditional-access-conditions.md#device-platforms) que quiera admitir de forma individual.

![Selección de plataformas de dispositivo de acceso condicional](./media/policy-migration/41.png)

## <a name="next-steps"></a>Pasos siguientes

- [Uso del modo de solo informe de acceso condicional para determinar el impacto de las nuevas decisiones de directiva.](concept-conditional-access-report-only.md)
- Si desea saber cómo configurar una directiva de acceso condicional, consulte [Directivas de acceso condicional habituales](concept-conditional-access-policy-common.md).
- Si está listo para configurar directivas de acceso condicional para su entorno, consulte el artículo [Instrucciones: Planeamiento de la implementación del acceso condicional en Azure Active Directory](plan-conditional-access.md). 
