---
title: Solución de problemas de creación de usuarios con el aprovisionamiento de RR. HH.
description: Descubra cómo solucionar problemas de creación de usuarios con el aprovisionamiento de RR. HH.
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: 0c660221c230f884b43252cef25626e3d2b1d700
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324581"
---
# <a name="troubleshoot-hr-user-creation-issues"></a>Solución de problemas de creación de usuarios de RR. HH.

## <a name="creation-fails-due-to-null--empty-values"></a>Se produce un error en la creación debido a valores NULL o vacíos 

**Se aplica a:**
* Aprovisionamiento de usuarios de Workday a Active Directory en el entorno local
* Aprovisionamiento de usuarios de Workday a Azure Active Directory
* Aprovisionamiento de usuarios de SAP SuccessFactors a Active Directory local
* Aprovisionamiento de usuarios de SAP SuccessFactors a Azure Active Directory

| Solución de problemas | Detalles |
|-- | -- |
| **Problema** | Ha configurado correctamente la aplicación de aprovisionamiento de entrada. Obtiene un valor NULL o vacío de la aplicación de RR. HH. Se produce un error en la operación de creación con el mensaje de error siguiente: `InvalidAttributeSyntax-LdapErr: The syntax is invalid. The parameter is incorrect. Error in attribute conversion operation, data 0, v3839`. |
| **Causa** | El servicio de aprovisionamiento no tiene una lógica predeterminada para el procesamiento de valores NULL. Cuando el servicio de aprovisionamiento obtiene una cadena vacía de la aplicación de origen, intenta proporcionar el valor "tal cual" a la aplicación de destino. En este caso, Active Directory local no admite la configuración de valores de cadena vacíos y por eso ve el error anterior. |
| **Resolución** | Compruebe los registros de aprovisionamiento. Identifique los atributos de la instancia de Active Directory de destino que reciben valores de cadena NULL o vacíos. Actualice la asignación de atributos para que estos atributos usen una asignación de expresiones. Consulte las soluciones recomendadas a continuación. |

**Soluciones recomendadas**

  Supongamos que el atributo `BusinessTitle` asignado al atributo de AD `jobTitle` puede ser NULL o estar vacío en Workday. 
  * Opción 1: defina una expresión para comprobar si hay valores vacíos o NULL mediante funciones como [IIF](functions-for-customizing-application-data.md#iif), [IsNullOrEmpty](functions-for-customizing-application-data.md#isnullorempty), [Coalesce](functions-for-customizing-application-data.md#coalesce) o [IsPresent](functions-for-customizing-application-data.md#ispresent) y pase un valor literal que no esté en blanco. 
  
     `IIF(IsNullOrEmpty([BusinessTitle]),"N/A",[BusinessTitle])`

  * Opción 2: use la función [IgnoreFlowIfNullOrEmpty](functions-for-customizing-application-data.md#ignoreflowifnullorempty) para quitar atributos vacíos o NULL en la carga enviada a Active Directory local o Azure AD. 
  
     `IgnoreFlowIfNullOrEmpty([BusinessTitle])` 


## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre los escenarios de integración de Azure AD y Workday y las llamadas a servicios web](workday-integration-reference.md)
* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](check-status-user-account-provisioning.md)

