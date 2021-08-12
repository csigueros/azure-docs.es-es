---
title: Solución de problemas comunes de Device Update para Azure IoT Hub | Microsoft Docs
description: En este documento se proporciona una lista de trucos y sugerencias que pueden ayudarle a solucionar muchos de los problemas que pueden aparecer en Device Update para IoT Hub.
author: lichris
ms.author: lichris
ms.date: 2/17/2021
ms.topic: troubleshooting
ms.service: iot-hub-device-update
ms.openlocfilehash: f5ea8cfe1df2ae89bb67675c9bf235d62dca4bf5
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110082079"
---
# <a name="device-update-for-iot-hub-troubleshooting-guide"></a>Guía para la solución de problemas de Device Update para Azure IoT Hub

En este documento se enumeran varios problemas y preguntas comunes que los usuarios de actualizaciones de dispositivos han comunicado. A medida que Device Update avance en la etapa de versión preliminar pública, esta guía para la solución de problemas se actualizará periódicamente con nuevas preguntas y soluciones. Si se topa con algún problema que no aparece en esta guía, consulte la sección de [contacto con el soporte técnico de Microsoft](#contact) para documentar la situación.

## <a name="importing-updates"></a><a name="import"></a>Importación de actualizaciones

### <a name="q-im-having-trouble-connecting-my-device-update-instance-to-my-iot-hub-instance"></a>P: tengo problemas para conectar una instancia de Device Update a una instancia de IoT Hub.
_Asegúrese de que las rutas de mensajes de IoT Hub estén configuradas correctamente, tal como se indica en la documentación de [los recursos de Device Update](./device-update-resources.md)_ .

### <a name="q-im-encountering-a-role-related-error-error-message-in-azure-portal-or-a-403-api-error"></a>P: aparece un error relacionado con el rol (un mensaje de error de Azure Portal o error 403 de API).
_Es posible que los permisos de acceso no se hayan configurado correctamente. Asegúrese de que ha configurado los permisos de acceso correctamente, tal como se indica en la documentación del [control de acceso de Device Update](./device-update-control-access.md)._ .

### <a name="q-im-encountering-a-500-type-error-when-importing-content-to-the-device-update-service"></a>P: aparece un error del tipo 500 al importar contenido en el servicio Device Update.
_Un código de error del rango de 500 puede indicar que hay un problema en el servicio Device Update. Espere 5 minutos y vuelva a intentarlo. Si el error vuelve a aparecer, siga las instrucciones de la sección de [ contacto con el soporte técnico de Microsoft](#contact) para presentar una solicitud de soporte técnico a Microsoft_.

### <a name="q-im-encountering-an-error-code-when-importing-content-and-would-like-to-parse-it"></a>P: aparece un código de error al importar contenido y deseo analizarlo.
_Consulte la documentación de los [códigos de error de Device Update](./device-update-error-codes.md) para obtener información sobre el análisis de códigos de error_.

## <a name="device-failures"></a><a name="device-failure"></a>Errores de dispositivo

### <a name="q-how-can-i-ensure-my-device-is-connected-to-device-update-for-iot-hub"></a>P: ¿cómo puedo asegurarme de que un dispositivo está conectado a Device Update para IoT Hub?
_Para comprobar si un dispositivo está conectado a Device Update para IoT Hub, fíjese en si aparece en la sección de dispositivos desagrupados de la vista de cumplimiento de Azure Portal_.

### <a name="q-one-or-more-of-my-devices-is-failing-to-update"></a>P: uno o varios dispositivos no se actualizan.
_Si un dispositivo no se actualiza, puede deberse a varios motivos. Compruebe que: el dispositivo está conectado a una instancia de IoT Hub, el dispositivo está conectado a una instancia de Device Update y el servicio de optimización de distribución (SO) está en ejecución. Si los tres condicionantes se dan en el dispositivo, siga las instrucciones de la sección de [contacto con el soporte técnico de Microsoft](#contact) para presentar una solicitud de soporte técnico a Microsoft._

## <a name="deploying-an-update"></a><a name="deploy"></a> Implementación de una actualización

### <a name="q-ive-deployed-an-update-to-my-devices-but-the-compliance-status-says-it-isnt-on-the-latest-update-what-should-i-do"></a>P: he implementado una actualización en los dispositivos, pero el estado de cumplimiento indica que no es la más reciente. ¿Qué debo hacer?
_El estado de cumplimiento del dispositivo puede tardar hasta 5 minutos en actualizarse. Espere un tiempo y vuelva a comprobarlo._
### <a name="q-my-devices-deployment-status-shows-incompatible-what-should-i-do"></a>P: el estado de implementación del dispositivo muestra que no es compatible, ¿qué debo hacer?
_Es posible que se hayan cambiado propiedades de fabricante y modelo de un dispositivo de destino después de conectar el dispositivo a IoT Hub, lo que hace que el dispositivo se considere ahora incompatible con el contenido de actualización de la implementación actual._

_Compruebe la [interfaz principal de ADU](./device-update-plug-and-play.md) para ver qué fabricante y modelo indica el dispositivo al servicio Device Update, y asegúrese de que coinciden con el fabricante y modelo que especificó en el [manifiesto de importación](./import-concepts.md) del contenido de actualización que se va a implementar. Estas propiedades se pueden cambiar para un dispositivo determinado mediante el [archivo de configuración de Device Update](./device-update-configuration-file.md)._

### <a name="q-i-see-my-deployment-is-in-active-stage-but-none-of-my-devices-are-in-progress-with-the-update-what-should-i-do"></a>P: veo que una implementación está en la fase "Activa" (Activo), pero ninguno de los dispositivos está "In progress" (En curso) con la actualización. ¿Qué debo hacer?
_Asegúrese de que la fecha de inicio de la implementación no se ha establecido en el futuro. Cuando se crea una implementación, una forma de protegerla es hacer que el valor predeterminado de su fecha de inicio sea el día siguiente al de la creación, salvo que se cambie explícitamente. Puede esperar a que llegue la fecha de inicio de la implementación o cancelar la implementación en curso y crear una implementación con la fecha de inicio deseada._

### <a name="q-im-trying-to-group-my-devices-but-i-dont-see-the-tag-in-the-drop-down-when-creating-a-group"></a>P: intento agrupar varios dispositivos, pero no veo la etiqueta en la lista desplegable al crear un grupo.
_Asegúrese de que ha configurado correctamente las rutas de mensajes de IoT Hub, tal como se indica en la documentación de [los recursos de Device Update](./device-update-resources.md). Tras configurar la ruta tendrá que volver a etiquetar el dispositivo._

_Otra causa principal podría ser que aplicó la etiqueta antes de conectar el dispositivo a Device Update para IoT Hub. Asegúrese de que el dispositivo ya esta conectado a Device Update. Para comprobar si un dispositivo está conectado a Device Update para IoT Hub, fíjese en si aparece en los dispositivos "desagrupados" de la vista de cumplimiento. Agregue temporalmente una etiqueta de otro valor y, después, vuelva a agregar la etiqueta pretendida una vez que el dispositivo esté conectado._

_Si usa Device Provisioning Service (DPS), asegúrese de que etiqueta los dispositivos después de aprovisionarlos, no durante el proceso de creación de dispositivos. Si ya ha etiquetado el dispositivo durante el paso de creación de dispositivos, tendrá que etiquetar temporalmente el dispositivo con otro valor después de aprovisionarlo y, después, vuelva a agregar la etiqueta pretendida._

### <a name="q-my-deployment-completed-successfully-but-some-devices-failed-to-update"></a>P: mi implementación se ha completado correctamente, pero algunos dispositivos no se han actualizado.
_Puede deberse a un error del cliente en los dispositivos que no se han actualizado. Consulte la sección Errores de dispositivo de esta guía para la solución de problemas._

### <a name="q-i-encountered-an-error-in-the-ux-when-trying-to-initiate-a-deployment"></a>P: se ha producido un error en la experiencia del usuario al intentar iniciar una implementación.
_Puede haberlo provocado un error del servicio o la experiencia del usuario, o bien un problema de permisos de la API. Siga las instrucciones de la sección de [contacto con el soporte técnico de Microsoft](#contact) para presentar una solicitud de soporte técnico a Microsoft_.

### <a name="q-i-started-a-deployment-but-it-isnt-reaching-an-end-state"></a>P: he iniciado una implementación pero no llega a un estado final.
_Puede deberse a un problema de rendimiento del servicio, un error del servicio o un problema del cliente. Vuelva a intentar realizar la implementación dentro de 10 minutos. Si aparece el mismo problema, extraiga los registros del dispositivo y consulte la sección Errores de dispositivo de esta guía para la solución de problemas. Si el problema vuelve a aparecer, siga las instrucciones de la sección de [contacto con el soporte técnico de Microsoft](#contact) para presentar una solicitud de soporte técnico a Microsoft._

### <a name="q-i-migrated-from-a-device-level-agent-to-adding-the-agent-as-a-module-identity-on-the-device-and-my-update-shows-as-in-progress-even-though-it-has-been-applied-to-the-device"></a>P: he realizado la migración desde un agente de nivel de dispositivo para agregar el agente como una identidad de módulo en el dispositivo y mi actualización se muestra "en curso" aunque se haya aplicado al dispositivo.
_Es posible que se deba a que no se ha eliminado el agente anterior que se comunicaba a través del dispositivo gemelo. Al aprovisionar el agente de Device Update como un módulo (consulte los [procedimientos](device-update-agent-provisioning.md)) todas las comunicaciones entre el dispositivo y el servicio Azure Device Update se realizarán a través del módulo gemelo, por lo que no olvide etiquetar el módulo gemelo del dispositivo al crear [grupos](device-update-groups.md) y todas las [comunicaciones](device-update-plug-and-play.md) deben realizarse a través del módulo gemelo.

## <a name="downloading-updates-onto-devices"></a><a name="download"></a> Descarga de actualizaciones en dispositivos

### <a name="q-how-do-i-resume-a-download-when-a-device-has-reconnected-after-a-period-of-disconnection"></a>P: ¿cómo se reanuda una descarga cuando un dispositivo se ha vuelto a conectar después de un período de desconexión?
_La descarga se reanudará automáticamente cuando se restaura la conectividad en un periodo de 24 horas. A las 24 horas será preciso que el usuario reinicie la descarga._
## <a name="using-microsoft-connected-cache-mcc"></a><a name="mcc"></a> Uso de Caché conectada de Microsoft (MCC)

### <a name="q-i-am-encountering-an-issue-when-attempting-to-deploy-the-mcc-module-on-my-iot-edge-device"></a>P: surge un problema al intentar implementar el módulo de MCC en un dispositivo de IoT Edge.
_Consulte la [documentación de IoT Edge]() para implementar módulos de Edge en dispositivos de IoT Edge. Para comprobar si el módulo de MCC se ejecuta correctamente en el dispositivo de IoT Edge, vaya a http://localhost:5100/Summary._
### <a name="q-one-of-my-iot-devices-is-attempting-to-download-an-update-through-mcc-but-is-failing"></a>P: un dispositivos de IoT intenta descargar una actualización a través de MCC, pero se produce un error.
_Hay varios errores que pueden provocar que un dispositivo no pueda conectarse a MCC. Para diagnosticar el problema, recopile los registros de Ngnix y del cliente de DO (en la sección sobre cómo [contactar con el soporte técnico de Microsoft](#contact) encontrará instrucciones sobre la recopilación de registros de cliente)._

_Es posible que el dispositivo no extraiga contenido de Internet para pasarlo a su módulo de MCC porque la dirección URL que usa no esté permitida. Para determinar si es así, tendrá que comprobar las variables de entorno de IoT Edge en Azure Portal._
## <a name="contacting-microsoft-support"></a><a name="contact"></a>Contacto con el soporte técnico de Microsoft

Si surgen problemas que no se pueden resolver mediante las preguntas frecuentes anteriores, puede presentar una solicitud al soporte técnico de Microsoft a través de la interfaz de Azure Portal. En función de la categoría a la que pertenezca el problema, es posible que se le pida que recopile y comparta datos adicionales que ayuden al soporte técnico de Microsoft a investigar el problema. 

A continuación encontrará las instrucciones necesarias para recopilar cada tipo de datos. Puede usar [getDevices]() para comprobar la información adicional de la respuesta de la carga de la API.

Además, la siguiente información puede ser útil para limitar la causa principal del problema:
* El tipo de dispositivo que intenta actualizar (Azure Percept, IoT Edge Gateway u otro).
* El tipo de cliente de Device Update que se usa (basado en imágenes, basado en paquetes o simulador).
* El sistema operativo que utiliza el cliente.
* Detalles relativos a la arquitectura del dispositivo.
* Si ha usado correctamente Device Update para actualizar un dispositivo antes.

Si tiene cualquiera de los siguientes datos disponible, inclúyalo en la descripción del problema.

### <a name="collecting-client-logs"></a>Recopilación de registros de cliente

* En el dispositivo Raspberry PI hay dos conjuntos de registros que se encuentran aquí:

    ```markdown
    /adu/logs
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* En el caso del cliente con paquetes, los registros se encuentran aquí:

    ```markdown
    /var/log/adu
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* En el caso del simulador, los registros se encuentran aquí:

    ```markdown
    /tmp/aduc-logs
    ```

### <a name="error-codes"></a>Códigos de error
Es posible que se le pida que incluya códigos de error al notificar un problema relacionado con la importación de una actualización, un error de dispositivo o la implementación de una actualización.

Para obtener los códigos de error, examine la interfaz de [ADUCoreInterface](./device-update-plug-and-play.md). Consulte la documentación de los [códigos de error de Device Update](./device-update-error-codes.md), donde encontrará información sobre cómo analizar los códigos de error para el diagnóstico automático y la solución de problemas.

### <a name="trace-id"></a>Identificación de seguimiento
Es posible que se le pida que incluya un identificador de seguimiento al notificar un problema relacionado con la importación o implementación de una actualización.

El identificador de seguimiento de una acción de usuario determinada se puede encontrar en la respuesta de la API o en la sección Historial de importación de la interfaz de usuario de Azure Portal. 

Actualmente, solo se puede acceder a los identificadores de seguimiento de las acciones de implementación a través de la respuesta de la API.

### <a name="deployment-id"></a>Id. de implementación
Es posible que se le pida que incluya un identificador de implementación al notificar un problema relacionado con la implementación de una actualización.

El usuario crea el identificador de implementación cuando llama a la API para iniciar una implementación.

Actualmente, los identificadores de implementación para implementaciones que se inician desde la interfaz de usuario de Azure Portal se generan automáticamente y no se muestran al usuario.

### <a name="iot-hub-instance-name"></a>Nombre de instancia de IoT Hub
Es posible que se le pida que especifique el nombre de la instancia de IoT Hub al notificar un problema relacionado con los errores de dispositivos o la implementación de una actualización.

El usuario elige el nombre de la instancia de IoT Hub la primera vez que lo aprovisiona.

### <a name="device-update-account-name"></a>Nombre de cuenta de Device Update
Es posible que se le pida que especifique el nombre de la cuenta de Device Update al notificar un problema relacionado con la importación de una actualización, los errores de dispositivos o la implementación de una actualización.

El usuario elige el nombre de la cuenta de Device Update al registrarse para el servicio. Puede encontrar más información en la documentación de los [recursos de Device Update](./device-update-resources.md).

### <a name="device-update-instance-name"></a>Nombre de instancia de Device Update
Es posible que se le pida que especifique el nombre de la instancia de Device Update al notificar un problema relacionado con la importación de una actualización, los errores de dispositivos o la implementación de una actualización.

El usuario elige el nombre de la instancia de Device Update la primera vez que lo aprovisiona. Puede encontrar más información en la documentación de los [recursos de Device Update](./device-update-resources.md).

### <a name="device-id"></a>Id. de dispositivo
Es posible que se le pida que especifique un identificador de dispositivo al notificar un problema relacionado con los errores de dispositivos o la implementación de una actualización.

El cliente define el identificador de dispositivo la primera vez que lo aprovisiona. También se puede recuperar del dispositivo gemelo del dispositivo.

### <a name="update-id"></a>Id. de actualización
Es posible que se le pida que incluya un identificador de actualización al notificar un problema relacionado con la implementación de una actualización.

El cliente define el identificador de actualización al iniciar una implementación.

### <a name="nginx-logs"></a>Registros de Nginx
Es posible que se le pida que proporcione registros de Nginx al notificar un problema relacionado con Caché conectada de Microsoft.

### <a name="adu-conftxt"></a>ADU-conf.txt
Es posible que se le pida que proporcione el archivo de configuración de Device Update ("adu-conf.txt") al notificar un problema relacionado con la implementación de una actualización.

El archivo de configuración es opcional y lo crea el usuario siguiendo las instrucciones de la documentación de la [configuración de Device Update](./device-update-configuration-file.md).

### <a name="import-manifest"></a>Manifiesto de importación
Es posible que se le pida que incluya un archivo de manifiesto de importación al notificar un problema relacionado con la importación o implementación de una actualización.

El manifiesto de importación es un archivo que crea el cliente al importar el contenido de la actualización en el servicio Device Update.

**[Siguiente paso: más información sobre los códigos de error de Device Update](.\device-update-error-codes.md)**
