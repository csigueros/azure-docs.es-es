---
title: Determinación de la posición de seguridad para la colaboración externa con Azure Active Directory
description: Antes de poder ejecutar un plan de seguridad de acceso externo, debe determinar lo que está intentando lograr.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fb5d74f0c912db098b5e309793963d5f07fbd58
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113730359"
---
# <a name="determine-your-security-posture-for-external-access"></a>Determinación de la posición de seguridad para el acceso externo 

Cuando considere la posibilidad de controlar el acceso externo, deberá evaluar las necesidades de seguridad y colaboración de la organización en general y dentro de cada escenario. En el nivel de organización, tenga en cuenta la cantidad de control que necesita que su equipo de TI tenga sobre la colaboración diaria. Las organizaciones de sectores regulados pueden necesitar más control de TI. Por ejemplo, puede ser necesario un contratista de defensa para identificar y documentar de forma positiva a cada usuario externo, su acceso y la eliminación de acceso. Este requisito puede estar en todo el acceso o en escenarios o cargas de trabajo específicos. En el otro extremo del espectro, una empresa de consultoría puede permitir de forma general que los usuarios finales determinen los usuarios externos con los que necesitan colaborar, dentro de determinadas barreras de seguridad de TI. 

![Control de colaboración de TI y de usuario final](media/secure-external-access/1-overall-control.png)

> [!NOTE]
> Un control demasiado estrecho sobre la colaboración puede dar lugar a mayores presupuestos de TI, menor productividad y resultados empresariales retrasados. Cuando los canales de colaboración oficiales se perciben como demasiado onerosos, los usuarios finales tienden a utilizar los sistemas proporcionados por TI para realizar su trabajo, por ejemplo, enviando documentos no seguros por correo electrónico.

## <a name="think-in-terms-of-scenarios"></a>Pensamiento en términos de escenarios

En muchos casos, TI puede delegar el acceso de los asociados, al menos en algunos escenarios, proporcionando al mismo tiempo barreras de seguridad. Las barreras de seguridad de TI pueden ayudar a garantizar que la propiedad intelectual permanezca segura, al tiempo que permiten a los empleados colaborar con los asociados para realizar el trabajo.

Al considerar los escenarios dentro de su organización, evalúe la necesidad de acceso a los recursos por parte de los empleados y por parte de los asociados comerciales. Un banco puede tener necesidades de cumplimiento que restrinjan el acceso a determinados recursos, como la información de la cuenta de usuario, a un pequeño grupo de empleados internos. Por el contrario, el mismo banco puede habilitar el acceso delegado para los asociados que trabajan en una campaña de marketing.

![continuación de la gobernanza por escenario](media\secure-external-access\1-scenarios.png)

En cada escenario, considere 

* la confidencialidad de la información en riesgo

* si necesita restringir o no lo que los asociados pueden ver sobre otros usuarios

* el costo de una vulneración frente al peso de control centralizado y los problemas del usuario final

 También puede empezar con controles administrados centralmente para satisfacer los objetivos de cumplimiento y delegar el control a los usuarios finales a lo largo del tiempo. Todos los modelos de administración de acceso pueden coexistir simultáneamente dentro de una organización. 

El uso de [credenciales administradas por el asociado](../external-identities/what-is-b2b.md) proporciona a la organización una señal esencial que termina el acceso a los recursos una vez que el usuario externo ha perdido el acceso a los recursos de su propia compañía.

## <a name="goals-of-securing-external-access"></a>Objetivos de la protección del acceso externo

Los objetivos de acceso delegado y controlado por TI difieren.

**Los principales objetivos del acceso controlado por TI son los siguientes:**

* Cumplir los objetivos de gobernanza, regulación y cumplimiento (GRC). 

* Controlar estrictamente el acceso de los asociados y lo que estos pueden ver sobre los usuarios miembros, los grupos y otros asociados.

**Los principales objetivos del acceso delegado son los siguientes:**

* Posibilitar que los propietarios de la empresa controlen con quién colaboran, dentro de las restricciones de TI.

* Permitir a los asociados empresariales solicitar acceso en función de las reglas definidas por los propietarios de la empresa.

Sea lo que fuere que apruebe para su organización y escenarios, deberá: 

* **Controlar el acceso a las aplicaciones, los datos y el contenido**. Esto puede realizarse a través de diversos métodos, en función de las versiones de [Azure AD](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing) y [Microsoft 365](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans). 

* **Reducir la superficie expuesta a ataques**. [Privileged Identity Management](../privileged-identity-management/pim-configure.md), [la prevención de pérdida de datos (DLP)](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) y las [capacidades de cifrado](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) reducen la superficie expuesta a ataques.

* **Revise periódicamente el registro de actividad y auditoría para confirmar el cumplimiento**. TI puede delegar las decisiones de acceso a los propietarios de empresas a través de la administración de derechos, mientras que las revisiones de acceso proporcionan una forma de confirmar periódicamente el acceso continuo. La clasificación de datos automatizada con etiquetas de confidencialidad ayuda a automatizar el cifrado de contenido confidencial, lo que facilita el cumplimiento por parte los usuarios finales de los empleados.

## <a name="next-steps"></a>Pasos siguientes 

Consulte los siguientes artículos sobre cómo proteger el acceso externo a los recursos. Se recomienda realizar las acciones en el orden de la lista.

1. [Determinación de la posición de seguridad para el acceso externo](1-secure-access-posture.md) (usted está aquí).

2. [Detección del estado actual](2-secure-access-current-state.md)

3. [Creación de un plan de gobernanza](3-secure-access-plan.md)

4. [Uso de grupos con fines de seguridad](4-secure-access-groups.md)

5. [Transición a B2B de Azure AD](5-secure-access-b2b.md)

6. [Acceso seguro mediante la administración de derechos](6-secure-access-entitlement-managment.md)

7. [Acceso seguro mediante directivas de acceso condicional](7-secure-access-conditional-access.md)

8. [Acceso seguro mediante etiquetas de confidencialidad](8-secure-access-sensitivity-labels.md)

9. [Acceso seguro a Microsoft Teams, OneDrive y SharePoint](9-secure-access-teams-sharepoint.md)
 

