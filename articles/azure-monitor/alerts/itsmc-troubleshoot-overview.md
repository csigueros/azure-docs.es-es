---
title: Solución de problemas en ITSMC
description: Obtenga información sobre cómo resolver problemas comunes en Conector de Administración de servicios de TI.
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: d6e41cd80e27fe673d04592c61e590c09645619e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430367"
---
# <a name="troubleshoot-problems-in-it-service-management-connector"></a>Solución de problemas en Conector de Administración de servicios de TI

En este artículo se describen los problemas comunes de Conector de Administración de servicios de TI (ITSM) y cómo solucionarlos.

Azure Monitor le informa de forma proactiva cuando se detectan condiciones importantes en los datos que se supervisan. Estas alertas le permiten identificar y solucionar los problemas antes de que los usuarios del sistema puedan verlos.

Puede seleccionar cómo quiere recibir las alertas. Puede elegir correo electrónico, SMS o webhook, o incluso automatizar una solución. 

Una alternativa es notificar a través de ITSMC. ITSM le ofrece la opción de enviar alertas a un sistema externo de vales como ServiceNow.

## <a name="use-the-dashboard-to-analyze-incident-and-change-request-data"></a>Uso del panel para analizar los datos de incidentes y solicitudes de cambios

En función de las opciones de configuración de una conexión, ITSMC puede sincronizar hasta 120 días de datos referentes a incidentes y a solicitudes cambios. Para obtener el esquema de registro de los datos, consulte el artículo [Datos sincronizados desde el producto de ITSM](./itsmc-synced-data.md).

Puede visualizar los datos de incidentes y solicitudes de cambio mediante el panel de ITSMC:

![Captura de pantalla en la que se muestra el panel de ITSMC.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

El panel también proporciona información sobre el estado del conector. Puede usar esa información como punto de partida para analizar problemas con las conexiones. Para obtener más información, consulte [Investigación de errores mediante el panel](./itsmc-dashboard.md).

## <a name="use-service-map-to-visualize-incidents"></a>Uso de Service Map para visualizar incidentes

También puede visualizar los incidentes sincronizados con los equipos afectados en Service Map.

Mapa de servicio detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux y asigna la comunicación entre servicios. Permite ver los servidores a medida que piensa en ellos: como los sistemas interconectados que ofrecen servicios críticos. 

Service Map muestra las conexiones entre servidores, procesos y puertos en cualquier arquitectura con conexión TCP. No se requiere ninguna configuración aparte de la instalación de un agente. Para más información, vea [Uso de la solución Service Map en Azure](../vm/service-map.md).

Si usa Service Map, puede ver los elementos de la consola de servicio creados en las soluciones de Administración de servicios de TI (ITSM), como se muestra en este ejemplo:

![Captura de pantalla en la que se muestra la pantalla de Log Analytics.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="resolve-problems"></a>Resolución de problemas

En las secciones siguientes se identifican los síntomas comunes, las causas posibles y las soluciones. 

### <a name="a-connection-to-the-itsm-system-fails-and-you-get-an-error-in-saving-connection-message"></a>Si una conexión no puede conectarse al sistema ITSM y recibe un mensaje "Error al guardar la conexión"

**Causa**: La causa puede deberse a una de estas opciones:

* Las credenciales son incorrectas.
* Los privilegios son insuficientes.
* Para conexiones de Service Manager: la aplicación web se ha implementado incorrectamente.

**Solución:**

* En el caso de conexiones de ServiceNow, Cherwell y Provance:
  * Asegúrese de que ha introducido correctamente el nombre de usuario, la contraseña, el identificador de cliente y el secreto de cliente de cada una de las conexiones.  
  * En el caso de ServiceNow, asegúrese de disponer de [privilegios suficientes](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role) en el producto de ITSM correspondiente.

* En el caso de conexiones de Service Manager:  
  * Asegúrese de que la aplicación web se implementa correctamente y de que se crea la conexión híbrida. Para comprobar que la conexión se ha establecido correctamente con el equipo de Service Manager local, visite la dirección URL de la aplicación web como se detalla en la [documentación para realizar una conexión híbrida](./itsmc-connections-scsm.md#configure-the-hybrid-connection).  

### <a name="duplicate-work-items-are-created"></a>Se crean elementos de trabajo duplicados

**Causa**: La causa puede deberse a una de estas dos opciones:

* Se definió más de una acción de ITSM para la alerta.
* Se resolvió la alerta.

**Solución:** Puede haber dos soluciones:

* Asegúrese de tener un único grupo de acciones de ITSM por alerta.
* ITSM no admite actualizaciones del estado de elementos de trabajo coincidentes cuando se ha resuelto una alerta. Cree un nuevo elemento de trabajo resuelto.

### <a name="work-items-are-not-created"></a>No se han creado elementos de trabajo.

**Causa**: Puede haber varios motivos para este síntoma:

* El código se modificó en el lado de ServiceNow.
* Los permisos están mal configurados.
* Los límites de frecuencia de ServiceNow son demasiado altos o bajos.
* El token de actualización expiró.
* Se ha eliminado ITSMC.

**Solución:** Consulte el [panel](itsmc-dashboard.md) y revise los errores en la sección de estado del conector. A continuación, revise los [errores comunes y sus soluciones](itsmc-dashboard-errors.md).

### <a name="you-cant-create-an-itsm-action-for-an-action-group"></a>No se puede crear una acción de ITSM para un grupo de acciones

**Causa**: Una instancia de ITSM recién creada aún tiene que finalizar la sincronización inicial.

**Solución:** Revise los [errores comunes y sus soluciones](itsmc-dashboard-errors.md).

### <a name="sync-connection"></a>Sincronización de la conexión 

**Causa**: Puede haber varios motivos para este síntoma:

* Las plantillas no se muestran como parte de la lista desplegable de definiciones de acción y aparece un mensaje de error que dice que no se puede recuperar la configuración de la plantilla, que consulte los registros del conector para más información.
* Los valores no se muestran en las listas desplegables de los campos predeterminados como parte de la definición de acción y aparece un mensaje de error que dice que no se encontraron valores para los campos siguientes: \<field names\>.
* No se crean incidentes o eventos en ServiceNow.

**Solución:** 
* [Sincronice el conector](itsmc-resync-servicenow.md).
* Consulte el [panel](itsmc-dashboard.md) y revise los errores en la sección de estado del conector. Luego, revise los [errores comunes y sus soluciones](itsmc-dashboard-errors.md).
