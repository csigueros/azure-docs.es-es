---
title: 'Adición de usuarios con licencias directas a licencias de grupos: Azure AD | Microsoft Docs'
description: Migración de licencias de usuarios individuales a licencias basadas en grupos mediante Azure Active Directory
services: active-directory
keywords: Licencias de Azure AD
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: df7188fa214e2b9407364b1f4d850a6f1c50cc27
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985834"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Migración de usuarios con licencias individuales a grupos de licencias

Puede que actualmente tenga licencias implementadas para usuarios de organizaciones mediante una asignación directa; es decir, mediante el uso de scripts de PowerShell u otras herramientas para asignar licencias de usuarios individuales. Antes de empezar a usar licencias basadas en grupos para administrar las licencias de su organización, puede usar este plan de migración para reemplazar con facilidad las soluciones existentes por licencias basadas en grupos.

Lo más importante es tener en cuenta que hay que evitar una situación tal en que la migración a licencias basadas en grupos conlleve que los usuarios pierdan temporalmente las licencias que actualmente tienen asignadas. Se debe evitar cualquier proceso que pueda dar lugar a la eliminación de licencias a fin de evitar el riesgo de que los usuarios pierdan el acceso a los servicios y a sus datos.

## <a name="recommended-migration-process"></a>Proceso de migración recomendado

1. Cuenta actualmente con un sistema de automatización (por ejemplo, PowerShell) que administra la asignación y la retirada de licencias de los usuarios. Deje que se ejecute de la forma habitual.

1. Cree un grupo de licencias nuevo (o decida qué grupos utilizar de entre los existentes) y asegúrese de que todos los usuarios necesarios se agregan como miembros.

1. Asigne las licencias necesarias a esos grupos; el objetivo debe consistir en reflejar el mismo estado de licencia que el sistema de automatización (por ejemplo, PowerShell) aplica a dichos usuarios.

1. Verifique que dichas licencias se hayan aplicado a todos los usuarios de esos grupos. Para ello, compruebe el estado de procesamiento de cada grupo y los registros de auditoría.

   - Puede realizar una revisión rápida de cada usuario observando los detalles de sus licencias. Observará que tienen las mismas licencias asignadas "directamente" o "heredadas" de los grupos.

   - Puede ejecutar un script de PowerShell para [verificar cómo se asignan las licencias a los usuarios](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Cuando se asigna la misma licencia de producto al usuario directamente y a través de un grupo, el usuario solo puede consumir una licencia. Por lo tanto, no se necesitan licencias adicionales para realizar la migración.

1. Compruebe si en algún grupo de usuarios aparece el estado de error, a fin de verificar que no se produzcan errores en las asignaciones de licencias. Para más información, vea [Identificación y resolución de problemas de licencias de un grupo](licensing-groups-resolve-problems.md).

Considere la posibilidad de quitar las asignaciones directas originales. Se recomienda hacerlo gradualmente y supervisar primero el resultado en un subconjunto de usuarios. Puede dejar las asignaciones directas originales de los usuarios pero, en ese caso, cuando el usuario deja los grupos con licencia, conserva las licencias asignadas directamente, y es posible que no sea esto lo que desee.

## <a name="an-example"></a>Un ejemplo

Una organización tiene 1.000 usuarios. Todos los usuarios necesitan licencias de Office 365 Enterprise E3. En la actualidad, la organización tiene un script de PowerShell que se ejecuta a nivel local mediante la adición y eliminación de licencias de usuarios a medida que se incorporan y se van. Pero la organización quiere reemplazar el script con licencias basadas en grupos, de forma que Azure AD administre las licencias automáticamente.

El proceso de migración podría ser similar al siguiente:

1. En Azure Portal, asigne la licencia de Office 365 E3 al grupo **Todos los usuarios** de Azure AD.

1. Confirme que se ha completado la asignación de licencia para todos los usuarios. Vaya a la página de información general del grupo, seleccione **Licencias** y compruebe el estado de procesamiento en la parte superior de la hoja **Licencias**.

   - Busque "Latest license changes have been applied to all users" (Los últimos cambios de licencia se han aplicado a todos los usuarios) para confirmar que el procesamiento se ha completado.

   - Busque si hay alguna notificación en la parte superior sobre algún usuario cuya licencia no se haya podido asignar correctamente. ¿Se han agotado las licencias para algunos usuarios? ¿Algunos usuarios tienen conflictos de planes de licencia que les impidan heredar las licencias de grupo?

1. Realice una revisión rápida de algunos usuarios para verificar que tengan aplicadas tanto licencias directas como de grupo. Vaya a la página de perfil de un usuario, seleccione **Licencias** y examine el estado de las licencias.

   - Este es el estado de usuario esperado durante la migración:

      ![estado de usuario esperado durante la migración](./media/licensing-groups-migrate-users/expected-user-state.png)

     Esto confirma que el usuario tiene licencias directas y heredadas. Vemos que se ha asignado Office 365 E3.

   - Seleccione cada licencia para ver qué servicios están habilitados. Para comprobar que las licencias directas y de grupo habilitan exactamente los mismos servicios para el usuario, seleccione **Asignaciones**.

1. Después de confirmar que las licencias directas y de grupo son equivalentes, puede empezar a quitar a los usuarios las licencias directas. Para probarlo, quítelos para usuarios individuales en el portal y luego ejecute los scripts de automatización para quitarlos en masa. Este es un ejemplo del mismo usuario con las licencias directas quitadas a través del portal. Tenga en cuenta que el estado de licencia no varía, pero aún no se ven las asignaciones directas.

   ![confirmación de que se han quitado las licencias directas](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Pasos siguientes

Más información sobre otros escenarios de administración de licencias de grupo:

- [¿En qué consisten las licencias basadas en grupos de Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [Asignación de licencias a un grupo en Azure Active Directory](licensing-groups-assign.md)
- [Identificación y resolución de problemas de licencias de un grupo en Azure Active Directory](licensing-groups-resolve-problems.md)
- [Cómo migrar usuarios entre diferentes licencias de productos con licencias basadas en grupos de Azure Active Directory](licensing-groups-change-licenses.md)
- [Escenarios adicionales de licencias basadas en grupos de Azure Active Directory](licensing-group-advanced.md)
- [Ejemplos de PowerShell para licencias basadas en grupos de Azure AD](licensing-ps-examples.md)
