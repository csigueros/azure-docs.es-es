---
title: 'Azure AD Connect Health: Alerta sobre que los datos del servicio de mantenimiento no están actualizados | Documentos de Microsoft'
description: Este documento describe la causa de la alerta "Los datos del servicio de mantenimiento no están actualizados" y cómo solucionar el problema.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bd83071e725d68a424642f68680748247a26706
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113110086"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Alerta sobre que los datos del servicio de mantenimiento no están actualizados

## <a name="overview"></a>Información general

Los agentes en las máquinas locales que Azure AD Connect Health supervisa periódicamente cargan los datos al servicio de Azure AD Connect Health. Si el servicio no recibe datos de un agente, la información que presenta el portal estará obsoleta. Para resaltar el problema, el servicio generará la alerta **Los datos del servicio de mantenimiento no están actualizados**. Esta alerta se genera cuando el servicio no ha recibido datos completos en las últimas dos horas.  

- Se desencadena una alerta de estado de **advertencia** si el servicio de mantenimiento solo ha recibido tipos de datos **parciales** enviados desde el servidor en las últimas dos horas. La alerta con el estado de advertencia no desencadena notificaciones de correo electrónico para los destinatarios configurados. 
- Se desencadena una alerta de estado de **error** si el servicio de mantenimiento no ha recibido ningún tipo de datos del servidor en las últimas dos horas. La alerta con el estado de error desencadena notificaciones de correo electrónico para los destinatarios configurados.

El servicio obtiene los datos de los agentes que se ejecutan en las máquinas locales, según el tipo de servicio. En la tabla siguiente se enumeran los agentes que se ejecutan en la máquina, lo que hacen y los tipos de datos que genera el servicio. En algunos casos, hay varios servicios implicados en el proceso, por lo que cualquiera de ellos podría ser el culpable. 

## <a name="understanding-the-alert"></a>Descripción de la alerta

La hoja **Detalles de la alerta** muestra cuándo se produjo la alerta y cuándo se detectó por última vez. Se genera un proceso en segundo plano que se ejecuta cada dos horas y se vuelve a evaluar la alerta. En el ejemplo siguiente, la alerta inicial se produjo el 10/03 a las 9:59. La alerta aún existía el 12/03 a las 10:00 cuando la alerta se volvió a evaluar. La hoja también detalla la última vez que el servicio de mantenimiento ha recibido un tipo de datos en particular. 
 
 ![Detalles de las alertas de Azure AD Connect Health](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
La tabla siguiente asigna los tipos de servicio a los tipos de datos requeridos correspondientes:

| Tipo de servicio | Agente (nombre del servicio de Windows) | Propósito | Tipo de datos generado  |
| --- | --- | --- | --- |  
| Azure AD Connect (sincronización) | Servicio de análisis de sincronización de Azure AD Connect Health | Recopilación de información específica de AAD Connect (conectores, reglas de sincronización, etc.) | - AadSyncService-SynchronizationRules <br />  - AadSyncService-Connectors <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Servicio de supervisión de sincronización de Azure AD Connect Health | Recopilación de archivos, seguimientos de ETW, contadores de rendimiento específicos de AAD Connect | Contador de rendimiento |
| AD DS | Servicio de análisis de AD DS de Azure AD Connect Health | Realización de pruebas sintéticas, recopilación de información de la topología, metadatos de replicación |  - Adds-TopologyInfo-Json <br /> - Common-TestData-Json (creates the test results)   | 
|  | Servicio de supervisión de AD DS de Azure AD Connect Health | Recopilación de archivos, seguimientos de ETW, contadores de rendimiento específicos de AADS | - Contador de rendimiento  <br /> - Common-TestData-Json (uploads the test results)  |
| AD FS | Servicio de diagnóstico de AD FS de Azure AD Connect Health | Realización de pruebas sintéticas | TestResult (crea los resultados de las pruebas) | 
| | Servicio de análisis de AD FS de Azure AD Connect Health  | Recopilación de métricas de uso de ADFS | Adfs-UsageMetrics |
| | Servicio de supervisión de AD FS de Azure AD Connect Health | Recopilación de archivos, seguimientos de ETW, contadores de rendimiento específicos de ADFS | TestResult (carga los resultados de las pruebas) |

## <a name="troubleshooting-steps"></a>Pasos para solucionar problemas 

A continuación se indican los pasos necesarios para diagnosticar el problema. El primero es un conjunto de comprobaciones básicas que son comunes a todos los tipos de servicios. 

> [!IMPORTANT] 
> Esta alerta sigue la [directiva de retención de datos](reference-connect-health-user-privacy.md#data-retention-policy) de Connect Health

* Asegúrese de que está instalada las versiones más recientes de los agentes. Consulte el [historial de versiones](reference-connect-health-version-history.md). 
* Asegúrese de que los servicios de agentes de Azure AD Connect Health se están **ejecutando** en la máquina. Por ejemplo, Connect Health para AD FS debe tener tres servicios.
  ![Comprobación de Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Asegúrese de repasar y cumplir la [sección de los requisitos](how-to-connect-health-agent-install.md#requirements).
* Utilice la [herramienta de pruebas de conectividad](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) para detectar problemas de conectividad.
* Si tiene un HTTP Proxy, siga estos [pasos de configuración](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Pasos siguientes
Si alguno de los pasos anteriores identificó un problema, arréglelo y espere a que se resuelva la alerta. El proceso de la alerta en segundo plano se ejecuta cada dos horas, por lo que tardará hasta dos horas en resolverse la alerta. 

* [Directiva de retención de datos de Azure AD Connect Health](reference-connect-health-user-privacy.md#data-retention-policy)
* [Preguntas más frecuentes de Azure AD Connect Health](reference-connect-health-faq.yml)
