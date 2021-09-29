---
title: Solución de problemas de afinidad de sesión
titleSuffix: Azure Application Gateway
description: En este artículo se proporciona información sobre cómo solucionar problemas de afinidad de sesión en Azure Application Gateway
services: application-gateway
author: KumudD
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: kumud
ms.openlocfilehash: b175aedc743eb724b42a025839b7e32f59dea5da
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124737890"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Solución de problemas de afinidad de sesión en Azure Application Gateway

Aprenda a diagnosticar y resolver problemas de afinidad de sesión con Azure Application Gateway.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Información general

La característica de afinidad de sesión basada en cookies es útil cuando se quiere mantener una sesión de usuario en el mismo servidor. Mediante el uso de cookies administradas por la puerta de enlace, Application Gateway puede dirigir el tráfico posterior de una sesión de usuario hasta el mismo servidor para su procesamiento. Esto es importante en aquellos casos en los que se guarda el estado de la sesión de forma local en el servidor para una sesión de usuario.

## <a name="possible-problem-causes"></a>Causas posibles del problema

El problema en el mantenimiento de la afinidad de sesión basada en cookies puede ocurrir debido a los siguientes motivos principales:

- La opción de configuración "Afinidad basada en cookies" no está habilitada
- La aplicación no puede administrar la afinidad basada en cookies
- La aplicación está usando la afinidad basada en cookies, pero las solicitudes siguen rebotando entre los servidores back-end

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Comprobación de si está habilitada la opción de configuración "Afinidad basada en cookies"

A veces, pueden producirse problemas de afinidad de sesión cuando se olvida de habilitar la opción de configuración "Afinidad basada en cookies". Para determinar si se ha habilitado la opción de configuración "Afinidad basada en cookies" en la pestaña Configuración de HTTP en Azure Portal, siga las instrucciones:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

2. En el **panel de navegación izquierdo**, haga clic en **Todos los recursos**. Haga clic en el nombre de la puerta de enlace de aplicación en la hoja Todos los recursos. Si la suscripción que seleccionó ya tiene varios recursos, puede escribir el nombre de la puerta de enlace de aplicación en el cuadro **Filtrar por nombre...** para acceder fácilmente a la puerta de enlace de la aplicación.

3. Seleccione la pestaña **Configuración HTTP**, en **CONFIGURACIÓN**.

   ![Captura de pantalla que muestra la sección Configuración con la configuración de HTTP seleccionada.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Haga clic en **appGatewayBackendHttpSettings** en el lado derecho para comprobar si ha seleccionado **Enabled** (Habilitado) para la afinidad basada en cookies.

   ![Captura de pantalla que muestra la configuración de puerta de enlace de una puerta de enlace de aplicaciones, incluido si está seleccionada la afinidad basada en cookies.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



También puede comprobar que el valor de "**CookieBasedAffinity**" está establecido en *Enabled*, bajo "**backendHttpSettingsCollection**" mediante uno de los métodos siguientes:

- Ejecute [Get-AzApplicationGatewayBackendHttpSetting](/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) en PowerShell
- Examine el archivo JSON con la plantilla de Azure Resource Manager

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>La aplicación no puede administrar la afinidad basada en cookies

#### <a name="cause"></a>Causa

La puerta de enlace de aplicación solo puede realizar la afinidad basada en la sesión mediante el uso de una cookie.

#### <a name="workaround"></a>Solución alternativa

Si la aplicación no puede controlar la afinidad basada en cookies, debe usar un equilibrador de carga interno o externo, u otra solución de terceros.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>La aplicación está usando la afinidad basada en cookies, pero las solicitudes siguen rebotando entre los servidores back-end

#### <a name="symptom"></a>Síntoma

Si ha habilitado la opción de configuración Afinidad basada en cookies, al acceder a Application Gateway con una dirección URL con un nombre corto en Internet Explorer, por ejemplo, `http://website`, es posible que la solicitud rebote permanentemente entre los servidores back-end.

Para identificar este problema, siga las instrucciones:

1. Realice un seguimiento del depurador web en el "cliente" que se conecta a la aplicación detrás de Application Gateway (en este ejemplo, se usa Fiddler).
    **Sugerencia** Si no sabe cómo usar Fiddler, active la opción "**I want to collect network traffic and analyze it using web debugger**" (Deseo recopilar el tráfico de red y analizarlo con el depurador web) en la parte inferior.

2. Compruebe y analizar los registros de sesión, para determinar si las cookies proporcionadas por el cliente tienen los detalles de ARRAffinity. Si no encuentra los detalles de ARRAffinity, como "**ARRAffinity=** *ARRAffinityValue*" dentro del conjunto de cookies, significa que el cliente no está respondiendo con la cookie ARRA, que Application Gateway proporciona.
    Por ejemplo:

    ![Captura de pantalla que muestra un registro de sesión con una única entrada resaltada.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![Captura de pantalla que muestra los encabezados de solicitud para HTTP, incluida la información de cookies.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

La aplicación continúa intentando establecer la cookie en cada solicitud hasta que obtiene respuesta.

#### <a name="cause"></a>Causa

Este problema se produce porque Internet Explorer y otros exploradores no pueden almacenar o usar la cookie con una dirección URL de nombre corto.

#### <a name="resolution"></a>Solución

Para corregir esta incidencia, debe acceder a Application Gateway con un nombre de dominio completo. Por ejemplo, use [http://website.com](https://website.com/) o [http://appgw.website.com](http://website.com/).

## <a name="additional-logs-to-troubleshoot"></a>Registros adicionales para solucionar problemas

Puede recopilar registros adicionales y analizarlos para solucionar problemas relacionados con la afinidad de sesión basada en cookies

### <a name="analyze-application-gateway-logs"></a>Análisis de los registros de Application Gateway

Para recopilar los registros de Application Gateway, siga las instrucciones:

Habilitación del registro mediante Azure Portal

1. En [Azure Portal](https://portal.azure.com/), busque su recurso y haga clic en **Registros de diagnóstico**.

   Hay tres registros de auditoría disponibles para Application Gateway: Registro de acceso, registro de rendimiento, registro de firewall

2. Para empezar a recopilar los datos, haga clic en **Activar diagnósticos**.

   ![Captura de pantalla que muestra una puerta de enlace de aplicación con los registros de diagnóstico seleccionados.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. En la hoja **Configuración de diagnóstico**, se encuentran las opciones de configuración de los registros de diagnóstico. En este ejemplo, se utiliza Log Analytics para almacenar los registros. Haga clic en la opción **Configurar** bajo **Log Analytics** para establecer el área de trabajo. Se pueden utilizar también Events Hubs y la cuenta de almacenamiento para guardar los registros de diagnóstico.

   ![Captura de pantalla que muestra el panel Configuración de diagnóstico con la opción Configurar Log Analytics seleccionada.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Confirme la configuración y haga clic en **Guardar**.

   ![Captura de pantalla que muestra el panel Configuración de diagnóstico con la opción Guardar seleccionada.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Visualización y análisis de los registros de acceso de Application Gateway

1. En Azure Portal, en la vista de recursos de Application Gateway, seleccione **Registros de diagnóstico** en la sección **SUPERVISIÓN**.

   ![Captura de pantalla que muestra la sección Supervisión con los registros de diagnóstico seleccionados.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. En el lado derecho, seleccione "**ApplicationGatewayAccessLog**" en la lista desplegable bajo **Categorías de registro.**  

   ![Captura de pantalla que muestra la lista desplegable de categorías de registro con ApplicationGatewayAccessLog seleccionado.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. En la lista de registro de acceso de Application Gateway, haga clic en el registro que desee analizar y exportar, y, a continuación, exporte el archivo JSON.

4. Convierta el archivo JSON que exportó en el paso 3 a CSV y véalo en Excel, Power BI o cualquier otra herramienta de visualización de datos.

5. Compruebe los datos siguientes:

- **ClientIP**: se trata de la dirección IP del cliente que se conecta.
- **ClientPort**: es el puerto de origen del cliente que se conecta para la solicitud.
- **RequestQuery**: indica el servidor de destino en el que se recibe la solicitud.
- **Server-Routed**: instancia del grupo de back-end a la que se ha enviado la solicitud.
- **X-AzureApplicationGateway-LOG-ID**: identificador de correlación que se ha usado para la solicitud. Se puede utilizar para solucionar problemas de tráfico en los servidores back-end. Por ejemplo: X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **SERVER-STATUS**: código de respuesta HTTP que Application Gateway ha recibido del back-end.

  ![Captura de pantalla que muestra el estado del servidor en texto sin formato, en su mayoría oscurecido, con clientPort y SERVER-ROUTED resaltados.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Si ve que dos elementos proceden de la misma dirección IP y puerto de cliente, y se envían al mismo servidor back-end, significa que Application Gateway se ha configurado correctamente.

Si ve que dos elementos proceden de la misma dirección IP y puerto de cliente, y se envían a los distintos servidores de back-end, significa que la solicitud está rebotando entre los servidores back-end, seleccione "**Application is using cookie-based affinity but requests still bouncing between back-end servers**" (La aplicación usa la afinidad basada en cookies, pero las solicitudes siguen rebotando entre los servidores back-end) en la parte inferior para solucionar el problema.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>Uso del depurador web para capturar y analizar el tráfico HTTP o HTTPS

Las herramientas de depuración web, como Fiddler Web, pueden ayudarle a depurar las aplicaciones web mediante la captura de tráfico de red entre los equipos de prueba e Internet. Estas herramientas permiten inspeccionar los datos entrantes y salientes a medida que el explorador los reciba o los envíe. Fiddler, en este ejemplo, tiene la opción de reproducir HTTP que puede ayudarle a solucionar problemas del cliente con las aplicaciones web, especialmente los relacionados con la autenticación.

Use el depurador web que prefiera. En este ejemplo usaremos Fiddler para capturar y analizar el tráfico HTTP o HTTPS; siga las instrucciones:

1. Descargue la herramienta Fiddler de <https://www.telerik.com/download/fiddler>.

    > [!NOTE]
    > Elija Fiddler4 si el equipo de captura tiene instalado .NET 4. De lo contrario, elija Fiddler2.

2. Haga clic con el botón derecho en el ejecutable del programa de instalación y ejecútelo como administrador para instalarlo.

    ![Captura de pantalla que muestra el programa de instalación de la herramienta Fiddler con un menú contextual con la opción Ejecutar como administrador seleccionada.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Cuando abra Fiddler, debe empezar automáticamente a capturar el tráfico (observe la captura en la esquina inferior izquierda). Presione F12 para iniciar o detener la captura del tráfico.

    ![Captura de pantalla que muestra el depurador web de Fiddler con el indicador de captura resaltado.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Probablemente, estará interesado en el tráfico HTTPS descifrado y puede habilitar el descifrado de HTTPS si selecciona **Herramientas** > **Opciones de Fiddler** y activa la casilla " **Descifrar el tráfico HTTPS**".

    ![Captura de pantalla que muestra las opciones de Fiddler con HTTP seleccionado y el descifrado del tráfico HTTPS seleccionado.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. Puede quitar las sesiones no relacionadas anteriores antes de reproducir el problema si hace clic en **X** (icono) > **Quitar todo** como en la captura de pantalla siguiente: 

    ![Captura de pantalla que muestra el icono X seleccionado, que muestra la opción Eliminar todo.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. Una vez que se haya reproducido el problema, guarde el archivo para su revisión; para ello, seleccione **Archivo** > **Guardar** > **Todas las sesiones...** . 

    ![Captura de pantalla que muestra la opción del menú Archivo para guardar todas las sesiones seleccionada.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Compruebe y analice los registros de sesiones para determinar cuál es el problema.

    Por ejemplo:

- **Ejemplo A:** Busque un registro de sesión que indique que la solicitud se envía desde el cliente y va a la dirección IP pública de Application Gateway; haga clic en este registro para ver los detalles.  En el lado derecho, los datos del cuadro inferior es lo que Application Gateway devuelve al cliente. Seleccione la pestaña "SIN FORMATO" y determine si el cliente recibe un valor "**Set-Cookie: ARRAffinity=** *ARRAffinityValue*." Si no hay ninguna cookie, la afinidad de la sesión no está establecida o bien Application Gateway no está aplicando la cookie al cliente.

   > [!NOTE]
   > Este valor ARRAffinity es el identificador de la cookie, que Application Gateway establece para que el cliente se envíe a un servidor back-end determinado.

   ![Captura de pantalla que muestra un ejemplo de los detalles de una entrada del registro con el valor Set-Cookie resaltado.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **Ejemplo B:** El siguiente registro de sesiones seguido del anterior es el cliente que responde a Application Gateway, que estableció el valor de ARRAAFFINITY anterior. Si el identificador de cookie de ARRAffinity coincide, el paquete se debe enviar al mismo servidor back-end que se usó anteriormente. Compruebe las siguientes líneas de comunicación HTTP para ver si la cookie de ARRAffinity del cliente está cambiando.

   ![Captura de pantalla que muestra un ejemplo de los detalles de una entrada del registro con una cookie resaltada.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> En la misma sesión de comunicación, la cookie no se debe cambiar. Active la casilla superior en el lado derecho, seleccione la pestaña "Cookies" para ver si el cliente usa la cookie y la envía de vuelta a Application Gateway. De lo contrario, el explorador del cliente no está manteniendo y usando la cookie para las conversaciones. En ocasiones, el cliente podría mentir.

 

## <a name="next-steps"></a>Pasos siguientes

Si los pasos anteriores no resuelven el problema, abra una [incidencia de soporte técnico](https://azure.microsoft.com/support/options/).