---
title: Solución de problemas de actualización del usuario con el aprovisionamiento de RR. HH.
description: Obtenga información sobre cómo solucionar problemas de actualización del usuario con el aprovisionamiento de RR. HH.
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: c983429508fa55f14bb8450c58a7a106df00278f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132342535"
---
# <a name="troubleshoot-hr-user-update-issues"></a>Solución de problemas de actualización del usuario con RR. HH.

## <a name="null-and-empty-values-not-processed-as-expected"></a>Los valores NULL y vacíos no se procesan según lo previsto
**Se aplica a:**
* Aprovisionamiento de usuarios de Workday a Active Directory en el entorno local
* Aprovisionamiento de usuarios de Workday a Azure Active Directory
* Aprovisionamiento de usuarios de SAP SuccessFactors a Active Directory local
* Aprovisionamiento de usuarios de SAP SuccessFactors a Azure Active Directory

| Solución de problemas | Detalles |
|-- | -- |
| **Problema** | Ha configurado correctamente la aplicación de aprovisionamiento de entrada. Obtiene un valor NULL o vacío de la aplicación de RR. HH. Espera que el servicio de aprovisionamiento borre el valor del atributo de destino correspondiente en Active Directory local o Azure AD. Sin embargo, se produce un error en la operación con el mensaje de error siguiente: `InvalidAttributeSyntax-LdapErr: The syntax is invalid. The parameter is incorrect. Error in attribute conversion operation, data 0, v3839`. |
| **Causa** | El servicio de aprovisionamiento no tiene una lógica predeterminada para el procesamiento de valores NULL. Cuando el servicio de aprovisionamiento obtiene una cadena vacía de la aplicación de origen, intenta proporcionar el valor "tal cual" a la aplicación de destino. En este caso, Active Directory local no admite la configuración de valores de cadena vacíos y por eso aparece el error anterior. |
| **Resolución** | Compruebe los registros de aprovisionamiento. Identifique los atributos de la instancia de Active Directory de destino que reciben valores de cadena NULL o vacíos. Actualice la asignación de atributos para que estos atributos usen una asignación de expresiones. Consulte las soluciones recomendadas a continuación. |

**Soluciones recomendadas**

  Supongamos que el atributo `BusinessTitle` asignado al atributo de AD `jobTitle` puede ser NULL o estar vacío en Workday. 
  * Opción 1: defina una expresión para comprobar si hay valores vacíos o NULL mediante funciones como [IIF](functions-for-customizing-application-data.md#iif), [IsNullOrEmpty](functions-for-customizing-application-data.md#isnullorempty), [Coalesce](functions-for-customizing-application-data.md#coalesce) o [IsPresent](functions-for-customizing-application-data.md#ispresent) y pase un valor literal que no esté en blanco. 
  
     `IIF(IsNullOrEmpty([BusinessTitle]),"N/A",[BusinessTitle])`

  * Opción 2: use la función [IgnoreFlowIfNullOrEmpty](functions-for-customizing-application-data.md#ignoreflowifnullorempty) para quitar atributos vacíos o NULL en la carga enviada a Active Directory local o Azure AD. 
  
     `IgnoreFlowIfNullOrEmpty([BusinessTitle])` 

## <a name="some-workday-attribute-updates-are-missing"></a>Faltan algunas actualizaciones de atributos de Workday
**Se aplica a:**
* Aprovisionamiento de usuarios de Workday a Active Directory en el entorno local
* Aprovisionamiento de usuarios de Workday a Azure Active Directory

| Solución de problemas | Detalles |
|-- | -- |
| **Problema** | Ha configurado correctamente la aplicación de aprovisionamiento de entrada de Workday y se ha conectado correctamente a la dirección URL del inquilino de Workday. Observa que hay un retraso en el flujo de ciertas actualizaciones de atributos de Workday o, en algunos casos, los cambios de atributos de Workday no fluyen según lo previsto durante la sincronización incremental. |
| **Causa** | Durante la sincronización incremental, la aplicación de aprovisionamiento consulta el registro de transacciones de Workday para ver si hay cambios en la entidad de trabajo principal y solo se procesan los cambios controlados por el registro de transacciones de Workday. <br> Si el registro de transacciones de Workday no realiza el seguimiento de los cambios de un atributo de Workday en la instalación, Azure AD no podrá capturar ese cambio. Por ejemplo: el atributo *LocalReference* de Workday forma parte de la asignación predeterminada de atributos y tiene el elemento XPATH `wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Local_Reference/wd:ID[@wd:type='Locale_ID']/text()`. Tenga en cuenta que este atributo forma parte de la entidad *Business_Site_Summary_Data*. Un cambio en el valor de este atributo en Workday no se mostrará en el registro de transacciones de Workday. Por lo tanto, durante la sincronización incremental, el nuevo valor de este atributo solo se mostrará si un atributo asociado a la entidad de trabajo principal también cambia durante el intervalo de sincronización. |
| **Resolución** | Si observa este comportamiento con frecuencia, donde no fluyen los cambios en determinados atributos de Workday, se recomienda ejecutar periódicamente una sincronización completa semanal o mensual. |

## <a name="user-match-with-extensionattribute-not-working"></a>La coincidencia de usuario con extensionAttribute no funciona
**Se aplica a:**
* Aprovisionamiento de usuarios de Workday a Azure Active Directory
* Aprovisionamiento de usuarios de SAP SuccessFactors a Azure Active Directory

| Solución de problemas | Detalles |
|-- | -- |
| **Problema** | Supongamos que usa *extensionAttribute3* en Azure AD para almacenar el identificador de empleado y que lo ha asignado al atributo *WorkerID* de Workday o *personIdExternal* de SuccessFactors para la coincidencia de usuarios. Con esta configuración, se produce un error en el paso de coincidencia en el proceso de aprovisionamiento. Este problema afecta tanto a la creación como a las actualizaciones del usuario. |
| **Causa** | *OnPremisesExtensionAttributes* (`extensionAttributes1-15`) de Azure AD no se puede usar como atributo de coincidencia porque el parámetro `$filter` de **Graph API de Azure AD** no [admite el filtrado por extensionAttributes](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#filter). |
| **Resolución** | No use *OnPremisesExtensionAttributes* (`extensionAttributes1-15`) de Azure AD en el par de atributos correspondiente. Use employeeID. |


## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre los escenarios de integración de Azure AD y Workday y las llamadas a servicios web](workday-integration-reference.md)
* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](check-status-user-account-provisioning.md)
