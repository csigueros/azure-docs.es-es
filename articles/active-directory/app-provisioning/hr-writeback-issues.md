---
title: Solución de problemas de reescritura con el aprovisionamiento de Recursos Humanos
description: Aprenda a solucionar problemas de reescritura con el aprovisionamiento de recursos humanos
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: 1d40b2738e52866ebc47bbe43b673c52b774619f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324543"
---
# <a name="troubleshoot-hr-write-back-issues"></a>Solución de problemas de reescritura con el aprovisionamiento de Recursos Humanos

## <a name="null-and-empty-values-not-processed-as-expected"></a>Los valores NULL y vacíos no se procesan según lo previsto
**Se aplica a:**
* Escritura diferida de Workday
* Escritura diferida de SAP SuccessFactors

| Solución de problemas | Detalles |
|-- | -- |
| **Problema** | Ha configurado correctamente la aplicación de reescritura. Obtiene un valor NULL o vacío de Azure AD. Espera que el servicio de aprovisionamiento borre el valor de correo electrónico o número de teléfono correspondiente en la aplicación de Recursos Humanos. Sin embargo, se produce un error en la operación. |
| **Causa** | El servicio de aprovisionamiento no tiene una lógica predeterminada para el procesamiento de valores NULL. Cuando el servicio de aprovisionamiento obtiene una cadena vacía de la aplicación de origen, intenta proporcionar el valor "tal cual" a la aplicación de destino. Si Workday o SuccessFactors no pueden procesar valores vacíos, se devuelve un error. |
| **Resolución** | Actualice la asignación de atributos para usar asignaciones de expresiones como se recomienda a continuación. |

**Soluciones recomendadas**

  Supongamos que el atributo `telephoneNumber` asignado al atributo `businessPhoneNumber` de SAP SuccessFactors puede ser NULL o estar vacío en Azure AD. 
  * Opción 1: defina una expresión para comprobar si hay valores vacíos o NULL mediante funciones como [IIF](functions-for-customizing-application-data.md#iif), [IsNullOrEmpty](functions-for-customizing-application-data.md#isnullorempty), [Coalesce](functions-for-customizing-application-data.md#coalesce) o [IsPresent](functions-for-customizing-application-data.md#ispresent) y pase un valor literal que no esté en blanco (ejemplo: 000-000-0000 en este caso). 
  
     `IIF(IsNullOrEmpty([telephoneNumber]),"000-000-0000",[telephoneNumber])`

  * Opción 2: use la función [IgnoreFlowIfNullOrEmpty](functions-for-customizing-application-data.md#ignoreflowifnullorempty) para quitar atributos vacíos o NULL en la carga enviada a SuccessFactors. 
  
     `IgnoreFlowIfNullOrEmpty([telephoneNumber])` 



## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre los escenarios de integración de Azure AD y Workday y las llamadas a servicios web](workday-integration-reference.md)
* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](check-status-user-account-provisioning.md)
