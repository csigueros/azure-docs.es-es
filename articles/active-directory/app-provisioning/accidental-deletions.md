---
title: Habilitación de la prevención de eliminaciones accidentales en Aprovisionamiento de aplicaciones en Azure Active Directory
description: Habilite la prevención de eliminaciones accidentales en Aprovisionamiento de aplicaciones de Azure Active Directory.
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 09/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 6f7cb755663b7bf454e33c5a4f785d29d862967d
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155427"
---
# <a name="enable-accidental-deletions-prevention-in-the-azure-ad-provisioning-service-preview"></a>Habilitación de la prevención de eliminaciones accidentales en el servicio de aprovisionamiento de Azure AD (versión preliminar)

El servicio de aprovisionamiento de Azure AD incluye una característica que ayuda a evitar eliminaciones accidentales. Esta garantiza que los usuarios no se deshabiliten ni se eliminen de una aplicación de forma inesperada. 

La característica permite especificar un umbral de eliminación, por encima del cual el administrador debe elegir de forma explícita si permite el procesamiento de las eliminaciones.

> [!NOTE]
> Las eliminaciones accidentales no se admiten en las integraciones de Workday o SuccessFactors. Tampoco se admiten para los cambios de ámbito (por ejemplo, cambiar un filtro de ámbito o cambiar de "sincronizar todos los usuarios y grupos" a "sincronizar los usuarios y grupos asignados"). Hasta que no se publique la versión completa de la característica de prevención de eliminaciones accidentales, deberá acceder a Azure Portal mediante esta dirección URL: https://aka.ms/AccidentalDeletionsPreview


## <a name="configure-accidental-deletion-prevention"></a>Configuración de la prevención contra eliminaciones accidentales
Para habilitar la prevención de eliminaciones accidentales:
1.  En Azure Portal, seleccione **Azure Active Directory**.
2.  Seleccione **Aplicaciones empresariales** y, después, seleccione su aplicación.
3.  Seleccione **Aprovisionamiento** y, en la página de aprovisionamiento, seleccione **Edit provisioning** (Editar aprovisionamiento).
4. En **Configuración**, seleccione la casilla **Prevent accidental deletions** (Evitar eliminaciones accidentales) y especifique un umbral de eliminación. Asimismo, asegúrese de que se haya completado la dirección de correo electrónico de notificación. Si se alcanza el umbral de eliminación, se enviará un correo electrónico.
5. Seleccione **Guardar** para guardar los cambios.

Cuando se alcance el umbral de eliminación, el trabajo entrará en cuarentena y se enviará un correo electrónico de notificación. A continuación, el trabajo en cuarentena puede permitirse o rechazarse. Para obtener más información sobre el comportamiento de la cuarentena, consulte [Aprovisionamiento de aplicaciones en el estado de cuarentena](application-provisioning-quarantine-status.md).

## <a name="known-limitations"></a>Limitaciones conocidas
Hay dos limitaciones clave que hay que tener en cuenta y en las que se está trabajando activamente para solucionarlas:
- El aprovisionamiento controlado por recursos humanos de Workday y SuccessFactors no admite la característica de eliminaciones accidentales. 
- La característica de eliminaciones accidentales no admite cambios en la configuración de aprovisionamiento (por ejemplo, el cambio de ámbito). 

## <a name="recovering-from-an-accidental-deletion"></a>Recuperación de una eliminación accidental
Si se produce una eliminación accidental, esta aparecerá en la página de estado de aprovisionamiento.  El mensaje será: **Provisioning has been quarantined. See quarantine details for more information.** (El aprovisionamiento se ha puesto en cuarentena. Consulte los detalles de la cuarentena para obtener más información).

Puede hacer clic en **Allow deletes** (Permitir eliminaciones) o en **View provisioning logs** (Ver registros de aprovisionamiento).

### <a name="allowing-deletions"></a>Permitir las eliminaciones

La acción para **Permitir eliminaciones** eliminará los objetos que desencadenó el umbral de eliminaciones accidentales.  Use el siguiente procedimiento para aceptar las eliminaciones.  

1. Seleccione **Allow deletes** (Permitir eliminaciones).
2. Haga clic en **Sí** en la confirmación para permitir la eliminación.
3. Verá una confirmación de que las eliminaciones se aceptaron y el estado volverá a ser correcto en el siguiente ciclo.

### <a name="rejecting-deletions"></a>Rechazar eliminaciones

Si no desea permitir las eliminaciones, debe hacer lo siguiente:
- Investigue el origen de las eliminaciones. Puede usar los registros de aprovisionamiento para obtener más detalles.
- Para evitar la eliminación, puede volver a asignar el usuario o el grupo a la aplicación, restaurar el usuario o el grupo o bien actualizar la configuración de aprovisionamiento.
- Una vez que haya realizado los cambios necesarios para evitar la eliminación del usuario o el grupo, reinicie el aprovisionamiento. No reinicie el aprovisionamiento hasta que no haya realizado los cambios necesarios para evitar que se eliminen los usuarios o los grupos. 


### <a name="test-deletion-prevention"></a>Probar la prevención de eliminaciones
Para probar esta característica, desencadene eventos de deshabilitación o eliminación; para ello, establezca el umbral en un número bajo (como tres) y cambie los filtros de ámbito, desasigne los usuarios y elimine usuarios del directorio (consulte los escenarios comunes en la sección siguiente). 

Deje que el trabajo de aprovisionamiento se ejecute (de 20 a 40 minutos) y vuelva a la página de aprovisionamiento. Verá que el trabajo de aprovisionamiento entra en cuarentena y puede optar por permitir las eliminaciones o revisar los registros de aprovisionamiento para entender por qué se han producido estas.

## <a name="common-de-provisioning-scenarios-to-test"></a>Escenarios de desaprovisionamiento comunes para probar
- Elimine un usuario o colóquelo en la papelera de reciclaje.
- Bloquee el inicio de sesión de un usuario.
- Cancele la asignación de un usuario o un grupo de la aplicación.
- Quite un usuario de un grupo que le proporciona acceso a la aplicación.

Para obtener más información sobre los escenarios de desaprovisionamiento, consulte [Funcionamiento del aprovisionamiento de aplicaciones](how-provisioning-works.md#de-provisioning).

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="what-scenarios-count-toward-the-deletion-threshold"></a>¿Qué escenarios cuentan para el umbral de eliminación?
Cuando se establece que un usuario se quite de la aplicación de destino, este se contará con respecto al umbral de eliminación. Entre los escenarios que pueden dar lugar a la eliminación de un usuario de la aplicación de destino se incluyen los siguientes: la desasignación del usuario de la aplicación y la eliminación temporal o permanente de un usuario del directorio. Los grupos evaluados para su eliminación se cuentan respecto al umbral de eliminación. Además de las eliminaciones, la misma funcionalidad se aplica también a las deshabilitaciones.

### <a name="what-is-the-interval-that-the-deletion-threshold-is-evaluated-on"></a>¿A qué intervalos se evalúa el umbral de eliminación?
Este se evalúa en cada ciclo. Si el número de eliminaciones no supera el umbral durante un solo ciclo, no se desencadenará el "disyuntor". Si se necesitan varios ciclos para alcanzar un estado estable, el umbral de eliminación se evaluará por ciclo.

### <a name="how-are-these-deletion-events-logged"></a>¿Cómo se registran estos eventos de eliminación?
Puede que haya usuarios que deban deshabilitarse o eliminarse, pero que no se haya hecho debido al umbral de eliminación. Navegue a **Registros de aprovisionamiento** y, a continuación, filtre **Acción** por *StagedAction* o *StagedDelete*.


## <a name="next-steps"></a>Pasos siguientes 

- [Funcionamiento del aprovisionamiento de aplicaciones](how-provisioning-works.md)
- [Planeación de la implementación del aprovisionamiento de aplicaciones](plan-auto-user-provisioning.md)
