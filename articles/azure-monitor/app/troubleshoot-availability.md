---
title: Solución de problemas de las pruebas de disponibilidad de Azure Application Insights
description: Solucione problemas de pruebas web en Azure Application Insights. Obtenga alertas si un sitio web deja de estar disponible o responde con lentitud.
ms.topic: conceptual
ms.date: 02/14/2021
ms.reviewer: sdash
ms.openlocfilehash: 09e7a59e25be45dcd0d0c68612fbe13715200d2f
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131702"
---
# <a name="troubleshooting"></a>Solución de problemas

En este artículo se proporciona ayuda para solucionar problemas comunes que pueden producirse al usar la supervisión de disponibilidad.

## <a name="troubleshooting-report-steps-for-ping-tests"></a>Pasos del informe de solución de problemas en las pruebas de ping

El informe de solución de problemas le permite diagnosticar fácilmente los problemas más comunes que provocan errores en las **pruebas de ping**.

![En esta animación el cursor se desplaza desde la pestaña de disponibilidad al seleccionar un error en los detalles de la transacción de un extremo a otro, para así poder ver el informe de solución de problemas.](./media/troubleshoot-availability/availability-to-troubleshooter.gif)

1. En la pestaña de disponibilidad del recurso de Application Insights, seleccione todas o una de las pruebas de disponibilidad.
2. Seleccione **Errónea** y una prueba de la sección "Drill into" (Obtener más detalles) en la parte izquierda, o seleccione uno de los puntos del gráfico de dispersión.
3. En la página de detalles de la transacción de un extremo a otro, seleccione un evento y, a continuación, en "Resumen del informe de solución de problemas", seleccione **[Go to step]** (Ir al paso) para ver el informe de solución de problemas.

> [!NOTE]
>  Si el paso para reutilizar la conexión está presente, los pasos de resolución de DNS, establecimiento de conexión y transporte de TLS no estarán presentes.

|Paso | Mensaje de error | Causa posible |
|-----|---------------|----------------|
| Reutilización de conexiones | N/D | Normalmente depende de una conexión establecida previamente, lo que significa que el paso de prueba web es dependiente. Por lo tanto, no se requiere ningún paso de DNS, conexión o SSL. |
| Resolución DNS | No se ha podido resolver el nombre remoto "Su dirección URL". | Se produjo un error en el proceso de resolución de DNS, probablemente debido a errores de configuración de DNS o del servidor DNS temporal. |
| Establecimiento de la conexión | Se ha producido un error en el intento de conexión porque la parte conectada no respondió correctamente después de un período de tiempo. | En general, esto significa que el servidor no responde a la solicitud HTTP. Una causa común es que un firewall del servidor haya bloqueado nuestros agentes de pruebas. Si quiere realizar pruebas en una instancia de Azure Virtual Network, debe agregar la etiqueta del servicio de disponibilidad al entorno.|
| Transporte TLS  | El cliente y el servidor no pueden comunicarse, ya que no poseen un algoritmo común".| Solo se admiten TLS 1.0, 1.1 y 1.2. SSL no se admite. Este paso no valida los certificados SSL y solo establece una conexión segura. Este paso solo se muestra cuando se produce un error. |
| Recepción del encabezado de respuesta | No se pueden leer los datos de la conexión de transporte. La conexión se cerró. | El servidor confirmó un error de protocolo en el encabezado de respuesta. Por ejemplo, la conexión que cerró el servidor cuando la respuesta no está completa. |
| Recepción del cuerpo de la respuesta | No se pueden leer los datos de la conexión de transporte: La conexión se cerró. | El servidor confirmó un error de protocolo en el cuerpo de la respuesta. Por ejemplo, el servidor cierra la conexión cuando la respuesta no se lee completamente o el tamaño del fragmento es incorrecto en el cuerpo de respuesta fragmentado. |
| Validación del límite de redirección | Esta página web tiene demasiadas redirecciones. El bucle finalizará aquí, ya que esta solicitud superó el límite de redirecciones automáticas. | Hay un límite de 10 redirecciones por prueba. |
| Validación del código de estado | `200 - OK` no coincide con el estado esperado `400 - BadRequest`. | el código de estado devuelto que se considera correcto. 200 es el código que indica que se ha devuelto una página web normal. |
| Validación del contenido | El texto necesario "hello" no aparece en la respuesta. | Por lo tanto, la cadena no es una coincidencia exacta con distinción de mayúsculas y minúsculas en la respuesta; por ejemplo, la cadena "Welcome!". Esta debe ser una cadena sin formato ni caracteres comodín (por ejemplo, un asterisco). Tenga en cuenta que, si cambia el contenido de la página, es posible que tenga que actualizar la cadena. En la coincidencia de contenido solo se admiten caracteres en inglés. |
  
## <a name="common-troubleshooting-questions"></a>Preguntas habituales sobre la solución de problemas

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Este sitio parece correcto, pero se ven errores de pruebas. ¿Por qué recibo alertas de Application Insights?

   * ¿La prueba tiene habilitada la opción **Analizar solicitudes dependientes**? Esta opción da como resultado una comprobación estricta de los recursos, como las secuencias de comandos y las imágenes, entre otros. Estos tipos de errores pueden no ser visibles en un explorador. Compruebe todas las imágenes, los scripts, las hojas de estilo y cualquier otro archivo cargado que haya cargado la página. Si se produce un error en cualquiera de ellos, se notifica que la prueba ha concluido con errores, incluso si la página HTML principal se carga correctamente. Para reducir la sensibilidad de la prueba para tales errores de recursos, simplemente desactive la opción "Analizar las solicitudes dependientes" de la configuración de pruebas.

   * Para reducir las probabilidades de ruido de señales de red transitorias etc., asegúrese de que se compruebe la configuración "Habilitar reintentos para errores de pruebas". También puede realizar pruebas desde más ubicaciones y administrar el umbral de la regla de alertas en consecuencia para evitar problemas específicos de ubicación que causan las alertas innecesarias.

   * Para ver los detalles de por qué se notificó el error, haga clic en cualquiera de los puntos rojos del gráfico de dispersión de la experiencia de disponibilidad o en cualquier error de disponibilidad del Explorador de búsqueda. El resultado de la prueba, junto con la telemetría del lado servidor correlacionada (si la opción está habilitada) ayudará a comprender el motivo del error de la prueba. Los problemas de conexión o red son las causas más comunes de los problemas transitorios.

   * ¿Se agotó el tiempo de espera de la prueba? Las pruebas se anulan después de 2 minutos. Si la prueba de ping o de varios pasos tarda más de 2 minutos, se notificará como un error. Considere la posibilidad de dividir la prueba en varias partes más pequeñas que puedan completarse en lapsos más cortos.

   * ¿Todas las ubicaciones ha informado de un error, o solo algunas de ellas? Si solo algunas informan de errores, puede ser debido a problemas de red o CDN. De nuevo, hacer clic en los puntos rojos debe ayudar a entender por qué la ubicación notifica errores.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>No recibí ningún correo electrónico cuando la alerta se desencadenó, se resolvió, o ambos.

Compruebe la configuración del grupo de acciones de las alertas para confirmar que el correo electrónico aparece directamente en la lista, o bien que la lista de distribución en la que se encuentra esté configurada para recibir notificaciones. De ser así, compruebe la configuración de la lista de distribución para confirmar que puede recibir mensajes de correo electrónico externos. Compruebe también si el administrador de correo tiene configurada alguna directiva que pueda estar causando este problema.

### <a name="i-did-not-receive-the-webhook-notification"></a>No recibí la notificación de webhook.

Compruebe que la aplicación que recibe la notificación de webhook esté disponible y procese correctamente las solicitudes de webhook. Para obtener más información, consulte [este artículo](../alerts/alerts-log-webhook.md).

### <a name="i-am-getting--403-forbidden-errors-what-does-this-mean"></a>Obtengo errores 403 Prohibido, ¿qué significa esto?

Este error indica que es necesario agregar excepciones de firewall para permitir que los agentes de disponibilidad prueben la dirección URL de destino. Para obtener una lista completa de las direcciones IP del agente admitidas, consulte el [artículo sobre excepciones de IP](./ip-addresses.md#availability-tests).

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Error de prueba intermitente con un error de infracción de protocolo.

El error ("infracción del protocolo... CR debe ir seguido de LF") indica un problema con el servidor (o las dependencias). Aparece cuando se establecen los encabezados con formato incorrecto en la respuesta. Puede deberse a equilibradores de carga o CDN. En concreto, algunos encabezados podrían no estar usando CRLF para indicar el final de línea, lo que provoca una infracción de la especificación del HTTP y, por tanto, no superan la validación en el nivel de WebRequest de .NET. Inspeccione la respuesta para detectar encabezados que podrían estar cometiendo una infracción.

> [!NOTE]
> La dirección URL podría no dar error en los exploradores que tienen una validación poco minuciosa de encabezados HTTP. Consulte esta entrada del blog para obtener una explicación detallada del problema: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>No veo telemetría de servidor relacionada para diagnosticar errores de pruebas.

Si tiene Application Insights configurado para la aplicación de servidor, esto puede deberse a que se está llevando a cabo un [muestreo](./sampling.md). Seleccione un resultado de disponibilidad diferente.

### <a name="can-i-call-code-from-my-web-test"></a>¿Puedo llamar a código desde mi prueba web?

No. Los pasos de la prueba deben encontrarse en el archivo .webtest. Y no se puede llamar a otras pruebas web ni utilizar bucles. Pero hay varios complementos que pueden resultarle útiles.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>¿Existe alguna diferencia entre las "pruebas web" y las "pruebas de disponibilidad"?

Los dos términos se pueden usar indistintamente. El término "pruebas de disponibilidad" es más genérico, e incluye las pruebas de ping de dirección URL única y las pruebas web de varios pasos.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Me gustaría usar pruebas de disponibilidad en nuestro servidor interno que se ejecuta detrás de un firewall.

   Hay dos soluciones posibles:

   * Configure el firewall para que permita las solicitudes entrantes de las [direcciones IP de los agentes de prueba web](./ip-addresses.md).
   * Escriba su propio código para comprobar periódicamente el servidor interno. Ejecute el código como un proceso en segundo plano en un servidor de prueba detrás del firewall. El proceso de prueba puede enviar sus resultados a Application Insights mediante la API [TrackAvailability()](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) en el paquete de SDK principal. Para ello, es necesario que el servidor de prueba tenga acceso saliente al punto de conexión de ingesta de Application Insights, pero plantea un riesgo de seguridad mucho menor que la alternativa de permitir las solicitudes entrantes. Los resultados aparecerán en las hojas de pruebas web de disponibilidad, aunque la experiencia se verá ligeramente simplificada con respecto a lo que está disponible para las pruebas creadas mediante el portal. Las pruebas de disponibilidad personalizadas también aparecerán como resultados de disponibilidad en los análisis, las búsquedas y las métricas.

### <a name="uploading-a-multi-step-web-test-fails"></a>Al cargar una prueba web de varios pasos, se produce un error

Algunas razones por las que puede ocurrir:
   * Hay un límite de tamaño de 300 000.
   * No se admiten los bucles.
   * No se admiten referencias a otras pruebas web.
   * No se admiten orígenes de datos.

### <a name="my-multi-step-test-doesnt-complete"></a>No se completa la prueba de varios pasos

Hay un límite de 100 solicitudes por prueba. Además, la prueba se detiene si se ejecuta durante más de dos minutos.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>¿Cómo se puede ejecutar una prueba con certificados de cliente?

Actualmente no se admite.

## <a name="next-steps"></a>Pasos siguientes

* [Pruebas web de varios pasos](availability-multistep.md)
* [Pruebas de ping de dirección URL](monitor-web-app-availability.md)