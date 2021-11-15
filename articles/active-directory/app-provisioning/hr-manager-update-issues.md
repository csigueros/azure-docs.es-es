---
title: Solución de problemas de actualización del administrador con el aprovisionamiento de RR. HH.
description: Obtenga información sobre cómo solucionar problemas de actualización del administrador con el aprovisionamiento de recursos humanos.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: 21635b7c7df6639745916aecd207780e6ae5df52
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478382"
---
# <a name="troubleshoot-hr-manager-update-issues"></a>Solución de problemas de actualización del administrador de RR. HH.

**Se aplica a:**
* Aprovisionamiento de usuarios de Workday a Active Directory en el entorno local
* Aprovisionamiento de usuarios de Workday a Azure Active Directory
* Aprovisionamiento de usuarios de SAP SuccessFactors a Active Directory local
* Aprovisionamiento de usuarios de SAP SuccessFactors a Azure Active Directory

## <a name="understanding-how-manager-reference-resolution-works"></a>Descripción del funcionamiento de la resolución de referencias de administrador
El servicio de aprovisionamiento de Azure AD actualiza automáticamente la información del administrador para que la relación entre los usuarios y el administrador de Azure AD esté siempre sincronizada con sus datos de RR. HH. Usa un proceso denominado *resolución de referencias de administrador* para actualizar con precisión el atributo *manager*. Antes de entrar en los detalles del proceso, es importante comprender cómo se almacena la información del administrador en Azure AD y Active Directory local. 

* En **Active Directory local**, el atributo *manager* almacena el atributo *distinguishedName (dn)* de la cuenta del administrador en AD. 
* En **Azure AD**, el atributo *manager* es una propiedad de navegación DirectoryObject de Azure AD. Cuando consulta el registro de usuarios en Azure Portal, este muestra el atributo *displayName* del registro del administrador en Azure AD. 

La *resolución de referencias de administrador* es un proceso de dos pasos: 
* Paso 1: Vincule el registro de origen de RR. HH. del administrador con el registro de la cuenta de destino del administrador mediante un par de atributos denominados *delimitador de origen* y *delimitador de destino*. 
* Paso 2: Use los atributos de referencia del administrador definidos en el esquema para actualizar el atributo "manager" en el destino en el formato necesario. 

A continuación se enumeran los atributos delimitadores predeterminados y los atributos de referencia de cada aplicación. 

| Nombre de la aplicación | Atributo de delimitador | Atributo de referencia en el perfil de usuario | 
|--|--|--| 
| Workday | WID | ManagerReference (apunta al WID del registro del administrador) |
| SAP SuccessFactors | personIdExternal | manager (apunta al atributo personIdExternal del registro del administrador) |
| Active Directory local | objectGUID | manager (apunta al DN del registro del administrador) |
| Azure AD | objectId | manager (apunta al registro de Azure AD del administrador) |

## <a name="prerequisites-for-successful-manager-update"></a>Requisitos previos para una actualización correcta del administrador
Para que la *resolución de referencias de administrador* funcione correctamente, deben cumplirse los siguientes requisitos previos: 
* La aplicación de aprovisionamiento debe configurarse para usar los delimitadores de origen y de destino predeterminados enumerados anteriormente. No cambie las propiedades de metadatos (tipo de datos y expresión de API) asociadas a estos atributos de delimitador y de referencia. 
* Las expresiones de API (XPATH para Workday y JSONPath para SuccessFactors) asociadas al atributo manager se resuelven en un valor válido que no es NULL. 
   * Expresión de API XPATH predeterminada para el atributo ManagerReference de Workday: `wd:Worker/wd:Worker_Data/wd:Management_Chain_Data/wd:Worker_Supervisory_Management_Chain_Data[position()=1]/wd:Management_Chain_Data[last()=position()]/wd:Manager_Reference/wd:ID[@wd:type='WID']/text()`
   * Expresión de API JSONPath predeterminada para el atributo manager de SuccessFactors: `$.employmentNav.results[0].userNav.manager.empInfo.personIdExternal`
* El registro de administrador también debe estar en el ámbito del trabajo de aprovisionamiento. 
* La aplicación de aprovisionamiento debe haber procesado el registro de administrador antes de procesar el registro de usuarios. 

## <a name="provision-on-demand-does-not-update-manager-attribute"></a>El aprovisionamiento a petición no actualiza el atributo manager
| | |
|--|--|
| **Problema** | Ha configurado correctamente la aplicación de aprovisionamiento de entrada. Está probando la sincronización con el aprovisionamiento a petición. No actualiza el atributo manager y recibe un mensaje de error *"Valor no válido"* .  |
| **Causa** | El trabajo de aprovisionamiento no cumple uno de los [requisitos previos para una actualización correcta del administrador](#prerequisites-for-successful-manager-update).  |
| **Resolución** | * Si ha cambiado la asignación predeterminada del atributo manager, restaure la asignación predeterminada. <br> * Asegúrese de que el registro del administrador está dentro del ámbito y de que la expresión de API del atributo manager se resuelve en un valor válido. <br> * Ejecute primero el aprovisionamiento a petición del registro del administrador y, después, ejecute el aprovisionamiento a petición para el registro de usuarios.  |

## <a name="full-sync-does-not-update-manager-attribute"></a>La sincronización completa no actualiza el atributo manager
| | |
|--|--|
| **Problema** | Ha configurado correctamente la aplicación de aprovisionamiento de entrada. Está usando un filtro de ámbito para procesar solo determinados registros de RR. HH. Observa que la resolución del administrador no se está produciendo para algunos usuarios.  |
| **Causa** | Si usa filtros de ámbito, lo más probable es que el registro del administrador no esté dentro del ámbito.  |
| **Resolución** | Actualice el filtro de ámbito para agregar el registro del administrador al ámbito.  |

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre los escenarios de integración de Azure AD y Workday y las llamadas a servicios web](workday-integration-reference.md)
* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](check-status-user-account-provisioning.md)
