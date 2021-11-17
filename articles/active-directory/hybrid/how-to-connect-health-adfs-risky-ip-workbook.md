---
title: Libro del informe de direcciones IP de riesgo para Azure AD Connect Health con AD FS | Microsoft Docs
description: Describe el informe de direcciones IP de riesgo de Azure AD Connect Health para AD FS con libros de Azure Monitor.
services: active-directory
documentationcenter: ''
ms.reviewer: ''
author: billmath
manager: karen444
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/14/2021
ms.author: billmath
ms.custom: ''
ms.collection: ''
ms.openlocfilehash: 8463da51788c7faa680f42aaeb1999c174419366
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478425"
---
# <a name="risky-ip-report-workbook"></a>Libro del informe de direcciones IP de riesgo 
> [!NOTE]
> Para usar el libro del informe de direcciones IP de riesgo, debe habilitar "ADFSSignInLogs" en la hoja "Configuración de diagnóstico". Se trata de un flujo de Log Analytics con inicios de sesión de AD FS que se envía a Azure AD mediante Connect Health. Para obtener más información sobre los inicios de sesión de AD FS en Azure AD, consulte nuestra documentación.

Los clientes de AD FS pueden exponer puntos de conexión de autenticación de contraseña en Internet para proporcionar servicios de autenticación para que los usuarios finales tengan acceso a aplicaciones de SaaS, como Microsoft 365. En este caso, es posible que un actor no deseado intente inicios de sesión en el sistema de AD FS para adivinar la contraseña de usuario final y acceder a los recursos de la aplicación. AD FS proporciona la funcionalidad de bloqueo de cuenta de extranet para evitar estos tipos de ataques desde AD FS en Windows Server 2012 R2. Si se encuentra en una versión anterior, se recomienda que actualice el sistema de AD FS a Windows Server 2016. <br />

Además, es posible que una única dirección IP intente varios inicios de sesión en varios usuarios. En estos casos, puede que el número de intentos por usuario esté por debajo del umbral de la protección de bloqueo de cuenta de AD FS. Azure AD Connect Health ahora proporciona el "informe de direcciones IP de riesgo", que detecta esta condición y notifica a los administradores cuando esto ocurre. Estas son las ventajas principales de este informe: 
- Detección de direcciones IP que superan un umbral de inicios de sesión basada en contraseña erróneos
- Admite inicios de sesión con error debidos a una contraseña incorrecta o debidos a un estado de bloqueo de la extranet
- Admite la habilitación de alertas mediante alertas de Azure
- Configuración de umbrales personalizables ajustados a la directiva de seguridad de una organización
- Consultas personalizables y visualizaciones expandidas para un análisis más profundo
- Funcionalidad ampliada frente al informe anterior de direcciones IP de riesgo, que quedará en desuso después del 24 de enero de 2022.

## <a name="requirements"></a>Requisitos
1.  Connect Health para AD FS instalado y actualizado con el agente más reciente.
2.  Un área de trabajo de Log Analytics con el flujo "ADFSSignInLogs" habilitado.
3.  Permisos para usar los libros Azure AD Monitor. Para usar los libros, necesita:
- Un inquilino de Azure Active Directory con una licencia Premium (P1 o P2).
- Acceso a un área de trabajo de Log Analytics y a los siguientes roles en Azure AD (si se accede a Log Analytics mediante el portal de Azure AD): Administrador de seguridad, Lector de seguridad, Lector de informes, Administrador global.


## <a name="what-is-in-the-report"></a>Contenido del informe
El libro del informe de direcciones IP de riesgo se basa en los datos del flujo ADFSSignInLogs y tiene consultas preexistentes para poder visualizar y analizar rápidamente direcciones IP de riesgo. Los parámetros se pueden configurar y personalizar para los recuentos de umbrales. El libro también se puede configurar en función de las consultas, y cada consulta se puede actualizar y modificar según las necesidades de la organización.

El libro de direcciones IP de riesgo analiza los datos de ADFSSignInLogs para ayudarle a detectar ataques por fuerza bruta para la obtención de contraseñas o ataques de difusión de contraseña. El libro tiene dos partes. La primera parte "Análisis de IP de riesgo" identifica las direcciones IP de riesgo en función de los umbrales de error designados y la duración del período de detección. La segunda parte proporciona los detalles de inicio de sesión y el recuento de errores de las IP seleccionadas.

[![Captura de pantalla que muestra una vista del libro con ubicaciones.](./media/how-to-connect-health-adfs-risky-ip-workbook/workbook-template-overview-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/workbook-template-overview-1.png#lightbox)

- El libro muestra la visualización de un mapa y un desglose por regiones para un análisis rápido de la ubicación de las IP de riesgo.
- La tabla de detalles de las IP de riesgo se asemeja a la funcionalidad del informe anterior de direcciones IP de riesgo. Para obtener más información sobre los campos de la tabla, consulte la siguiente sección.
- La escala de tiempo de las IP de riesgo muestra una vista rápida de las anomalías o los picos en las solicitudes en una vista de escala de tiempo.
- Los detalles de inicio de sesión y el recuento de errores por IP permiten expandir la tabla de detalles con una vista filtrada detallada por IP o por usuario. 

Cada elemento de la tabla del informe de direcciones IP de riesgo muestra información agregada sobre las actividades de inicio de sesión de AD FS con errores que sobrepasan el umbral designado. La tabla proporciona la siguiente información: [. ![Captura de pantalla que muestra un informe de direcciones IP de riesgo con los encabezados de columna resaltados.](./media/how-to-connect-health-adfs-risky-ip-workbook/risky-ip-table-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/risky-ip-table-1.png#lightbox)

| Elemento de informe | Descripción |
| ------- | ----------- |
| Hora de inicio del período de detección | Muestra la marca de tiempo según la hora local de Azure Portal cuando se inicia la ventana de tiempo de detección.<br /> Todos los eventos diarios se generan a media noche hora UTC. <br />Los eventos por hora tienen la marca de tiempo redondeada al principio de la hora. Puede encontrar la primera hora de inicio de actividad en "firstAuditTimestamp" en el archivo exportado. |
| Duración del período de detección | Muestra el tipo de ventana de tiempo de detección. Los tipos de desencadenador de agregación son por hora o por día. Esto resulta útil para detecciones de ataques por fuerza bruta de alta frecuencia frente a un ataque lento en el que el número de intentos se distribuye a lo largo del día. |
| Dirección IP | La dirección IP de riesgo individual con actividades de inicio de sesión con contraseña incorrecta o de bloqueo de extranet. Podría tratarse de una dirección IPv4 o IPv6. |
| Número de errores de contraseña incorrecta (50126) | El número de errores de contraseña incorrecta producidos desde la dirección IP durante la ventana de tiempo de detección. Los errores de contraseña incorrecta pueden producirse varias veces con determinados usuarios. Tenga en cuenta que no se incluyen los intentos con error debidos a contraseñas que han expirado. |
| Número de errores de bloqueo de extranet (30030) | El número de errores de bloqueo de extranet que se han producido desde la dirección IP durante la ventana de tiempo de detección. Los errores de bloqueo de extranet pueden producirse varias veces con determinados usuarios. Esto solo es visible si se configura el bloqueo de extranet de AD FS (versiones 2012R2 o superior). <b>Nota:</b> Se recomienda activar esta característica si se permiten inicios de sesión de extranet mediante contraseñas. |
| Usuarios únicos con intentos | El número de cuentas de usuario únicas con intentos desde la dirección IP durante la ventana de tiempo de detección. Esto proporciona un mecanismo para diferenciar un patrón de ataque de un solo usuario en comparación con un patrón de ataque de varios usuarios.  |

Filtre el informe por dirección IP o nombre de usuario para ver una vista ampliada de los detalles de los inicios de sesión para cada evento de IP de riesgo.

## <a name="load-balancer-ip-addresses-in-the-list"></a>Direcciones IP del equilibrador de carga de la lista
La instancia agregada de Load Balancer no pudo iniciar la sesión de las actividades y alcanzó el umbral de alerta. Si ve direcciones IP del equilibrador de carga, es muy probable que el equilibrador de carga externo no esté enviando la dirección IP del cliente cuando pasa la solicitud al servidor proxy de la aplicación web. Configure el equilibrador de carga correctamente para que pase la dirección IP de reenvío del cliente. 

## <a name="configure-threshold-settings"></a>Configuración de las opciones del umbral
El umbral de alerta se puede actualizar mediante las Opciones del umbral. De forma inicial, el sistema tiene un umbral establecido de forma predeterminada. La configuración del umbral se puede establecer por horas o días de detección, y se puede personalizar con los filtros.

[![Filtros de umbral](./media/how-to-connect-health-adfs-risky-ip-workbook/threshold-settings-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/threshold-settings-1.png#lightbox)

| Elemento del umbral | Descripción |
| --- | --- |
| Umbral de errores de bloqueo de extranet y de contraseñas incorrectas  | Opción del umbral para informar de la actividad y desencadenar la notificación de alertas cuando el número de contraseñas erróneas más el número de bloqueos de extranet se ha superado por **hora o día**.|
| Umbral de errores de bloqueo de extranet | Opción del umbral para informar de la actividad y desencadenar la notificación de alertas cuando el número de bloqueos de extranet se ha superado por **hora o día**. El valor predeterminado es 50.|

La duración del período de detección por **hora o día** se puede configurar mediante el botón de alternancia situado encima de los filtros para personalizar los umbrales. 

## <a name="configure-notification-alerts-using-azure-monitor-alerts-through-the-azure-portal"></a>Configuración de alertas de notificación mediante las alertas de Azure Monitor en Azure Portal
[![Regla de alertas de Azure](./media/how-to-connect-health-adfs-risky-ip-workbook/azure-alerts-rule-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/azure-alerts-rule-1.png#lightbox)
1.  En Azure Portal, busque "Monitor" en la barra de búsqueda para ir al servicio "Monitor" de Azure. En el menú de la izquierda, seleccione "Alertas" y, luego, "+ Nueva regla de alertas". 
2.  En la hoja "Crear regla de alertas":
   * Ámbito: haga clic en "Seleccionar recurso" y seleccione el área de trabajo de Log Analytics que contiene el flujo ADFSSignInLogs que quiere supervisar.
   * Condición, haga clic en "Agregar condición". Seleccione "Registro" en "Tipo de señal" y "Log Analytics" en "Supervisar servicio". Elija "Custom log search" (Búsqueda de registros personalizada). 

3. Configure la condición para desencadenar la alerta. Para asegurarse de que las notificaciones por correo electrónico coinciden con el informe de direcciones IP de riesgo de Connect Health, siga las instrucciones que se indican a continuación.
   * Copie y pegue la siguiente consulta y especifique los umbrales de recuento de errores. Esta consulta genera el número de direcciones IP que superan los umbrales de error designados.

```
ADFSSignInLogs
| extend ErrorCode = ResultType
| where ErrorCode in ("50126", "300030")
| summarize badPasswordErrorCount = countif(ErrorCode == "50126"), extranetLockoutErrorCount = countif(ErrorCode == "300030") by IPAddress
| where extranetLockoutErrorCount > [TODO: put error count threshold here] 
```
En el caso de un umbral combinado:
```
badPasswordErrorCount + extranetLockoutErrorCount > [TODO: put error count threshold here] // Customized thresholds
```

> [!NOTE]
> La lógica de alerta significa que la alerta se desencadenará si al menos una dirección IP del recuento de errores de bloqueo de extranet o del recuento combinado de errores de contraseña incorrecta y bloqueo de extranet supera los umbrales designados. Puede seleccionar la frecuencia de evaluación de la consulta para detectar direcciones IP de riesgo.


## <a name="faq"></a>Preguntas más frecuentes
**¿Por qué veo direcciones IP del equilibrador de carga en el informe?**  <br />
Si ve direcciones IP del equilibrador de carga, es muy probable que el equilibrador de carga externo no esté enviando la dirección IP del cliente cuando pasa la solicitud al servidor proxy de la aplicación web. Configure el equilibrador de carga correctamente para que pase la dirección IP de reenvío del cliente. 

**¿Qué hacer para bloquear la dirección IP?**  <br />
Debe agregar la dirección IP identificada como malintencionada al firewall o bloquearla en Exchange.   <br />

**¿Por qué no veo ningún elemento de este informe?** <br />
- El flujo de Log Analytics "ADFSSignInLogs" no está habilitado en la configuración de diagnóstico.
- Las actividades de inicio de sesión con errores no superan los valores del umbral.
- Asegúrese de que no haya ninguna alerta del tipo "Health Service no está actualizado" en la lista de servidores de AD FS.  Obtenga más información sobre [cómo solucionar problemas de esta alerta](how-to-connect-health-data-freshness.md).
- Las auditorías no están habilitadas en las granjas de servidores de AD FS.

## <a name="next-steps"></a>Pasos siguientes
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalación del agente de Azure AD Connect Health](how-to-connect-health-agent-install.md)
